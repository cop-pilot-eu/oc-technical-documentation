# 🟢 Data Management Layer

## Deploy Orion-LD with Docker Compose

This guide deploys Orion-LD (NGSI-LD) and MongoDB directly on a Linux host. It does not use Jenkins or the deployment pipeline.

### Prerequisites

- A Linux host with Docker Engine and the Docker Compose v2 plugin installed.
- Network access to pull `fiware/orion-ld` and `mongo` images.
- TCP port `1026` available for Orion-LD. Restrict access to this port using the host firewall as appropriate.

MongoDB is only accessible to the Orion-LD container on the private Compose network; its port is not published on the host.

### Start the services

From the repository's `DM` directory, run:

```bash
docker compose -f docker-compose.orionld.yml pull
docker compose -f docker-compose.orionld.yml up -d
```

Check the service status:

```bash
docker compose -f docker-compose.orionld.yml ps
```

Confirm Orion-LD responds:

```bash
curl http://localhost:1026/version
```

The NGSI-LD API is available at `http://<host>:1026/ngsi-ld/v1/`.

### Connect sensors

Orion-LD manages context data; it does not connect to physical sensors by itself. The Compose stack above only starts Orion-LD and MongoDB. To send sensor readings to the broker, use either:

- **An IoT Agent** to translate a device's protocol (such as HTTP or MQTT) into NGSI-LD. Start with the FIWARE tutorials for [IoT sensor concepts](https://ngsi-ld-tutorials.readthedocs.io/en/latest/iot-sensors.html), then follow [Provisioning the JSON IoT Agent](https://ngsi-ld-tutorials.readthedocs.io/en/latest/iot-agent-json.html). The tutorial's IoT Agent is a separate component and must be configured to use this Orion-LD instance.
- **Your own sensor application** to create and update sensor entities directly through the [NGSI-LD CRUD operations tutorial](https://ngsi-ld-tutorials.readthedocs.io/en/latest/ngsi-ld-operations.html). Configure the application to send requests to `http://<host>:1026/ngsi-ld/v1/` and use the NGSI-LD entity and attribute formats.

For Orion-LD project information, see the [FIWARE Orion-LD repository](https://github.com/FIWARE/context.Orion-LD). Do not confuse it with the [FIWARE Orion NGSI-v2 documentation](https://fiware-orion.readthedocs.io/en/latest/), which covers the separate NGSI-v2 broker.

### Operations

View logs:

```bash
docker compose -f docker-compose.orionld.yml logs -f
```

Stop and remove the containers and network while retaining MongoDB data:

```bash
docker compose -f docker-compose.orionld.yml down
```

To remove the MongoDB data as well, run `docker compose -f docker-compose.orionld.yml down --volumes`. This permanently deletes the persisted database.

The [Jenkins pipeline document](README-orionld_context_broker_deploy.md) describes the pipeline for reference only; it is not required for this deployment.
