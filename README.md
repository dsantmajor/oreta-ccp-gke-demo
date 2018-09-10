# Kubernetes MultiCluster Deployment

## Deploy Oretademosite Across Clusters
1. Install oretademo-site on the first cluster. 

```
kubectl config use-context gke_vantage2018-gke_australia-southeast1-a_oreta-gke

kubectl apply -f pods/app/namespace/oretademosite-namespace.yaml

kubectl apply -f <(istioctl kube-inject -f pods/app/oretademosite-app.yaml)

kubectl apply -f <(istioctl kube-inject -f pods/app/networking/oretademosite-gateway.yaml)
```

2. Remove the oretademosite-v3 deployment to deploy on remote:

```
kubectl delete deployment reviews-v3
```
3. Install the oretademosite-v3 deployment on the remote.

```
kubectl config use-context kubernetes-admin@kubernetes
kubectl apply -f pods/app/namespace/oretademosite-namespace.yaml
kubectl apply -f <(istioctl kube-inject -f pods/app/oretademosite-v3-app.yaml)
```
4. Get the istio-ingressgateway service’s external IP to access the oretademoaite to validate that Istio is including the remote’s oretademosite-v3 instance in the load balancing of app versions between on-prem and cloud:

```
$ kubectl get svc istio-ingressgateway -n istio-system
```

Access http://<GATEWAY_IP>/ repeatedly and each version of app should be equally loadbalanced, including oretademosite-v3  in the remote cluster (dark theme). It may take several accesses (dozens) to demonstrate the equal loadbalancing between the app versions.