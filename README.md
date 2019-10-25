# Learning Kubernetes with the guestbook application

The guestbook example shows how to build a simple multi-tier web application using Kubernetes and Docker. The application consists of a web front end, Redis master for storage, and replicated set of Redis slaves, all for which we will create Kubernetes deployments, pods, and services.

There are two versions of this application. Version 1 (in the `v1` directory) is the the basic guestbook application in go with deployment files for you to exampine at your convenience. Version 2 (in the `v2` directory) extends the guestbook example with additional functionality to call the IBM Watson Tone Analyzer service.

The `v2` directory contains the application version that will be the focus for the digital developer conference hands-on lab.

## Prerequisites

* An [IBM Cloud account](http://ibm.biz/Bdz3ru) that is either upgraded with a feature code to trial, paid or subscription.

## Time required

The lab flow will take approximately 1 hour

## Getting started

Begin by creating a local clone of this repository on your workstation with either git clone:

```text
git clone https://github.com/timroster/guestbook-openshift.git
```

Or by downloading a zip file of the repository and unpacking locally.

## Steps to install Minishift, configure Operator Framework support, create the IBM Cloud operator and deploy the guestbook application

* [Set up Minishift on your system](workshop/minishift.md)
* [Install Operator Framework and IBM Cloud Operator](workshop/ibm-operator.md)
* [Create an instance of the Watson Tone Analyzer service on IBM Cloud](workshop/create-tone.md)
* [Deploy the enhanced guestbook application](workshop/deploy-guestbook.md)

## Summary and next steps

Visit the IBM Developer [OpenShift on IBM Cloud collection](https://developer.ibm.com/collections/openshift-on-ibm/) to learn more about using OpenShift on IBM Cloud. For additional step-by-step learning activities related to OpenShift check out [learn.openshift.com](https://learn.openshift.com)
