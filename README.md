# k8-selector
# Taint and Toleration.

## Step-by-step Guide

### Step 1: Understand Taints
- A **taint** is applied to a node to **prevent certain pods from being scheduled** on it unless they explicitly tolerate it.
- It’s used to dedicate nodes for specific purposes (e.g., GPU workloads, logging, infra).

#### Example command:
```bash
kubectl taint node ip-192-168-22-49.ec2.internal hardware=gpu:NoSchedule
o/p: node/ip-192-168-22-49.ec2.internal tainted


```
### Step 2: Understand Tolerations

A **toleration** is added to pod specs so they can be scheduled on nodes with taints.

Without the toleration, the pod will be **blocked** from landing on tainted nodes.

#### Example toleration in a pod/deployment YAML:

```yaml
tolerations:
  - key: "key"
    operator: "Equal"
    value: "value"
    effect: "NoSchedule"
```
#### Example for simple affinity:
```bash
nodeSelector:
    hardware:gpu  
```

### Affinity vs AntiAffinity

To keep it simple and easy to remember:

- **Affinity** = "Attracts" pods  
- **AntiAffinity** = "Repels" pods

#### Node-level:
- **Node Affinity** – Node attracts the pod
- **Node AntiAffinity** – Node does **not** attract the pod

#### Pod-level:
- **Pod Affinity** – Pod attracts other pods (schedules together)
- **Pod AntiAffinity** – Pod repels other pods (schedules apart)

> Think of affinity like a **magnet**:  
> - Affinity pulls things together  
> - AntiAffinity pushes them apart

### Label a Node
Before using Node Affinity, label your node appropriately.

#### Example command:
```bash
kubectl label nodes <node-name> node-type=high-memory
kubectl get nodes --show-labels  # to verify the label
```

### Step 2: Add Required Node Affinity in Pod YAML

Use this when you want your pod to run **only** on nodes that have a specific label.

```yaml
affinity:
  nodeAffinity:
    requiredDuringSchedulingIgnoredDuringExecution:
      nodeSelectorTerms:
        - matchExpressions:
            - key: "env"
              operator: "In"
              values:
                - production

#Pod will be scheduled only on nodes where env=production label exists.
---

```
Use Preferred Node Affinity (Optional)

This lets you suggest (not enforce) preferred nodes for scheduling.

```yaml
affinity:
  nodeAffinity:
    preferredDuringSchedulingIgnoredDuringExecution:
      - weight: 1
        preference:
          matchExpressions:
            - key: "env"
              operator: "In"
              values:
                - production

#Pod will prefer nodes with env=production, but will still run elsewhere if needed.
---

```
### Step 3: Use Pod Affinity

This allows the pod to be scheduled **with** other pods based on labels.
```yaml
affinity:
  podAffinity:
    requiredDuringSchedulingIgnoredDuringExecution:
      - labelSelector:
          matchExpressions:
            - key: "app"
              operator: "In"
              values:
                - backend
        topologyKey: "kubernetes.io/hostname"
```
Pod will be scheduled on the same node as pods labeled app=backend.

This prevents the pod from being scheduled **with certain other pods**.
```yaml
affinity:
  podAntiAffinity:
    requiredDuringSchedulingIgnoredDuringExecution:
      - labelSelector:
          matchExpressions:
            - key: "app"
              operator: "In"
              values:
                - frontend
        topologyKey: "kubernetes.io/hostname"

```
Pod will avoid nodes that already run pods with label app=frontend
