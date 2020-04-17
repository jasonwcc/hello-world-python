# OpenShift Deployment

**Prerequisites:**

1. An application that you can build, test and run locally. If you don't have one,
   please see instructions in the README's at the corresponding [sample app directories](./../hello-apps/).

2. Container image for an application. Please see [container build](./container_build.md)
   instructions if you don't have a container image.

## Deployment to OpenShift (a.k.a OCP)

Install [oc cli](https://duckduckgo.com/?q=openshift+cli+install)

```bash
$ oc version
oc v3.11.0+0cbc58b
...
```

Create Deployment artifacts for the app : _Deployment_, _Service_, _Route_, etc.

```bash
# cd in to the .deployment directory in the project root (hello-apps/.deployment/)
$ cd .deployment/

# Process/compile deployment templates
$ oc process --help

# (optional): Update configurations for the app and the platform (OCP)
# Change values in `env_dev/ocp_params.txt`, such as container image etc. if desired

# process/compile all resource templates: deployconfig, service, route, etc.
$ alias oc_process="oc process --local --ignore-unknown-parameters -o yaml $@"
$ oc_process --param-file "./../env_dev/ocp_params.txt" -f "deploy_templates/deploymentconfig_template.yml" > compiled_dev/dc.yaml
$ oc_process --param-file "./../env_dev/ocp_params.txt" -f "deploy_templates/service_template.yml" > compiled_dev/service.yaml
$ oc_process --param-file "./../env_dev/ocp_params.txt" -f "deploy_templates/route_template.yml" > compiled_dev/route.yaml
```

Create deployment artifacts for app: _ConfigMaps_ and _Secrets_

```bash
# (optional)
# Update environment variable configs and secrets for the app
# Change values in `env_dev/[app_cfg.env | app_sec.env]` if desired

--
# ConfigMaps:
$ oc create configmap --help

# create Kube/OCP configmap for the app env variable configs
$ oc create configmap hello-app-cfg-appenv --from-env-file="./../env_dev/app_cfg.env" --dry-run --output yaml > compiled_dev/cm.yaml

# (optional) add labels to the generated configmap
# $ oc label  configmap <configmap_name> app=<app_name>

--
# Secrets
$ oc create secret --help

# create Kube/OCP Secret resource for the app secrets
$ oc create secret generic hello-app-sec-appenv --from-env-file="./../env_dev/app_sec.env" --dry-run --output yaml > compiled_dev/secret.yaml

# (optional) add labels to the generated secret
# $ oc label secret <secret_name> app=<app_name>
```

Deploy app by applying deployment artifacts

```bash
# Login in to OCP
$ oc login --server=https://c101-e.us-east.containers.cloud.ibm.com:32205 --token=12345

--
# Namespace/Project:

# create new project/namespace
$ oc create namespace <name>

# (or)

# switch to project namespace
$ oc project <name> # Example: oc project rg-dev

--
# Create deployment by applying Kubernetes/OCP resources generated in previous steps.
$ oc apply -f <directory where compiled templates are located>
# Example: oc apply -f compiled_dev/
```

## Verify Deployment

```bash
# Check OCP resources
$ oc get dc <name>
$ oc get pods
$ oc get pods | grep <name>
$ oc get dc,svc,route hello-app

--
# Check application

$ curl <host>/debug

# (or)

# In Browser -> <host>/debug/ui
```

## View app logs

```bash
$ oc logs --help

$ oc logs -f dc/<name>
# Example: oc logs -f dc/hello-app
```

## Scale

```bash
# scale number of replicas
ToDo

# rollout new deployment
ToDo
```

## Rollout

```bash
# Rollout or rollback a deployment of an app
$ oc get dc
$ oc rollout latest <dc_name>
# Example: oc rollout latest hello-app
```

## Remove deployment

```bash
# delete all the resources
$ oc delete --help

$ oc delete -f <directory where compiled templates are located>
#(or)
$ oc delete ..
```

END

---
