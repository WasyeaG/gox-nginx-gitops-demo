# GOX NGINX GitOps Demo

This repository demonstrates a repeatable GitOps workflow using OpenShift GitOps (Argo CD) to deploy and manage an NGINX application on the gox-sbx OpenShift cluster.

## Environment

- Cluster: gox-sbx
- Namespace: gox-nginx
- Argo CD Namespace: openshift-gitops
- Argo CD Application: gox-nginx
- Git Branch: main

## Repository Structure

manifests/
- namespace.yaml
- configmap.yaml
- deployment.yaml
- service.yaml
- route.yaml

## Verify Argo CD

Run:

oc get pods -n openshift-gitops

Verify the application:

oc get application gox-nginx -n openshift-gitops

Expected:
- Sync Status: Synced
- Health Status: Healthy

## Verify Deployment

oc get all -n gox-nginx

## Validate GitOps Reconciliation

1. Update manifests/configmap.yaml
2. Commit and push:

git add .
git commit -m "Update application"
git push origin main

3. Verify Argo CD synchronizes the change.

## Validate Prune

Remove the Route manifest:

git rm manifests/route.yaml
git commit -m "Validate ArgoCD prune"
git push origin main

Verify:

oc get route -n gox-nginx

Expected:

No resources found in gox-nginx namespace.

## Restore the Route

git restore --source=HEAD^ -- manifests/route.yaml
git add manifests/route.yaml
git commit -m "Restore Route after Argo CD prune validation"
git push origin main

Verify:

oc get application gox-nginx -n openshift-gitops
oc get route -n gox-nginx

Expected:
- Application is Synced
- Application is Healthy
- Route exists again

## Validation Completed

- OpenShift GitOps installed
- Argo CD application created
- Application reaches Synced / Healthy
- Git changes automatically reconciled
- Prune behavior validated
- Restore behavior validated
