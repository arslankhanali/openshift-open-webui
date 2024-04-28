# To deploy on OpenShift 
Open webui container needs to run as a root. Most Kubernetes distributions do not let containers run as a root by default (which is a good security practice). If you are just looking to run open-webui in a non-prod environment, a simple solution can be to add an `anyuid` security context to `default` service account in your project.
```bash
# Login to Openshift
oc login --token=<> --server=<>
# Create a new project
oc new-project open-webui
# Create policy. [Not recommended in production]
oc adm policy add-scc-to-user anyuid -z default   
# Deploy the helm chart                  
helm install open-webui ./kubernetes/helm 
# Create route
oc create route edge --service open-webui 
# Get route
oc get routes.route.openshift.io open-webui -o json | jq -r '.spec.host' | sed 's/^/https:\/\//'
# To ROLLBACK!
helm uninstall open-webui
oc delete project open-webui
```
# Run locally on mac
```sh 
podman run -d -p 3000:8080 -e OLLAMA_BASE_URL=http://ollama-route-ollama.apps.cluster-882w8.dynamic.redhatworkshops.io -v open-webui:/app/backend/data --name open-webui --restart always ghcr.io/open-webui/open-webui:main

podman stop open-webui
podman start open-webui
```
### Settings > Connection > Give url of your ollama deployment
![ollamarunning](ollama/open-webui/images/ollama1.png)
https://ollama-open-webui.apps.cluster-882w8.dynamic.redhatworkshops.io:443

### Crate repo on quay.io and make it public before pushing
podman commit open-webui quay.io/arslankhanali/open-webui:latest
podman push quay.io/arslankhanali/open-webui:latests
