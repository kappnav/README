# Using actions in Kubernetes Application Navigator

Kubernetes Application Navigator ({k}AppNav) provides a set of default, context-based actions for Kubernetes resources and some runtimes within the managed containers.

These actions are based on resource kind, optionally qualified by a {k}AppNav concept called "subkind". Subkind is used with the Deployment and DeploymentConfig resource kinds, to designate the content type of the images comprising the Deployment, recognizing that the actions supported by Deployment are influenced by the content. For example, a Deployment image implemented in Node.js might support an action to display the Node.js [AppMetrics dashboard](https://github.com/RuntimeTools/appmetrics-dash); or a Deployment image implemented using [Open Liberty](https://openliberty.io/) might support an action to display [mpMetrics-2.0](https://openliberty.io/blog/2018/09/19/get-more-metrics-microprofile20.html) via a [Grafana dashboard](https://developer.ibm.com/tutorials/configure-an-observable-microservice-with-appsody-openshift-open-liberty/#). 

Subkind qualification is optional. For out-of-the-box actions it is used with the Deployment and DeploymentConfig kinds to designate what additional actions the Deployment (or DeploymentConfig) support based on content.  The syntax is: 

```
kind: Deployment
metadata: 
   annotations: 
      kappnav.subkind: Liberty | Nodejs 
```

## Actions by Kind[.Subkind] with out-of-the box Actions

1. [Deployment](https://github.com/kappnav/README/blob/master/actions-deployment.md)
1. [Deployment.Liberty](https://github.com/kappnav/README/blob/master/actions-deployment-liberty.md)
1. [Deployment.Nodejs](https://github.com/kappnav/README/blob/master/actions-deployment-nodejs.md)
1. [DeploymentConfig (Red Hat Openshift only)](https://github.com/kappnav/README/blob/master/actions-deploymentconfig.md)
1. [DeploymentConfig.Nodejs (Red Hat Openshift only](https://github.com/kappnav/README/blob/master/actions-deployment-config-nodejs.md)
1. [Ingress](https://github.com/kappnav/README/blob/master/actions-ingress.md)
1. [Job](https://github.com/kappnav/README/blob/master/actions-job.md)
1. [Pod](https://github.com/kappnav/README/blob/master/actions-pod.md)
1. [Route (Red Hat Openshift only)](https://github.com/kappnav/README/blob/master/actions-route.md)
1. [Service](https://github.com/kappnav/README/blob/master/actions-service.md)
  
## Adding your own custom actions 

Under construction ... 
