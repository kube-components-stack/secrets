# secrets

## declare vars and create directory structure and files
```zsh
addon=minio
cluster=kind
namespace=minio
secrets=(bucket-s3)

mkdir -p cluster-addons/$addon/overlays/$cluster-{dev,prod}/secrets
touch cluster-addons/$addon/overlays/$cluster-{dev,prod}/kustomization.yaml
for s in $secrets; do touch cluster-addons/$addon/overlays/$cluster-{dev,prod}/secrets/${s}.yaml; done

cat << EOF > cluster-addons/$addon/overlays/$cluster-{dev,prod}/kustomization.yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
namespace: $namespace
EOF

for s in $secrets; do 
cat << EOF > cluster-addons/$addon/overlays/$cluster-{dev,prod}/secrets/${s}.yaml
apiVersion: builtin
kind: SecretGenerator
metadata:
  name: ${s}
options:
  disableNameSuffixHash: true
literals:
  - foo=bar
EOF
done

for e in dev prod; do cd cluster-addons/$addon/overlays/$cluster-${e}; kustomize edit add generator secrets/*.yaml; cd $OLDPWD; done
```