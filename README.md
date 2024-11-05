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
