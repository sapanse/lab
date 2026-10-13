# Lab 02: Pods and Architecture

## Goal and prerequisites

Understand how control-plane components coordinate Pod replacement and Service
backend updates, then compare a standalone Pod with a Deployment-managed Pod.
Use the local `rancher-desktop` context and the `cka-lab` namespace from Lab 01.

## Learning notes — October 12, 2026

- The API server exposes the Kubernetes API; components read and update cluster
  objects through it.
- The datastore persists cluster state, including desired configuration; it
  does not run containers or store container images or application data on PVs.
- The Deployment controller manages ReplicaSets. The ReplicaSet controller
  replaces deleted Deployment-managed Pods to maintain the desired count.
- The scheduler assigns Pods to nodes. The kubelet uses the container runtime
  to run the assigned Pods' containers and reports their status.
- The Service's `spec.selector`, `app=web`, matches the labels on the web Pods.
  The Service's own `metadata.labels` do not determine its backends.
- The EndpointSlice controller updates backend addresses through the API server.
  The Service's selector and ClusterIP remain unchanged when Pod IPs change.
- My local cluster uses K3s through Rancher Desktop. Its control-plane packaging
  differs from the course's kubeadm examples. No etcd Pod appeared in
  `kube-system`; the node version confirmed K3s, but did not establish which
  datastore was configured.

## What I observed

I deleted one Pod from my three-replica `web` Deployment. A replacement Pod
appeared with a new IP. The Service's EndpointSlice updated to include that
new IP without an edit to the Service.

I reviewed these concepts with tutor feedback and verified backend updates
myself. No diagnosis or repair of a failure was performed in this session.

## Inspection commands

With the Lab 01 resources running, inspect the cluster and resource connections:

```bash
kubectl config current-context
kubectl --context rancher-desktop get nodes -o wide
kubectl --context rancher-desktop -n cka-lab get service web -o yaml
kubectl --context rancher-desktop -n cka-lab get pods --show-labels
kubectl --context rancher-desktop -n cka-lab get pods -o wide
kubectl --context rancher-desktop -n cka-lab get endpointslices \
  -l kubernetes.io/service-name=web -o wide
```

Expected results: three ready web Pods labelled `app=web`; the Service selector
matches those labels, and its EndpointSlices list the ready Pods' IP addresses.
Lab 01 documents the Pod deletion exercise for repeating this observation.

## Standalone Pod exercise — October 12, 2026

- [x] Create and inspect a standalone Pod using my own YAML manifest.
- [x] Delete it and compare its behavior with a Deployment-managed Pod.
- [x] Record verification output, any errors, and cleanup steps for that exercise.

### Recreate and inspect

From this lab directory, confirm the local context, then apply the saved manifest:

```bash
kubectl config current-context
kubectl --context rancher-desktop apply -f pod.yaml
kubectl --context rancher-desktop -n cka-lab get pod standalone-web -o wide
kubectl --context rancher-desktop -n cka-lab describe pod standalone-web
```

I generated the YAML with `kubectl run --dry-run=client -o yaml`, then edited it.
The final manifest uses the label `app=standalone-web`, container name `nginx`,
and image `nginx:stable`. The different label keeps it outside the `web` Service's
selector. I also set `cka-lab` as my current context's default namespace so that
my interactive commands could omit `-n`; the commands here are explicit for reuse.

### Verification evidence

Observed output (timestamps and container identifiers omitted):

```text
NAME             READY   STATUS    RESTARTS
standalone-web   1/1     Running   0

Namespace: cka-lab
Node: sharmilapanse/192.168.127.2
Labels: app=standalone-web
IP: 10.42.0.31
Container: nginx
Image: nginx:stable

Events:
Scheduled  default-scheduler  Successfully assigned cka-lab/standalone-web to sharmilapanse
Pulled     kubelet            Container image "nginx:stable" already present on machine
Created    kubelet            Container created
Started    kubelet            Container started
```

There was no `Controlled By` entry. After deleting `standalone-web` and watching
the Pod list, I observed that it did not return. The three Deployment-managed
`web` Pods remained. Unlike their ReplicaSet, no controller maintained the
standalone Pod's existence.

