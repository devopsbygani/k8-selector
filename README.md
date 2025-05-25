# k8-selector
# Taint and Toleration.

## Step-by-step Guide

### Step 1: Understand Taints
- A **taint** is applied to a node to **prevent certain pods from being scheduled** on it unless they explicitly tolerate it.
- It’s used to dedicate nodes for specific purposes (e.g., GPU workloads, logging, infra).

#### Example command:
```bash
kubectl taint nodes <node-name> key=value:NoSchedule
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