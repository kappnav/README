# View Metrics Action for [Open Liberty](https://openliberty.io/)

Java-based apps built with the Open Liberty framework can expose performance metrics through a 
[Prometheus](https://prometheus.io/) endpoint with no additional coding required.  

These performance metrics can be viewed through a [Grafana](https://prometheus.io/docs/visualization/grafana/) dashboard. The 
grafana dashboard can be launched from {k}AppNav for a Liberty Deployment.

To enable this capability


You reached this page because you attempted to view the Grafana dashboard for a Liberty Deployment resource's metrics, but application monitoring is not set up on your Kubernetes cluster.  The Kabanero guide for [Application Monitoring on OKD with Prometheus and Grafana](https://kabanero.io/guides/app-monitoring) provides an example of how to set it up.

**Avoid Trouble**

Note that even with Application Monitoring with Prometheus and Grafana setup on your cluster, you will see no metric data for your Liberty Deployment unless your Liberty Deployment has been enabled for metrics reporting.  The following tutorial explains how to setup Liberty for metrics reporting:  [Configure an observable microservice with Appsody, OpenShift, and Open Liberty](https://developer.ibm.com/tutorials/configure-an-observable-microservice-with-appsody-openshift-open-liberty/#).
