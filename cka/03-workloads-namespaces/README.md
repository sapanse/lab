# Lab 03: Namespaces, Labels, Selectors and Scaling

## Goal and prerequisites

Select Pods by labels and namespace, distinguish direct Pod changes from
Deployment template changes, and observe scaling versus a template rollout.

Use the `rancher-desktop` cluster and Lab 01's `web` Deployment and Service in
`cka-lab`. The reusable manifest remains at
[`../01-deployments-services/deployment.yaml`](../01-deployments-services/deployment.yaml).
This exercise reuses that workload rather than creating another Deployment.

## Completed practice — October 12, 2026

- [x] Filter Pods by `app=web` and display their labels.
- [x] Query matching Pods across all namespaces.
- [x] Add a label to one existing Pod and select it.
- [x] Delete that Pod and verify the replacement lacks the manually added label.
- [x] Add the label to the Deployment's Pod template and observe a rollout.
- [x] Delete a new Pod and verify its replacement inherits the template label.
- [x] Scale from three replicas to two and back to three without changing the template.

I performed the commands and edited the manifest myself. Tutor hints helped with
command syntax, template placement and interpreting ReplicaSets. I initially
expected scaling to create a new ReplicaSet, then corrected that understanding
after inspecting the results.

## 1. Namespace scope and selectors

I had set `cka-lab` as the current context's default namespace:

```bash
kubectl config set-context "$(kubectl config current-context)" --namespace=cka-lab
```

This changes the namespace setting for the current context; it does not switch
clusters. Confirm `rancher-desktop` before using the shorter commands below:

```bash
kubectl config current-context
kubectl get pods -l app=web --show-labels
kubectl get pods -l app=web --show-labels -A
```

The first query returned three matching Pods. The second added a `NAMESPACE`
column; all matches were in `cka-lab`. A query scoped to `default` would not find
these Pods. That namespace comparison was discussed conceptually; no output
from a `default` query was recorded.

`-l` filters by label; `-A` means all namespaces. Additional labels on a Pod do
not prevent a match for `app=web`. Label keys and values must match exactly:
`app=webb` returned `No resources found in cka-lab namespace` because the value
was mistyped. This was an empty result, not a syntax error.

