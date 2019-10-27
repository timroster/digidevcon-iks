# Installing the Operator Framework and IBM Operator

The Operator Framework provides support for Kubernetes-native extensions to manage custom resource types through operators. Many operators are available through [operatorhub.io](https://operatorhub.io/), including the IBM Cloud operator. The IBM Cloud operator simplifies the creation of IBM Cloud services and resouces and binding credentials from these resources into a Kubernetes cluster. The instructions in this guide are adapted from the IBM Developer tutorial [Simplify the lifecycle management process for your Kubernetes apps with operators](https://developer.ibm.com/tutorials/simplify-lifecycle-management-kubernetes-openshift-ibm-cloud-operator/).

## Installing the Operator Framework

1. Begin by applying the Operator Lifecycle Manager (OLM) release to your cluster:

    ```text
    kubectl apply -f https://github.com/operator-framework/operator-lifecycle-manager/releases/download/0.10.0/crds.yaml
    kubectl apply -f https://github.com/operator-framework/operator-lifecycle-manager/releases/download/0.10.0/olm.yaml
    ```

    Before continuing, check to ensure that the pods in the `olm` namespace have started. This can take a few minutes for the container images to be pulled and the pods to start.

    ```text
    kubectl get pods --namespace olm
    NAME                                READY     STATUS    RESTARTS   AGE
    catalog-operator-6bb8ffd7c5-4jwwz   1/1       Running   0          2m
    olm-operator-78ff5d69cf-2ssbs       1/1       Running   0          2m
    olm-operators-ts4h4                 1/1       Running   0          1m
    operatorhubio-catalog-q2kwj         1/1       Running   0          1m
    packageserver-567b8d96b7-d75vp      1/1       Running   0          11s
    packageserver-567b8d96b7-hlx8g      0/1       Running   0          11s
    ```

1. Install the Marketplace Operator (allows for adding operators from the operator marketplace) perform these steps from a folder/directory outside of the `guestbook-openshift` repository folder:

    ```text
    git clone https://github.com/operator-framework/operator-marketplace.git
    kubectl apply -f operator-marketplace/deploy/upstream/
    ```

1. Configure a namespace for the marketplace operators

    ```text
    kubectl apply -f - <<END
    apiVersion: operators.coreos.com/v1alpha2
    kind: OperatorGroup
    metadata:
      name: marketplace-operators
      namespace: marketplace
    END
    ```

    At this point, you should be able to easily install any operator from the [operatorhub.io](https://operatorhub.io/) marketplace.

## Installing the IBM Cloud operator

1. With the OLM framework and marketplace support installed, it's time to install the IBM Cloud operator. This operator will use an IBM Cloud API key to manage resources within the cluster. Begin to configure the IBM Cloud operator by logging in to the IBM Cloud account using the IBM Cloud CLI. Start by logging in to IBM Cloud

    ```text
    ibmcloud login
    ```

1. In order to be able to provision Cloud Foundry services, choose the correct Org and Space for the IBM Cloud account (this will choose a default Org and Space if there is only one available in the account. Otherwise it will prompt )

    ```text
    ibmcloud target --cf
    ```

    Check the outpuit from the `ibmcloud target` command. If there is no resource group set, resulting in a message including:

    `Resource group:    No resource group targeted, use 'ibmcloud target -g RESOURCE_GROUP'`

    then set the target resource group to the `Default` using:

    ```text
    ibmcloud target -g Default
    ```

1. Use the script from the IBM Cloud operator repository to create the API key and add it as a secret to the `default` namespace in the kubernetes cluster:

    ```text
    curl -sL https://raw.githubusercontent.com/IBM/cloud-operators/master/hack/config-operator.sh | bash
    ```

1. The operator marketplace catalog provides a URL for the resources to install for each operator. Install the IBM Cloud Operator with the following command:

    ```text
    kubectl create -f https://operatorhub.io/install/ibmcloud-operator.yaml
    ```

    Check that the pod for the IBM Cloud operator is running:

    ```bash
    $ kubectl get pods --namespace operators
    NAME                                 READY     STATUS    RESTARTS   AGE
    ibmcloud-operator-7dd98d9754-lphll   1/1       Running   0          1m
    ```

## Understanding Operators

The [Operator Pattern](https://kubernetes.io/docs/concepts/extend-kubernetes/operator/) is an emerging approach to extend through automation the expertise of human operators into the cluster environment. Operators are intended to support applications and management of other resources in and related to kubernetes clusters starting at installation, but continuing to day 2 operations of monitoring, backup, fault recovery and of course updates.

![Operator Pattern](../.gitbook/assets/operator-pattern.png)

Operators are implemented by custom code that is a client of the Kubernetes API that implements a controller for a [**Custom Resource**](https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/). In addition to the IBM Cloud Operator, there are many operators available that can manage resources within your cluster from the [Operator Hub](https://operatorhub.io). Operators are available to install many databases, monitoring tools, application development frameworks, application runtimes and more.

Your cluster now has the IBM Cloud operator installed. This operator is able to configure two kinds in the cluster, a **Service** and a **Binding**. The **Service** defines a specific IBM Cloud service instance type to create, and the **Binding** specifies a named binding of a service instance to a secret in the cluster. For more details about the IBM Cloud operator see the [project repository](https://github.com/IBM/cloud-operators)

Continue by using the IBM Cloud operator to [Create a Tone Analyzer service on IBM Cloud](../exercise-4/README.md)
