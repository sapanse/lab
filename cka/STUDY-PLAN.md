# CKA study plan

Target exam: December 28–30, 2026  
Fallback: January 2027 if practice results show more preparation is needed  
Readiness checkpoint: December 10, 2026  
Daily commitment: 30–45 minutes

Daily assignments: [DAILY-SCHEDULE.md](DAILY-SCHEDULE.md), mapped to the
local course notes in `../../certified-kubernetes-administrator-course/docs`.
Use the dated daily schedule for session assignments; the weekly checklist
below tracks coverage. Updated October 12, 2026.

## Starting point

- Some day-to-day EKS experience.
- Windows laptop with 32 GB RAM, WSL, Docker, and Rancher Desktop.
- Local Kubernetes cluster accessible from WSL.
- GitHub practice repository: `sapanse/lab`.
- Repository in WSL: `/mnt/c/Users/sharm/repo/lab`.
- Lab 01 completed: YAML manifests, three nginx replicas, Service inspection,
  port forwarding, Pod replacement, learning notes, and Git commit/push.

## Main resources

Use Mumshad Mannambeth / KodeKloud's Udemy course, **Certified Kubernetes
Administrator (CKA) with Practice Tests**, as the main course. Confirm access
to its accompanying labs. Use its topic sections to follow this plan; course
section numbers can change.

The copied course folder contains notes and practice walkthroughs, not a
replacement for the live lab environments. Use its lesson files as reading
references, the Udemy lectures for explanations, and prepared course labs for
node administration. Practice files can contain answers: attempt the lab first.

The local copy has no dedicated lessons for Helm, Kustomize, Gateway API,
CRDs/operators, or autoscaling. Keep these topics in the schedule using the
current course or official documentation. Do not use older notes' statements
about exam scope or versions as current exam guidance.

Tutor workflow: explain the concept, give a small exercise, let me attempt it,
then review my output and offer progressively stronger hints. Show a solution
only after my attempt or when I ask. I perform the cluster commands and write
the exercise manifests. Keep deliverables under `lab/cka` and temporary files
under workspace `scratch`. Do not commit or push unless I explicitly ask.

- Course: https://www.udemy.com/course/certified-kubernetes-administrator-with-practice-tests/
- Official objectives: https://training.linuxfoundation.org/certification/certified-kubernetes-administrator-cka/
- Official curriculum: https://github.com/cncf/curriculum
- Exam FAQ: https://docs.linuxfoundation.org/tc-docs/certification/faq-cka-ckad-cks
- Allowed resources: https://docs.linuxfoundation.org/tc-docs/certification/certification-resources-allowed
- Kubernetes documentation: https://kubernetes.io/docs/

Recheck the official objectives, exam version, and allowed resources at the
December checkpoint and before taking the exam. December is a readiness
target, not a guaranteed cutoff before an exam update.

## Daily routine

For a 30-minute session:

1. Review yesterday's topic for 3 minutes.
2. Watch or read one small topic for 7 minutes.
3. Practise for 17 minutes.
4. Record a mistake or learning point for 3 minutes.

For a 45-minute session, add 15 minutes of practice. Split long lessons over
several days. On review days, spend the entire session repeating labs.

Attempt labs before reading solutions. If stuck, inspect events, logs, and
official documentation, then use the solution to understand the missing
step. Repeat that task independently on another day.

Aim for five topic/lab sessions, one review session, and one catch-up session
each week. An optional 60–90-minute weekend session helps with longer
administration labs. Arrange uninterrupted two-hour sessions for full mocks
in December. Daily study alone totals roughly 40–60 hours by late December;
the target is ambitious and depends on how much existing experience transfers.

## Weekly schedule

### Week 1: October 12–18 — architecture and core resources

- [x] Complete and push Lab 01: Deployments and Services.
- [ ] Study API server, etcd, scheduler, controller manager, and kubelet.
- [ ] Explain what happens after `kubectl apply` creates a Deployment.
- [ ] Create and inspect a standalone Pod using YAML.
- [ ] Practise `get`, `describe`, `logs`, `exec`, and `explain`.

