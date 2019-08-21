# Kubernetes Application Navigator

The Kubernetes Application Navigator is a tool that extends the Kubernetes console to provide display, inspection, understanding, and navigation through the deployed resources that comprise an application.

The Kubernetes Application Navigator uses the [Application Custom Resource Definition (CRD)](https://github.com/kubernetes-sigs/application/blob/master/config/crds/app_v1beta1_application.yaml) from the [Kubernetes Application SIG](https://github.com/kubernetes-sigs/application) as the basis for describing applications.

# Install

To install Kubernetes Application Navigator, perform these actions: 

1. git clone https://github.com/kappnav/operator.git
1. kubectl create namespace kappnav
1. kubectl create -f operator/kappnav.yaml -n kappnav 

# Install Samples

1. git clone https://github.com/kappnav/samples.git
1. kubectl create -f samples/stocktrader -n default 

# Uninstall

To uninstall Kubernetes Application Navigator, perform these actions: 

1. kubectl delete -f operator/kappnav-delete-CR.yaml -n kappnav --now
1. kubectl delete -f operator/kappnav-delete.yaml -n kappnav
1. kubectl delete namespace kappnav 

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
