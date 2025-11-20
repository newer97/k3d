

# k3s oci cluster

### first master
```bash
MYSECRET=iambatman
curl -fL https://get.k3s.io | K3S_TOKEN=${MYSECRET} \
    sh -s - --disable traefik server
```

## initialize argocd

```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```


### add private repo 

```bash
argocd repocreds add https://github.com/newer97/k3s-oci --username newer97 --password accesstoken
```


### apply app-of-apps
```bash
argocd app create -f app-of-apps.yml
```

### cf token ( for certs )

```bash
kubectl create secret generic cloudflare-api-token-secret --from-literal=api-token='<API Token>' -n cert-manager

kubectl create namespace letsencrypt-wildcard-cert
```

we use the cf token for the cert (cert-manager, cloudflare-ddns, external-dns) namespaces
