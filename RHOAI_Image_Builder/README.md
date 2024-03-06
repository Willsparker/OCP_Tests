# RHOAI_Notebook_Builder

This project's focus is on how you could have a Containerfile in Soure Control, and use Openshift Pipelines to build that image and push it to an (external) image registry. 

The pipeline defined here goes on to create an ImageStream that will reference the newly built image, and place it within the available Notebook Images within Red Hat Openshift AI's Dashboard. 

An example usecase of this may be to allow data scientists to easily create their own Notebook images, containing all their data-science specific packages, via a PR. To show this, the Containerfile within this repository is a very basic one that installs example Data Science tools. 

## Using the Image

You can build the image via:

```bash
$ podman image build .
```

The base image in the Containerfile references a workbench image from Open Data Hub. Within Red Hat Openshift AI, a 'Workbench' whose 'Notebook Image' is 'Standard Data Science' 
, will be using these images. As a result the images built from this Containerfile are already setup to work as RHOAI Workbenches, with any additional enhancements added to the Containerfile.  

## Pre-reqs:

- Admin access to a Red Hat Openshift Container Platform. (This was built on OCP 4.14).

- RHOAI Installed + Configured. (Thankfully, I wrote a helm chart that does this for you!)

- 'Openshift Pipelines' Operator installed. (Built on 1.14.0)

## Step by Step [To Be Tested]:

- Login to image registry you intend to push the new image to.

- Login to your OCP Cluster

- Apply the 'templates/pipeline_definition.yaml' file

```bash
oc create -f templates/pipeline_definition.yaml
```

- Create a secret, with the dockerconfigjson

```bash
$ oc create secret generic quay_secret --from-file=.dockerconfigjson=/run/user/<UID>/containers/auth.json --type=kubernetes.io/dockerconfigjson -n rhoai-notebook-builder
```

- Link the 'pipeline' service account with your secret.

```bash
$ oc secrets link pipeline quay_secret --for=pull,mount
```

- Apply the 'templates/service_account_config.yaml' file. This will create the Role + RoleBinding to create ImageStreams within the redhat-ods-applications namespace.

```bash
$ oc create -f templates/service_account_config.yaml
```

- Try out the pipeline!

## Future Enhancements:

When I first started off this project, I had intended to have this Pipeline triggered by a Git PR - this would allow Data Scientists to simply raise a PR, and their custom notebook image would appear in RHOAI after a little while, once the PR had been merged.

This is possible, with the use of Openshift Pipelines' EventListeners/TriggerTemplates/TriggerBindings, and GitHub Webhooks, and likely a lot of time testing. Assuming a instance in which ALL custom notebook images are kept in a single repository, CEL interceptors within an Openshift EventListener would allow for pipelines to be kicked off with different TriggerTemplates and bindings.

The difficulty I found was attempting to extract the information required for the pipeline, such as the 'IMAGE_NAME' and 'IMAGE', from the payload given by the GitHub "Push" Webhook. Some use of a commit-message scheme could be used to fill in some of this; such as is demonstrated in the Red Hat Blog linked below, but ultimately this would likely be too 'fragile' to use in the real-world. 

### Helpful Links for development:

- Tekton Triggers and CEL interception

- OpenShift Pipelines Advanced Triggers Part 1 - Triggering Different Project Builds in the Same Repository