Goal: explain the difference between a Pod, ReplicaSet, Deployment, and Service.

### Week 2: October 19–25 — workloads and command-line fluency

- [ ] Practise namespaces, contexts, labels, and selectors.
- [ ] Scale a Deployment by editing its manifest.
- [ ] Perform a rolling update and rollback.
- [ ] Study DaemonSets, Jobs, CronJobs, and basic StatefulSet behavior.
- [ ] Generate YAML with `--dry-run=client -o yaml`; edit it using Vim.
- [ ] Recreate Lab 01 without following its README.

Goal: create, inspect, update, and recover a simple workload independently.

### Week 3: October 26–November 1 — application configuration

- [ ] Use ConfigMaps and practice-only Secrets as environment variables and volumes.
- [ ] Configure container commands and arguments.
- [ ] Practise init containers and understand multi-container Pods.
- [ ] Configure readiness, liveness, and startup probes.
- [ ] Diagnose a failing probe and a CrashLoopBackOff.

Goal: distinguish configuration errors from application failures.
Do not commit credentials or real Secret values; base64 is not encryption.

### Week 4: November 2–8 — resources and scheduling

- [ ] Configure CPU/memory requests and limits.
- [ ] Study quotas, LimitRanges, and workload autoscaling.
- [ ] Practise node selectors, affinity, taints, and tolerations.
- [ ] Understand cordon, drain, and uncordon using suitable labs.
- [ ] Diagnose Pending Pods and an unschedulable workload.

Goal: explain why a Pod cannot be scheduled and fix the cause.
Use course labs for exercises requiring multiple nodes or metrics support.

### Week 5: November 9–15 — Services and DNS

- [ ] Compare ClusterIP, NodePort, and LoadBalancer Services.
- [ ] Inspect selectors, Pod IPs, and EndpointSlices.
- [ ] Test Service access and DNS from a Pod inside the cluster.
- [ ] Study CoreDNS and troubleshoot name resolution.
- [ ] Break a Service selector or target port, diagnose it, and repair it.

Goal: troubleshoot the path from a client Pod to an application.
Port forwarding alone does not verify ClusterIP routing or load balancing.

### Week 6: November 16–22 — traffic and network policies

- [ ] Study CNI responsibilities and Pod connectivity.
- [ ] Create and test NetworkPolicies in a lab with a policy-enforcing CNI.
- [ ] Study Ingress resources and controllers.
- [ ] Practise Gateway API resources, including Gateway and HTTPRoute.
- [ ] Diagnose a routing or connectivity failure.

Goal: understand how traffic reaches applications and how policy restricts it.
Use a prepared course lab when controllers or CNI features are required.

### Week 7: November 23–29 — storage and access control

- [ ] Practise PVs, PVCs, StorageClasses, and dynamic provisioning.
- [ ] Understand access modes and reclaim policies.
- [ ] Diagnose a Pending PVC.
- [ ] Practise ServiceAccounts, Roles, ClusterRoles, and bindings.
- [ ] Verify authorization with `kubectl auth can-i`.

Goal: attach persistent storage and grant the intended Kubernetes permissions.

### Week 8: November 30–December 6 — cluster administration

- [ ] Study CRI, CNI, CSI, and the infrastructure needed by a cluster.
- [ ] Install a cluster and join a worker using kubeadm in a suitable lab.
- [ ] Practise a cluster upgrade, including draining and returning a worker.
- [ ] Study static Pods, control-plane manifests, kubeconfig, and certificates.
- [ ] Inspect kubelet with `systemctl` and `journalctl` in a prepared lab.
- [ ] Practise etcd snapshot/recovery and understand HA control-plane design.

Goal: perform core administration tasks using documentation rather than a
step-by-step solution. This is a dense week: split across extra sessions or
carry unfinished items forward and account for them at the checkpoint.

