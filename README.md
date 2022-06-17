Prerequisites:


Create a test cluster

```
kind create cluster --config kind.yaml
```

```
helm repo add k8s_gateway https://ori-edge.github.io/k8s_gateway/
helm repo add metallb https://metallb.github.io/metallb
helm repo add istio https://istio-release.storage.googleapis.com/charts
```

Install `metallb`:

```
helm install metallb metallb/metallb -f values.yaml
```

Install GatewayCRD (standard support for TCPRoute and HTTPRoute resources):

```
kubectl kustomize "github.com/kubernetes-sigs/gateway-api/config/crd?ref=v0.4.0" | kubectl apply -f -
```

Install `k8s_gateway`:

```
helm install exdns -f dns-values-http.yml k8s_gateway/k8s-gateway
```

Update internal CoreDNS to forward traffic to local plugin
```
k apply -f coredns-config.yaml
```

Install Istio (GatewayAPI controller):

```
kubectl create namespace istio-system
helm install istio-base istio/base -n istio-system
helm install istiod istio/istiod -n istio-system 
kubectl create namespace istio-ingress
helm install istio-ingress istio/gateway -n istio-ingress
```



Create a registry

```
kubectl apply -f registry.yaml
```

```
kubectl apply -f node.yaml
```



---

dns-sd -B  _nmos-register._tcp

dns-sd -B  _nmos-register._tcp nmos.tv

# create a nodeport to access this port
curl http://127.0.0.1:8010
["admin/","log/","schemas/","settings/","x-dns-sd/","x-nmos/"]

{
  "pri": 10, # set to 4,294,967,295
  "logging_level": 0,
  "http_trace": false,
  "label": "nmos-cpp-registry",
  "http_port": 8010,
  "query_ws_port": 8011,
  "registration_expiry_interval": 12
}



new yaml with

    - name: RUN_NODE
      value: "true"

    https://github.com/sony/nmos-cpp/blob/master/Development/nmos-cpp-registry/config.json

  override the default domain 

      // domain [registry, node]: the domain on which to browse for services or an empty string to use the default domain (specify "local." to explictly select mDNS)
    //"domain": "nmos.tv",



 b._dns-sd._udp	IN	PTR	@
    lb._dns-sd._udp	IN	PTR	@
_services._dns-sd._udp.nmos.tv PTR

https://github.com/sony/nmos-cpp/blob/master/Development/nmos-cpp-node/config.json