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

## Next practice

- [ ] Recreate the standalone Pod and inspect it with `logs`, `exec` and `explain`.
- [ ] Record what each command reveals, then delete only the standalone Pod.
