# Wazuh Kubernetes Deployment

Ansible-based deployment for Wazuh SIEM on Kubernetes with Minikube support.

## Prerequisites

- Python 3.8+
- kubectl
- Ansible 2.9+
- Minikube (for local testing)

## Quick Setup

### 1. Environment Setup
```bash
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

### 2. Generate Certificates
```bash
./ansible/cert/dashboard_http/generate_cert.sh 
./ansible/cert/indexer_cluster/generate_cert.sh 
```

### 3. Deploy to Minikube

**Important**: For first deployment, comment out these volume mounts in `wazuh-master-sts.yml.j2` and `wazuh-worker-sts.yml.j2`:

```yaml
# - name: writable-local-rules
#   mountPath: /var/ossec/etc/rules/local_rules.xml
#   subPath: local_rules.xml
# - name: local-internal-options
#   mountPath: /var/ossec/etc/local_internal_options.conf
#   subPath: local_internal_options.conf
#   readOnly: true
# - name: telegram-alert
#   mountPath: /var/ossec/integrations/custom-telegram.py
#   subPath: custom-telegram.py
#   readOnly: true
```

Run deployment:
```bash
minikube start --memory=6GB --cpus=6 --driver=docker
ansible-playbook -i inventory.yml playbook.yml 
```

After successful first deployment, uncomment the volumes and redeploy.

## Storage Configuration

Minikube StorageClass:
```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: wazuh-storage
  namespace: wazuh
provisioner: k8s.io/minikube-hostpath
```

## Access Dashboard

```bash
kubectl port-forward svc/wazuh-dashboard 5601:5601 -n wazuh
```

Navigate to `https://localhost:5601`

## Troubleshooting

Check pod status:
```bash
kubectl get pods -n wazuh
kubectl logs -f <pod-name> -n wazuh
```

For certificate issues, regenerate:
```bash
rm -rf ansible/cert/*/certs/
./ansible/cert/dashboard_http/generate_cert.sh
./ansible/cert/indexer_cluster/generate_cert.sh
```