### Install age

	apt get install age
### Install sops

	https://github.com/mozilla/sops/releases/download/v3.7.3/sops_3.7.3_amd64.deb

### Encrypt

	sops --age age1fea... --encrypt --in-place test.yaml

### Decrypt

	SOPS_AGE_KEY_FILE=$(pwd)/key.txt sops --decrypt test.yaml 


### How to encrypt ssh-rsa

age -R file.pub -e file.yaml > file.enc



#### Create secrets


	# create the Kubernetes secret
	kubectl create secret generic demo \
    	--from-literal mysecret=secret_value \
    	-o yaml \
    	--dry-run=client > secret.encoded.yml

	# print the contents of secret.encoded.yml
	cat secret.encoded.yml

# apiVersion: v1
# data:
#   mysecret: c2VjcmV0X3ZhbHVl
# kind: Secret
# metadata:
#   creationTimestamp: null
#   name: demo


#### Encrypt part of the file

sops --encrypt --encrypted-regex '^(data|stringData)$' \
    --azure-kv $KEY_ID secret.encoded.yml > secret.encrypted.yml

#### Decrypt and apply on k8s

	sops --decrypt secret.encrypted.yml | kubectl apply -f -

