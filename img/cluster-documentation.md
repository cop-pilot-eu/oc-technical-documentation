# 🚀 COP-PILOT Clusters' Documentation
This document helps clusters navigate the available documentation,
automation scripts, and CI/CD pipelines required to connect to the
COP-PILOT platform and use its services.

------------------------------------------------------------------------

# 🟣 SIF Layer

## 🔐 CloudZiti Access (Secure Networking)

For the COP-PILOT project, NetFoundry has deployed a CloudZiti
environment:

https://cop-pilot.cloudziti.io/

To gain access:

1.  Send an email to the NETC team.
2.  NETC will coordinate with the appropriate NetFoundry members.
3.  You will receive an invitation to join the CloudZiti network.

------------------------------------------------------------------------

## 🔗 Connecting Your VM to the Ziti Network

To allow your VM (e.g., hosting OpenSlice) to communicate securely with
the rest of the platform (e.g., Multi-Domain Orchestrator via SIF), an
automation script has been created.

Repository location:
https://github.com/cop-pilot-eu/platform-integration-pipelines/tree/main/sif-layer-pipelines/identity-script

A README file with usage instructions is included in the repository.  

For your OpenSlice instance identity, please use a name in the following format: openslice-clx-company

Where clx is your cluster number and company is your company name. Example: openslice-cl2-upv

------------------------------------------------------------------------

# 🔵 DO Layer (Domain Orchestrator -- OpenSlice)

## 📦 OpenSlice Installation

Official documentation:
https://osl.etsi.org/documentation/2025Q4/getting_started/deployment/kubernetes/

------------------------------------------------------------------------

## 🌐 Recommended Ingress Configuration (Kubernetes Deployment)

For consistency with the secure networking setup of COP-PILOT (OpenZiti,
etc.), it is recommended to:

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

# 🟡 ESO Layer (Hyper Orchestrator Peering)

For the peering with the [HypO](https://portal.multi-domain-orchestrator.cop-pilot.rid-intrasoft.eu/), pipelines have been created to automate the process.  

To execute the pipeline and peer Maestro with your OpenSlice instance, please ensure that an account is available for HypO to log in to your OpenSlice instance (OpenSlice Realm) with administrator privileges, using the following credentials:  

- Username: maestro  
- Password: maestro  

First, a pipeline is available that automatically creates a service to expose your OpenSlice instance in CloudZiti. A second pipeline handles the peering of your OpenSlice instance with the Multi-Domain Orchestrator.  

Once your OpenSlice (OS) instance is deployed, you can contact the NETC team to coordinate the execution of these pipelines.  

Pipeline documentation:

OpenSlice Service Creation:
https://github.com/cop-pilot-eu/platform-integration-pipelines/blob/main/sif-layer-pipelines/OpenSlice-service-creation.md

Peering Pipeline:
https://github.com/cop-pilot-eu/platform-integration-pipelines/blob/main/service-orchestrator-pipelines/peering/README.md

------------------------------------------------------------------------

# 🟢 Data Management Layer
There is also a need for context brokers. For this purpose, we have created pipelines that can automatically deploy a context broker to a dedicated VM

## 🧠 Context Broker Deployment

This is the Pipelines for automates deployment of the Context Brokers:
https://github.com/cop-pilot-eu/platform-integration-pipelines/tree/main/data-management-pipelines/orion-context-broker

------------------------------------------------------------------------

## ⚙️ Jenkins Agent Setup
To run these pipelines for context brokers, you must first follow this documentation to connect your VM to the COP-PILOT CI/CD services. Once connected, Jenkins can deploy the context broker on your VM.  
Documentation:
https://github.com/cop-pilot-eu/cicd-documentation/blob/main/install-jenkins-agent-documentation.md

Once connected, Jenkins can automatically deploy the Context Broker to
your VM.

------------------------------------------------------------------------

For coordination or assistance, contact the NETC team.
