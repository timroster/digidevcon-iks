# Scale and Update Deployments

In this lab, you'll learn how to update the number of instances in a deployment has and how to roll out an update of your application on Kubernetes with zero downtime. A key requirement of a container orchestration system is to automate the management steps of applications. Scaling up/down and handling application updates with support for rollback are a couple of essential use cases (but not the only ones).

For this lab, you need a running deployment of the `guestbook` application from the previous lab.

## Scale apps with replicas

A *replica* is a copy of a pod that contains a running service. By having multiple replicas of a pod, you can ensure your deployment has the available resources to handle increasing load on your application.

1. `kubectl` provides a `scale` subcommand to change the size of an existing deployment. Let's increase our capacity from a single running instance of `guestbook` up to 10 instances using:

   ```text
   kubectl scale --replicas=10 deployment guestbook
   ```

   ``` console
   $ kubectl scale --replicas=10 deployment guestbook
   deployment "guestbook" scaled
   ```

   If you remember back to the architecture overview in the previous exercise, the `kubectl scale` command updates the desired state in the etcd database in Kubernetes. The watchers and controllers will now try to make reality match the desired state of 10 replicas by starting 9 new pods with the same configuration as the first.

1. To see your changes being rolled out, you can run:

    ```text
   kubectl rollout status deployment guestbook
   ```

   The rollout might occur so quickly that you may only see `deployment "guestbook" successfully rolled out` for the output.

   ```console
   $ kubectl rollout status deployment guestbook
   Waiting for rollout to finish: 1 of 10 updated replicas are available...
   Waiting for rollout to finish: 2 of 10 updated replicas are available...
   Waiting for rollout to finish: 3 of 10 updated replicas are available...
   Waiting for rollout to finish: 4 of 10 updated replicas are available...
   Waiting for rollout to finish: 5 of 10 updated replicas are available...
   Waiting for rollout to finish: 6 of 10 updated replicas are available...
   Waiting for rollout to finish: 7 of 10 updated replicas are available...
   Waiting for rollout to finish: 8 of 10 updated replicas are available...
   Waiting for rollout to finish: 9 of 10 updated replicas are available...
   deployment "guestbook" successfully rolled out
   ```

1. Once the rollout has finished, ensure your pods are running by using:

    ```text
   kubectl get pods
   ```

   You should see output listing 10 replicas of your deployment:

   ```console
   $ kubectl get pods
   NAME                        READY     STATUS    RESTARTS   AGE
   guestbook-562211614-1tqm7   1/1       Running   0          1d
   guestbook-562211614-1zqn4   1/1       Running   0          2m
   guestbook-562211614-5htdz   1/1       Running   0          2m
   guestbook-562211614-6h04h   1/1       Running   0          2m
   guestbook-562211614-ds9hb   1/1       Running   0          2m
   guestbook-562211614-nb5qp   1/1       Running   0          2m
   guestbook-562211614-vtfp2   1/1       Running   0          2m
   guestbook-562211614-vz5qw   1/1       Running   0          2m
   guestbook-562211614-zksw3   1/1       Running   0          2m
   guestbook-562211614-zsp0j   1/1       Running   0          2m
   ```

## Update and roll back apps

Kubernetes allows you to do a rolling upgrade of your application to a new container image. Kubernetes allows you to easily update the running image but also allows you to easily undo a rollout if a problem is discovered during or after deployment.

In the previous lab, we used an image with a `v1` tag. For our upgrade, we'll use the image with the `v2` tag.

To update and roll back:

