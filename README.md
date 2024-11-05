# Local setup of a kubernetes infrastructure

Note: All the procedures has been tested on Mac using
- Docker desktop
- [k3d](https://k3d.io/) 
- Helm
- Traefik

### Local docker registry

Create a new docker registry locally
```bash
sh docker-registry.sh
```

Edit your /etc/hosts/ file
```bash
127.0.0.1 registry.local
```

Test the registry
```bash
docker pull containous/whoami
docker tag  containous/whoami registry.local:5000/containous/whoami:latest
docker push registry.local:5000/containous/whoami:latest
```

### Create k3d cluster

Create a new k3d cluster using the command below. It will install the cluster without (Traefik)[https://github.com/traefik/traefik-helm-chart] that we will deploy in the next step.

```bash
k3d cluster create $cluster_name \
  --k3s-arg "--no-deploy=traefik@server:0" \
  --port "80:32080@loadbalancer" \
  --port "443:32443@loadbalancer" \
  --port "9000:32090@loadbalancer" \
  --port "9042:32091@loadbalancer" \
  --port "9142:32092@loadbalancer" \
  --port "8880:30080@loadbalancer" \
  --port "8881:30081@loadbalancer" \
  --port "8882:30082@loadbalancer"
```

Use the Traefik Helm values file `traefik.values.yaml` to install. 

```bash
helm repo add traefik https://helm.traefik.io/traefik
helm repo update
helm install traefik traefik/traefik -n traefik --create-namespace -f traefik.values.yaml
``` 

To make sure that the deployment is running 
```bash
kubectl get svc -n traefik
```
To test the Traefik deployment, apply the `test-k3d-traefik.yaml` and run the following command
```bash
curl -k https://localhost:80/whoami/
```
