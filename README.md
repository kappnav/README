# Documentation [Here](https://github.com/kappnav/README/blob/master/doc.md)

# What is Kubernetes Application Navigator?

The Kubernetes Application Navigator is a tool that extends the Kubernetes console to provide visualization, inspection, and navigation of the deployed resources that comprise an application.  It provides high level status and customizable in-context day 2 operations for your applications. 

# What is an Application?

An application consists of custom-written program components that combine with the supporting infrastructure, application middleware, middleware services, and other components to define a complete solution.

The Kubernetes Application Navigator uses the [Application Custom Resource Definition (CRD)](https://raw.githubusercontent.com/kubernetes-sigs/application/master/config/crd/bases/app.k8s.io_applications.yaml) from the [Kubernetes Application SIG](https://github.com/kubernetes-sigs/application) as the basis for describing applications.

# Installing Kubernetes Application Navigator

## Prerequisites

1. Install a Kubernetes cluster. The Kubernetes Application Navigator project is verified on the following clusters:
   * [Minishift](https://docs.okd.io/latest/minishift/getting-started/installing.html)
   * [OKD](https://www.okd.io/)
   * [Minikube](https://kubernetes.io/docs/tasks/tools/install-minikube/)
1. Install the [Kubernetes command-line tool](https://kubernetes.io/docs/tasks/tools/install-kubectl/).

> **Avoid trouble**: Ensure that your target Kubernetes cluster is running and accessible to the Kubernetes command-line tool (kubectl). Run the `kubectl get nodes` command to return a response that indicates that Kubernetes is running.

You can install Kuberenetes Application Navigator by using one of the following methods: 

1. [OKD or minishift into kappnav namespace](https://github.com/kappnav/README#install-on-okd-or-minishift-into-kappnav-namespace)
1. [OCP v4.2](https://github.com/kappnav/README#install-on-ocp-42)
1. [minikube](https://github.com/kappnav/README#install-kubernetes-application-navigator-on-minikube) 
1. [OKD or minishift into user-defined namespace](https://github.com/kappnav/README#install-on-okd-or-minishift-into-user-defined-namespace)

## Install Kubernetes Application Navigator on OKD or minishift into a kappnav namespace

1. Create the kAppNav namespace with the following command: `kubectl create namespace kappnav`

   ```
   # kubectl create namespace kappnav
   namespace/kappnav created
   ```

1. Deploy the kAppNav operator with the following command: `curl -L https://raw.githubusercontent.com/kappnav/operator/master/releases/latest/kappnav.yaml | kubectl create -f - -n kappnav`

   ```
   # curl -L https://raw.githubusercontent.com/kappnav/operator/master/releases/0.1.2/kappnav.yaml | kubectl -f - -n kappnav
   customresourcedefinition.apiextensions.k8s.io/kappnavs.charts.helm.k8s.io created
   serviceaccount/helm-operator created
   clusterrole.rbac.authorization.k8s.io/helm-operator created
   clusterrolebinding.rbac.authorization.k8s.io/helm-operator created
   deployment.apps/helm-operator created
   kappnav.charts.helm.k8s.io/instance created
   ```
1. Use the following command to ensure that all the pods are running: `kubectl get pods -n kappnav`
   * It might take a couple of minutes for all of the pods to be created

   ```
   # kubectl get pods -n kappnav
   NAME                                  READY   STATUS    RESTARTS   AGE
   kappnav-controller-67c66df5f4-5h5pl   2/2     Running   0          6h32m
   kappnav-operator-c5c8fddfc-74dl8      1/1     Running   0          6h33m
   kappnav-ui-96897bddd-z466s            3/3     Running   0          6h32m
   ```
   
1. Use the following command to ensure that the routes are created: `kubectl get routes -n kappnav`
   1. Find the kappnav-ui-service route.
   ```
   # kubectl get routes -n kappnav
   NAME                  HOST/PORT                                           PATH          SERVICES             PORT      TERMINATION          WILDCARD
   kappnav-ui-service    kappnav-ui-service-kappnav.apps.myhost.com                        kappnav-ui-service   <all>     reencrypt            None
   ```
   2. Access the kAppNav UI with the following URL: `http://kappnav-ui-service-kappnav.apps.myhost.com/kappnav-ui`

## Install [Stock-Trader Sample](https://github.com/kappnav/samples/blob/master/stocktrader/README.md)

## Installation validation

After you install Kubernetes Application Navigator and the stock-trader sample, you can access the Kubernetes Application Navigator UI by finding and opening the UI route in the OpenShift console:

![image](/images/routes.png?raw=true)

The Kubernetes Application Navigator shows your installed applications:

![image](/images/applications.png?raw=true)

Click the **stock-trader** application name to view the **stock-trader** application components:

![image](/images/components.png?raw=true)

The Kubernetes Application Navigator icon appears in the OKD Service Catalog:

![image](/images/kappnav_in_okd_console.png?raw=true)

To add existing applications, or create a new application, follow the instructions for [How to Create Applications](how-to-create-applications.md).


## Uninstall Kubernetes Application Navigator

1. Delete the kAppNav components with the following command: `curl -L https://raw.githubusercontent.com/kappnav/operator/master/releases/latest/kappnav-delete-CR.yaml | kubectl delete -f - -n kappnav --now`

   ```
   # curl -L https://raw.githubusercontent.com/kappnav/operator/master/releases/latest/kappnav-delete-CR.yaml | kubectl create -f - -n kappnav --now
   kappnav.charts.helm.k8s.io "instance" deleted
   ```
1. Delete the operator components with the following command: `curl -L https://raw.githubusercontent.com/kappnav/operator/master/releases/latest/kappnav-delete.yaml | kubectl delete -f - -n kappnav`

   ```
   # curl -L https://raw.githubusercontent.com/kappnav/operator/master/releases/latest/kappnav-delete.yaml | kubectl delete -f - -n kappnav
   deployment.apps "helm-operator" deleted
   clusterrolebinding.rbac.authorization.k8s.io "helm-operator" deleted
   clusterrole.rbac.authorization.k8s.io "helm-operator" deleted
   serviceaccount "helm-operator" deleted
   customresourcedefinition.apiextensions.k8s.io "kappnavs.charts.helm.k8s.io" deleted
   ```
1. Delete the Kubernetes Application Navigator namespace with the following command: `kubectl delete namespace kappnav`

   ```
   # kubectl delete namespace kappnav
   namespace "kappnav" deleted
   ```


## Install Kubernetes Application Navigator on OCP 4.2
   Use the following commands to install Kubernetes Application Navigator on OCP 4.2:
   1. `kubectl create namespace kappnav`
   1. `curl -L https://raw.githubusercontent.com/kappnav/operator/master/releases/latest/kappnav.yaml | sed "s|kubeEnv: okd|kubeEnv: ocp|" | kubectl create -f - -n kappnav`

## Install Kubernetes Application Navigator on Minikube

**NOTE** kAppNav presently works only on Kubernetes versions < 1.16 !  Also, at least 4GB memory is recommended.  We have verified kAppNav on minikube, using the following startup:

```
minikube start --kubernetes-version=1.14.9 --vm-driver=virtualbox --memory=4000mb
```

   1. Use the following commands to install Kubernetes Application Navigator on Minikube:
      1. `kubectl create namespace kappnav`
      2. `curl -L https://raw.githubusercontent.com/kappnav/operator/master/releases/latest/kappnav.yaml | sed "s|kubeEnv: okd|kubeEnv: minikube|" | kubectl create -f - -n kappnav`

   2. Start the Kubernetes Application Navigator UI with the following command:
   
      ```
      minikube service kappnav-ui-service -n kappnav --format "http://{{.IP}}:{{.Port}}/kappnav-ui"
      ```


## Installing Kubernetes Application Navigator on OKD or Minishift into a user-defined namespace
  Use the following commands to install Kubernetes Application Navigator on OKD or minishft into a user-defined namespace
   1. `kubectl create namespace my-namespace`
   2. `curl -L https://raw.githubusercontent.com/kappnav/operator/master/releases/latest/kappnav.yaml | sed "s|namespace: kappnav|namespace: my-namespace|" | kubectl create -f - -n my-namespace`

## Uninstall Kubernetes Application Navigator from a user-defined namespace
 Use the following commands to install Kubernetes Application Navigator from a user-defined namespace: 
   1. `curl -L https://raw.githubusercontent.com/kappnav/operator/master/releases/latest/kappnav-delete-CR.yaml | kubectl delete -f - -n my-namespace --now`
   2. `curl -L https://raw.githubusercontent.com/kappnav/operator/master/releases/latest/kappnav-delete.yaml | sed "s|namespace: kappnav|namespace: my-namespace|" | kubectl delete -f - -n my-namespace`
   3. `kubectl delete namespace my-namespace`
