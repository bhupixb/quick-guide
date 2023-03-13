# helm charts: a quick guide

[Helm](https://helm.sh/) is a package manager for Kubernetes.

### Creating a new Helm Chart:

Let’s say you want to create a new helm chart for a service from scratch. The boilerplate templates helm will generate out of the box. 

```bash
$ helm create brand-new-service
```

This will create a new helm chart named *brand-new-service* with standard files like a K8s deployment, service, service account, hpa & ingress etc.

```bash
$ tree brand-new-service
brand-new-service
├── Chart.yaml
├── charts
├── templates
│   ├── NOTES.txt
│   ├── _helpers.tpl
│   ├── deployment.yaml
│   ├── hpa.yaml
│   ├── ingress.yaml
│   ├── service.yaml
│   ├── serviceaccount.yaml
│   └── tests
│       └── test-connection.yaml
└── values.yaml
```

> For more options, refer [https://helm.sh/docs/helm/helm_create/](https://helm.sh/docs/helm/helm_create/).
> 

### Rendering a helm chart:

To render chart templates & write output to stdout, we can use [helm template](https://helm.sh/docs/helm/helm_template/) 

```bash
cd brand-new-service

# Render chart
helm template mychart .

# Pass override values file
helm template mychart . -f override-values.yaml

# Render specific template e.g. deployment.yaml
# We can pass template file path using -s flag
helm template mychart . -s templates/deployment.yaml
```

If there’s an error while templating your chart. You might have a hard time **to understand what’s causing the error looking at output. So you can add —debug flag to print out more info.

```bash
helm template mychart . --debug
```

> For more options, refer [https://helm.sh/docs/helm/helm_template/](https://helm.sh/docs/helm/helm_template/).
> 

### Override values file:

When doing *helm template* by default values are read from *values.yaml* file. If we want to provide an override values file, we can use -f flag.

```bash
# create a test template
$ cat > template/test.yaml
replicas: {{ .Values.replicas }}

# create an override file
$ cat > override-values.yaml
replicas: 5

# default values.yaml file
...
replicas: 1

# Our default values.yaml has replicas set to 1. But say we want to 
# override that value to 5 in prod. We can pass an override file.
$ helm template mychart . -s templates/test.yaml -f override-values.yaml
---
# Source: brand-new-service/templates/test.yaml
replicas: 5
```

The rendered *test.yaml* file has been set to 5 replicas as when resolving values for key **replicas** , the *override* file will be given more precedence over default *values.yaml.*

### Installing a Helm chart:

To install a chart, we can use:

```bash
helm install mychart . [-f overridefile1.yaml -f overridefile2.yaml ...]

# To install helm chart in a particular K8s namespace, use -n flag
helm install mychart . -n production
```

After you have installed the chart, if you want to get rendered manifest files for all k8s objects created. You can do

```bash
helm get manifest mychart -n my-namespace
```

> For more options, refer [https://helm.sh/docs/helm/helm_install/.](https://helm.sh/docs/helm/helm_install/)
> 

### Listing all helm charts installed in a Namespace:

```bash
helm ls -n my-namespace
```

### Helm repo:

There are multiple public chart repositories where helm charts for different applications are stored.

To add any repo to your local helm repository index, you can do:

```bash
helm repo add https://jfrog.myorg.com/

# update information of available charts locally from chart repositories
helm repo update 

# To list all helm repo's
helm repo ls
```
