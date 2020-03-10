# View Metrics Action for Open Liberty

Java-based applications that are built with the [Open Liberty](https://openliberty.io/) framework can expose performance metrics through a 
[Prometheus](https://prometheus.io/) endpoint with no additional coding required.  

These performance metrics can be viewed through a [Grafana](https://prometheus.io/docs/visualization/grafana/) dashboard. The 
grafana dashboard can be launched from {k}AppNav for a Liberty Deployment.

There are three requirements to enable this capability:

* Platform preparation 

   Prometheus, Grafana, and the Liberty dashboard are not configured for use on Kubernetes by default. The Kabanero guide for [Application Monitoring on OKD with Prometheus and Grafana](https://kabanero.io/guides/app-monitoring) provides an example of how to set up the Prometheus, Grafana, and Liberty dashboards.

* Image preparation

    When you have application monitoring with Prometheus and Grafana setup on your cluster, you see no metric data for your Liberty Deployment kind unless your Liberty Deployment kind is enabled for reporting metrics. The following tutorial explains how to set up Liberty for reporting metrics:  [Configure an observable microservice with Appsody, OpenShift, and Open Liberty](https://developer.ibm.com/tutorials/configure-an-observable-microservice-with-appsody-openshift-open-liberty/#).

* Deployment Configuration

   To expose the **View Metrics** action in the {k}AppNav action menu for the Deployment kind, the following annotation and label is required: 

   ```
   kind: Deployment
   metadata: 
      labels: 
         kappnav.action.metrics.{mpxx}: true
      annotations: 
         kappnav.subkind: Liberty 
   ```

   In the `kappnav.action.metrics.{mpxx}: true` label, `{mpxx}` can be specified as either `mp11` or `mp20` for the MicroProfile metrics 1.1 or 2.0 releases. This specification depends on the decisions you make during image preparation.

   The following image shows how the **View Metrics** action appears in the {k}AppNav UI:
  ![image](images/view-metrics-action.png?raw=true)
