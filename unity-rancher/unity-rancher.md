https://rancher.opr.unity.com/ 
user: collins.afanwi 
zwMrIiiZWZKfgsJI

# LETS GO
```sh
## List the opr charts
helm ls -n opr-develop
## Export the Helm Chart Manifests
helm get manifest opr -n opr-develop > opr-chart.yaml
helm get manifest networkpolicy -n opr-develop > networkpolicy-chart.yaml

## Export the Helm Values Used in Deployment
helm get values opr -n opr-develop --all > opr-values.yaml
helm get values networkpolicy -n opr-develop --all > networkpolicy-values.yaml

## Verify the Current Format
cat opr-values.yaml | grep -A 5 "imagePullSecrets"


## Getting the secrets
kubectl get secrets -n opr-develop -o yaml > opr-secrets.yaml
kubectl get configmap -n opr-develop -o yaml > opr-configmaps.yaml
kubectl get ingress -n opr-develop -o yaml > opr-ingress.yaml



## reinstall - Convert YAML Back into a Helm Chart (If Original Chart is Lost

### Create a New Helm Chart Directory
helm create opr-new-chart
cd opr-new-chart
### Replace the templates/ Directory with Your Extracted YAML:
rm -rf templates/*
mv ../opr-chart.yaml templates/
### Package and Install the New Chart:
helm package .
helm install opr-new ./opr-new-chart-0.1.0.tgz -n opr-develop

```