`restartPolicy: Always` controls container restarts within an existing Pod;
it does not recreate a deleted Pod.

### Corrections and assistance

- Fixed the image typo `naginx` to `nginx:stable` before applying.
- Removed duplicate `metadata.labels` blocks and changed `apps` to `app`.
- Renamed the container to `nginx` to match the exercise; this name is a lab
  convention, not a Kubernetes requirement.
- Learned that `kubectl run --labels=app=standalone-web` can generate the label.
- Used tutor guidance to review the YAML and select inspection commands;
  performed creation, inspection, deletion and observation myself.
- No runtime errors were reported after applying the corrected manifest.

## Cleanup

```bash
kubectl --context rancher-desktop -n cka-lab delete pod standalone-web
kubectl --context rancher-desktop -n cka-lab get pods -w
```

Press Ctrl+C to stop watching. The standalone Pod stayed deleted, completing its
cleanup. Keep `pod.yaml` for reuse. The Lab 01 Deployment, Service and namespace
remain; use Lab 01's cleanup instructions when finished with them.

## Logs, exec and explain practice — October 12, 2026

- [x] Recreate the standalone Pod and inspect it with `logs`, `exec` and `explain`.
- [x] Record what each command reveals and clarify restart policies.
- [x] Confirm cleanup of the recreated standalone Pod with `kubectl get pods`.

### Commands and observed results

With `rancher-desktop` active and its default namespace set to `cka-lab`, I ran:

```bash
kubectl apply -f pod.yaml
kubectl get pod standalone-web
kubectl logs standalone-web
kubectl exec standalone-web -c nginx -- nginx -t
kubectl explain pod.spec.containers
kubectl explain pod.spec.restartPolicy
kubectl get pod standalone-web -o yaml
```

The recreated Pod showed `1/1 Running` with zero restarts. Its logs showed normal
nginx entrypoint configuration and worker-process startup; no errors appeared in
the shared output.

The configuration test returned:

```text
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```

`nginx -t` ran inside the `nginx` container in the `standalone-web` Pod, not in my
WSL environment. `-c nginx` selects the container, and `--` separates kubectl's
arguments from the command executed inside it.

### What each inspection command reveals

| Command | Purpose |
| --- | --- |
| `get pod` | Shows a brief summary of the actual Pod's state. |
| `describe pod` | Summarizes configuration, container state and Kubernetes events; it does not show every field. |
| `logs` | Reads the container's standard output and standard error. |
| `exec` | Runs a command inside a running container. |
| `get pod -o yaml` | Shows the actual Pod object, including defaulted fields. |
| `explain` | Documents a resource field, rather than displaying my Pod's value. |

### Restart-policy clarification

I initially could not find `restartPolicy` in `describe` output. Looking at the
YAML showed it under `spec`. The Pod-level field is `pod.spec.restartPolicy`,
and its default is `Always`:

- `Always`: restart containers after successful or unsuccessful exits.
- `OnFailure`: restart containers after unsuccessful exits.
- `Never`: do not restart containers.

The explanation of `pod.spec.containers` also included a container-level
`restartPolicy` field. That is a separate field from the Pod-level policy and
introduces overrides and sidecar behavior; I will revisit it with multi-container
Pods. My manifest sets the Pod-level policy, without a container-level override.
Restarting a container does not recreate a deleted standalone Pod.

I used tutor guidance for the commands and to clarify the two policy fields;
I ran the commands and interpreted their results myself. No runtime errors were
reported during this inspection exercise.

### Cleanup after the second creation

The earlier deletion exercise was completed. I recreated the Pod for this
inspection exercise, so it needs another cleanup:

```bash
kubectl --context rancher-desktop -n cka-lab delete pod standalone-web
kubectl --context rancher-desktop -n cka-lab get pods
```

Confirmed output after cleanup:

```text
pod "standalone-web" deleted from cka-lab namespace
NAME                  READY   STATUS    RESTARTS
web-85b88cddc-cxt4c   1/1     Running   0
web-85b88cddc-nj4dj   1/1     Running   0
web-85b88cddc-s24ht   1/1     Running   0
```

`standalone-web` is absent and the three Deployment-managed `web` Pods remain.
Cleanup of the second creation is complete; `pod.yaml` remains available for reuse.
