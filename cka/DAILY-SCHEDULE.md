# CKA daily schedule — October 12 to December 30, 2026

Target: December 28–30, 2026. Readiness checkpoint: December 10.
Fallback: January 2027. Lab 01 is already complete; all sessions below are
planned, not evidence of completion.

## How we use this schedule

Spend 3 minutes recalling the previous session, 7–12 minutes reading/watching,
17–25 minutes practising, and 3–5 minutes recording one learning point.
Stop at 30–45 minutes. Course references identify the relevant material, not
a requirement to finish every linked file or video that day. Read the part
needed for the exercise; continue unfinished work on the catch-up day.

I act as tutor: explain, give an exercise, wait for your attempt, then offer
hints and review your output. You run commands and write the manifests.
Solutions come after an attempt or on request. The course supports our guided
labs. Its practice-test notes often include answers, so use the live lab prompt
first and keep answer sections closed until review. Do not preview mock solutions.

After each session, add the date, task, verification evidence, assistance used,
and one mistake/learning point to the relevant lab README. A task is complete
when you can verify it and explain the result. Record a retry if hints were
needed. Each Saturday reviews previous work; each Sunday catches up or repeats
the weakest task if nothing is unfinished. Do not double the next day's load
after a missed session. Rebalance at the checkpoint if catch-up days fill up.

Use Rancher Desktop for ordinary workload labs. Use a prepared course/VM lab
for kubeadm, upgrades, etcd, certificates, node failures, and drain exercises.
Confirm context and namespace before each exercise. NetworkPolicy requires
a policy-enforcing CNI; Ingress/Gateway exercises require suitable controllers;
autoscaling/resource metrics require metrics support. Use prepared labs when
the local cluster does not support the feature.

Deliverables belong in `lab/cka`; temporary output belongs in workspace
`scratch` (`../../scratch` from this directory). Do not save real credentials
or kubeconfigs in the repo. Save your own procedures and observations, not
copied course questions or answers. No commits or pushes unless requested.

## Course reference key

Course section links open the source repository on GitHub, so they work both
in GitHub and in your editor's Markdown preview. Your local copy remains at
`../../certified-kubernetes-administrator-course/docs` relative to this file.
The numbers in daily rows are filename prefixes, not Udemy lecture numbers.
For example, **C02** means `02-Core-Concepts/02-Cluster-Architecture.md`.
Match Udemy lessons by topic title. A practice file is a reference for review,
not an instruction to read its solution before trying the exercise.

