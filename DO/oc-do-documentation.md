# 🔵 DO Layer (Domain Orchestrator -- OpenSlice)

## 📦 OpenSlice Installation

Official documentation:
https://osl.etsi.org/documentation/2025Q4/getting_started/deployment/kubernetes/

------------------------------------------------------------------------

## 🌐 Required Ingress Configuration (Kubernetes Deployment)

For consistency with the secure networking setup of COP-PILOT (OpenZiti,
etc.), it is required to:

-   Configure the Ingress Controller as a NodePort service
-   Operate over HTTP
-   Use NGINX Ingress

Example root URL:
```bash
http://<master-node-ip>:<nodeport>
```
------------------------------------------------------------------------

## ⚠️ Keycloak HTTPS Disable (Kubernetes Deployment)

To disable SSL enforcement:

```bash
kubectl exec -it `<keycloak_pod_name>` -n openslice -- /bin/sh

cd /opt/jboss/keycloak/bin/

./kcadm.sh config credentials --server http://localhost:8080/auth
--realm master --user admin --password <YOUR_ADMIN_PASS>
./kcadm.sh update realms/openslice -s sslRequired=NONE ./kcadm.sh update
realms/master -s sslRequired=NONE
```

------------------------------------------------------------------------

# 🧩 Application Deployment via Domain Orchestrator

Before creating your service specification, please navigate to the presentation on the project's [SharePoint](https://netcompany.sharepoint.com/:p:/r/sites/NCTMS.Project.RID.COP-PILOT/_layouts/15/Doc.aspx?sourcedoc=%7B09058744-E65E-458A-98F9-1D58135CC8B5%7D&file=COP-PILOT-WP3-Orch-Peering-M21-Review.pptx&wdLOR=cBD2898BF-D315-428B-99D8-BB4C21387730&action=edit&mobileredirect=true), which provides an overview of how you should name your Catalog in OpenSlice, as well as your service. Then, under the appropriate catalog for your cluster, please add your service specification and inform the NETC team to review the name with the Technical Coordinator, George Katsikas, in order to approve it if it is representative. Also provide the address at which you would like your OpenSlice instance to be displayed in the HypO UI on slide 7-8.


## ✅ Step 1 --- Validate CRIDGE Component

- Ensure the CRIDGE component is properly configured:
https://osl.etsi.org/documentation/latest/getting_started/deployment/kubernetes/#cridge  
- In case, you overlooked the CRIDGE step during the installation guide, it’s now time to validate that the Kubeconfig file of the Cluster (that will host the application) is provided.  
- Validate that CRIDGE works properly.
  Log in to your OpenSlice instance (as admin user), navigate to Resources -> Resources Specifications -> List Resource Specifications, and you should be able to see Kubernetes resources ending with @{your_cluster_master_node_IP}:6443/

------------------------------------------------------------------------

## 🚀 Step 2 --- Install ArgoCD

Guide:
https://osl.etsi.org/documentation/latest/service_design/kubernetes/design_helm_aas/

------------------------------------------------------------------------

## 📦 Step 3 --- Add COP-PILOT Helm Repository to ArgoCD

Guide:
https://argo-cd.readthedocs.io/en/stable/user-guide/private-repositories/#helm

- Once ArgoCD is installed, you need to log in to ArgoCD UI and add the [COP-PILOT Helm Chart Repository](https://harbor.cop-pilot.rid-intrasoft.eu) to the known repo list (guide). Project field should be “default”.
  
- Ιf you have any doubts about filling in the fields, try the command `helm registry login {registry name} –username {username} –password {password}` until it succeeds. Then input these values in the ArgoUI. In case of an OCI registry, remember to tick the box “Enable OCI”.

- Once you log in and upload/pull your Helm chart or image, you should use your CLI secret as the password when pulling or pushing. The CLI secret is available under your user profile in the top-right corner on the Harbor.
------------------------------------------------------------------------

## 🛒 Step 4 --- Order & Deploy the Service

-	Once the repo is added in ArgoCD and the Application resource is available in your OpenSlice instance (see Resource Specifications List), you are ready to design the Service, using the guide. The YAML definition should be identical with the guide, except for the source property (repoURL, targetRevision, chart, and helm.values).

-	When it comes to catalog exposure, you may edit what your domain exposes from the respective menus (Services -> Manage Services -> Service Catalogs & Service Categories). Your changes will be available at the Service Marketplace tab and your Service Catalog Explorer section.
o	See similar naming conventions (for Catalogs/Categories/Specifications) with other clusters (link in Slack Cluster 3A channel).

-	Once the application service specification is exposed, as you wish, you need to browse the Service Marketplace -> select the Specification -> Add to the Cart -> Checkout the Service Order. As admin (default user for you), you must navigate to the issued Service Order (Manage Services -> Service Orders -> click the last one). A new Order is always in the “INITIAL” state. The admin must click the “edit” button, change the state to “ACKNOWLEDGED” (and optionally the duration, if needed), and then the deployment is automated, based on your design. 

------------------------------------------------------------------------
