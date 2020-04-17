# Container Build

**Build container image using Docker** (local build)

```bash
# Build container image
$ docker build -f Dockerfile -t <org>/<app_name>:<version> .
# Example: docker build -f Dockerfile -t tmp/hello-python:latest .

# Run the container image
$ docker run -it -p 5001:5001 --name hello_py tmp/hello-python:latest

# (optional) push image to remote registry
# $ docker push --help

# for more info
$ docker --help

--
# Note: If the project has docker-compose.yml file then you could also
# build containers using docker-compose commands.

# Build container image
$ docker-compose build --no-cache --pull

# Run container image
$ docker-compose up <service_name> && docker-compose rm -fsv

$ docker-compose -h
```

---

**Build container image using OCP** (via cli commands, remote build)

```bash
$ oc login https://c100-e.us-south.containers.cloud.ibm.com:30403 --token=<token>

$ oc new-project <namespace_name>
# Example: oc new-project hello-apps

$ oc new-build --binary --strategy docker --name <buildconfig_name>
# (or)
$ oc new-build --binary --strategy docker --docker-image <base_image> --name <buildconfig name>
# Example1: oc new-build --binary --strategy docker --docker-image python:3.8.0-alpine3.10 --name hello-python
# Example2: oc new-build --binary --strategy docker --docker-image node:12.16.1-alpine3.10 --name hello-nodejs

# see list of all buildconfigs, imagestreams in a project namespace
$ oc get bc,is

$ oc start-build <buildconfig_name> --from-dir . --follow
# Example: oc start-build hello-python --from-dir . --follow

# Note: By default build saves container image to local container registry on OCP

# Start a new build
$ oc start-build <buildconfig name> --from-dir . --follow
# Example: oc start-build hello-app --from-dir . --follow

# see buildconfig logs
$ oc logs -f bc/<name>

# Note: By default build saves container image to local container registry on OCP

# See the build resources of an app
$ oc get bc <buildconfig name>
# Example: oc get bc hello-app

$ oc get is <imagestream name>
# Example: oc get is hello-app

# Get container image full url ([registry-url]/[namespace]/[image]:[tag/@sha])
$ oc get istag <is name>:<tag>
# Example: oc get istag hello-app:latest
```

---

**Build container image using OCP** (via cli + templates, remote build)

```bash
$ oc login ...

# switch to project namespace
$ oc project <namespace_name>

# process build templates
$ alias oc_process="oc process --local --ignore-unknown-parameters -o yaml $@"
$ oc_process --param-file "./../env_dev/ocp_params.txt" -f "build_templates/buildconfig_template.yaml" > compiled_build/bc.yaml
$ oc_process --param-file "./../env_dev/ocp_params.txt" -f "build_templates/imgstream_app.yaml" > compiled_build/is_app.yaml

# Apply/create build resources on OpenShift
$ oc apply -f /compiled_build

# see list of all buildconfigs, imagestreams in a project namespace
$ oc get bc,is
# Other image related OCP resources: bc, is, istag, isimage, images, imagesignatures

# Start a new build
$ oc start-build <buildconfig name> --from-dir . --follow
# Example: oc start-build hello-app --from-dir . --follow

# Note: By default build saves container image to local container registry on OCP

# See the build resources of an app
$ oc get bc <buildconfig name>
# Example: oc get bc hello-app

$ oc get is <imagestream name>
# Example: oc get is hello-app

# Get container image full url ([registry-url]/[namespace]/[image]:[tag/@sha])
$ oc get istag <is name>:<tag>
# Example: oc get istag hello-app:latest
```

---

**Build container image using cloud service** (remote build)

IBM Cloud Container Registry

```bash
# Login to ibmcloud icr and create a registry namespace for images
$ ibmcloud login # use --sso flag if you're an IBMer

# IBM Docker Registry login
$ ibmcloud cr login

# Create a new container image namespace
$ ibmcloud cr namespace-add <my_namespace>
# Example: ibmcloud cr namespace-add my_awesome_apps

--
# build image and list images on icr

# the following command builds and saves the image to ibm cloud (remote) container registry
$ ibmcloud cr build --tag registry.ng.bluemix.net/<my_namespace>/myapp:v1 .

$ ibmcloud cr image-list

--
# pull the container from the remote registry to local in order to run/verify app locally

# (first point docker to point to remote registry)
$ ibmcloud cr login # or using `docker login` command
# (or)
$ docker login registry.ng.bluemix.net # for more options please see `docker login --help`

# pull image from remote registry to local and verify app by running the image
$ docker pull registry.ng.bluemix.net/<my_namespace>/myapp:v1

$ docker run -it registry.ng.bluemix.net/<my_namespace>/myapp:v1

# remove image from local registry
$ docker rmi registry.ng.bluemix.net/<my_namespace>/myapp:v1

# operate on a remote registry (please see `ibmcloud cr --help` for more info)
# (optional: this will delete the container image)
$ ibmcloud cr image-rm us.icr.io/<my_namespace>/myapp:v1
```

---

**Build container image using Podman/Buildah**

[Podman](https://podman.io/), [Buildah](https://buildah.io/), [Skopeo](https://github.com/containers/skopeo)

ToDo

---

**Build a container image from source code Using S2I**

ToDo

---
