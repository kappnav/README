# README
Start Here 

# Install

To install Kubernetes Application Navigator, perform these actions: 

1. git clone https://github.com/kappnav/operator.git
1. kubectl create namespace kappnav
1. kubectl create -f operator/kappnav.yaml -n kappnav 

# Install Samples

1. git clone https://github.com/kappnav/samples.git
1. kubectl create -f samples/stocktrader

# Uninstall

To install Kubernetes Application Navigator, perform these actions: 

1. kubectl delete -f operator/kappnav-delete-CR.yaml --now
1. kubectl delete -f operator/kappnav-delete.yaml 
1. kubectl delete namespace kappnav 
