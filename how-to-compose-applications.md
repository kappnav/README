# Composing Applications in kAppNav

kAppNav allows you to create applications and reflect the set of underlying Kubernetes resources which that application uses. It also allows you to compose an application which includes other applications. This guide will show you how to do that.

Composing an application which includes other applications has a few benefits:
1. The application can reflect the logical, organizational or business unit struture of a set of applications which are  tightly coupled or collaborate to perform work.
1. The application provides a single, combined status which reflect the status of all included applications and resources. If there is an issue with any included application or resource, the application's status will reflect that for easy monitoring.

This guide assumes you have an OpenShift (or OKD) environment, have already [installed kAppNav](/README.md) and are familiar with [creating an application](/how-to-create-applications.md). 

# Before you begin

kAppNav uses Kubernetes's flexible labeling system and the [Application CRD](https://github.com/kubernetes-sigs/application/blob/master/config/crds/app_v1beta1_application.yaml) to group various elements together into a logical application view. This group can include Kubernetes resources (such as Pods) but it can also include other applications. The grouping is represented by an Application Custom Resource and a set of label selectors. This guide will show two ways of composing applications: autonomous membership and directed membership. 

# Step 1: Quick Setup

To demonstrate composing applications, we will use two applications in the same namespace which follow the Backend for Frontend (BFF) pattern. This pattern is composed of two separate applications: the frontend application (usually a web client or mobile app) and the backend application (such as a Java or Node.js application). Both of these applications collaborate to provide the application experience and are often developed and owned by the same team.

To keep the guide simple, we will simulate this concept using two applications which do not actually implement the BFF pattern. This guide assumes you've already followed the [Creating Applications in kAppNav](/how-to-create-applications.md) guide as it will re-use the my-node-project namespace and add a new application to it. We will think of the my-node-app as the "frontend" application, and add a new "backend" application and compose them together.

## Adding the new "backend" application to the existing namespace

1. Log into your OpenShift Console.

1. Navigate to the "My Node Project", select "Add to Project" and browse the Catalog.
![image](/images/manual-compose-create-browse-catalog.png?raw=true)

1. Create a new "Node.js + MongoBD" application and accept the defaults by clicking Create.

1. Create an Application Custom Resource YAML for the Node.js + MongoDB application.
   * Note that the matchLabels used for this Application Custom Resource is different from that used in the my-node-app, which matched on the 'app' label. For this Node.js + MongoDB app, we match on the label 'template'. This is because the Node+MongoDB application does not automatically provide the 'app' label.
   * Application Custom Resource YAML `my-node-mongo-app-cr.yaml`:
   ```
   apiVersion: app.k8s.io/v1beta1
   kind: Application
   metadata:
     name: my-node-mongo-app
     namespace: my-node-project
     labels:
       app: my-node-mongo-app
       solution: my-node-bff
   spec:
     componentKinds:
     - group: resources
       kind: Route
     - group: resources
       kind: DeploymentConfig
     selector:
       matchLabels:
         template: nodejs-mongo-persistent
   ```

1. Apply the my-node-mongo-app-cr.yaml Application Custom Resource: `kubectl apply -f my-node-mongo-app-cr.yaml`
   * The Application Custom Resource will be created:
   ```
   # kubectl apply -f my-node-mongo-app-cr.yaml
   application.app.k8s.io/my-node-mongo-ap created
   ```

1. Navigate to the kAppNav UI to see both my-node-mongo-app.
![image](/images/manual-compose-kappnav-node-mongo-created.png?raw=true)

1. We now have our (faked) BFF pattern: my-node-app is our "frontend" and the my-node-mongo-app is our "backend".

## Defining labels on the Application Custom Resource

Before we can compose an application which includes both my-node-app and my-node-mongo-app, the applications need to be properly labeled. Recall that the Application CRD works based on label selectors. That means the Application Custom Resources themselves need to be labeled. In the previous step, the my-node-mongo-app-cr.yaml already included two labes: `app: my-node-mongo-app` and `solution: my-node-bff`. This was done with the foreknowledge that this application would be included by another application. The my-node-app application was not created with that foreknowledge so we will need to update it to define similar labels.

1. Navigate to the kAppNav UI, select my-node-app and the Edit action.
![image](/images/manual-compose-kappnav-edit-my-node-app.png?raw=true)

1. Add the app and solution labels: `app: my-node-app` and `solution: my-node-bff` and click Save.
   * Add the following JSON to the editor:
   ```
   "labels": {
      "app": "my-node-app",
      "solution": "my-node-bff"
   },
   ```
   ![image](/images/manual-compose-kappnav-edit-add-labels.png?raw=true)

1. Inspect the Basic Details section to see the updated labels.
   ![image](/images/manual-compose-kappnav-updated-labels.png?raw=true)

Now, both my-node-app and my-node-mongo-app define its own 'app' label, and have a shared 'solution' label. We will use these labels to compse the my-node-bff application using two different methods.

# Step 2: Compose the Application using Autonomous Membership

Let's start with the autonomous membership model, which is how the [stock-trader sample](https://github.com/kappnav/samples/tree/master/stocktrader) works. In this model, individual components choose to be part of a known application by defining a consistent label throughout. We did this when we created the my-node-mongo-app and defined the label `solution: my-node-bff` with the foreknowledge that it would be part of a bigger whole. We then updated the my-node-app to have that label as well when we realized we want to include it. This model offers the flexibility for future components to be added to the application simply by adding the label `solution: my-node-bff`.

