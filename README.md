# helm-ingress-nginx-example

ingress-nginx is an Ingress controller for Kubernetes using NGINX as a reverse proxy and load balancer.

You can modify it and use ingress-nginx. There is example about installing it with Helm in the article.

## Prerequisites

- [Kubernetes(K8S)](https://kubernetes.io/)
    Kubernetes (K8s) is an open-source system for automating deployment, scaling, and management of containerized applications.

- [Helm](https://helm.sh/)
    Helm is the best way to find, share, and use software built for Kubernetes.

## How to Install

```shell
# git clone example.
$ git clone --recursive https://github.com/CloudoLife/helm-ingress-nginx-example

$ cd helm-ingress-nginx-example
```

### Custom Values.yaml

Edit [values.yaml](./values.yaml) in helm-ingress-nginx-example directory, and replace content within < and >.

How to expose Ingress Nginx controller pod outside Kuberneters(K8S):
- use hostNetwork to expose Ingress Nginx controller pod.
- run Ingress Nginx controller as a DaemonSet.
- specify nodeSelector with Your Node Host Name or Label.

```shell
# cat values.yaml

# ingress-nginx/values.yaml at master · kubernetes/ingress-nginx
# https://github.com/kubernetes/ingress-nginx/blob/master/charts/ingress-nginx/values.yaml

# helm install stable/ingress-nginx --set controller.hostNetwork=true,controller.service.type="",controller.kind=DaemonSet
# https://medium.com/@craignewtondev/how-to-fix-kubernetes-namespace-deleting-stuck-in-terminating-state-5ed75792647e

## nginx configuration
## Ref: https://github.com/kubernetes/ingress/blob/master/controllers/nginx/configuration.md
##
controller:

  # Required for use with CNI based kubernetes installations (such as ones set up by kubeadm),
  # since CNI and hostport don't mix yet. Can be deprecated once https://github.com/kubernetes/kubernetes/issues/23920
  # is merged
  hostNetwork: true

  # DaemonSet or Deployment
  #
  kind: DaemonSet

  service:

    type: "ClusterIP"

  dnsPolicy: "ClusterFirstWithHostNet"

  # ## Node tolerations for server scheduling to nodes with taints
  # ## Ref: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
  # ##
  # tolerations:
  #   - operator: "Exists"

  # Node labels for default backend pod assignment
  # Ref: https://kubernetes.io/docs/user-guide/node-selection/
  #
  nodeSelector:
    kubernetes.io/hostname: <Your Node Host Name or Label> 

  # affinity:
  #   podAntiAffinity:
  #       requiredDuringSchedulingIgnoredDuringExecution:
  #       - labelSelector:
  #           matchExpressions:
  #           - key: app
  #             operator: In
  #             values:
  #             - ingress-nginx
  #           - key: component
  #             operator: In
  #             values:
  #             - controller
  #         topologyKey: kubernetes.io/hostname
  # tolerations:
  #     - key: node-role.kubernetes.io/master
  #       operator: Exists
  #       effect: NoSchedule
  #     - key: node-role.kubernetes.io/master
  #       operator: Exists
  #       effect: PreferNoSchedule
# ## Default 404 backend
# ##
# defaultBackend:
#   ##
#   enabled: true

#   tolerations:
#     - operator: "Exists"

#   nodeSelector:
#     kubernetes.io/hostname: atyun-centos-k8s200.servers.atyun.com.cn

  admissionWebhooks:
    enabled: false
```

### Install by Helm

Helm install ingress-nginx within ingress-nginx namespace. Please create ingress-nginx namespace first if not exist.

```shell
# Add the Jetstack Helm repository:
$ helm repo add jetstack https://charts.jetstack.io

# Update your local Helm chart repository cache:
$ helm repo update

# To install the ingress-nginx Helm chart:
$ helm install ingress-nginx jetstack/ingress-nginx --namespace ingress-nginx -f values.yaml
```

See Helm release about ingress-nginx.

```shell
$ helm list -n ingress-nginx
NAME                       	NAMESPACE   	REVISION	UPDATED                               	STATUS  	CHART                            	APP VERSION
ingress-nginx	ingress-nginx	1       	2020-09-25 12:57:30.168666 +0800 +0800	deployed	webhook-alidns-0.1.0             	1.0
```

See pods about ingress-nginx.

```shell
$ kubectl get pods -n ingress-nginx
...
```

## References
[1] [CloudoLife/helm-ingress-nginx-example: Examples about Helm install Nginx Ingress. https://github.com/CloudoLife/helm-ingress-nginx-example - https://github.com/CloudoLife/helm-ingress-nginx-example](https://github.com/CloudoLife/helm-ingress-nginx-example)

[2] [Welcome - NGINX Ingress Controller - https://kubernetes.github.io/ingress-nginx/](https://kubernetes.github.io/ingress-nginx/)

[3] [kubernetes/ingress-nginx: NGINX Ingress Controller for Kubernetes - https://github.com/kubernetes/ingress-nginx](https://github.com/kubernetes/ingress-nginx)

[4] [Helm - https://helm.sh/](https://helm.sh/)

[5] [Kubernetes - https://kubernetes.io/](https://kubernetes.io/)