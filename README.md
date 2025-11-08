

# k3s cluster

### first master
```bash
MYSECRET=naser1945
curl -fL https://get.k3s.io | K3S_TOKEN=${MYSECRET} sh -s - server --cluster-init --disable traefik --disable servicelb --node-external-ip 192.168.1.210
```
### other masters
```bash
MYSECRET=naser1945
curl -fL https://get.k3s.io | K3S_TOKEN=${MYSECRET} \
    sh -s - server --disable servicelb --server https://192.168.1.210:6443
```

## initialize argocd

```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```


