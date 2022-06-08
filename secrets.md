### Install age

	apt get install age
### Install sops

	https://github.com/mozilla/sops/releases/download/v3.7.3/sops_3.7.3_amd64.deb

### Encrypt

	sops --age age1fea... --encrypt --in-place test.yaml

### Decrypt

	SOPS_AGE_KEY_FILE=$(pwd)/key.txt sops --decrypt test.yaml 

 