To check unfamiliar command syntax, use `kubectl get --help` or the
[official kubectl get reference](https://kubernetes.io/docs/reference/kubectl/generated/kubectl_get/)
and find `--selector` and `--show-labels`.

## 2. Label one existing Pod

I ran:

```bash
kubectl label pod web-85b88cddc-cxt4c environment=practice
kubectl get pods -l environment=practice --show-labels -A
```

Only that Pod matched, with these labels:

```text
app=web,environment=practice,pod-template-hash=85b88cddc
```

Its `app=web` label remained, so it still matched the Service selector. Adding
`environment=practice` directly to this Pod did not update its controller's
template.

Historical Pod names here document my observations. When repeating the exercise,
choose an actual Pod name from the current list; do not reuse an old name blindly.

## 3. Delete the directly labelled Pod

```bash
kubectl delete pod web-85b88cddc-cxt4c
kubectl get pods -l app=web --show-labels
kubectl get pods -l environment=practice --show-labels -A
```

The replacement was `web-85b88cddc-6pn9l`, ready with labels
`app=web,pod-template-hash=85b88cddc`. It lacked `environment=practice`.
The environment-selector query returned `No resources found`.

My prediction was correct: a replacement gets its configuration from the
ReplicaSet's Pod template, not from manual edits to the deleted Pod.

## 4. Make the label persist through replacements

For a Deployment-managed workload, edit the Deployment's Pod template. I added
`environment: practice` under `spec.template.metadata.labels` in Lab 01's
manifest, retaining `app: web`:

```yaml
spec:
  replicas: 3
  selector:
    matchLabels:
      app: web
  template:
    metadata:
      labels:
        app: web
        environment: practice
```

This is an excerpt, not a complete manifest. The Deployment's top-level
`metadata.labels` labels the Deployment itself; template labels label its Pods.
The Pod template still matches `spec.selector.matchLabels` and the Service's
`app=web` selector.

From the `01-deployments-services` directory, I ran:

```bash
kubectl apply -f deployment.yaml
kubectl rollout status deployment/web
kubectl get pods -l app=web --show-labels
kubectl get replicasets -l app=web
kubectl get pods -l environment=practice --show-labels -A
```

Changing the template triggered a rollout. Observed ReplicaSet counts:

```text
NAME             DESIRED   CURRENT   READY
web-65cc694f8c   3         3         3
web-85b88cddc    0         0         0
```

All three new Pods had `app=web,environment=practice` and
`pod-template-hash=65cc694f8c`. One old Pod briefly showed `Terminating` while
cleanup finished. The old ReplicaSet remained at zero as rollout history.

## 5. Verify persistent labels on replacement

```bash
kubectl delete pod web-65cc694f8c-7znrs
kubectl get pods -l environment=practice --show-labels -A
```

The replacement, `web-65cc694f8c-r2g2p`, was ready and inherited
`environment=practice`. There were again three ready matching Pods. The hash
stayed `65cc694f8c` because the template had not changed.

## 6. Scale down and back up

I edited `spec.replicas` from `3` to `2` in the saved Deployment manifest:

```bash
kubectl apply -f deployment.yaml
kubectl get all
kubectl get replicasets -l app=web
kubectl get pods -l app=web --show-labels
```

Observed: `web-65cc694f8c` had `2` desired, current and ready replicas. The two
remaining Pods kept their names and template hash; the older ReplicaSet stayed
at zero. No new ReplicaSet was created by scaling.

I restored `spec.replicas` to `3` and applied again. Final verification:

```text
NAME             DESIRED   CURRENT   READY
web-65cc694f8c   3         3         3
web-85b88cddc    0         0         0

NAME                   READY   STATUS    LABELS
web-65cc694f8c-94kdz   1/1     Running   app=web,environment=practice,pod-template-hash=65cc694f8c
web-65cc694f8c-md2hk   1/1     Running   app=web,environment=practice,pod-template-hash=65cc694f8c
web-65cc694f8c-tnkqd   1/1     Running   app=web,environment=practice,pod-template-hash=65cc694f8c
```

Two existing Pods remained; only `web-65cc694f8c-md2hk` was added.

## What I learned

| Change | Observed behavior |
| --- | --- |
| Add a label directly to one Pod | Only that Pod changes; a replacement does not inherit the label. |
| Add a label to the Deployment's Pod template | A rollout creates Pods from the updated template; replacements retain the label. |
| Change only `spec.replicas` | The existing active ReplicaSet scales; the Pod template and its hash stay the same. |

The template is the blueprint for each Pod; replicas is the number of copies.
A template change triggers a rollout, usually creating a new ReplicaSet. A
previously used template can reuse its existing ReplicaSet. The template hash
identifies the template, not the number of replicas or an individual Pod.

## Final state and cleanup

No separate namespace or workload was added. The practice Deployment is back
to three ready replicas. Its saved template intentionally retains
`environment: practice` for future exercises. The directly labelled Pod was
deleted, so no one-off label remains to remove.

The workload remains available for the next lab. When finished with it, use
Lab 01's scoped cleanup from its directory:

```bash
kubectl --context rancher-desktop delete -f service.yaml -f deployment.yaml
```

This removes the shared practice application; keep it running until ready to
clean it up. Do not delete the whole namespace as part of this exercise.

## Course review and next session

Tomorrow, October 13, reinforce labels and selectors before moving to rolling
updates. References use filenames in the copied course, not Udemy lecture numbers:

- [S04: Labels and Selectors](https://github.com/kodekloudhub/certified-kubernetes-administrator-course/blob/master/docs/03-Scheduling/04-Labels-and-Selectors.md)
- [S05: Practice Test — Labels and Selectors](https://github.com/kodekloudhub/certified-kubernetes-administrator-course/blob/master/docs/03-Scheduling/05-Practice-Test-Labels-and-Selectors.md): attempt the live lab before reading answers.
- [C18: Namespaces](https://github.com/kodekloudhub/certified-kubernetes-administrator-course/blob/master/docs/02-Core-Concepts/18-Namespaces.md): review if namespace scope is unclear.

Use 10–15 minutes for lessons, 20–25 for practice, and 5 for notes. This is the
next-session adjustment after completing the earlier labs ahead of the dated
schedule; it does not mark the remaining scheduled topics complete.

Following session: A02 Rolling Updates and Rollback, followed by guided practice
and A03's practice test without previewing solutions. We observed a label-driven
rollout here, but image updates and rollback have not yet been practised.
