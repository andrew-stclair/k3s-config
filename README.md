# Install k3s on a master node
curl -sfL https://get.k3s.io | sh -

# Install rancher
`curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3`

`chmod 700 get_helm.sh`

`./get_helm.sh`

`helm repo add rancher-latest https://releases.rancher.com/server-charts/latest`

`helm repo update`

`kubectl create namespace cattle-system`

`kubectl apply --validate=false -f https://github.com/jetstack/cert-manager/releases/download/v1.5.4/cert-manager.crds.yaml`

`kubectl create namespace cert-manager`

`helm repo add jetstack https://charts.jetstack.io`

`helm repo update`

`cp /etc/rancher/k3s/k3s.yaml ~/.kube/config`

`helm install cert-manager jetstack/cert-manager --namespace cert-manager --version v1.5.4`

`helm install rancher rancher-latest/rancher --namespace cattle-system --set hostname=rancher.lan`

`kubectl -n cattle-system rollout status deploy/rancher`

### rancher.lan should point to your master node or if you have multiple master nodes, to a seperate nginx server acting as a load balancer for all your master nodes.

# Install Longhorn
long horn is a Provisioned Volume manager that syncs volumes across nodes so pods always have access to their data.
Using Rancher ui, browse to apps and search for Longhorn.
When installing make sure to set Default Sturage Class to True, Replica count to 3 or the number of nodes you have if less.
Click install.

# Join worker nodes
curl -sfL https://get.k3s.io | K3S_URL=https://myserver:6443 K3S_TOKEN=mynodetoken sh -