Follow these steps to create the 'my-node-bff' application using the kAppNav UI.

1. Navigate to the kAppNav UI and create a new application named 'my-node-bff' in the 'my-node-project' namespace.
![image](/images/manual-compose-kappnav-create-general.png?raw=true)

1. On the Selectors tab, specify the 'solution' label and value 'my-node-bff' which we previously defined for both applications.
   * Label: 'solution'
   * Value: 'my-node-bff'
   ![image](/images/manual-compose-kappnav-create-selectors.png?raw=true)

1. On the Kinds tab, specify the 'Application' kind in the 'applications' group.
   * Group 1: 'applications'
   * Kind 1: 'Application'
   ![image](/images/manual-compose-kappnav-create-kinds.png?raw=true)
   
1. Click Create and wait for the my-node-bff application status to turn Normal, reflecting the combined status of all included applications and resources.
![image](/images/manual-compose-kappnav-created.png?raw=true)

1. Navigate into my-node-bff application to see the included applications.
![image](/images/manual-compose-kappnav-created-details.png?raw=true)

Altneratively, this can be created using the following Application Custom Resource YAML and applying it with `kubectl apply`:
```
apiVersion: app.k8s.io/v1beta1
kind: Application
metadata:
  name: my-node-bff
  namespace: my-node-project
spec:
  componentKinds:
  - group: applications
    kind: Application
  selector:
    matchLabels:
      solution: my-node-bff
```

# Step 3: Compose the Application using Directed Membership

The directed membership model gives application authors more control over the components of the application, selecting only the components they wish to include.

Follow these steps to create the 'my-node-bff-directed' application using the kAppNav UI.

1. Navigate to the kAppNav UI and create a new application named 'my-node-bff-directed' in the 'my-node-project' namespace.
![image](/images/manual-compose-kappnav-create-directed-general.png?raw=true)

1. On the Selectors tab, specify the following match expression: `{key: app, operator: In, values: [my-node-app,my-node-mongo-app]}`.
   * Key: 'app'
   * Operator: 'In'
   * Values: 'my-node-app,my-node-mongo-app'
   * Note! Kubernetes is case sense. This is true for matchExpressions. As per the official [Kubernetes documentation](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/) you need to use 'In' (Upper case I).
   ![image](/images/manual-compose-kappnav-create-directed-selectors.png?raw=true)

1. On the Kinds tab, specify two resource kinds ('Pod' and 'DeploymentConfig') in the 'resources' group and the 'Application' kind in the 'applications' group.
   * Group 1: 'applications'
   * Kind 1: 'Application'
   * Group 2: 'resources'
   * Kind 2: 'Pod'
   * Group 3: 'resources'
   * Kind 3: 'DeploymentConfig'
   ![image](/images/manual-compose-kappnav-create-directed-kinds.png?raw=true)
   
1. Click Create and wait for the my-node-bff-directed application status to turn Normal, reflecting the combined status of all included applications and resources.
![image](/images/manual-compose-kappnav-directed-created.png?raw=true)

1. Navigate into my-node-bff application to see the included applications.
   * Note that there are more resources shown in this view than in the my-node-bff view. This is because we specified more resource kinds and because the 'app' label is consistently applied to resources in the my-node-app application, but not applied at all of the resources in my-node-mongo-app (other than the Application Custom Resource itself). This highlights the reason to have consistent labeling when using Kubernetes.
   ![image](/images/manual-compose-kappnav-directed-created-details.png?raw=true)

Altneratively, this can be created using the following Application Custom Resource YAML and applying it with `kubectl apply`:
```
apiVersion: app.k8s.io/v1beta1
kind: Application
metadata:
  name: my-node-bff-directed
  namespace: my-node-project
spec:
  componentKinds:
  - group: applications
    kind: Application
  - group: resources
    kind: Pod
  - group: resources
    kind: DeploymentConfig
  selector:
    matchExpressions:
    - key: app
      operator: In
      values:
      - my-node-mongo-app
      - my-node-app
```

# Considerations, Benefits, and Trade-offs

The two different membership models each satisfy different use cases and needs.

The directed membership gives control to the application author to decide which components they wish to include. This can be useful in scenarios were the application is composed of things the author owns or is responsible for (such as all of the applications owned by a person or team), or if the composition of the application includes applications which are reused by many parts of the system (which can occur with a microservice architecture).

The autononmous membership model affors a great deal of flexibility for an application to evolve over time, allowing new components of the application to be added or removed by modifying their own labels. This can be useful in scenarios where many applications are being developed in support of a broader "business application", all of the developed applications can opt-in to be represented as part of the larger, logical application.

Both of these models can be used to create applications and include resources of all kinds. The autonomous membership model  relies on `matchLabels:` and directed membership relies on `matchExpression:`. This guide demonstrated both models in the context of composing an application which includes other applications, but the model extends to any kind, as shown with 'my-node-bff-directed' which included Pod and DeploymentConfig.

Irrespective of which membership model is approriate to use, it is strongly encouraged to have consistent labeling throughout. This is best practice to keep resources organized and orderly, and labels are fundamentally how the Application CRD groups application resources.
