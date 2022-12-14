# Requirement
- Ubuntu >= 20.04
- docker
- microk8s

# Deploy on [microk8s](https://microk8s.io/)

```bash
# Install docker first
sudo apt update
sudo apt install docker.io -y
# Add current to docker group in order to run docker command without root privilege
sudo usermod -aG docker "$USER"

# Install and set up MicroK8s
sudo snap install microk8s --classic
# Join the microk8s group
sudo usermod -a -G microk8s "$USER"
sudo chown -f -R "$USER" ~/.kube
newgrp microk8s # or reboot
```

```bash
# Enable addons
microk8s enable dns ingress storage registry helm3 rbac gpu openebs
microk8s status --wait-ready > /dev/null
microk8s enable metallb:10.0.0.100-10.0.0.200
```

```bash
# Enable iscsid
sudo systemctl enable iscsid.service
```

```bash
# Update project dependencies
microk8s helm3 dep up ./helm-chart
```

```bash
# Deploy our project
microk8s helm3 install taiao ./helm-chart --render-subchart-notes -f ./deployment/microk8s/config.yaml
```

You can access the platform through 10.0.0.100 with you browser now. The username and password are the same as your ubuntu account currently using. 

# Known issues
- [Python has to be 3.7 both in host and docker](https://issues.apache.org/jira/browse/FLINK-22517)
- [Numpy should not be 1.9](https://stackoverflow.com/questions/20518632/importerror-numpy-core-multiarray-failed-to-import)
- For microk8s on bare metal with Ubuntu, enable traffic forwarding with:

    ``` sudo iptables -P FORWARD ACCEPT ```
