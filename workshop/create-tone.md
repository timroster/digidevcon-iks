# Creating an instance of Tone Analyzer

For an application running within a Kubernetes cluster to be able to access an IBM Cloud service, the service needs to be created and the credentials to access the service must be added to the cluster so that they can be read by deployed applications. The Kubernetes cluster running the application accessing the service instance can be anywhere, including running in your locally installed Minishift cluster.

You should perform all of these steps using a user with a standard role in Minishift.

Begin by logging out as the `admin` user and then log in to the cli using the `developer` user.

```bash
$ oc logout
Logged "admin" out on "https://192.168.64.7:8443"
$ oc login -u developer -p anything
Login successful.

You have one project on this server: "myproject"

Using project "myproject"
```

## Create the service instance and bind to the cluster

Change into the `guestbook-operator` folder and apply the `tone.yaml` file. This file defines a **Service** and **Binding** resource:

```bash
$ cd guestbook-openshift/
$ oc apply -f tone.yaml
service.ibmcloud.ibm.com/mytone created
binding.ibmcloud.ibm.com/binding-tone created
```

Check that there is a secret for the Tone Analyzer service instance added to the current project:

```bash
$ oc get secret binding-tone
NAME           TYPE      DATA      AGE
binding-tone   Opaque    6         40s
```

With the credentials added to the current project, now it is time to deploy the enhanced guestbook application that uses the analyzer microservice.

## Cleaning up and lifecycle management with the IBM Cloud operator

The IBM Cloud operator will monitor the IBM Cloud account service instances. If something happens to the service instance, for example if it is deleted, the operator will create a new service instance and update the credentials stored in the binding secret. When you are finished with the activities in this guide, you can remove the secret and the deployed service instance by just deleting the resources defined by `tone.yaml`

## Continue with deploying the guestbook application

Continue the exercise by [deploying the guestbook application](deploy-guestbook.md)
