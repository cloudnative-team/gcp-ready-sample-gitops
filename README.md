# gcp-ready-sample-gitops

Sample GitOps content consumed by the `liferay-infrastructure-applicationset` and `liferay-applicationset` ApplicationSets installed by the GCP-ready bootstrap.

## Layout

```
liferay/
    projects/
        <project-slug>/
            base/
                infrastructure.yaml    # shared infra values, merged first
                liferay.yaml           # shared app values, merged first
            environments/
                <env-id>/
                    infrastructure.yaml    # triggers infrastructure-applicationset
                    liferay.yaml           # triggers liferay-applicationset
```

Each ApplicationSet's `git` generator watches the matching `environments/*/<filename>.yaml` path. A file at that path produces one Argo Application. The base file (under `base/`) is merged in first via Helm `valueFiles`; the per-environment file overrides it.

ArgoCD also passes a fixed set of Helm parameters from Terraform (`environmentId`, `projectId`, `region`, `gateway.*`, `secretStoreName`, `global.deploymentName`, etc.). Do not duplicate those in the YAML.

## Projects

- **`lcd-51702`** — fixture for reproducing [LCD-51702](https://liferay.atlassian.net/browse/LCD-51702): Cloud SQL `RESTORE_VOLUME` wipes the IAM user list and in-DB grants, and the provisioning `db-grant` Job is `Create/Observe/Delete`-only so Crossplane never re-fires it. Two environments are provisioned (`dev`, `uat`) so a restore can be exercised end-to-end.

## Charts

- Infrastructure: `oci://us-central1-docker.pkg.dev/external-assets-prd/liferay-helm-chart/liferay-gcp-infrastructure`
- Liferay app: `oci://us-central1-docker.pkg.dev/liferay-artifact-registry/liferay-helm-chart/liferay-gcp`
