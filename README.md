

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


### add private repo 

```bash
argocd repocreds add https://github.com/newer97/k3s-local-vms --username newer97 --password accesstoken
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

# VPN

### aerver public key

```bash
AGoxsjDofZrYrdN80nSP1XhlXTcSEzJ1uZbQnehMdWM=
```

## create new peer

```bash
wg genkey > client1_private_key
wg pubkey < client1_private_key > client1_public_key
```

- add client1_public_key to server config