### Week 9: December 7–13 — extensions and readiness checkpoint

- [ ] Practise Helm installation, values, upgrades, and inspection.
- [ ] Practise Kustomize bases and overlays.
- [ ] Understand CRDs and install/configure an operator in a prepared lab.
- [ ] Troubleshoot a cluster/node/component failure.
- [ ] Take a mixed timed assessment and review the December 10 checkpoint.
- [ ] Compare course coverage with the current official curriculum.

Goal: identify remaining gaps and choose December or January based on evidence.

### Week 10: December 14–20 — timed practice

- [ ] Complete a full two-hour course mock exam.
- [ ] Review errors and repeat missed tasks independently.
- [ ] Use the first exam simulator session if included in your registration.
- [ ] Practise checking the target context, node, and namespace for every task.
- [ ] Practise finding approved documentation quickly.

Goal: solve mixed tasks under time pressure and verify the resulting state.
Check simulator access duration before activating a session; leave time to review it.

### Week 11: December 21–27 — final validation

- [ ] Complete a second full timed mock with a different or sufficiently unfamiliar set.
- [ ] Use another simulator session if available and useful.
- [ ] Repeat weak tasks without solutions.
- [ ] Complete the proctoring system check and review exam-day requirements.
- [ ] Confirm exam version, objectives, appointment, ID, and allowed resources.

Goal: consistent independent performance, with time left to check completed work.

### December 28–30 — exam target

- [ ] Take the exam if the readiness checks are satisfied.
- [ ] Keep final revision light; revisit recurring mistakes and documentation paths.

## December 10 readiness checkpoint

Keep December as the target if:

- You have covered most of the curriculum and have a specific plan for remaining gaps.
- You can complete topic labs without watching solutions.
- You can diagnose workload, Service, DNS, storage, and scheduling failures.
- You have practised kubeadm, upgrades, and recovery in suitable labs.
- A mixed timed assessment shows that speed and documentation use are improving.
- You can reserve full mock sessions in the following two weeks.

Before sitting the exam, aim for two consistent timed course mock results around
80% or better, without solution assistance. This is a personal preparation target,
not an official requirement or a guarantee of passing. Interpret simulator scores
in the context of their difficulty and your ability to correct mistakes.

If substantial gaps remain, move to January. Use the first two January weeks for
unfinished administration topics and weak labs, then repeat timed assessments.
Do not compress those gaps into passive video watching to meet the December date.

## Git practice structure

Keep this plan at `cka/STUDY-PLAN.md` in the repository. Suggested lab folders:

```text
cka/
  STUDY-PLAN.md
  01-deployments-services/
  02-pods-architecture/
  03-workloads-namespaces/
  04-config-probes/
  05-scheduling-resources/
  06-services-dns/
  07-networking-traffic/
  08-storage-rbac/
  09-cluster-administration/
  10-helm-kustomize-operators/
  11-troubleshooting/
```

Each lab should contain reusable manifests where applicable and a README with:

1. Goal and prerequisites.
2. Steps to recreate the exercise.
3. Verification commands and expected results.
4. What failed, how you diagnosed it, and what fixed it.
5. Cleanup instructions scoped to the practice resources.

Keep kubeconfig files, private keys, credentials, and real Secret values out of Git.
For VM/course labs, save your own procedure and learning notes; do not copy
proprietary questions or exam content into a public repository.

## Next session

1. Follow the October 12 row in [DAILY-SCHEDULE.md](DAILY-SCHEDULE.md):
   read/watch Cluster Architecture and connect it to the completed Lab 01.
2. Create `cka/02-pods-architecture/README.md`.
3. Write one sentence each explaining API server, etcd, scheduler,
   controller manager, and kubelet.
4. Describe how the Deployment's desired replica count led to a replacement
   Pod after you deleted one in Lab 01.

Stop after 30–45 minutes. Continue with the next small topic the following day.
