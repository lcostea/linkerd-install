# linkerd-install

Installation

``` sh
kubectl apply -f namespace.yaml

NAMESPACE=linkerd
SERVICE_NAME=linkerd

step certificate create root.linkerd.cluster.local root_ca.crt root_ca.key --profile root-ca --no-password --insecure --not-after 87600h

helm dep update chart

# echo $CA_CRT | base64 -d > root_ca.crt
# echo $CA_KEY | base64 -d > root_ca.key

kubectl create secret tls linkerd-trust-anchor --save-config --dry-run=client --cert="root_ca.crt" --key="root_ca.key" -o yaml | kubectl apply --namespace $NAMESPACE -f -

helm upgrade --install $SERVICE_NAME chart --namespace $NAMESPACE --set-file linkerd2.identityTrustAnchorsPEM=root_ca.crt --set linkerd2.identity.issuer.scheme=kubernetes.io/tls --debug
```