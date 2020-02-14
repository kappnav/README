# # View Kibana Logs Action for [Open Liberty](https://openliberty.io/)

Java-based apps built with the Open Liberty framework can expose performance metrics through a 
[Prometheus](https://prometheus.io/) endpoint with no additional coding required.  

These performance metrics can be viewed through a [Grafana](https://prometheus.io/docs/visualization/grafana/) dashboard. The 
grafana dashboard can be launched from {k}AppNav for a Liberty Deployment.

To enable this capability, three things are required: 

1. Platform prepartion 

   Prometheus, Grafana, and the Liberty dashboard are not configured for use on Kubernetes by default. The Kabanero guide for [Application Monitoring on OKD with Prometheus and Grafana](https://kabanero.io/guides/app-monitoring) provides an example of how to set it up.

1. Image preparation

   Note that even with Application Monitoring with Prometheus and Grafana setup on your cluster, you will see no metric data for your Liberty Deployment unless your Liberty Deployment has been enabled for metrics reporting.  The following tutorial explains how to setup Liberty for metrics reporting:  [Configure an observable microservice with Appsody, OpenShift, and Open Liberty](https://developer.ibm.com/tutorials/configure-an-observable-microservice-with-appsody-openshift-open-liberty/#).

1. Deployment Configuration

   To expose the 'View Metrics' action in the {k}AppNav action menu for the Deployment kind, the following annotation and label is required: 

   ```
   kind: Deployment
   metadata: 
      labels: 
         kappnav.action.metrics.{mpxx}: true
         kappnav.action.metrics.{mpxx}: true
      annotations: 
         kappnav.subkind: Liberty 
   ```

   Where {mpxx} is either mp11 or mp20 for MicroProfile metrics 1.1 or 2.0, depending on the decisions you made during image prepration, above.

   In the {k}AppNav UI, it appears like this: 

  ![image](images/view-metrics-action.png?raw=true)
