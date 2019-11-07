# Creating Applications in Kubernetes Application Navigator

You can create projects and applications in Kubernetes Application Navigator with the OpenShift console.


# Before you begin

Ensure that you install an OpenShift environment with [kAppNav installed](/README.md). You need access to the console and also need to have a user account that is authorized to create projects and applications.


# About this task

 Many names, labels, and resources are defined and used by convention with Kubernetes. Depending on which development tools that you use, some names, labels, and resources can vary. Kubernetes Application Navigator relies on matching these names, labels, and resources to define and display your application. Because of this condition, you need to understand how your application is deployed, what resources are used, and how it is named and labeled. The exact names, labels, and resource kinds can vary based on your application and environment.


# Step 1: Create a simple Node.js application

1. Log in to your OpenShift Console.

1. From the Service Catalog, create a new Node.js project.
   * Select a Node.js project from either the catalog ...
   ![image](/images/manual-app-create-catalog-icon-node.png?raw=true)
   *  ... or search for it.
   ![image](/images/manual-app-create-catalog-search-node.png?raw=true)

1. Use the project creation dialog box to create a new project.
![image](/images/manual-app-create-wizard-page1.png?raw=true)

1. Create the Node.js application.
   * In the **Configuration** section of the project creation dialog box, enter the details to create a new project.
      * Under **Git Repository**, click **Try Sample Repository**.
      * Click **Create**.
   ![image](/images/manual-app-create-wizard-page2-top.png?raw=true)
   ![image](/images/manual-app-create-wizard-page2-bottom.png?raw=true)

1. Close the dialog box and navigate to the newly created **My Node Project**.
![image](/images/manual-app-create-wizard-page3.png?raw=true)

1. Navigate to the **My Node Project** project view. If necessary, wait for the build to complete.
![image](/images/manual-app-create-wait-for-build.png?raw=true)

1. When the build is complete and the pod is deployed, access the URL of the application.
![image](/images/manual-app-create-build-complete.png?raw=true)

1. The application is now deployed and receiving traffic.
![image](/images/manual-app-create-access-page.png?raw=true)

# Step 2: Add the application in Kubernetes Application Navigator

To display the application with Kubernetes Application Navigator, you can create an Application Custom Resource. You can create an Application Custom Resource by using either the kAppNav GUI or the kubectl CLI.

## Creating the Application Custom Resource through the GUI

1. Go to the kAppNav UI and click the **Create Applications** button.
![image](/images/manual-app-kappnav-create-button.png?raw=true)

1. On the **General** tab, specify the application name and namespace.
   * The **Name** value can be anything and does not need to match the name of the application. The **Namespace** value is the namespace that the application belongs to.
   ![image](/images/manual-app-kappnav-create-dialog-general.png?raw=true)

1. On the **Selectors** tab, specify the **app** label name and the application name that appears under **Value**.
   * The label name is provided by convention and the application name is provided when the application is created.
   ![image](/images/manual-app-kappnav-create-dialog-selectors.png?raw=true)

1. On the **Kinds** tab, specify two resource kinds in the **resources** group.
   ![image](/images/manual-app-kappnav-create-dialog-kinds.png?raw=true)

1. Click **create**.
   * The application is created. The status might display as **Unknown** for a few moments until it changes to **Normal**. The **Unknown** status occurs while Kubernetes Application Navigator is interrogating the resources to determine the overall status.
![image](/images/manual-app-kappnav-create-normal.png?raw=true)

1. Navigate into the **my-node-app** application to see the details and components that compose the application.
![image](/images/manual-app-kappnav-created-details.png?raw=true)

## Editing the Application Custom Resource through the GUI

You can add more resources, such as Services and Routes, to the application components by editing the Application Custom Resource.

1. Navigate into the application. From the action menu, click **Edit** to modify the Application Custom Resource as a JSON file.
![image](/images/manual-app-kappnav-created-edit.png?raw=true)

1. Add more **Kinds** to the componentKinds list and click **Save**.
   * `{ "group": "resources", "kind": "Service" }, { "group": "resources", "kind": "Route"},`
   ![image](/images/manual-app-kappnav-created-add-kinds.png?raw=true)

1. Kubernetes Application Navigator automatically updates the display to show the added resources. The new resources might display as **Unknown** under **Status** for a few moments until the status changes to **Normal**.
![image](/images/manual-app-kappnav-created-updated-kinds.png?raw=true)

## Creating the Application Custom Resource through the CLI

> **Important**: If you do not create the Application Custom Resource through the GUI before you create the Application Custom Resource through the CLI, the output of the steps might look different.

1. Optional: Use the following command to ensure that the pods in the `my-node-app` are running: `kubectl get pods -n my-node-project`.
   ```
   # kubectl get pods -n my-node-project
   NAME                  READY     STATUS      RESTARTS   AGE
   my-node-app-1-build   0/1       Completed   0          31m
   my-node-app-1-mlvgl   1/1       Running     0          31m
   ```

1. Create an Application Custom Resource YAML file.
   * The YAML file describes the specific Kubernetes resources that are included in the Application that you define.
   * Application Custom Resource YAML `my-node-app-cr.yaml`:
   ```
   apiVersion: app.k8s.io/v1beta1
   kind: Application
   metadata:
     name: my-node-app2
     namespace: my-node-project
   spec:
     componentKinds:
     - group: resources
       kind: Pod
     - group: resources
       kind: DeploymentConfig
     selector:
       matchLabels:
         app: my-node-app
   ```

1. Create the `my-node-app-crd.yaml`file Application Custom Resource with the following command: `kubectl apply -f my-node-app-cr.yaml`
   
   ```
   # kubectl apply -f my-node-app-crd.yaml 
   application.app.k8s.io/my-node-app2 created
   ```

1. Confirm that the Application Custom Resource is created by using the following command: `kubectl get application -n my-node-project`
   * The `my-node-app` resource is created when you create the Application Custom Resource through the GUI.
   ```
   # kubectl get application -n my-node-project
   NAME           AGE
   my-node-app    48m
   my-node-app2   19m
   ```

1. Navigate to Kubernetes Application Navigator to see the `my-node-app` and `my-node-app2` resources.
![image](/images/manual-app-kappnav-cli-result.png?raw=true)


## Editing the Application Custom Resource through the CLI

You can add more resources, such as `Services` and `Routes`, to the application components by editing the Application Custom Resource.

1. Update the `my-node-app-cr.yaml` file to include the `Service` and `Route`
```
apiVersion: app.k8s.io/v1beta1
kind: Application
metadata:
  name: my-node-app2
  namespace: my-node-project
spec:
  componentKinds:
  - group: resources
    kind: Pod
  - group: resources
    kind: DeploymentConfig
  - group: resources
    kind: Service
  - group: resources
    kind: Route
  selector:
    matchLabels:
      app: my-node-app
```

2.  Apply the updated my-node-app-cr.yaml file with the following command: `kubectl apply -f my-node-app-cr.yaml`
   * The Application Custom Resource is configured:
   ```
   # kubectl apply -f my-node-app-cr.yaml 
   application.app.k8s.io/my-node-app2 configured
   ```
3. Navigate to Kubernetes Application Navigator to see the changes.
![image](/images/manual-app-kappnav-cli-updated.png?raw=true)

