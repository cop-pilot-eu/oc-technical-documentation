# 🟡 ESO Layer (Hyper Orchestrator Peering)

For the peering with the [HypO](https://portal.multi-domain-orchestrator.cop-pilot.rid-intrasoft.eu/), pipelines have been created to automate the process.  

To execute the pipeline and peer HypO with your OpenSlice instance, please ensure that an account is available for HypO to log in to your OpenSlice instance (OpenSlice Realm) with administrator privileges, using the following credentials:  

- Username: oc-<your-username>
- Password: your-password  

First, a pipeline is available that automatically creates a service to expose your OpenSlice instance in CloudZiti. A second pipeline handles the peering of your OpenSlice instance with the Multi-Domain Orchestrator.  

Once your OpenSlice (OS) instance is deployed, you can contact the NETC team to coordinate the execution of these pipelines.  

Pipeline documentation:

OpenSlice Service Creation:
https://github.com/cop-pilot-eu/platform-integration-pipelines/blob/main/sif-layer-pipelines/OpenSlice-service-creation.md

Peering Pipeline:
https://github.com/cop-pilot-eu/platform-integration-pipelines/blob/main/service-orchestrator-pipelines/peering/README.md
