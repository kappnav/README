# Creating Applications in kAppNav

This guide does not require that you know how to create an application in Kubernetes. If you have done so before, and are comfortable tweaking the instructions, you can skip step 1.

This guide does assume that you have an OpenShift (or OKD) environment which has [kAppNav installed](/README.md), and that you have access to the console and your user account has the authorziation to create projects and applications.

# Before you begin

It is important to understand that Kubernetes is a highly flexibile system.
Many names, labels and resources are defined and used by convention.
Depending on the distribution of Kubernetes you use some names, labels and resources may be different.
kAppNav relies on matching these names, labels and resources in order to define and display your application. 
Because of this, you need to understand how your application is deployed, using what resources, and how it is named and labeled.
The steps laid out in these instructions will work for any Kubernetes environment, but the exact names, labels and resource kinds may vary based on your application and environment.

# Step 1: Create a simple Node.js app

1. Log into your OpenShift Console.

1. From the Service Catalog, create a new Node.js project.
   * Select a Node.js project from either the catalog ...
   ![image](/images/manual-app-create-catalog-icon-node.png?raw=true)
   *  ... or search for it.
   ![image](/images/manual-app-create-catalog-search-node.png?raw=true)

1. Follow the project creation dialog to create a new project.
![image](/images/manual-app-create-wizard-page1.png?raw=true)

