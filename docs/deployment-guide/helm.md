Dug and Roger deployments can be managed via a kubernetes package manager called helm. 
More about helm can be found here [helm.sh](https://helm.sh/).

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

### Instance installation

The following section describes the steps needed to install an instance of Search(DUG).

During our install we supply a configuration via helm values files.

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



### 🚀 Install Search

Once we have the proper values configured and stored in the file `search-values.yaml` we can use the following command install/upgrade a Search (DUG) instance.

```bash
helm -n <your-namespace> upgrade --install --skip-crds -f search-values.yaml search helx-charts/search
```
