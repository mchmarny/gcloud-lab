# Cloud Asset Inventory

Asset Inventory command in `gcloud` provides a quick way of listing and searching resources across multiple projects. Here are few commands that may be helpful in use-cases related to containerize workload.

## Setup

When searching across project in a given organization you will need to set the `$ORG_ID`. List the organizations, and export `ORG_ID` as environment variable: 

```shell
gcloud organizations list
```

## Artifact Registry (AR)

Lists all AR repositories created after a given date:

```shell
gcloud asset search-all-resources \
  --scope "organizations/$ORG_ID" \
  --asset-types "artifactregistry.googleapis.com/Repository" \
  --query "createTime>2023-04-01" \
  --order-by "location" \
  --format "value(name)"
```

This will return a list of repos in this format: 

```shell
//artifactregistry.googleapis.com/projects/$PROJECT_ID/locations/$LOCATION/repositories/$REPO
```

You can then use one of the returned registries to list its images:

```shell
gcloud asset search-all-resources \
  --scope "organizations/$ORG_ID" \
  --asset-types "artifactregistry.googleapis.com/DockerImage" \
  --query "parentFullResourceName=//artifactregistry.googleapis.com/projects/$PROJECT_ID/locations/$LOCATION/repositories/$REPO" \
  --read-mask "displayName" \
  --format "value(displayName)"
```
## Cloud Run 

Cloud Run service can have multiple revisions. To list all image digests for each revision:

> Note, this will list all revision images, there is no currently way to list only the revisions actively taking traffic. 

```shell
gcloud asset search-all-resources \
  --scope "organizations/$ORG_ID" \
  --asset-types "run.googleapis.com/Revision" \
  --order-by "createTime DESC"
```

## GKE

List GKE deployments across all regions/clusters/namespaces in an organization: 

```shell
gcloud asset search-all-resources \
  --scope "organizations/$ORG_ID" \
  --asset-types "apps.k8s.io/Deployment" \
  --read-mask "name" \
  --format "value(name)"
```

> You can use the same approach to listing the pods by using `--asset-types "k8s.io/Pod"`

Whether deployments or pods, there is also a number of ways to filter these results using `NOT` and `labels` content. These queries can be also combined with things like `createTime`: 

```shell
gcloud asset search-all-resources \
  --scope "organizations/$ORG_ID" \
  --asset-types "apps.k8s.io/Deployment" \
  --query "NOT labels:k8s-app AND NOT labels:control-plane AND NOT labels:cluster-service AND NOT labels:addonmanager AND NOT labels:cnrm AND createTime>2022-06-01" \
  --read-mask "name" \
  --format "value(name)"
```

Common labels: 

* `k8s-app` - kube-dns
* `control-plane` - BinAuthZ stuff
* `cluster-service` - GKE event exporter
* `cnrm` - CNRM resource stats recorder

## Disclaimer

This is my personal project and it does not represent my employer. While I do my best to ensure that everything works, I take no responsibility for issues caused by this code.