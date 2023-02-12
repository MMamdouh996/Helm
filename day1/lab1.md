# HELM

## Lab1

## 1-Add bitnami helm chart repository in the controlplane node

```bash
controlplane $ helm repo add my-repo https://charts.bitnami.com/bitnami
"my-repo" has been added to your repositories
```

```bash
controlplane $ helm repo list
NAME    URL                               
my-repo https://charts.bitnami.com/bitnami
```

## 2-Deploy the Apache application on the cluster using the apache from the bitnami repository. Set the release Name to: amaze-surf

```bash
controlplane $ helm install amaze-surf my-repo/apache
NAME: amaze-surf
LAST DEPLOYED: Sun Feb 12 10:52:09 2023
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
CHART NAME: apache
CHART VERSION: 9.2.15
APP VERSION: 2.4.55

** Please be patient while the chart is being deployed **

1. Get the Apache URL by running:

** Please ensure an external IP is associated to the amaze-surf-apache service before proceeding **
** Watch the status using: kubectl get svc --namespace default -w amaze-surf-apache **

  export SERVICE_IP=$(kubectl get svc --namespace default amaze-surf-apache --template "{{ range (index .status.loadBalancer.ingress 0) }}{{ . }}{{ end }}")
  echo URL            : http://$SERVICE_IP/


WARNING: You did not provide a custom web application. Apache will be deployed with a default page. Check the README section "Deploying your custom web application" in https://github.com/bitnami/charts/blob/main/bitnami/apache/README.md#deploying-a-custom-web-application.
```

```bash
controlplane $ helm list
NAME            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART           APP VERSION
amaze-surf      default         1               2023-02-12 10:52:09.364599188 +0000 UTC deployed        apache-9.2.15   2.4.55 
```

## 3-Uninstall the apache chart release  from the cluster

```bash
controlplane $ helm uninstall amaze-surf
release "amaze-surf" uninstalled
```

## 4- install specfic version of nginx 1.22.0, then update it to specfic 1.23.1 , then rollback

```bash
controlplane $ helm search repo nginx --versions
NAME                                    CHART VERSION   APP VERSION     DESCRIPTION                                       
my-repo/nginx                           13.2.23         1.23.3          NGINX Open Source is a web server that can be a...
my-repo/nginx                           13.2.22         1.23.3          NGINX Open Source is a web server that can be a...
my-repo/nginx                           13.2.21         1.23.3          NGINX Open Source is a web server that can be a...
my-repo/nginx                           13.2.20         1.23.3          NGINX Open Source is a web server that can be a...
.... # etc
```

```bash
controlplane $ helm install test-release my-repo/nginx --version 12.0.1
NAME: test-release
LAST DEPLOYED: Sun Feb 12 10:58:36 2023
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
CHART NAME: nginx
CHART VERSION: 12.0.1
APP VERSION: 1.22.0

** Please be patient while the chart is being deployed **
NGINX can be accessed through the following DNS name from within your cluster:

    test-release-nginx.default.svc.cluster.local (port 80)

To access NGINX from outside the cluster, follow the steps below:

1. Get the NGINX URL by running these commands:

  NOTE: It may take a few minutes for the LoadBalancer IP to be available.
        Watch the status with: 'kubectl get svc --namespace default -w test-release-nginx'

    export SERVICE_PORT=$(kubectl get --namespace default -o jsonpath="{.spec.ports[0].port}" services test-release-nginx)
    export SERVICE_IP=$(kubectl get svc --namespace default test-release-nginx -o jsonpath='{.status.loadBalancer.ingress[0].ip}')
    echo "http://${SERVICE_IP}:${SERVICE_PORT}"

```

```bash
controlplane $ helm upgrade test-release my-repo/nginx --version 13.1.3
Release "test-release" has been upgraded. Happy Helming!
NAME: test-release
LAST DEPLOYED: Sun Feb 12 11:01:24 2023
NAMESPACE: default
STATUS: deployed
REVISION: 2
TEST SUITE: None
NOTES:
CHART NAME: nginx
CHART VERSION: 13.1.3
APP VERSION: 1.23.1

** Please be patient while the chart is being deployed **
NGINX can be accessed through the following DNS name from within your cluster:

    test-release-nginx.default.svc.cluster.local (port 80)

To access NGINX from outside the cluster, follow the steps below:

1. Get the NGINX URL by running these commands:

  NOTE: It may take a few minutes for the LoadBalancer IP to be available.
        Watch the status with: 'kubectl get svc --namespace default -w test-release-nginx'

    export SERVICE_PORT=$(kubectl get --namespace default -o jsonpath="{.spec.ports[0].port}" services test-release-nginx)
    export SERVICE_IP=$(kubectl get svc --namespace default test-release-nginx -o jsonpath='{.status.loadBalancer.ingress[0].ip}')
    echo "http://${SERVICE_IP}:${SERVICE_PORT}"

```
```bash
controlplane $ helm history test-release   
REVISION        UPDATED                         STATUS          CHART           APP VERSION     DESCRIPTION     
1               Sun Feb 12 10:58:36 2023        superseded      nginx-12.0.1    1.22.0          Install complete
2               Sun Feb 12 11:01:24 2023        deployed        nginx-13.1.3    1.23.1          Upgrade complete
```
```bash
controlplane $ helm rollback test-release 1
Rollback was a success! Happy Helming!
```
``bash
controlplane $ helm history test-release
REVISION        UPDATED                         STATUS          CHART           APP VERSION     DESCRIPTION     
1               Sun Feb 12 10:58:36 2023        superseded      nginx-12.0.1    1.22.0          Install complete
2               Sun Feb 12 11:01:24 2023        superseded      nginx-13.1.3    1.23.1          Upgrade complete
3               Sun Feb 12 11:03:03 2023        deployed        nginx-12.0.1    1.22.0          Rollback to 1  
```