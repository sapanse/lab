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

## Still to practise

- [ ] Create and inspect a standalone Pod using my own YAML manifest.
- [ ] Delete it and compare its behavior with a Deployment-managed Pod.
- [ ] Record verification output, any errors, and cleanup steps for that exercise.

## Cleanup

This session reused Lab 01 resources and added no separate workload. Use Lab 01's
scoped cleanup instructions when finished with those resources. Add cleanup for
the standalone Pod after completing the next exercise.
