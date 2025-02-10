
# Dug Configuration

RENCI uses [Helm](https://helm.sh/) charts, [Kubernetes](https://kubernetes.io/), [Redis Server](https://redis.io/), [Apache Airflow](https://airflow.apache.org/) and [Elasticsearch](https://www.elastic.co/). The Dug Configuration discussed below relies on that tooling. Please contact us if you want to use something else... 

Any customizations to this configuration for specific instantiations of Dug (e.g., BDC Dug) are documented in that version of Dug's Helm chart and are not made publicly available on this website. 

Laura's notes:
If you need to customize: 
Deploy using helm: https://dug.readthedocs.io/en/latest/deployment-guide/helm/
Customize through the helm chart: https://github.com/helxplatform/dug/blob/develop/src/dug/config.py



## 1. Set Up the Dug Environment
PJ's stuff here

Client machine must be able to access enviro


## 2. Add the Helm Repo and Pull the Latest Helm Chart

RENCI uses Helm charts to deploy Dug in Kubernetes. A Helm chart is like a recipe. It bundles everything an application needs&mdash;code, settings, and dependencies&mdash;into a single package. This makes it easier to install, update, and manage applications consistently across different environments without having to manually configure everything each time.

1. To access Dug's Helm chart, you must [install Helm](https://helm.sh/docs/intro/install/) on your local machine. 

2. Once installed, initialize Dug's Helm chart repository.
```bash
# Ensure the name 'helx-charts' is used in the command below. 
$ helm repo add helx-charts https://helxplatform.github.io/helm-charts/
```
For more help on initatilizing, see [Initialize a Helm Chart Repository](https://helm.sh/docs/intro/install/) and [Helm Repo Add](https://helm.sh/docs/helm/helm_repo_add/).

3. Now that you've initialized Dug's Helm repo, pull the latest Helm chart or [update](https://helm.sh/docs/helm/helm_repo_update/) any existing charts on your local machine with the latest changes.
```bash
# Update the repo
$ helm repo update
```

4. To ensure you can access Helm charts, [search](https://helm.sh/docs/helm/helm_search/) the repo for `helx-charts`
```bash
$ helm search repo helx-charts
```


## 3. Install Dug

From your local machine, create a Helm values file (config file) and inject those values into the existing Search Helm chart shown below.

In the Kubernetes environment, (in a single pod or what?) Dug's configuration is supplied by helm values files that 

```bash
$ helm -n <your-namespace> upgrade --install \
--skip-crds -f <path-to-your-values-file> \
search helx-charts/search
```

 The `--skip-crds`, in the first command above is required if your Kubernetes user does not have permissions to create custom resource definitions (CRDs).


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
        # AIRFLOW__CLI__ENDPOINT_URL: https://yk-heal.apps.renci.org/airflow
        AIRFLOW__WEBSERVER__BASE_URL: https://yk-heal.apps.renci.org/airflow
        
        AIRFLOW__KUBERNETES__DELETE_WORKER_PODS: "FALSE"
    users:
      # Configure airflow user
      - email: kebedey@renci.org
        firstName: Yaphet
        lastName: Kebede
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
    hidden_result_tabs: "cdes"
```  

Full list of configurable options can be found [here](https://github.com/helxplatform/search-chart/blob/master/values.yaml).

## 4. Customizing Helm Chart Values

## 5. Installing Search

Once we have the proper values configured and stored in the file `search-values.yaml` we can use the following command install/upgrade a Search (DUG) instance.

```bash
helm -n <your-namespace> upgrade --install --skip-crds -f search-values.yaml search helx-charts/search
```
