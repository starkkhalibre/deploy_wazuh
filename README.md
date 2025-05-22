# deploy_wazuh

## Prerequisites

- Python3
- Python virtual environment
- kubectl
- ansible
- minikube for (local testing)

## Setup

### Setup python virtual environment

```console
python3 -m venv .venv
```
### Activate virtual environment

```console
source .venv/bin/activate
```

### Install required packages

```console
pip install -r requirements.txt
```
### Generate local certificate (before deploy)
#### Generate wazuh dashboard certs
```console
./ansible/cert/dashboard_http/generate_cert.sh 
```
#### Generate wazuh indexer certs
```console
./ansible/cert/indexer_cluster/generate_cert.sh 
```
## Deploy to minikube
Notice: At first time deployment comment out these volumes in wazuh-master-sts.yml.j2 and wazuh-worker-sts.yml.j2. After complete first initialzed uncomment it and redeploy.

```
---
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

### Run ansible playbook 
```console
ansible-playbook -i inventory.yml playbook.yml 
```

## Storageclass 
Notice: This storageclass is for minikube

```
---
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: wazuh-storage
  namespace: wazuh
provisioner: k8s.io/minikube-hostpath
```


