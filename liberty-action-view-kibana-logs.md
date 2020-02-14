# # View Kibana Logs Action for [Open Liberty](https://openliberty.io/)

Java-based apps built with the Open Liberty framework can send log data an EFK stack with no additional coding required.  

The log data for all PODs belonging to the same Liberty Deployment can be viewed through a [Kibana]() dashboard. The 
Kibana dashboard can be launched from {k}AppNav for a Liberty Deployment.

To enable this capability, two things are required: 

1. Platform prepartion 

   The EFK (or ELK) logging stack and Liberty Kibana dashboard are not configured for use on Kubernetes by default. The Kabanero guide for [Application Logging with Kabanero on OKD with Elasticsearch, FluentD, and Kibana](https://kabanero.io/guides/app-logging) provides an example of how to set it up.

1. Deployment Configuration

   To expose the 'View Metrics' action in the {k}AppNav action menu for the Deployment kind, the following annotation is required: 

   ```
   kind: Deployment
   metadata: 
      annotations: 
         kappnav.subkind: Liberty 
   ```

   In the {k}AppNav UI, it appears like this: 

  ![image](images/view-kibana-logs-action.png?raw=true)