1. Using `kubectl`, you can now update your deployment to use the `v2` image. `kubectl` allows you to change details about existing resources with the `set` subcommand. We can use it to change the image being used.

    ```text
    kubectl set image deployment/guestbook guestbook=ibmcom/guestbook:v2
    ```

   Note that a pod could have multiple containers, each with its own name. Each image can be changed individually or all at once by referring to the name. In the case of our `guestbook` Deployment, the container name is also `guestbook`. Multiple containers can be updated at the same time. ([More information](https://kubernetes.io/docs/user-guide/kubectl/kubectl_set_image/).)

1. Check the status of the rollout. The rollout might occur so quickly that you may only see `deployment "guestbook" successfully rolled out` for the output.

    ```text
    kubectl rollout status deployment/guestbook
    ```

   ```console
   $ kubectl rollout status deployment/guestbook
   Waiting for rollout to finish: 2 out of 10 new replicas have been updated...
   Waiting for rollout to finish: 3 out of 10 new replicas have been updated...
   Waiting for rollout to finish: 3 out of 10 new replicas have been updated...
   Waiting for rollout to finish: 3 out of 10 new replicas have been updated...
   Waiting for rollout to finish: 4 out of 10 new replicas have been updated...
   Waiting for rollout to finish: 4 out of 10 new replicas have been updated...
   Waiting for rollout to finish: 4 out of 10 new replicas have been updated...
   Waiting for rollout to finish: 4 out of 10 new replicas have been updated...
   Waiting for rollout to finish: 4 out of 10 new replicas have been updated...
   Waiting for rollout to finish: 5 out of 10 new replicas have been updated...
   Waiting for rollout to finish: 5 out of 10 new replicas have been updated...
   Waiting for rollout to finish: 5 out of 10 new replicas have been updated...
   Waiting for rollout to finish: 6 out of 10 new replicas have been updated...
   Waiting for rollout to finish: 6 out of 10 new replicas have been updated...
   Waiting for rollout to finish: 6 out of 10 new replicas have been updated...
   Waiting for rollout to finish: 7 out of 10 new replicas have been updated...
   Waiting for rollout to finish: 7 out of 10 new replicas have been updated...
   Waiting for rollout to finish: 7 out of 10 new replicas have been updated...
   Waiting for rollout to finish: 7 out of 10 new replicas have been updated...
   Waiting for rollout to finish: 8 out of 10 new replicas have been updated...
   Waiting for rollout to finish: 8 out of 10 new replicas have been updated...
   Waiting for rollout to finish: 8 out of 10 new replicas have been updated...
   Waiting for rollout to finish: 8 out of 10 new replicas have been updated...
   Waiting for rollout to finish: 9 out of 10 new replicas have been updated...
   Waiting for rollout to finish: 9 out of 10 new replicas have been updated...
   Waiting for rollout to finish: 9 out of 10 new replicas have been updated...
   Waiting for rollout to finish: 1 old replicas are pending termination...
   Waiting for rollout to finish: 1 old replicas are pending termination...
   Waiting for rollout to finish: 1 old replicas are pending termination...
   Waiting for rollout to finish: 9 of 10 updated replicas are available...
   Waiting for rollout to finish: 9 of 10 updated replicas are available...
   Waiting for rollout to finish: 9 of 10 updated replicas are available...
   deployment "guestbook" successfully rolled out
   ```

1. Test the application as before, by accessing `<public-IP>:<nodeport>` (use the same as the previous lab) in the browser to confirm your new code is active.

   Remember, to get the "nodeport" and "public-ip" use:

   ````text
   kubectl describe service guestbook
   ```

   and (replace *mycluster* below if you used a different name for your cluster):

   ```text
   ibmcloud ks workers -c mycluster
   ```

   To verify that you're running "v2" of guestbook, press down **shift** while clicking on reload to discard the cached content and look at the title of the page, it should now be `Guestbook - v2`

1. If you want to undo your latest rollout, use:

   ```console
   kubectl rollout undo deployment guestbook
   ```

   You can then use `kubectl rollout status deployment/guestbook` to see
   the status.

1. When doing a rollout, you see references to *old* replicas and *new* replicas. The *old* replicas are the original 10 pods deployed when we scaled the application. The *new* replicas come from the newly created pods with the different image. All of these pods are owned by the Deployment. The deployment manages these two sets of pods with a resource called a ReplicaSet. We can see the guestbook ReplicaSets with:

   ```text
   kubectl get replicasets -l app=guestbook
   ```

   ```console
   $ kubectl get replicasets -l app=guestbook
   NAME                   DESIRED   CURRENT   READY     AGE
   guestbook-5f5548d4f    10        10        10        21m
   guestbook-768cc55c78   0         0         0         3h
   ```

Before we continue, let's delete the application so we can learn about a different way to achieve the same results by using resource files instead of providing command line options.

 To remove the deployment, use:

```text
kubectl delete deployment guestbook
```

 To remove the service,use:

 ```text
 kubectl delete service guestbook
 ```

## Deeper dive on configuring resources

 Although it was convenient to create the guestbook deployment using the cli, in practice most applications and other kubernetes objects are created using configuration files in `.yaml` format. For example, the [`guestbook-deployment.yaml`](../../v1/guestbook-deployment.yaml) file is an example configuration file that would deploy the guestbook image with a total of 3 instances. This file shows the key parts needed for each kubenetes object. After the API version and resource type, there is a `metadata` section which specfies the name of the resource and a set of labels. Then, there is a `spec` section which defines the desired state. First, there's the definition of the [**replica set**](https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/) object for the deployment. Within the **replica set**, there is a template for the pod controlled by the set. Within this template, you can find labels applied at the pod level and the `spec` for the container(s) that will be deployed with each pod.

 In the last section of this lab, you will again deploy the guestbook application, along with other objects for a multi-tier application using resource files.

After these are deleted, proceed to the [next lab of this course](../exercise-3/README.md)
