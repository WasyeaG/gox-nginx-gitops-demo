# GOX NGINX GitOps Demo

This repository demonstrates a repeatable GitOps workflow using OpenShift GitOps (Argo CD) to deploy and manage an NGINX application on the **gox-prd** OpenShift cluster.

## Environment

- **Cluster:** gox-prd
- **Namespace:** gox-nginx-demo
- **Argo CD Namespace:** openshift-gitops
- **Argo CD Application:** gox-nginx-demo
- **Git Branch:** main

## Repository Structure

```
argocd/
└── application.yaml

manifests/
├── namespace.yaml
├── configmap.yaml
├── deployment.yaml
├── service.yaml
├── route.yaml

README.md
```

## Verify OpenShift GitOps

```bash
oc get pods -n openshift-gitops
oc get application gox-nginx-demo -n openshift-gitops
```

Expected:

- Sync Status: Synced
- Health Status: Healthy

## Verify Deployment

```bash
oc get all -n gox-nginx-demo
oc get route -n gox-nginx-demo
```

Application URL:

https://nginx-gox-nginx-demo.apps.gox-prd.ephemeral-ovh.dev.li9.com

## Validate GitOps Reconciliation

Update the ConfigMap, commit, and push changes. Verify Argo CD automatically synchronizes the updated application.

## Validate Prune

```bash
git rm manifests/route.yaml
git commit -m "Validate Argo CD prune on gox-prd"
git push origin main
```

Verify:

```bash
oc get route nginx -n gox-nginx-demo
```

Expected:

```
Error from server (NotFound): routes.route.openshift.io "nginx" not found
```

## Restore the Route

```bash
git checkout HEAD~1 -- manifests/route.yaml
git add manifests/route.yaml
git commit -m "Restore Route after Argo CD prune validation"
git push origin main
```

Verify:

```bash
oc get application gox-nginx-demo -n openshift-gitops
oc get route nginx -n gox-nginx-demo
```

Expected:

- Application is Synced
- Application is Healthy
- Route exists again

## Validation Completed

- OpenShift GitOps Operator installed successfully
- Argo CD application created and managing the local cluster
- Application reached **Synced** and **Healthy**
- Git changes automatically reconciled by Argo CD
- Browser validation confirmed **Version 2 - GitOps Update**
- Prune behavior validated
- Restore behavior validated