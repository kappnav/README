# What is Kubernetes Application Navigator?

   ```
   # kubectl create namespace kappnav
   namespace/kappnav created
   ```

   ```
   # kubectl create -f operator/kappnav.yaml -n kappnav
   customresourcedefinition.apiextensions.k8s.io/kappnavs.charts.helm.k8s.io created
   serviceaccount/helm-operator created
   clusterrole.rbac.authorization.k8s.io/helm-operator created
   clusterrolebinding.rbac.authorization.k8s.io/helm-operator created
   deployment.apps/helm-operator created
   kappnav.charts.helm.k8s.io/instance created
   ```
1. Use the following command to ensure that all the pods are running: `kubectl get pods -n kappnav`

   ```
   # kubectl get pods -n kappnav
   NAME                                 READY     STATUS      RESTARTS   AGE
   helm-operator-6bf5fb5b68-hvtbf       1/1       Running     0          2m
   kappnav-controller-c6bdfdf59-mv2wh   2/2       Running     0          1m
   kappnav-f5578677f-6r9js              2/2       Running     0          1m
   kappnav-init-post-49nt8              0/1       Completed   0          1m
   kappnav-init-pre-7fgcb               0/1       Completed   0          1m
   ```
1. Use the following command to ensure that the routes are created: `kubectl get routes -n kappnav`

   ```
   # kubectl get routes -n kappnav
   NAME                  HOST/PORT                                           PATH          SERVICES             PORT      TERMINATION          WILDCARD
   kappnav-api-service   kappnav-ui-service-kappnav.apps.myhost.com   /kappnav      kappnav-ui-service   <all>     reencrypt/Redirect   None
   kappnav-ui-service    kappnav-ui-service-kappnav.apps.myhost.com   /kappnav-ui   kappnav-ui-service   <all>     reencrypt/Redirect   None
   ```

   ```
   # kubectl create -f samples/stocktrader -n stocktrader
   application.app.k8s.io/stock-trader created
   deployment.extensions/loyalty-level created
   service/loyalty-level-service created
   deployment.extensions/notification-twitter created
   service/notification-service created
   deployment.extensions/portfolio created
   service/portfolio-service created
   deployment.extensions/stock-quote created
   service/stock-quote-service created
   deployment.extensions/trader created
   service/trader-service created
   ```
1. Ensure that the **stock-trader** application starts.
   * You can watch the application be deployed in the kAppNav UI. The **stock-trader** application appears and the status changes from **Unknown**, to **Problem**, then to **Normal**.
   * You can also check the installation progress with the following command to see whether all the pods are running:  `kubectl get pods -n stocktrader`


   ```
   # kubectl get pods -n stocktrader
   NAME                                  READY     STATUS    RESTARTS   AGE
   loyalty-level-5fd5c9b855-rfxss        1/1       Running   0          54s
   notification-twitter-f568cc54-d5xck   1/1       Running   0          54s
   portfolio-8fb7cf89c-zjw58             1/1       Running   0          54s
   stock-quote-5c88f57659-tlcl8          1/1       Running   0          53s
   trader-59c9dcb74-2cv8b                1/1       Running   0          53s
   ```



![image](/images/routes.png?raw=true)

The Kubernetes Application Navigator shows your installed applications:

![image](/images/applications.png?raw=true)



![image](/images/components.png?raw=true)

The Kubernetes Application Navigator icon appears in the OKD Service Catalog:

![image](/images/kappnav_in_okd_console.png?raw=true)

To add existing applications, or create a new application, follow the instructions for [How to Create Applications](how-to-create-applications.md).


   ```
   # kubectl delete -f operator/kappnav-delete-CR.yaml -n kappnav --now
   kappnav.charts.helm.k8s.io "instance" deleted
   ```

   ```
   # kubectl delete -f operator/kappnav-delete.yaml -n kappnav
   deployment.apps "helm-operator" deleted
   clusterrolebinding.rbac.authorization.k8s.io "helm-operator" deleted
   clusterrole.rbac.authorization.k8s.io "helm-operator" deleted
   serviceaccount "helm-operator" deleted
   customresourcedefinition.apiextensions.k8s.io "kappnavs.charts.helm.k8s.io" deleted
   ```

   ```
   # kubectl delete namespace kappnav
   namespace "kappnav" deleted
   ```


```
minikube service kappnav-ui-service -n kappnav --format "http://{{.IP}}:{{.Port}}/kappnav-ui"
```


