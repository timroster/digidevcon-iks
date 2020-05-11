# Deploy an application on Kubernetes

In this part of the lab we will deploy an application called `guestbook` that has already been built and uploaded to DockerHub under the name`ibmcom/guestbook:v1`. In Kubernetes, creating an application means deploying a set of **pods** that run containers. In this lab, you will begin with the most simple scenario of creating a **deployment** with a single **pod** using the `kubectl` cli.

## Create the application and service

1. Create the `guestbook` application deployment:

   ```text
   kubectl create deployment guestbook --image=ibmcom/guestbook:v1
   ```

   This action will take a bit of time. To check the status of the running application, you can use:

   ```text
   kubectl get pods
   ```

   You should see output similar to the following:

   ```console
   $ kubectl get pods
   NAME                          READY     STATUS              RESTARTS   AGE
   guestbook-59bd679fdc-bxdg7    0/1       ContainerCreating   0          1m
   ```

   Eventually, the status should show up as `Running`.

   ```console
   $ kubectl get pods
   NAME                          READY     STATUS              RESTARTS   AGE
   guestbook-59bd679fdc-bxdg7    1/1       Running             0          1m
   ```

   The end result of the run command is not just the pod containing our application containers, but a **Deployment** resource that manages the lifecycle of those pods.

1. Once the status reads `Running`, we need to expose that deployment as a **Service** so that it can be accessed. By specifying a service type of `NodePort`, the service will also be mapped to a high-numbered port on each cluster node. The `guestbook` application listens on port 3000, so this is also specified in the command.  Run:

    ```text
    kubectl expose deployment guestbook --type="NodePort" --port=3000
    ```

   ```console
   $ kubectl expose deployment guestbook --type="NodePort" --port=3000
   service "guestbook" exposed
   ```

1. To find the port used on that worker node, examine your new service:

    ```text
    kubectl get service guestbook
    ```

   ```console
   $ kubectl get service guestbook
   NAME        TYPE       CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
   guestbook   NodePort   172.21.12.235   <none>        3000:30805/TCP   1m
   ```

   The output shows that the `<nodeport>` is `30805`. The service will take incoming connections to the high numbered port, `30805` and forward to port `3000` to the container inside the pod. For a service of type NodePort, a port in the range 30000-32767 is automatically chosen, and could be different for you.

1. `guestbook` is now running on your cluster, and exposed to the internet. We need to find out where it is accessible. The worker nodes running in the container service get external IP addresses. Run `$ ibmcloud cs workers <name-of-cluster>`, and note the public IP listed on the `<public-IP>` line.

    ```text
    ibmcloud ks workers -c mycluster
    ```

    ```console
    $ ibmcloud ks workers -c mycluster
    OK
    ID                                                 Public IP        Private IP     Flavor   State    Status   Zone    Version  
    kube-hou02-pa1e3ee39f549640aebea69a444f51fe55-w1   184.172.252.167  10.76.194.30   free     normal   Ready    hou02   1.14.7_1535
    ```

    We can see that our `<public-IP>` is `184.172.252.167`.

1. Now that you have both the address and the port, you can now access the application in the web browser at `<public-IP>:<nodeport>`. In the example case this is `184.172.252.167:30805`. Enter in a browser tab your IP address and NodePort for your deployment. Try out the guestbook by putting in a few entries. Keep this browser tab handy as you can use it in the next exercise as well.

Congratulations, you've now deployed an application to Kubernetes!

## Understanding what happened

At its core, you can think of Kubernetes as being a highly-available database and a collection of watchers and controllers. Kubernetes objects and their required metadata, such as a name and their desired state, are stored in this database and the watchers and controllers act to ensure that the configuration of actual resources in the cluster matches the state stored in the database.

![Kubernetes conceptual diagram](../.gitbook/assets/kubernetes-concepts.png)

Included in Kubernetes are a number of basic objects necessary for supporting applications as well as abstractions to simplify the configuration and management of applications. The most common basic object is a [**pod**](https://kubernetes.io/docs/concepts/workloads/pods/pod-overview/) which encapsulates one or more containers along with storage resources, a unique network address and configuration options. The **pod** reflects the smallest unit of deployment. Although **pods** are technically transient, they will usually run until something destroys them, either a human operator or a controller. A [**Deployment**](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/) is an abstraction that you can use to create a deployment of an application. It provides support for horizontally scaling pods, updating the container image used by the pods and also rollbacks.

To create your application, you used the `kubectl` command to create a **deployment** object and provided a name for the deployment, "guestbook", and also the container image to use. These options were combined with defaults for the object to create the desired state that was stored in the database. Reconciliation of the desired state resulted in a single pod being started in the cluster. Then, you used the `kubectl expose` command to make the **deployment** resource accessible both inside and outside of the cluster. This command creates a [**Service**](https://kubernetes.io/docs/concepts/services-networking/service/) for a number of different resource types (deployment, replica set, replication controler, pod) to allow access to a network port on the resource.

You can use this deployment in the
[next lab of this course](../exercise-2/README.md)