1. Create the Node.js application.
   * Add to Project: Create Project
   * Project Name: 'my-node-project' (this will be the application's namespace)
   * Project Display Name: 'My Node Project'
   * Application Name: 'my-node-app'
   * Git Repository: Click 'Try Sample Repository'
   * Click Create
   ![image](/images/manual-app-create-wizard-page2-top.png?raw=true)
   ![image](/images/manual-app-create-wizard-page2-bottom.png?raw=true)

1. Close the dialog and navigate to the newly created "My Node Project".
![image](/images/manual-app-create-wizard-page3.png?raw=true)

1. Navigate to the "My Node Project" project view. If necessary, wait for the build to complete.
![image](/images/manual-app-create-wait-for-build.png?raw=true)

1. Once the build is complete and the pod is deployed, access the application's URL.
![image](/images/manual-app-create-build-complete.png?raw=true)

1. The application is now deployed and receiving traffic.
![image](/images/manual-app-create-access-page.png?raw=true)

1. Now let's add this application to kAppNav.

# Step 2: Add the application in kAppNav

These instructions assume you have already [installed kAppNav](/README.md).

In order for kAppNav to display the application an Application Custom Resource must be created. There are two ways to do this: either through the kAppNav GUI or through the kubectl CLI. Instructions are provided for both.

## Creating the Application Custom Resource through the GUI

1. Go to the kAppNav UI and click the "Create Applications" button.
![image](/images/manual-app-kappnav-create-button.png?raw=true)

1. On the General tab, specify the application name and namespace.
   * Name: 'my-node-app' (the value of name can be anything, it does not need to match the application's name)
   * Namespace: 'my-node-project' (this must be the namespace in which the application belongs)
   ![image](/images/manual-app-kappnav-create-dialog-general.png?raw=true)

1. On the Selectors tab, specify the app label and name.
   * Label: 'app' (this label name is provided by convention)
   * Value: 'my-node-app' (this must be the application name provied when the application was created)
   ![image](/images/manual-app-kappnav-create-dialog-selectors.png?raw=true)

1. On the Kinds tab, specify two resource kinds ('Pod' and 'DeploymentConfig') in the 'resources' group.
   * Group 1: 'resources'
   * Kind 1: 'Pod'
   * Group 2: 'resources'
   * Kind 2: 'DeploymentConfig'
   * [Understand the fields](#understand-the-fields)
   ![image](/images/manual-app-kappnav-create-dialog-kinds.png?raw=true)

1. Hit create. The application will be created. The status may show as "Unknown" for a few moments until it flips to "Normal". The "Unknown" status occurs while kAppNav is interograting the resources to determine the overall status.
![image](/images/manual-app-kappnav-create-normal.png?raw=true)

1. Navigate into the my-node-app application to see the details and components which make up the application.
![image](/images/manual-app-kappnav-created-details.png?raw=true)

## Editing the Application Custom Resource through the GUI

You can add additional resources, such as Services and Routes, to the application components by editing the Application Custom Resource.

1. Navigate into the application. From the action menu, select Edit to modify the Application Custom Resource as JSON.
![image](/images/manual-app-kappnav-created-edit.png?raw=true)

1. Add additional Kinds to the componentKinds list and click Save.
   * `{ "group": "resources", "kind": "Service" }, { "group": "resources", "kind": "Route"},`
   ![image](/images/manual-app-kappnav-created-add-kinds.png?raw=true)

1. kAppNav will automatically update the display to show the added resources. The new resources might show as Unknown status for a few moments until it flips to Normal status.
![image](/images/manual-app-kappnav-created-updated-kinds.png?raw=true)

## Creating the Application Custom Resource through the CLI

The steps and output provided in these instruction assume you have followed the previous instructions for creating the Application Custom Resource through the GUI. If you have skipped that step, no problem! Just know that the output may look different.

1. As a sanity check, make sure the my-node-app is running. This is not strictly needed but this will ensure the status of the application is displayed as expected.
   * Ensure the pods are running: `kubectl get pods -n my-node-project`
   ```
   # kubectl get pods -n my-node-project
   NAME                  READY     STATUS      RESTARTS   AGE
   my-node-app-1-build   0/1       Completed   0          31m
   my-node-app-1-mlvgl   1/1       Running     0          31m
   ```

1. Create an Application Custom Resource YAML file. This YAML file describes the specific Kubernetes resources which are included in the Application you define. The GUI steps create this YAML file through the "Create Application" dialog.
   * This step assumes you previously created the 'my-node-app' Application Custom Resource through the GUI and uses a different name (e.g. 'my-node-app2').
   * [Understand the fields](#understand-the-fields)
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

1. Apply the my-node-app-crd.yaml Application Custom Resource: `kubectl apply -f my-node-app-cr.yaml`
   * The Application Custom Resource will be created:
   ```
   # kubectl apply -f my-node-app-crd.yaml 
   application.app.k8s.io/my-node-app2 created
   ```

1. Confirm that the Application Custom Resource has been created: `kubectl get application -n my-node-project`
   * If you followed the GUI steps, you'll already have the my-node-app resource:
   ```
   # kubectl get application -n my-node-project
   NAME           AGE
   my-node-app    48m
   my-node-app2   19m
   ```

1. Navigate to kAppNav UI to see both my-node-app and my-node-app2.
![image](/images/manual-app-kappnav-cli-result.png?raw=true)


## Editing the Application Custom Resource through the CLI

You can add additional resources, such as Services and Routes, to the application components by editing the Application Custom Resource.

1. Update the my-node-app-cr.yaml to include the Service and Route
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

1. Apply the updated my-node-app-cr.yaml: `kubectl apply -f my-node-app-cr.yaml`
   * The Application Custom Resource will be configured:
   ```
   # kubectl apply -f my-node-app-cr.yaml 
   application.app.k8s.io/my-node-app2 configured
   ```
1. Navigate to kAppNav to see the changes.
![image](/images/manual-app-kappnav-cli-updated.png?raw=true)


# Understand the fields

To add an application to kAppNav it is important to understand how the Application CRD is used. Provided here is a brief description of some of the most important fields. Refer to the [Application Custom Resource Definition (CRD)](https://github.com/kubernetes-sigs/application/blob/master/config/crds/app_v1beta1_application.yaml) from the [Kubernetes Application SIG](https://github.com/kubernetes-sigs/application) for the authoritative documentation.

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
  selector:
    matchLabels:
      app: my-node-app
```

How this works:
* `metadata: name` - This is the display name of your application in kAppNav as well as the name of the application resource. It must be unique within the namespace.
* `metadata: namespace` -  This must the namespace of your applicaiton (details on cross-namespace configuration is coming later)
* `spec: componentKinds: group` - The value for group (e.g. 'resources') can be any value. It does not presently have a use.
* `spec: componentKinds: kind` -  The resource kinds (e.g. 'Pod') come from the underlying application deployment. Depending on how the application is deployed, different resources may be available. The kind value must be Capitalized - that is the correct syntax for type references.
* `selector: matchLabels:` -  The matchLabels look for existing label name=value pairs which exist on the application and its resources. The 'app' label is a convention and is usually applied to all resources for the same application.

You can see all of the resources in the application namespace with the `kubectl get all -n my-node-project` command.
