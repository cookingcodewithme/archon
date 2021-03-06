Kubernetes cluster with CentOS, jsonnet and okdc
================================================

In this guide, we'll demonstrate how to bootstrap a Kubernetes cluster with
CentOS using [okdc]. All resources are defined with [jsonnet].

Step 1
------

First, please follow the [installation guide] to install `archon-controller`
locally or into your Kubernetes cluster.


Step 2
------

Create a new namespace for this cluster:

```
kubectl create namespace ucloud-centos
```

Step 3
------

For demo purposes, you could use the default password `archon` to initialize machines.
To update the password, you could fill it in the `instancePassword` field of `config.libsonnet`. 
Then create the passwd resource. This is used to ssh into your machines and start 
bootstrapping process.

```
jsonnet -J PATH_TO_KSONNET_LIB -J PATH_TO_ARCHON password.jsonnet | kubectl create -f - --namespace=ucloud-centos
```

Step 4
------

(Optional) Create a vpc network in UCloud dashboard. Fill in the blanks in `k8s-net.yaml`.
Defaults will be used if this step is skipped.


Step 5
------

Create the network resource. Notice this needs to be created regardless of you have created
a new vpc or not. You could customize network settings in `config.libsonnet`.

```
jsonnet -J PATH_TO_KSONNET_LIB -J PATH_TO_ARCHON k8s-net.jsonnet | kubectl create -f - --namespace=ucloud-centos
```

Step 6
------

Create the user resource. Fill in your ssh key in `config.libsonnet`.

```
jsonnet -J PATH_TO_KSONNET_LIB -J PATH_TO_ARCHON k8s-user.jsonnet | kubectl create -f - --namespace=ucloud-centos
```
Step 7
------

Modify `config.libsonnet`. Replace `YOUR_KUBEADM_TOKEN` with the token generated by `generate_token.py`
Create the instance group and let the `archon-controller` create the instance for you.
This will also install flannel to your cluster.

```
jsonnet -J PATH_TO_KSONNET_LIB -J PATH_TO_ARCHON k8s-master.jsonnet | kubectl create -f - --namespace=ucloud-centos
```

Step 8
-------

Update `config.libsonnet` with the master ip and create
it.

```
jsonnet -J PATH_TO_KSONNET_LIB -J PATH_TO_ARCHON k8s-node.jsonnet | kubectl create -f - --namespace=ucloud-centos
```


[installation guide]: https://github.com/kubeup/archon/blob/master/docs/installation_ucloud.md
[okdc]: https://github.com/kubeup/okdc
[jsonnet]: http://jsonnet.org
