# OCP_Tests

A public repository for me to test things with Openshift :-)

## RHOAI_Provision_helm_chart

A simple helm chart for deploying the components of RHOAI, onto a vanilla cluster.

## RHOAI_Image_Builder

This is an Openshift Pipeline, that create Images from source control, pushes them to image registries, and then creates an ImageStream which allows RHOAI to use them as "Custom Notebook Images".
