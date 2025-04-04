# Dug Incident Contingency Standard Operating Procedure


## Purpose

This document covers planning and documentation of standard procedure for
responding to a range of potential contingencies.

## Scope

The procedures outlined in this document are limited to those of the Dug
Semantic Search engine and the associated services which directly constitute its
core services. The search engine itself relies on a number of upstream processes
for collecting, transforming, and annotating data from a range of sources.

## Review cadence

This document is reviewed annually in the first quarter of the calendar
year. Updates should be completed by the first of April of every year.

This document represents standard procedures as of 1 April, 2025.

## Search installation procedure

Dug may be installed through a number of different procedures, as are outlined
within the Dug repository. What is documented here is the method by which
RENCI-hosted Dug services are installed, onto Kubernetes infrastructure using
the Helm Kubernetes installation management software.


### Configuring helm repository

Add entry for helm repository:
```bash
# make sure that the name helx-charts is used here.
$ helm repo add helx-charts https://helxplatform.github.io/helm-charts/
```

Update local helm repository by pulling latest changes:
```bash
# update the repo
$ helm repo update
```

Check if all is working:
```bash
$ helm search repo helx-charts
```

Some details on standard procedures for using `helm` at RENCI are documented in
the [RENCI DevOps
Wiki](https://renci.atlassian.net/wiki/spaces/DSO/overview?homepageId=229461).

### Development values configuration

The following instructions are for installing into the personal namespace of a
user named `someuser` and the application would be accessible at
`https://user-dug.apps.renci.org`. These values should be changed to match their
appropriate values.

During our install we supply a configuration via helm values files.

```bash
$ helm -n <your-namespace> upgrade --install \
--skip-crds -f <path-to-your-values-file> \
search helx-charts/search
```

 The `--skip-crds`, in the first command above is required if your Kubernetes
 user does not have permissions to create custom resource definitions (CRDs).


When installing search instance the following sub-components are installed.

- Airflow
- Redis
- Elasticsearch
- Tranql API
- Dug Search API
- Dug UI

The configuration below can be used as a starting point for installation.

```yaml
airflow:
  airflow:
    image:
      tag: "develop"
    config:
        # AIRFLOW__CLI__ENDPOINT_URL: https://user-dug.apps.renci.org/airflow
        AIRFLOW__WEBSERVER__BASE_URL: https://user-dug.apps.renci.org/airflow

        AIRFLOW__KUBERNETES__DELETE_WORKER_PODS: "FALSE"
    users:
      # Configure airflow user
      - email: someuser@renci.org
        firstName: Some
        lastName: User
        password: somePass
        role: Admin
        username: admin
    # Resource config for all task runner pods.
    #kubernetesPodTemplate:
    #  resources:
    #    limits:
    #      cpu: 2
    #      memory: 1G
    #    requests:
    #      cpu: 2
    #      memory: 1G
  dags:
    gitSync:
      branch: "develop"
api:
  image:
    repository: containers.renci.org/helxplatform/dug
    tag: "v2.10.0-prerelease12"
    pullPolicy: Always
  # scale api pods as desired
  replicas: 1
#elasticsearch:
  # uncomment the following to run just a single elastic search , by default a cluster
  # of three es pods is created.
  #replicas: 1
  #clusterHealthCheckParams: "wait_for_status=yellow&timeout=1s"

config:
    data_source: s3
    # select datasets to ingest , comma seperated values allowed.
    input_sets: crdc:v1.0
    # select graphs to ingest
    kgx_data_sets: baseline-graph,cde-graph
    node_to_queries_enabled: true
    s3:
      # aquire this from dug devs
      access_key: ***
      bucket: **
      host: ***
      secret_key: ***


ingress:
  # Enables Ingress
  enabled: True
  hosts:
      # eg : mysite.apps.renci.org
    - host: user-dug.apps.renci.org
  tls:
   - secretName: user-dug.apps.renci.org-tls
     hosts:
       - user-dug.apps.renci.org
tranql:
  image: containers.renci.org/helxplatform/tranql
  imageTagOverride: 0.4.dev5
redis-insight:
  # -- Enable/Disable Redis UI
  enabled: True
  # -- Url should be same as public ingress url
  rootUrl: "https://user-dug.apps.renci.org/redis-insight"

ui:
  # Enables UI
  enabled: true
  config:
    brand_name: dug
    search:
      url: https:\/\/user-dug.apps.renci.org\/search-api
    tranql_enabled: "true"
    tranql_url: https:\/\/user-dug.apps.renci.org\/tranql
    # hide tabs on UI
    hidden_result_tabs: "cdes"
```

Full list of configurable options can be found
[here](https://github.com/helxplatform/search-chart/blob/master/values.yaml).

### Production values installation

Production configurations will be similar to the above development values
configuration, but will inevitably have some values customized to the particular
instance. Values for an existing installation can be retrieved with the
following helm command:

```bash
$ helm get values [installation_name]
```

### Install Search

Once we have the proper values configured and stored in the file
`search-values.yaml` we can use the following command install/upgrade a Search
(DUG) instance.

```bash
$ helm -n <your-namespace> upgrade --install --skip-crds -f search-values.yaml search helx-charts/search
```

## Incident response

Because Dug is largely a search engine that ingests data from external sources,
indexes them for search, and then provides a search interface for those
results, very little unique data or state information is stored on any given
instance of Dug. While recovery may require some length of time, depending on
how much of the upstream data has to be re-ingested by various support processes
to the search engine, the primary means of recovering from service corruption,
compromise, or other outage is to simply re-install the search engine as per the
instructions detailed above in this document.

## Planned SOP changes

There are known issues with the current operating procedure which are currently
under review but are not established as standard procedures.

* RENCI uses Hashicorp's Vault (service at `vault.apps.renci.org`) to store
  production values files. However, these are not always kept up to date. We are
  evaluating changes to the installation procedure to standardize storing and
  versioning of values files.
* Currently there is considerable repetition in the values file that has to be
  kept in sync manually. We are evaluating `kustomize` as a means of templating
  the values file.
