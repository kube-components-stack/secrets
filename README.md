# secrets

## declare vars and create directory structure and files
```zsh
addon=vector
cluster=kind
namespace=vector

mkdir -p cluster-addons/$addon/overlays/$cluster-{dev,prod}
touch cluster-addons/$addon/overlays/$cluster-{dev,prod}/kustomization.yaml

cat << EOF > cluster-addons/$addon/overlays/$cluster-{dev,prod}/kustomization.yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
namespace: $namespace
# kustomize edit add generator *.secret
EOF
```