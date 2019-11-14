# Composing applications in Kubernetes Application Navigator

Kubernetes Application Navigator creates applications and reflects the set of underlying Kubernetes resources that an application uses. You can also use Kubernetes Application Navigator to compose an application that includes other applications.

Composing an application that includes other applications has a few benefits:
1. The application can reflect the logical, organizational, or business unit structure of a set of applications that are tightly coupled or collaborate to perform work.
1. The application provides a single, combined status that reflects the status of all included applications and resources. If there is an issue with any included application or resource, the application's status reflects that for easy monitoring.

# Prerequisites

1. You must have an OpenShift or OKD environment.
2. You must [install Kubernetes Application Navigator](/README.md).
3. You must be familiar with [creating an application](/how-to-create-applications.md).

# Before you begin

Kubernetes Application Navigator uses the flexible labeling system of Kubernetes and the [Application CRD](https://github.com/kubernetes-sigs/application/blob/master/config/crds/app_v1beta1_application.yaml) to group various elements together into a logical application view. The various elements can include Kubernetes resources (such as Pods) but the elements can also include other applications. The grouping is represented by an Application Custom Resource and a set of label selectors. You can compose applications by using either autonomous membership or directed membership.

# Quick setup

To demonstrate how to compose applications, you can use two applications in the same namespace that follow the Backend for Frontend (BFF) pattern. This pattern is composed of two separate applications that include the front-end application and the backend application. Both of these applications collaborate to provide the application experience and are often developed and owned by the same team.

You can use two applications that do not implement the BFF pattern to simulate the process of composing applications. You can reuse the `my-node-project` namespace that was used when you [created applications in Kubernetes Application Navigator](/how-to-create-applications.md) and add an application to it. When you compose two applications, the `my-node-app` application can be used as the front-end application, and you can add a new back-end application.

## Adding the new back-end application to the existing namespace

1. Log in to your OpenShift Console.

1. Navigate to the **My Node Project**, select **Add to Project**, and browse the catalog.
![image](/images/manual-compose-create-browse-catalog.png?raw=true)

1. Create a new "Node.js + MongoBD" application and accept the defaults by clicking **Create**.

1. Create an Application Custom Resource YAML file for the Node.js + MongoDB application.
   * The `matchLabels` that are used for this Application Custom Resource are different from the `matchLabels` that are used in the `my-node-app` application that is matched on the `app` label. For the Node.js + MongoDB application, you can match on the `template`label because the Node+MongoDB application does not automatically provide the `app` label.
   * `my-node-mongo-app-cr.yaml` is the Application Custom Resource YAML file:
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

1. Apply the `my-node-mongo-app-cr.yaml` file Application Custom Resource with the following command: `kubectl apply -f my-node-mongo-app-cr.yaml`
   * The Application Custom Resource is created:
   ```
   # kubectl apply -f my-node-mongo-app-cr.yaml
   application.app.k8s.io/my-node-mongo-ap created
   ```

1. Navigate to the Kubernetes Application Navigator UI to see both the **my-node-app** applications and the **my-node-mongo-app** application.
![image](/images/manual-compose-kappnav-node-mongo-created.png?raw=true)

1. You can now see the BFF pattern that displays the **my-node-app** application as the front-end application and the **my-node-mongo-app** application as the back-end application.

## Defining labels on the Application Custom Resource

Before you can compose an application that includes the `my-node-app` application and the `my-node-mongo-app` application, the applications need to be properly labeled. Because the Application CRD works based on label selectors, the Application Custom Resources also need to be labeled. You need to update the `my-node-app` application to include the `app: my-node-mongo-app` and `solution: my-node-bff` labels to match the labels that are included in the `my-node-mongo-app-cr.yaml` file.

1. Navigate to the Kubernetes Application Navigator UI and click **my-node-app**.
   * Click **Edit**.
![image](/images/manual-compose-kappnav-edit-my-node-app.png?raw=true)

1. Add the `app: my-node-app` and `solution: my-node-bff` labels
   * Click **Save**.
   * Add the following JSON file to the editor:
   ```
   "labels": {
      "app": "my-node-app",
      "solution": "my-node-bff"
   },
   ```
   ![image](/images/manual-compose-kappnav-edit-add-labels.png?raw=true)

1. Inspect the **Basic Details** section to see the updated labels.
   ![image](/images/manual-compose-kappnav-updated-labels.png?raw=true)

The `my-node-app` and `my-node-mongo-app` applications now have a defined **app** label and have a shared **solution** label. These labels are used to compose the `my-node-bff` application by using two different methods.

# Compose the Application by using Autonomous Membership

The autonomous membership model enables the [stock-trader sample](https://github.com/kappnav/samples/tree/master/stocktrader). In the autonomous membership model, individual components are chosen to comprise an application by defining a consistent label throughout the application. You can use the autonomous membership model to provide the flexibility to add future components to the application by adding the `solution: my-node-bff` label.


1. Open the Kubernetes Application Navigator UI and create a new application that is named **my-node-bff** in the **my-node-project** namespace.
![image](/images/manual-compose-kappnav-create-general.png?raw=true)

1. On the **Selectors** tab, specify the 'solution' label and the 'my-node-bff' value that was defined for both applications.
   ![image](/images/manual-compose-kappnav-create-selectors.png?raw=true)

1. On the **Kinds** tab, specify the **Application** kind in the **applications** group.
   ![image](/images/manual-compose-kappnav-create-kinds.png?raw=true)
   
1. Click **Create** and wait for the **my-node-bff** application status to change to **Normal**.
     * The **Normal** status reflects the combined status of all included applications and resources.
![image](/images/manual-compose-kappnav-created.png?raw=true)

1. Open the **my-node-bff** application to see the included applications.
![image](/images/manual-compose-kappnav-created-details.png?raw=true)

1. Optional: You can create the **my-node-bff** application view by using the Application Custom Resource YAML file and applying the file with the following command: `kubectl apply`
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

# Compose the Application by using Directed Membership

You can use the directed membership model to gain more control over the components of the application by selecting only the components that you want to include.


1. Open the Kubernetes Application Navigator UI and create a new application that is named **my-node-bff-directed** in the **my-node-project** namespace.
![image](/images/manual-compose-kappnav-create-directed-general.png?raw=true)

1. On the **Selectors** tab, specify the following match expression: `{key: app, operator: In, values: [my-node-app,my-node-mongo-app]}`.
   * > **Important**: Kubernetes is case-sensitive. When you specify the match expression, you need to enter the **In** operator exactly as displayed. For more information, see the official [Kubernetes documentation](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/).
   ![image](/images/manual-compose-kappnav-create-directed-selectors.png?raw=true)

1. On the **Kinds** tab, specify the **Pod** and **DeploymentConfig** resource kinds in the **resources** group and the **Application** kind in the **applications** group.
   ![image](/images/manual-compose-kappnav-create-directed-kinds.png?raw=true)
   
1. Click **Create** and wait for the **my-node-bff-directed** application status to change to **Normal**
    * The **Normal** status reflects the combined status of all the included applications and resources.
![image](/images/manual-compose-kappnav-directed-created.png?raw=true)

1. Open the **my-node-bff-directed** application to see the included applications.
   * There are more resources that are shown in the **my-node-bff-directed** view than in the **my-node-bff** application view. The **my-node-bff-directed** application view has more resources because more resources kinds are specified and the **app** label is consistently applied to resources in the **my-node-app** application.
   ![image](/images/manual-compose-kappnav-directed-created-details.png?raw=true)

1. Optional: You can create the **my-node-bff-directed** application view by using the Application Custom Resource YAML file and applying the file with the following command: `kubectl apply`. 

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

# Considerations, Benefits, and tradeoffs

The two different membership models each satisfy different use cases and needs.

You can use the directed membership model to gain the control to decide which components you want to include in an application. The directed membership model can be useful when the application is composed of things that you are responsible for or own. You can also use directed membership if the composition of the application includes applications that are reused by many parts of the system that you are using.

You can use the autonomous membership model to provide the flexibility for an application to evolve over time. The autonomous membership model can be used to add components to an application or remove components through the modification of labels. You can also use autonomous membership in scenarios that include the development of many applications that support a broader business application.

You can use the autonomous membership model and the directed membership model to create applications and include resources of all kinds. The autonomous membership model relies on the `matchLabels:`selector and the directed membership model relies on the `matchExpression:` selector. 

It is important to maintain consistent labeling throughout an application because the Application CRD uses labels to group application resources. If you maintain consistent labeling, you can keep resources organized and avoid many errors.
