# Applications that span multiple namespaces

By default, the components of an application are in the same namespace that the application is in as the Kubernetes resources are all created in the same namespace. The default behavior of Kubernetes Application Navigator is to scope the label selector specified in the Application Custom Resource to the namespace that the label selector was created in. This behavior means that the label selector of an application is applied only to the specified component kinds in the same namespace as the application.

Components from other namespaces can be included in the label selector search by listing multiple namespaces within a comma-delimited list on the `kappnav.component.namespaces` annotation in the Application custom resource.

Spanning namespaces is valuable when an application is composed of multiple microservices that are created and managed by teams that deploy to isolated namespaces.

## Microservice architecture

In the following example, a business application that is named `music-service` is composed of microservices that are created by the user interface team and the API team.

Each team is responsible for creating multiple microservices, and they organize themselves into either the `ui-namespace` or the `api-namespace`. The teams develop and deploy multiple microservices in their respective namespaces.

The `music-service` application uses the `web-ui` that is developed by the user interface team and the `streaming-api` that is developed by the API team.

To create an Application custom resource that reflects the `music-service` application, more configuration is needed to instruct Kubernetes Application Navigator to span the namespaces:
```
metadata:
  annotations:
    kappnav.component.namespaces: ui-namespace, api-namespace
```

The optional `kappnav.component.namespaces` annotation instructs Kubernetes Application Navigator to apply the label selector of the application to the 'ui-namespace' and 'api-namespace' namespaces in addition to the namespace of the application.

The [music-service sample application](https://github.com/kappnav/samples/tree/master/music-service) uses the `solution: music-service` label to group all of the resources together.

![image](/images/span-namespace-music-service.png?raw=true)

The following `music-service-app-cr.yaml` file is the [YAML file](https://github.com/kappnav/samples/blob/master/music-service/music-service-app-cr.yaml) of the sample application:
```
$ kubectl get application music-service -n music-service -o yaml
apiVersion: app.k8s.io/v1beta1
kind: Application
metadata:
  name: music-service
  annotations:
    kappnav.component.namespaces: ui-namespace, api-namespace
  labels:
    app: music-service
    solution: music-service
spec:
  componentKinds:
  - group: resources
    kind: Pod
  - group: resources
    kind: Deployment
  - group: resources
    kind: Service
  - group: resources
    kind: Route
  selector:
    matchLabels:
      solution: music-service
```
