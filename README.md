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

