# POC For Flipt Feature Flag with MySQL Database as Backend
## Hashicopr Vault



```
helm upgrade --install vault hashicorp/vault -n vault -f vault-values.yaml

kubectl exec vault-0 -- vault status

kubectl exec vault-0 -- vault operator init \
    -key-shares=1 \
    -key-threshold=1 \
    -format=json > cluster-keys.json


cat cluster-keys.json | jq -r ".unseal_keys_b64[]"
IIbnmaPQaGMMadwH5dGjO7YD4M9FEuufoPnIiPyGZq8=

VAULT_UNSEAL_KEY=$(cat cluster-keys.json | jq -r ".unseal_keys_b64[]")
kubectl exec vault-0 -- vault operator unseal $VAULT_UNSEAL_KEY


cat cluster-keys.json | jq -r ".root_token"
<YOUR_VAULT_ROOT-TOEKN>

CLUSTER_ROOT_TOKEN=$(cat cluster-keys.json | jq -r ".root_token")

kubectl exec vault-0 -- vault login $CLUSTER_ROOT_TOKEN
kubectl exec vault-0 -- vault operator raft list-peers


kubectl exec --stdin=true --tty=true vault-0 -- /bin/sh
vault auth enable kubernetes

export VAULT_ADDR='http://127.0.0.1:8200'
export VAULT_TOKEN="<YOUR_VAULT_ROOT-TOEKN>"

kubectl port-forward \
svc/vault 8200:8200 \
-n vault
```
