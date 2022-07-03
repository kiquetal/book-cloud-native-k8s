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


#### Creating Config Files From ConfigMaps

```yaml
  apiVersion: v1
  kind: ConfigMap
  metadata:
    name: demo-config
  data:
   config: |
     greeting: Buongiono

 ```

#### Using ConfigMap

```yaml
spec:
  containers:
    - name: demo
      image: cloudnatived/demo:hello-config-file
      ports:
        - containerPort: 8888
      volumeMounts:
      - mountPath: /config/
        name: demo-config-volume
        readOnly: true
  volumes:
  - name: demo-config-volume
    configMap:
      name: demo-config
      items:
      - key: config
        path: demo.yaml
```        

#### Kubernetes Secrets: Used for store sensitive data.

```yaml
  apiVersion: v1
  kind: Secret
  metadata:
    name: demo-secret
  stringData:
    magicWord: xyzzy
```
#### Using secrets within container

```yaml
  spec:
    containers:
      - name: demo
        image: cloudnative/demo-hello-secret-env
        ports:
          - containerPort: 8088
        env:
          - name: MAGIC_WORD
            valueFrom: 
              secretKeyRef:
                name: demo-secret
                key: magicWord
```






