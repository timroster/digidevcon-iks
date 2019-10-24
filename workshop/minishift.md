# Minishift setup

[Minishift](https://docs.okd.io/latest/minishift/index.html) provides a local development-ready version of OpenShift by creating a VM on your workstation. To be able to use Minishift, you will need a hypervisor platform installed and enabled. In addition to the VM, Minishift includes a command-line program `minishift` to manage the OpenShift cluster. By default, the `minishift start` command will configure a small VM, suitable for basic experimentation. This configuration is sufficient for all of the activities in this guide.

## Prerequisites

The default VM size is 2 virtual CPU and 4 GB of memory. To be able to efficiently run Minishift in the default configuration, you will need a MacOS X, Windows 10, or Linux PC a recent x86 multicore (4+) CPU and 8 GB of memory. This system will require connectivity to the Internet to download the Minishift VM contents.

### Hypervisor setup

You will need to configure the hypervisor on your workstation. Although VirtualBox is supported by Minishift, using the native platform hypervisor, Windows 10 hyper-V, MacOS xhyve or hyperkit, Linux KMM will probably give better performance. **Note** recommendations here (hyperkit for MacOS, using Static IP on Windows 10) are based on workstations that have already installed Docker runtimes. Choose the set of instructions to follow based on your platform:

* MacOS - use the instructions for [hyperkit Driver](https://docs.okd.io/latest/minishift/getting-started/setting-up-virtualization-environment.html#setting-up-hyperkit-driver)

* Windows 10 - use the instructions for [hyper-V Driver](https://docs.okd.io/latest/minishift/getting-started/setting-up-virtualization-environment.html#for-windows) , follow steps 1 & 2, but instead of steps 3, 4, 5 "Add an External Virtual Switch...", configure [A static IP address on Hyper-V](https://docs.okd.io/latest/minishift/using/static-ip.html#static-ip-hyperv). There are instructions for using the existing Docker virtual network if you have Docker for Windows installed. Open a PowerShell with Administrator access and run only the first command to add NAT service to the DockerNAT network. If you do not have Docker installed, follow the steps to create a new Virtual Switch with NAT. If you have a Windows 10 version that does not support hyper-V, use VirtualBox instead.

* Linux - use the instructions for [KVM Driver](https://docs.okd.io/latest/minishift/getting-started/setting-up-virtualization-environment.html#for-linux)

* VirtualBox - use the instructions for [VirtualBox](https://docs.okd.io/latest/minishift/getting-started/setting-up-virtualization-environment.html#setting-up-virtualbox-driver)

### Installing the minishift application

Follow the instructions to [install minishift](https://docs.okd.io/latest/minishift/getting-started/installing.html) for your platform either manually or automatically with homebrew (MacOS). If installing manually, be sure to add the `minishift` application to your PATH.

## Creating your Minishift cluster

To create a default size cluster, you can generally use the `minishift start` command. Note that if you are using a hypervisor other than the default, you will need to add a parameter on startup to specify the correct hypervisor driver. These instructions recommend hyperkit for MacOS and using a static IP address for Windows 10 with hyper-V. Use these commands to start your cluster:

* MacOS: `minishift start --vm-driver=hyperkit`

* Windows 10 (hyper V, static IP on Docker network, adjust according if using a different local NAT network):

    ```text
    minishift config set hyperv-virtual-switch "DockerNAT"
    minishift start  --network-ipaddress 10.0.75.128  --network-gateway 10.0.75.1 --network-nameserver 8.8.8.8
    ```

* Linux: `minishift start`

* VirtualBox: `minishift start --vm-driver virtualbox`

Cluster creation takes 5-10 minutes depending on the resources of the workstation, and internet speed to download the Minishift VM contents. If you experience issues with network connections for ssh commands or very slow installation with an eventual timeout on MacOS using hyperkit, restart the system and try again.

## Accessing Minishift

When the minishift installation is complete, it will print out a URL for accessing the local cluster (example):

```text
Server Information ...
OpenShift server started.

The server is accessible via web console at:
    https://192.168.64.7:8443/console

You are logged in as:
    User:     developer
    Password: <any value>

To login as administrator:
    oc login -u system:admin
```

Note that Minishift provides a simplified OpenShift installation, suitable for local development use. For example, the Minishift cluster is configured by with a single user `developer` that is a member of the project `myproject` only. This user can add other projects, but is not able to perform tasks on the cluster as a system administrator.

Use the provided link and sign in as `developer` to the Web UI. This UI is also slightly simplified from a standard OpenShift/OKD installation and focuses on tasks for local development.

## Configure the oc cli

Minishift includes a cli called **oc** that is configured as part of the cluster installation. Use the `minishift oc-env` command to get information on how to configure access to the installed cli. Output from the command will vary based on the platform being used. For example with MacOS, the output is:

```text
minishift oc-env
export PATH="/Users/username/.minishift/cache/oc/v3.11.0/darwin:$PATH"
# Run this command to configure your shell:
# eval $(minishift oc-env)
```

## Configuring Minishift Add-on for cluster admin login

Minishift includes a set of **Add-ons** that extend the vanilla configuration of the cluster to support more general use cases with OpenShift. There are a number of [default add-ons](https://docs.okd.io/latest/minishift/using/addons.html#default-addons) that are included with Minishift and there are also [community provided](https://github.com/minishift/minishift-addons/tree/master/add-ons) add-ons.

The exercises in this guide require some steps to performed as a cluster administrator. Rather than make the standard `developer` user a cluster-admin, enable the `admin-user` add-on which will create a user called `admin` on the cluster which is configured with the cluster-admin role. Like the `developer` user, you can use any password to authenticate as the `admin` user once the add-on is applied:

```bash
$ minishift addons enable admin-user
Add-on 'admin-user' enabled
$ minishift addons apply admin-user
-- Applying addon 'admin-user':..
```

After enabling the addon, check that access as a cluster-admin is working (should see the full set of projects after sign-on)

```bash
$ oc login -u admin
Authentication required for https://192.168.64.7:8443 (openshift)
Username: admin
Password:
Login successful.

You have access to the following projects and can switch between them with 'oc project <projectname>':

    default
    kube-dns
    kube-proxy
    kube-public
    kube-system
  * myproject
    openshift
    openshift-apiserver
    openshift-controller-manager
    openshift-core-operators
    openshift-infra
    openshift-node
    openshift-service-cert-signer
    openshift-web-console

Using project "myproject".
$ oc whoami
admin
```

## Managing the cluster with the minishift command

Your cluster is ready to go for the exercises in this guide, but there are some general commands for managing the cluster. Here are commands you will use often:

`minishift start` - this command will start up the cluster if it has been stopped for any reason. If there is no cluster on the workstation, it will create a new cluster.

`minishift stop` - this command will stop the running cluster by halting the VM in the hypervisor. It does not delete the VM. Run this command before attempting to remove the VM

`minishift delete` - this command will delete the VM. It will not remove the local configuration. If you want to start over from scratch after removing the vm, remove the minishift configuation directory with: `rm -rf ~/.minishift`

`minishift config` - this command option will allow you to manage a specific minishift profile or all profiles for the user allowing the customization of the cluster configuration. It has subcommands for get, set and view of the config. Common adjustments to the configuration are adding additional CPU, memory, or storage from the default properties. Use the [config command reference](https://docs.okd.io/latest/minishift/command-ref/minishift_config.html) page for details.

`minishift profile` - allows for the creation of a named profile including a VM and customized configuration

For more information, visit the Minishift documetation [basic usage](https://docs.okd.io/latest/minishift/using/basic-usage.html) pages.

## Continue with setup of the IBM Cloud Operator

Continue the exercise by [setting up the operator framework and IBM Cloud operator](ibm-operator.md)
