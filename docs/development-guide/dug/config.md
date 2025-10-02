
# Dug Configuration

The reference Dug environments at RENCI are deployed using the Helm deployment
system on Kubernetes compliant systems. It is possible, and has been done at
RENCI partner institutions, to successfully deploy Dug at scale using the
command line interface. The details of doing that are not fully documented
here. 

The Dug configuration outlined below relies on a stack that includes
[Helm](https://helm.sh/) charts, [Kubernetes](https://kubernetes.io/), [Redis
Server](https://redis.io/), [Apache Airflow](https://airflow.apache.org/), and
[Elasticsearch](https://www.elastic.co/) running in [Google
Cloud](https://cloud.google.com/).

Customizations for specific implementations of Dug (*e.g.*, BDC Dug) are
documented in the corresponding version of Dug's Helm chart and are not publicly
available on this website.

## 1. Set Up the Dug Environment

To deploy on a Kubernetes cluster, the deploying user will need access to a
Kubernetes cluster and credentials on that cluster with rights to create pods,
ingests, persistent volumes, and deployments. Further, to use the Apache Airflow
Kubernetes executor, those users will further need to be able
to set rights on pods so that they can create their own pods. 

A full Dug deployment may create 10-20 pods, depending on the level of
parallelization in Airflow. While memory usage may vary, Dug developers at RENCI
usually have quotas with at least 64 GB of memory, 24 CPUs, 256 GB of persistent
storage, and 10 GB of ephemeral storage.

## 2. Add the Helm Repo and Pull the Latest Helm Chart

RENCI uses Helm charts to deploy Dug in Kubernetes. A Helm chart is like a
recipe. It bundles everything an application needs&mdash;code, settings, and
dependencies&mdash;into a single package. This makes it easier to install,
update, and manage applications consistently across different environments
without having to manually configure everything each time.

1. To access Dug's Helm chart, you must [install
   Helm](https://helm.sh/docs/intro/install/) on your local machine.

2. Once installed, initialize Dug's Helm chart repository.
```bash
# Ensure the name 'helx-charts' is used in the command below. 
$ helm repo add helx-charts https://helxplatform.github.io/helm-charts/
```

For more help on initatilizing, see [Initialize a Helm Chart
Repository](https://helm.sh/docs/intro/install/) and [Helm Repo
Add](https://helm.sh/docs/helm/helm_repo_add/).

3. Now that you've initialized Dug's Helm repo, pull the latest Helm chart or
[update](https://helm.sh/docs/helm/helm_repo_update/) any existing charts on
your local machine with the latest changes.

```bash
# Update the repo
$ helm repo update
```

4. To ensure you can access Helm charts,
   [search](https://helm.sh/docs/helm/helm_search/) the repo for `helx-charts`

```bash
$ helm search repo helx-charts
```


## 3. Create a Helm Values File 

From your local machine, create a Helm [values
file](https://helm.sh/docs/chart_best_practices/values/) with values of your
choice. The full list of configurable options can be found on the Dug GitHub
repo
either[here](https://github.com/helxplatform/search-chart/blob/develop/README.md)
or
[here](https://github.com/helxplatform/search-chart/blob/master/values.yaml).

The sample Helm values file below can be used as a starting point. It will
install the following sub-components:

- Airflow
- Redis
- Elasticsearch
- Tranql API
- Dug Search API
- Dug UI

__Sample Helm values file:

```yaml
airflow:
  airflow:
    config:        
        AIRFLOW__WEBSERVER__BASE_URL: https://example.com/airflow/        
        AIRFLOW__KUBERNETES__DELETE_WORKER_PODS: "FALSE"
    users:
      # Configure airflow user
      - email: mail-of-user@email.com
        firstName: user
        lastName: pass
        password: somePass
        role: Admin
        username: admin


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
    - host: <your ingress address>
  tls:
   - secretName: <your ingress address>-tls
     hosts:
       - <your ingress address>
tranql:
  image: containers.renci.org/helxplatform/tranql
  imageTagOverride: 0.4.dev5
redis-insight:
  # -- Enable/Disable Redis UI
  enabled: True
  # -- Url should be same as public ingress url
  rootUrl: "https://<your ingress address>/redis-insight"   

ui:
  # Enables UI 
  enabled: true
  config:
    brand_name: heal
    search:
      url: https:\/\/<your ingress address>\/search-api
    tranql_enabled: "true"    
    tranql_url: https:\/\/<your ingress address>\/tranql
    # hide tabs on UI    
```  

## 4. Install or Upgrade Dug

Use the command below to install or upgrade Dug using your Helm values file.
```bash
$ helm -n <your-namespace> upgrade --install --skip-crds -f <path-to-your-values-file> search helx-charts/search
```

Replace `<your-namespace>` with the actual Kubernetes namespace you want to
target.

The `upgrade --install` command upgrades the Dug release if it already exists,
and installs it if it doesn't.

The `--skip-crds` option tells Helm not to install any [Custom Resource
Definitions](https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/)
(CRDs). CRDs define new types of resources for Kubernetes, but sometimes, you
don't want Helm to install them if they’ve already been defined elsewhere or if
you want to handle them separately. If your Kubernetes user does not have
permissions to create CRDs, then this option is required or the
installation/upgrade will fail.

`-f <path-to-your-values-file>` The `-f` flag allows you to specify a values
file (`<path-to-your-values-file>`) that contains configuration settings for the
Helm chart. Replace `<path-to-your-values-file>` with the path to and filename
of your values file.

`search` is the the name you give to this installation/upgrade of Dug, which
allows you to refer to it later when you need to upgrade or delete it. You may
replace `search` with a name of your choosing.

`helx-charts/search` is the Helm chart you’re using. It’s a package of
pre-configured Kubernetes resources. helx-charts is the repository (could be an
official or private Helm chart repository), and search is the specific chart you
are installing or upgrading.


