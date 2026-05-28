# RHACM + CAPI + GitOps Playground

A playground repository exploring Red Hat Advanced Cluster Management (RHACM), Cluster API (CAPI), and OpenShift GitOps (ArgoCD) working together.

## Repository Structure

```
bootstrap/
  gitops-operator/     # Step 1: Install OpenShift GitOps operator (manual apply)
  root-app/            # Step 2: Root ArgoCD Application pointing at rhacm-operator/

rhacm-operator/        # Synced by ArgoCD: installs RHACM operator + MultiClusterHub
```

## Prerequisites

- OpenShift cluster with admin access
- `oc` CLI authenticated to the cluster

## Getting Started

### 1. Update the repo URL

Replace `YOUR_ORG` in `bootstrap/root-app/root-application.yaml` with your GitHub org/username.

### 2. Install OpenShift GitOps operator

```bash
oc apply -k bootstrap/gitops-operator/
```

Wait for it to be ready:

```bash
oc wait deployment/openshift-gitops-server \
  -n openshift-gitops \
  --for=condition=Available \
  --timeout=120s
```

### 3. Apply the root Application

```bash
oc apply -k bootstrap/root-app/
```

ArgoCD will now sync `rhacm-operator/`, installing the RHACM operator and creating the `MultiClusterHub`.

> **Note:** The `MultiClusterHub` takes several minutes to fully reconcile after the operator is installed. ArgoCD may show it as `Progressing` during this time — this is expected.
