# Kubernetes Application Navigator

An application consists of one (or usually more) custom-written program components together with the supporting infrastructure, application middleware, middleware services (such as databases, queueing software, integration software and other aspects) and other components that make up a complete solution.

The Kubernetes Application Navigator is a tool that extends the Kubernetes console to provide display, inspection, understanding, and navigation of the deployed resources that comprise an application. This can include both Kubernetes-based components, and through custom resource definitions, legacy components as well. 

The Kubernetes Application Navigator uses the [Application Custom Resource Definition (CRD)](https://github.com/kubernetes-sigs/application/blob/master/config/crds/app_v1beta1_application.yaml) from the [Kubernetes Application SIG](https://github.com/kubernetes-sigs/application) as the basis for describing applications.

# Install Pre-reqs

To install, you need to pre-install the following:

1. A Kubernetes cluster.  The kAppNav project has been verified on: 
   1. [Minishift](https://docs.okd.io/latest/minishift/getting-started/installing.html)
   1. [OKD](https://www.okd.io/) 
   1. [Minikube]()
1. [kubectl CLI](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

Avoid problems:  ensure your target Kubernetes is running and accessible to kubectl.  E.g. 'kubectl get nodes' should promptly return a response. 

# Install

To install Kubernetes Application Navigator (for minikube, see below), perform these actions: 

1. git clone https://github.com/kappnav/operator.git
1. kubectl create namespace kappnav
1. kubectl create -f operator/kappnav.yaml -n kappnav 

# Install Samples

1. git clone https://github.com/kappnav/samples.git
1. kubectl create namespace stocktrader
1. kubectl create -f samples/stocktrader -n stocktrader

# Uninstall

To uninstall Kubernetes Application Navigator, perform these actions: 

1. kubectl delete -f operator/kappnav-delete-CR.yaml -n kappnav --now
1. kubectl delete -f operator/kappnav-delete.yaml -n kappnav
1. kubectl delete namespace kappnav 

# Install on Minikube

1. git clone https://github.com/kappnav/operator.git
1. kubectl create namespace kappnav
1. cat operator/kappnav.yaml | sed "s|kubeEnv: okd|kubeEnv: minikube|" | kubectl create -f - -n kappnav

Launch kAppNav UI with this command:

```
minikube service kappnav-ui-service -n kappnav --format "http://{{.IP}}:{{.Port}}/kappnav-ui"
```

# Install to user defined namespace

1. git clone https://github.com/kappnav/operator.git
1. kubectl create namespace my-namespace
1. cat operator/kappnav.yaml | sed "s|namespace: kappnav|namespace: my-namespace|" | kubectl create -f - -n my-namespace

# Uninstall from user defined namespace

1. kubectl delete -f operator/kappnav-delete-CR.yaml -n my-namespace --now
1. cat operator/kappnav-delete.yaml | sed "s|namespace: kappnav|namespace: my-namespace|" | kubectl delete -f - -n my-namespace
1. kubectl delete namespace my-namespace 

# Install Validation

After installing Kubernetes Application Navigator and the stocktrader sample, access the Kubernetes Application Navigator UI by finding and opening the ui route in the Openshift console: 

![image](https://github.com/kappnav/README/blob/master/images/routes.png)

The Kubernetes Application Navigator shows your installed applications:

![image](https://github.com/kappnav/README/blob/master/images/applications.png)

Click on stocktrader application name to view the stock trader application components: 

![image](https://github.com/kappnav/README/blob/master/images/components.png)
