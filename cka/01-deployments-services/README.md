# CKA Lab 01: Deployments and Services

## Goal

Deploy nginx, scale it to three replicas, and access it through a Kubernetes
Service. Save reusable YAML manifests in Git.

## Prerequisites

- Rancher Desktop running with Kubernetes enabled.
- WSL integration enabled.
- kubectl available inside WSL.

Check the cluster:

```bash
kubectl --context rancher-desktop get nodes
```

The node should show `Ready`.

## 1. Select the local cluster

```bash
kubectl config use-context rancher-desktop
kubectl config current-context
```

Expected context: `rancher-desktop`.

## 2. Generate the manifests

Run these commands from the repository's `cka/01-deployments-services` folder.

`--dry-run=client -o yaml` generates YAML without creating resources.
The `>` operator saves the output to a file.

```bash
kubectl create namespace cka-lab \
  --dry-run=client -o yaml > namespace.yaml

kubectl -n cka-lab create deployment web \
  --image=nginx:stable --replicas=1 \
  --dry-run=client -o yaml > deployment.yaml

kubectl -n cka-lab create service clusterip web \
  --tcp=80:80 \
  --dry-run=client -o yaml > service.yaml
```

Inspect the generated files:

```bash
cat namespace.yaml
cat deployment.yaml
cat service.yaml
```

## 3. Create the resources

Create the namespace first:

```bash
kubectl apply -f namespace.yaml
kubectl apply -f deployment.yaml -f service.yaml
kubectl -n cka-lab rollout status deployment/web
kubectl -n cka-lab get deployments,pods,services
```

Expected result:

- A Deployment named `web` with one ready replica.
- One running nginx Pod.
- A ClusterIP Service named `web`, exposing port 80.

## 4. Scale the Deployment

Open the Deployment manifest:

```bash
nano deployment.yaml
```

Change `spec.replicas` from `1` to `3`, save, and apply:

```bash
kubectl apply -f deployment.yaml
kubectl -n cka-lab rollout status deployment/web
kubectl -n cka-lab get pods -l app=web
```

Expected result: three running Pods once the rollout finishes.

Editing the manifest keeps the saved configuration consistent with the cluster.

## 5. Access nginx

```bash
kubectl -n cka-lab port-forward service/web 8080:80
```

Leave the command running and open this address in the Windows browser:

http://localhost:8080

Expected result: the nginx welcome page.

Alternatively, from a second WSL terminal:

```bash
curl http://localhost:8080
```

Press Ctrl+C in the first terminal to stop port forwarding.

Port forwarding creates a temporary local connection. The ClusterIP Service
itself is intended for access within the cluster.

## 6. Inspect how the resources connect

```bash
kubectl -n cka-lab describe deployment web
kubectl -n cka-lab get pods --show-labels
kubectl -n cka-lab describe service web
kubectl -n cka-lab get endpointslices \
  -l kubernetes.io/service-name=web
```

Check that:

- The Pods have the label `app=web`.
- The Service selector is `app=web`.
- The EndpointSlices contain addresses for the ready Pods.

## 7. Observe self-healing

List the Pods:

```bash
kubectl -n cka-lab get pods
```

Delete one Pod using its actual name:

```bash
kubectl -n cka-lab delete pod <pod-name>
kubectl -n cka-lab get pods -w
```

Do not type the angle brackets: replace `<pod-name>` with a name from the list.

Expected result: a replacement Pod is created to maintain three replicas.
Press Ctrl+C to stop watching.

## Key concepts

| Resource | Responsibility |
|---|---|
| Namespace | Groups and scopes namespaced resources. |
| Deployment | Manages application rollouts and the desired replica count through ReplicaSets. |
| ReplicaSet | Maintains the desired number of matching Pods. |
| Pod | Runs the nginx container. |
| Service | Provides a stable address and selects Pods by their labels. |

## Learning notes

Complete these after the lab:

- How does the Service select the nginx Pods?
- What happened when I deleted a Pod?
- What is the difference between port and targetPort?
- What does kubectl apply do?
- What errors did I encounter, and how did I diagnose them?

## Cleanup

Delete only the resources created by this exercise:

```bash
kubectl delete -f service.yaml -f deployment.yaml
```

The namespace remains available for future exercises. The YAML files remain
in Git, so the application can be recreated by repeating the apply commands.

## Save to Git

From the repository root:

```bash
git status
git add cka/01-deployments-services
git commit -m "Add CKA deployment and service lab"
```

Never commit kubeconfig files, credentials, or real Secret values.

## Learning notes

- My Deployment requests three replicas, and Kubernetes maintains three Pods.
- When I deleted one Pod, the ReplicaSet created a replacement with a new
  name to restore the desired replica count.
- Each Pod has the label `app=web`. The Service uses the selector `app=web`
  to identify the Pods it should send traffic to.
- The Service exposes port 80 and forwards traffic to targetPort 80
  on the selected Pods.
- I accessed nginx using `kubectl port-forward service/web 8080:80`
  and opened http://localhost:8080 in my browser.
- Port forwarding selected one Pod behind the Service. This confirmed nginx
  was reachable, but did not test ClusterIP routing or load balancing.
- `kubectl apply` creates or updates resources from my saved YAML manifests.
- Initially, WSL could not find kubectl. After fixing the Rancher Desktop
  integration, kubectl worked and the local node showed Ready.
