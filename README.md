

# k3s oci cluster

### first master
```bash
curl -sfL https://get.k3s.io | sh -s - 	--flannel-backend=none 	--disable-kube-proxy --disable servicelb  --disable-network-policy  --disable traefik  --cluster-init

```


```bash
CILIUM_CLI_VERSION=$(curl -s https://raw.githubusercontent.com/cilium/cilium-cli/main/stable.txt)
CLI_ARCH=amd64
if [ "$(uname -m)" = "aarch64" ]; then CLI_ARCH=arm64; fi
curl -L --fail --remote-name-all https://github.com/cilium/cilium-cli/releases/download/${CILIUM_CLI_VERSION}/cilium-linux-${CLI_ARCH}.tar.gz{,.sha256sum}
sha256sum --check cilium-linux-${CLI_ARCH}.tar.gz.sha256sum
sudo tar xzvfC cilium-linux-${CLI_ARCH}.tar.gz /usr/local/bin
rm cilium-linux-${CLI_ARCH}.tar.gz{,.sha256sum}
```

NOTE: in oci (oracle cloud) we need to run theese. [REF](https://github.com/k3s-io/k3s/issues/977#issuecomment-552504848)

```bash
iptables -I INPUT 3 -s 10.42.0.0/16 -j ACCEPT
iptables -I INPUT 3 -d 10.42.0.0/16 -j ACCEPT
```


```bash
API_SERVER_IP="10.0.0.65" # the ip of the node
API_SERVER_PORT=6443

cilium install --set kubeProxyReplacement=true --set k8sServiceHost=${API_SERVER_IP} --set k8sServicePort=${API_SERVER_PORT} --set ipam.operator.clusterPoolIPv4PodCIDRList="10.42.0.0/16" --helm-set=operator.replicas=1

cilium status --wait
```

## initialize argocd

```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```
SEE: [Crossplane with Argo CD](https://docs.crossplane.io/latest/guides/crossplane-with-argo-cd/)

### add private repo 

```bash
argocd repocreds add https://github.com/newer97/k3d --username newer97 --password accesstoken
```


### apply app-of-apps
```bash
argocd app create -f app-of-apps.yml
```
#one password
kubectl create secret generic onepassword-connect-token --from-literal=token='<API Token>' -n external-secrets



#cellum hubble
```bash
cilium hubble enable --ui
```