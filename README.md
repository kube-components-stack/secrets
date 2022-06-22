# secrets

## declare vars and create directory structure and files
```zsh
addon=argo-cd
cluster=kind
namespace=argocd
secrets=(argocd-notifications-secret argocd-secret grafana-secret)

mkdir -p cluster-addons/$addon/overlays/$cluster-{dev,prod}
touch cluster-addons/$addon/overlays/$cluster-{dev,prod}/kustomization.yaml
for s in $secrets; do touch cluster-addons/$addon/overlays/$cluster-{dev,prod}/${s}.secret; done

cat << EOF > cluster-addons/$addon/overlays/$cluster-{dev,prod}/kustomization.yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
namespace: $namespace
EOF

for s in $secrets; do 
cat << EOF > cluster-addons/$addon/overlays/$cluster-{dev,prod}/${s}.secret
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

for e in dev prod; do cd cluster-addons/$addon/overlays/$cluster-${e}; kustomize edit add generator *.secret; cd $OLDPWD; done
```