# Revisiting 
I revisted the course problem [4.02](https://devopswithkubernetes.com/part-4/1-update-strategies-and-prometheus), this time using kind instead of k3d.

# Local Kubernetes using kind
Create a volume:
```
docker volume create kind-data
```
Double check the mountpoint is the same as declared on [kind-config.yaml](./kind-config.yaml):
```
docker volume inspect kind-data
```
The cluster can be created with:
```
kind create cluster --config kind-config.yaml
```
Install the patched nginx ingress controller:
```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/kind/deploy.yaml
```
Wait until it is ready:
```
kubectl wait --namespace ingress-nginx \
  --for=condition=ready pod \
  --selector=app.kubernetes.io/component=controller \
  --timeout=90s
```
Verify the its status:
```
kubectl get pods -A
```
Which should output something like:
```
NAMESPACE            NAME                                         READY   STATUS      RESTARTS   AGE
ingress-nginx        ingress-nginx-admission-create-gw4kn         0/1     Completed   0          42s
ingress-nginx        ingress-nginx-admission-patch-jtz2k          0/1     Completed   0          42s
ingress-nginx        ingress-nginx-controller-b7b74c7b7-k74j2     1/1     Running     0          42s
```
## Step-by-step deployment
1. Create the namespace
```
kubectl apply -f dependencies/namespace.yaml
```
2. Add the helm chart (if not yet):
```
helm repo add sealed-secrets https://bitnami-labs.github.io/sealed-secrets
```
And install SealedSecret CRD, server-side controller into kube-system namespace:
```
helm install sealed-secrets --namespace kube-system --version 1.16.1 sealed-secrets/sealed-secrets
```
3. Generate the sealed secret:
```
kubeseal --controller-namespace kube-system \
    --controller-name sealed-secrets \
    -o yaml <secret/secret.yaml> secret/sealedsecret.yaml
```
Apply it so that it becomes available for usage in the cluster:
```
kubectl apply -f secret/sealedsecret.yaml
```
Check it is available:
```
kubectl get secrets -n=project-namespace
```
4. Deploy the dependencies with kustomize:
```
kubectl apply -k dependencies/.
```
## Step-by-step deployment
1. Make sure the dependencies are ready and only then deploy the project:
```
kubectl apply -k manifests/.
```
2. Monitor the deployment state:
```
watch -n 1 "kubectl get po -n=project-namespace"
```

Once the pod is on running state, open http://localhost:8081 to view it in the browser.