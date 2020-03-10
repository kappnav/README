# View Kibana Logs Action for Open Liberty

Java-based applications that are built with the [Open Liberty](https://openliberty.io/) framework can send log data to an EFK (Elasticsearch, FluentD, and Kibana) stack with no additional coding required.  

The log data for all PODs that belong to the same Liberty Deployment kind can be viewed through a [Kibana]() dashboard. The 
Kibana dashboard can be launched from {k}AppNav for a Liberty Deployment kind.

There are two requirements to enable this capability:

* Platform preparation 

   The EFK logging stack and Liberty Kibana dashboard are not configured for use on Kubernetes by default. The Kabanero guide for [Application Logging with Kabanero on OKD with Elasticsearch, FluentD, and Kibana](https://kabanero.io/guides/app-logging) provides an example of how to set up the EFK logging stack and Liberty Kibana dashboard.  

* Deployment configuration

   To expose the **View Kibana Logs** action in the {k}AppNav action menu for the Deployment kind, the following annotation is required: 

   ```
   kind: Deployment
   metadata: 
      annotations: 
         kappnav.subkind: Liberty 
   ```

   The following image shows how the **View Kibana Logs** action appears in the {k}AppNav UI:

  ![image](images/view-kibana-logs-action.png?raw=true)
