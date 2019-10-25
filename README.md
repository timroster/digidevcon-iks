# Learning Kubernetes with the guestbook application

The guestbook example shows how to build a simple multi-tier web application using Kubernetes and Docker. The application consists of a web front end, Redis master for storage, and replicated set of Redis slaves, all for which we will create Kubernetes deployments, pods, and services.

There are two versions of this application. Version 1 (in the `v1` directory) is the the basic guestbook application in go with deployment files for you to exampine at your convenience. Version 2 (in the `v2` directory) extends the guestbook example with additional functionality to call the IBM Watson Tone Analyzer service.

The `v2` directory contains the application version that will be the focus for the digital developer conference hands-on lab.

## Prerequisites

* An [IBM Cloud account](http://ibm.biz/Bdz3ru) that is either upgraded with a feature code to trial, paid or subscription.

## Time required

The lab flow will take approximately 1 hour

## Getting started

Start off with the lab by going to [the first exercise](workshop/exercise-0/README.md).



## Summary and next steps (WIP)

Visit the IBM Developer [OpenShift on IBM Cloud collection](https://developer.ibm.com/collections/openshift-on-ibm/) to learn more about using OpenShift on IBM Cloud. For additional step-by-step learning activities related to OpenShift check out [learn.openshift.com](https://learn.openshift.com)