| Key | Course section on GitHub |
| --- | --- |
| C | [02-Core-Concepts](https://github.com/kodekloudhub/certified-kubernetes-administrator-course/tree/master/docs/02-Core-Concepts/) |
| S | [03-Scheduling](https://github.com/kodekloudhub/certified-kubernetes-administrator-course/tree/master/docs/03-Scheduling/) |
| L | [04-Logging-and-Monitoring](https://github.com/kodekloudhub/certified-kubernetes-administrator-course/tree/master/docs/04-Logging-and-Monitoring/) |
| A | [05-Application-Lifecycle-Management](https://github.com/kodekloudhub/certified-kubernetes-administrator-course/tree/master/docs/05-Application-Lifecycle-Management/) |
| M | [06-Cluster-Maintenance](https://github.com/kodekloudhub/certified-kubernetes-administrator-course/tree/master/docs/06-Cluster-Maintenance/) |
| R | [07-Security](https://github.com/kodekloudhub/certified-kubernetes-administrator-course/tree/master/docs/07-Security/) |
| V | [08-Storage](https://github.com/kodekloudhub/certified-kubernetes-administrator-course/tree/master/docs/08-Storage/) |
| N | [09-Networking](https://github.com/kodekloudhub/certified-kubernetes-administrator-course/tree/master/docs/09-Networking/) |
| D | [10-Design-and-Install-Kubernetes-Cluster](https://github.com/kodekloudhub/certified-kubernetes-administrator-course/tree/master/docs/10-Design-and-Install-Kubernetes-Cluster/) |
| K | [11-Install-Kubernetes-the-kubeadm-way](https://github.com/kodekloudhub/certified-kubernetes-administrator-course/tree/master/docs/11-Install-Kubernetes-the-kubeadm-way/) |
| T | [12-Troubleshooting](https://github.com/kodekloudhub/certified-kubernetes-administrator-course/tree/master/docs/12-Troubleshooting/) |
| J | [13-Other-Topics](https://github.com/kodekloudhub/certified-kubernetes-administrator-course/tree/master/docs/13-Other-Topics/) |
| LL | [14-Lightning-Labs](https://github.com/kodekloudhub/certified-kubernetes-administrator-course/tree/master/docs/14-Lightning-Labs/) |
| Mock | [15-Mock-Exams](https://github.com/kodekloudhub/certified-kubernetes-administrator-course/tree/master/docs/15-Mock-Exams/) |

**Supplement** means no dedicated lesson was found in this local copy. Use the
matching current Udemy lesson if available, otherwise the official references
below and a tutor-provided original exercise. This includes probes and workload
types retained for practical understanding, as well as curriculum gaps.

- [Official CKA competencies](https://training.linuxfoundation.org/certification/certified-kubernetes-administrator-cka/)
- [Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/)
- [Workload controllers](https://kubernetes.io/docs/concepts/workloads/controllers/)
- [Autoscaling](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/)
- [Resource quotas](https://kubernetes.io/docs/concepts/policy/resource-quotas/) and [LimitRanges](https://kubernetes.io/docs/concepts/policy/limit-range/)
- [Gateway API](https://gateway-api.sigs.k8s.io/guides/)
- [Helm](https://helm.sh/docs/) and [Kustomize](https://kubernetes.io/docs/tasks/manage-kubernetes-objects/kustomization/)
- [CRDs](https://kubernetes.io/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definitions/) and [operators](https://kubernetes.io/docs/concepts/extend-kubernetes/operator/)

Current competencies checked October 12, 2026 include autoscaling, Helm,
Kustomize, CRDs/operators, and Gateway API. The copied notes are incomplete for
those topics. Use current documentation for version-sensitive procedures;
recheck exam version, objectives, and allowed resources December 10 and before
the exam. Older CNI examples are conceptual references, not a requirement to
install their historical versions.

## Week 1 — architecture and Pods

Deliverables: `02-pods-architecture/README.md` and your own Pod manifest.

| Date | Read/watch | Guided exercise and evidence |
| --- | --- | --- |
| Oct 12 | C02: Cluster Architecture | Connect the completed Lab 01 to control-plane/worker responsibilities; draft five component explanations. |
| Oct 13 | C04–06: etcd and API server | Explain how desired state is stored; trace a Deployment request. Inspect your Deployment's spec and status. |
| Oct 14 | C07–10: controllers, scheduler, kubelet, proxy | Trace replacement of a deleted managed Pod. Identify which component creates, assigns, and runs it. |
| Oct 15 | C11–13: Pods; practice introduction | Write/apply a standalone nginx Pod in the practice namespace. Verify status, node and container image. Predict then observe deletion behavior. |
| Oct 16 | C11; L04: application logs | Recreate your Pod; practise get, describe, logs, exec and explain. Record what each reveals. |
| Oct 17 | Review C14/C16 and Lab 01 | Explain Pod → ReplicaSet → Deployment ownership and Service selection without notes; inspect owner references. |
| Oct 18 | Catch-up | Finish Lab 02 notes or recreate the standalone Pod unaided. |

## Week 2 — workloads and command fluency

Deliverables: `03-workloads-namespaces/`; retain Lab 01 as your baseline.

| Date | Read/watch | Guided exercise and evidence |
| --- | --- | --- |
| Oct 19 | C18–19; S04–05: namespaces, labels | Use explicit context/namespace; select only your labelled Pods and explain selectors. |
| Oct 20 | C23–24: imperative commands | Generate YAML with client dry-run, edit in Vim, apply and inspect. Time one simple creation task. |
| Oct 21 | C14–17: ReplicaSets/Deployments | Scale through your manifest; inspect ownership and desired/current counts. |
| Oct 22 | A02–03: updates/rollback | Update an image, observe rollout, undo and verify recovery in a disposable workload. |
| Oct 23 | S14–15; supplement workload controllers | Inspect a DaemonSet in a suitable lab; create a small Job. Explain when a CronJob or StatefulSet fits. Defer deeper examples if time expires. |
| Oct 24 | Review | Recreate Lab 01 without its steps; verify Pods, Service selector and EndpointSlices. |
| Oct 25 | Catch-up / workload supplement | Finish unfinished work; otherwise create a CronJob and explain basic StatefulSet identity/storage behavior. |

## Week 3 — configuration and application health

Deliverables: `04-config-probes/` with practice-only values.

| Date | Read/watch | Guided exercise and evidence |
| --- | --- | --- |
| Oct 26 | A04–06: commands/arguments | Override a container command; inspect exit behavior and logs. |
| Oct 27 | A07–09: environment/ConfigMaps | Inject a ConfigMap through environment variables and a volume; verify inside the Pod. |
| Oct 28 | A10–11; R23–26: Secrets/security | Use dummy Secret data; inspect a simple security context. Explain base64 and image-pull credentials. |
| Oct 29 | A12–16: multi-container/init | Add an init container and inspect its completion; inspect logs per container in a small multi-container example. |
| Oct 30 | A17; supplement probes | Add readiness and liveness probes; explain failure effects. Include startup-probe purpose in notes. |
| Oct 31 | Review / L04–06 | Diagnose one failing probe or CrashLoopBackOff using events and logs before changing anything. |
| Nov 1 | Catch-up | Retry the failing task unaided; finish startup-probe practice if needed. |

## Week 4 — scheduling, resources and metrics

Deliverables: `05-scheduling-resources/`.

| Date | Read/watch | Guided exercise and evidence |
| --- | --- | --- |
| Nov 2 | S02–03; S12–13: scheduling/resources | Set requests/limits; diagnose an intentionally oversized Pending Pod from scheduler events. |
| Nov 3 | S08–11: node selection/affinity | Place a workload by label/affinity; diagnose an unmatched selector in a suitable lab. |
| Nov 4 | S06–07: taints/tolerations | Predict placement, then test a taint/toleration in a prepared multi-node lab. |
| Nov 5 | L02–03; supplement quotas/LimitRanges | Inspect metrics where available; test a default request or quota rejection and explain the event. |
| Nov 6 | Supplement autoscaling | Create/inspect an HPA with required CPU requests and metrics; test scaling in a prepared lab. |
| Nov 7 | Review scheduling | Diagnose one unfamiliar Pending workload; justify your fix from events. |
| Nov 8 | Catch-up | Repeat the weakest scheduling/metrics task; check prepared administration lab access for later weeks. |

## Week 5 — Services and DNS

Deliverables: `06-services-dns/`.

| Date | Read/watch | Guided exercise and evidence |
| --- | --- | --- |
| Nov 9 | C20–22; N17–18: Services | Compare Service types; map selectors, port and targetPort to your running workload. |
| Nov 10 | N17–18: Service networking | Test ClusterIP access from a client Pod; inspect Pod IPs/EndpointSlices. Explain what port-forward did not test. |
| Nov 11 | N03–04; N19: DNS | Resolve a Service from the same and another namespace; explain the full DNS name. |
| Nov 12 | N20–21: CoreDNS | Inspect DNS configuration in a suitable lab; distinguish failed resolution from failed connectivity. |
| Nov 13 | T02: application failure (prompt only) | Diagnose a tutor-provided selector or targetPort mismatch and verify repair from a client Pod. |
| Nov 14 | Review | Trace client → DNS → Service → endpoint → container; repeat one failure without hints. |
| Nov 15 | Catch-up | Finish DNS/Service tasks or repeat a short mixed workload/network task. |

## Week 6 — networking and traffic control

Deliverables: `07-networking-traffic/`.

| Date | Read/watch | Guided exercise and evidence |
| --- | --- | --- |
| Nov 16 | N02; N05–11: network foundations/CNI | Focus on weak prerequisites; draw the Pod-to-Pod path and inspect interfaces/routes in a prepared lab. |
| Nov 17 | R27–28: NetworkPolicies | Predict then test allowed/blocked traffic using a policy-enforcing CNI. |
| Nov 18 | N22–24: Ingress | Identify controller vs resource; route a request to your test Service in a prepared lab. |
| Nov 19 | Supplement Gateway API | Identify GatewayClass, Gateway and HTTPRoute; inspect a prepared controller and route. |
| Nov 20 | Supplement Gateway API; T10 | Configure/test an HTTPRoute; inspect route status and diagnose a routing failure. |
| Nov 21 | Review | Repeat a policy or routing failure without the walkthrough and record evidence. |
| Nov 22 | Catch-up | Finish networking tasks; otherwise explain CNI vs Service routing vs Ingress/Gateway. |

## Week 7 — storage and RBAC

Deliverables: `08-storage-rbac/`.

| Date | Read/watch | Guided exercise and evidence |
| --- | --- | --- |
| Nov 23 | V05–09: CSI, volumes, PV/PVC | Mount storage; explain volume lifecycle, access modes and reclaim policy. |
| Nov 24 | V10; V12–13: provisioning | Create a PVC using a StorageClass; verify binding and data survival after Pod replacement. |
| Nov 25 | V08–10: PVC review | Diagnose a Pending PVC; use events and matching requirements to justify the fix. |
| Nov 26 | R15–18; R21–22: RBAC/accounts | Create a ServiceAccount, Role and binding; verify intended permission with auth can-i. |
| Nov 27 | R19–20: cluster roles | Compare namespace/cluster scope; test both an allowed and denied action. |
| Nov 28 | Review | Repeat a storage failure or RBAC task without solutions; explain the result. |
| Nov 29 | Catch-up | Finish work; confirm access to a prepared kubeadm lab for next week. |

## Week 8 — cluster installation and maintenance

Deliverables: `09-cluster-administration/` with your procedures and observations.
Use prepared course labs; continue longer tasks into reserved catch-up sessions.

| Date | Read/watch | Guided exercise and evidence |
| --- | --- | --- |
| Nov 30 | C03; D01–02; K01–04 | Review CRI/CNI/CSI prerequisites; begin kubeadm installation in a prepared lab. Record completed steps and node state. |
| Dec 1 | K04–05: kubeadm | Continue installation, join a worker, verify Ready nodes and workload scheduling. |
| Dec 2 | S16–17; R13–14: static Pods/kubeconfig | Inspect control-plane manifests and kubeconfig context fields; identify static/mirror Pods. |
| Dec 3 | M02–06: maintenance/upgrade | Cordon/drain a prepared worker; begin the documented upgrade procedure. Verify versions as you proceed. |
| Dec 4 | M05–06: upgrade practice | Complete control-plane/worker upgrade steps as required; uncordon and verify nodes/workloads. |
| Dec 5 | Review / M07–08: backup/recovery | Recall installation/upgrade order; make an etcd snapshot in a prepared lab and record verification. |
| Dec 6 | Catch-up / M09–10 | Practise etcd restore and verify recovery in the prepared lab. Carry unfinished recovery to Dec 11/13 and record the gap at the checkpoint. |

## Week 9 — extensions and readiness checkpoint

Deliverables: `10-helm-kustomize-operators/` and `11-troubleshooting/`.

| Date | Read/watch | Guided exercise and evidence |
| --- | --- | --- |
| Dec 7 | Supplement Helm | Install a chart with values; inspect release/resources, upgrade and roll back. |
| Dec 8 | Supplement Kustomize | Build a base and overlay; render, apply and verify the intended differences. |
| Dec 9 | Supplement CRDs/operators | Inspect a CRD and custom resource; install/configure a small operator in a prepared lab and verify reconciliation. |
| Dec 10 | Mixed assessment; official competencies | Spend 25–30 minutes on unfamiliar mixed tasks, then record gaps and choose December/January using STUDY-PLAN criteria. Reserve two-hour mock windows if continuing with December. |
| Dec 11 | R02–12; T04–08; D03–04 | Focus on one certificate/node failure; use systemctl/journalctl in a prepared lab. Explain HA/etcd quorum. Retry unfinished recovery first if needed. |
| Dec 12 | Review / LL | Repeat a weak timed task; check extension-topic coverage and certificate/HA gaps. |
| Dec 13 | Catch-up | Complete the biggest administration gap; carry substantial gaps into January rather than only reading about them. |

## Week 10 — timed practice

Most sessions stay 30–45 minutes. Dec 19 is a proposed **two-hour exception**;
reserve an uninterrupted window or move it to another day. Split practice is
useful, but does not count as a full timed mock.

| Date | Read/watch | Guided exercise and evidence |
| --- | --- | --- |
| Dec 14 | J02–04: JSONPath/advanced kubectl | Practise extracting task-specific fields and navigating current official documentation. |
| Dec 15 | T02; L02/L04: troubleshooting | Timed application/logs/resources tasks; verify each fix. |
| Dec 16 | T04–08: node/control-plane failures | Diagnose one prepared component failure; record evidence before fixing it. |
| Dec 17 | T10; storage/RBAC review | Timed Service/DNS/storage or permissions tasks selected from your weak areas. |
| Dec 18 | LL; Mock01: introduction | Short mixed practice; check context/namespace discipline and reserve mock environment. Avoid answer files. |
| Dec 19 | Mock02: Mock Exam 1 — 2 hours | Full unfamiliar mock without tutor hints or solutions; record score, unfinished tasks and time use. |
| Dec 20 | Mock 1 review / catch-up | Identify highest-impact errors; repeat one missed task independently after reviewing its cause. |

## Week 11 — final validation

Dec 26 is the proposed second **two-hour exception**, movable within the week.
Optional exam simulator sessions replace practice sessions when useful; check
access duration before activation and reserve review time. Do not squeeze a
full simulator attempt into 30–45 minutes or count a familiar mock as fresh.

| Date | Read/watch | Guided exercise and evidence |
| --- | --- | --- |
| Dec 21 | Weak-topic references | Retry the weakest administration task unaided. |
| Dec 22 | Weak-topic references | Retry the weakest workload/network/storage task; optionally begin a reserved simulator window. |
| Dec 23 | Mock/simulator error review | Repeat missed tasks; track independent success and documentation speed. |
| Dec 24 | Official exam guidance | System check; verify appointment, ID, objectives/version and allowed resources. Revisit recurring errors briefly. |
| Dec 25 | Light review / catch-up | Short documentation navigation and concept recall; finish any critical gap. |
| Dec 26 | Mock03: Mock Exam 2 — 2 hours | A different, unfamiliar full timed mock; record score and verification time. Use another unfamiliar set if already seen. |
| Dec 27 | Review and readiness decision | Correct recurring errors; evaluate both mock results and practical independence. Keep December only if readiness criteria are met. |

## Exam window

| Date | Assignment |
| --- | --- |
| Dec 28 | Exam if booked and ready; otherwise 30 minutes of light review of recurring mistakes. |
| Dec 29 | Exam if this is your booked date; otherwise light revision before the exam or record post-exam learning without exam content. |
| Dec 30 | Final target date if booked and ready. If gaps remain, follow the January branch below. |

## January fallback

If December 10 or final validation shows substantial gaps, revise remaining
December sessions toward those gaps and target January. Do not mark planned
activities complete merely because their dates have passed.

| Date | Daily focus (30–45 minutes unless noted) |
| --- | --- |
| Jan 1 | Review gap log; choose the three highest-impact weaknesses. |
| Jan 2 | Repeat weakest topic's first task with evidence. |
| Jan 3 | Catch-up and independent retry. |
| Jan 4 | Complete unfinished kubeadm installation/join practice. |
| Jan 5 | Repeat drain/upgrade/uncordon in a prepared lab. |
| Jan 6 | Repeat etcd snapshot/recovery and verification. |
| Jan 7 | Diagnose a node/control-plane/certificate failure. |
| Jan 8 | Repeat weakest networking or Gateway task. |
| Jan 9 | Mixed review without hints. |
| Jan 10 | Catch-up or retry the remaining weakest task. |
| Jan 11 | Repeat unfinished storage/RBAC task. |
| Jan 12 | Repeat unfinished Helm/Kustomize/operator/autoscaling task. |
| Jan 13 | Short timed mixed assessment; select remaining fixes. |
| Jan 14 | Reserve a fresh two-hour mock and review window; reassess exam readiness and choose a January appointment accordingly. |

Reorder January days based on actual results. Keep the same readiness criteria
and obtain two consistent independent full timed results before sitting the exam.
