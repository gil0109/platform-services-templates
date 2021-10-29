# Crunchy DB PGO Monitoring

Use this to install the Crunchy DB Monitoring stack into your tools namespace to monitor all your PGO instances.

```bash
kustomize build . -o out.yaml
sed -i 's/CHANGEME/your-namespace/' out.yaml
```
