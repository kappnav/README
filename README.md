# Kubernetes Application Navigator

An application consists of one (or usually more) custom-written program components together with the supporting infrastructure, application middleware, middleware services (such as databases, queueing software, integration software and other aspects) and other components that make up a complete solution.

The Kubernetes Application Navigator is a tool that extends the Kubernetes console to provide display, inspection, understanding, and navigation of the deployed resources that comprise an application. This can include both Kubernetes-based components, and through custom resource definitions, legacy components as well. 

The Kubernetes Application Navigator uses the [Application Custom Resource Definition (CRD)](https://github.com/kubernetes-sigs/application/blob/master/config/crds/app_v1beta1_application.yaml) from the [Kubernetes Application SIG](https://github.com/kubernetes-sigs/application) as the basis for describing applications.

# Install Pre-reqs

To install, you need to pre-install the following:

1. A Kubernetes cluster.  The kAppNav project has been verified on: 
   1. [Minishift](https://docs.okd.io/latest/minishift/getting-started/installing.html)
   1. [OKD](https://www.okd.io/) 
   1. [Minikube](https://kubernetes.io/docs/tasks/tools/install-minikube/)
1. [kubectl CLI](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

Avoid problems:  ensure your target Kubernetes is running and accessible to kubectl, e.g. `kubectl get nodes` should promptly return a response. 

# Install

To install Kubernetes Application Navigator (for OCP v4.2, minikube or a user-defined namespace, see sections below), perform these actions:

1. Clone the operator repository: `git clone https://github.com/kappnav/operator.git`
1. Create the kAppNav namespace: `kubectl create namespace kappnav`
   * The namespace will be created:
   ```
   # kubectl create namespace kappnav
   namespace/kappnav created
   ```
1. Deploy the kAppNav operator: `kubectl create -f operator/kappnav.yaml -n kappnav`
   * The operators will be created:
   ```
   # kubectl create -f operator/kappnav.yaml -n kappnav
   customresourcedefinition.apiextensions.k8s.io/kappnavs.charts.helm.k8s.io created
   serviceaccount/helm-operator created
   clusterrole.rbac.authorization.k8s.io/helm-operator created
   clusterrolebinding.rbac.authorization.k8s.io/helm-operator created
   deployment.apps/helm-operator created
   kappnav.charts.helm.k8s.io/instance created
   ```
1. Ensure the pods are running: `kubectl get pods -n kappnav`
   * It may take a couple minutes for all of the pods to be created
   * All pods should be runnning:
   ```
   # kubectl get pods -n kappnav
   NAME                                 READY     STATUS      RESTARTS   AGE
   helm-operator-6bf5fb5b68-hvtbf       1/1       Running     0          2m
   kappnav-controller-c6bdfdf59-mv2wh   2/2       Running     0          1m
   kappnav-f5578677f-6r9js              2/2       Running     0          1m
   kappnav-init-post-49nt8              0/1       Completed   0          1m
   kappnav-init-pre-7fgcb               0/1       Completed   0          1m
   ```
1. Ensure the routes are created: `kubectl get routes -n kappnav`
   * Find the kappnav-ui-service route:
   ```
   # kubectl get routes -n kappnav
   NAME                  HOST/PORT                                           PATH          SERVICES             PORT      TERMINATION          WILDCARD
   kappnav-api-service   kappnav-ui-service-kappnav.apps.myhost.com   /kappnav      kappnav-ui-service   <all>     reencrypt/Redirect   None
   kappnav-ui-service    kappnav-ui-service-kappnav.apps.myhost.com   /kappnav-ui   kappnav-ui-service   <all>     reencrypt/Redirect   None
   ```
   * Access the the kAppNav UI URL, e.g. `http://kappnav-ui-service-kappnav.apps.myhost.com/kappnav-ui`
1. It is recommended to install the sample application.

# Install Sample Application

1. Clone the sample repository: `git clone https://github.com/kappnav/samples.git`
1. Create the stocktrader sample namespace: `kubectl create namespace stocktrader`
   * The namespace will be created:
   ```
   # kubectl create namespace stocktrader
   namespace/stocktrader created
   ```
1. Deploy the stock-trader sample application: `kubectl create -f samples/stocktrader -n stocktrader`
   * The application will be created:
   ```
   # kubectl create -f samples/stocktrader -n stocktrader
   application.app.k8s.io/stock-trader created
   deployment.extensions/loyalty-level created
   service/loyalty-level-service created
   deployment.extensions/notification-twitter created
   service/notification-service created
   deployment.extensions/portfolio created
   service/portfolio-service created
   deployment.extensions/stock-quote created
   service/stock-quote-service created
   deployment.extensions/trader created
   service/trader-service created
   ```
1. Ensure the stock-trader application has started.
   * You can watch the application be deployed in the kAppNav UI. The stock-trader application will appear and the status will go from Unknown, to Problem, then to Normal.
   * You can also check the install progress with `kubectl get pods -n stocktrader`
   * All pods should be runnning:
   ```
   # kubectl get pods -n stocktrader
   NAME                                  READY     STATUS    RESTARTS   AGE
   loyalty-level-5fd5c9b855-rfxss        1/1       Running   0          54s
   notification-twitter-f568cc54-d5xck   1/1       Running   0          54s
   portfolio-8fb7cf89c-zjw58             1/1       Running   0          54s
   stock-quote-5c88f57659-tlcl8          1/1       Running   0          53s
   trader-59c9dcb74-2cv8b                1/1       Running   0          53s
   ```

# Install Validation

After installing Kubernetes Application Navigator and the stock-trader sample, access the Kubernetes Application Navigator UI by finding and opening the UI route in the Openshift console: 

![image](/images/routes.png?raw=true)

The Kubernetes Application Navigator shows your installed applications:

![image](/images/applications.png?raw=true)

Click on stocktrader application name to view the stock trader application components: 

![image](/images/components.png?raw=true)

The Kubernetes Application Navigator icon will apear in the OKD Service Catalog:

![image](/images/kappnav_in_okd_console.png?raw=true)

To add existing applications, or create a new application, follow the instructions for [How to Create Applications](how-to-create-applications.md).

# Uninstall

To uninstall Kubernetes Application Navigator, perform these actions: 

1. Delete the kAppNav components: `kubectl delete -f operator/kappnav-delete-CR.yaml -n kappnav --now`
   * The application will be deleted:
   ```
   # kubectl delete -f operator/kappnav-delete-CR.yaml -n kappnav --now
   kappnav.charts.helm.k8s.io "instance" deleted
   ```
1. Delete the Helm operator components: `kubectl delete -f operator/kappnav-delete.yaml -n kappnav`
   * The Helm operator will be deleted:
   ```
   # kubectl delete -f operator/kappnav-delete.yaml -n kappnav
   deployment.apps "helm-operator" deleted
   clusterrolebinding.rbac.authorization.k8s.io "helm-operator" deleted
   clusterrole.rbac.authorization.k8s.io "helm-operator" deleted
   serviceaccount "helm-operator" deleted
   customresourcedefinition.apiextensions.k8s.io "kappnavs.charts.helm.k8s.io" deleted
   ```
1. Finally, delete the kAppNav namespace: `kubectl delete namespace kappnav`
   * The namespace will be deleted:
   ```
   # kubectl delete namespace kappnav 
   namespace "kappnav" deleted
   ```

# Install on OCP 4.2

1. git clone https://github.com/kappnav/operator.git
1. cat operator/kappnav.yaml | sed "s|kubeEnv: okd|kubeEnv: ocp|" | kubectl create -f - -n kappnav

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
