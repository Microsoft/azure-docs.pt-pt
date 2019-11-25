---
title: EdgeAgent and EdgeHub desired properties reference - Azure IoT Edge | Microsoft Docs
description: Review the specific properties and their values for the edgeAgent and edgeHub module twins
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/17/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 1ab45a6bde9ead69a7ea23dd095de84b8ff01334
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456700"
---
# <a name="properties-of-the-iot-edge-agent-and-iot-edge-hub-module-twins"></a>Properties of the IoT Edge agent and IoT Edge hub module twins

The IoT Edge agent and IoT Edge hub are two modules that make up the IoT Edge runtime. For more information about what duties each module performs, see [Understand the Azure IoT Edge runtime and its architecture](iot-edge-runtime.md). 

This article provides the desired properties and reported properties of the runtime module twins. For more information on how to deploy modules on IoT Edge devices, see [Learn how to deploy modules and establish routes in IoT Edge](module-composition.md).

A module twin includes: 

* **Desired properties**. The solution backend can set desired properties, and the module can read them. The module can also receive notifications of changes in the desired properties. Desired properties are used along with reported properties to synchronize module configuration or conditions.

* **Reported properties**. The module can set reported properties, and the solution backend can read and query them. Reported properties are used along with desired properties to synchronize module configuration or conditions. 

## <a name="edgeagent-desired-properties"></a>EdgeAgent desired properties

The module twin for the IoT Edge agent is called `$edgeAgent` and coordinates the communications between the IoT Edge agent running on a device and IoT Hub. The desired properties are set when applying a deployment manifest on a specific device as part of a single-device or at-scale deployment. 

| Propriedade | Descrição | Obrigatório |
| -------- | ----------- | -------- |
| schemaVersion | Has to be "1.0" | Sim |
| runtime.type | Has to be "docker" | Sim |
| runtime.settings.minDockerVersion | Set to the minimum Docker version required by this deployment manifest | Sim |
| runtime.settings.loggingOptions | A stringified JSON containing the logging options for the IoT Edge agent container. [Docker logging options](https://docs.docker.com/engine/admin/logging/overview/) | Não |
| runtime.settings.registryCredentials<br>.{registryId}.username | The username of the container registry. For Azure Container Registry, the username is usually the registry name.<br><br> Registry credentials are necessary for any module images that are not public. | Não |
| runtime.settings.registryCredentials<br>.{registryId}.password | The password for the container registry. | Não |
| runtime.settings.registryCredentials<br>.{registryId}.address | The address of the container registry. For Azure Container Registry, the address is usually *{registry name}.azurecr.io*. | Não |  
| systemModules.edgeAgent.type | Has to be "docker" | Sim |
| systemModules.edgeAgent.settings.image | The URI of the image of the IoT Edge agent. Currently, the IoT Edge agent is not able to update itself. | Sim |
| systemModules.edgeAgent.settings<br>.createOptions | A stringified JSON containing the options for the creation of the IoT Edge agent container. [Docker create options](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Não |
| systemModules.edgeAgent.configuration.id | The ID of the deployment that deployed this module. | IoT Hub sets this property when the manifest is applied using a deployment. Not part of a deployment manifest. |
| systemModules.edgeHub.type | Has to be "docker" | Sim |
| systemModules.edgeHub.status | Has to be "running" | Sim |
| systemModules.edgeHub.restartPolicy | Has to be "always" | Sim |
| systemModules.edgeHub.settings.image | The URI of the image of the IoT Edge hub. | Sim |
| systemModules.edgeHub.settings<br>.createOptions | A stringified JSON containing the options for the creation of the IoT Edge hub container. [Docker create options](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Não |
| systemModules.edgeHub.configuration.id | The ID of the deployment that deployed this module. | IoT Hub sets this property when the manifest is applied using a deployment. Not part of a deployment manifest. |
| modules.{moduleId}.version | A user-defined string representing the version of this module. | Sim |
| modules.{moduleId}.type | Has to be "docker" | Sim |
| modules.{moduleId}.status | {"running" \| "stopped"} | Sim |
| modules.{moduleId}.restartPolicy | {"never" \| "on-failure" \| "on-unhealthy" \| "always"} | Sim |
| modules.{moduleId}.imagePullPolicy | {"on-create" \| "never"} | Não |
| modules.{moduleId}.settings.image | The URI to the module image. | Sim |
| modules.{moduleId}.settings.createOptions | A stringified JSON containing the options for the creation of the module container. [Docker create options](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Não |
| modules.{moduleId}.configuration.id | The ID of the deployment that deployed this module. | IoT Hub sets this property when the manifest is applied using a deployment. Not part of a deployment manifest. |

## <a name="edgeagent-reported-properties"></a>EdgeAgent reported properties

The IoT Edge agent reported properties include three main pieces of information:

1. The status of the application of the last-seen desired properties;
2. The status of the modules currently running on the device, as reported by the IoT Edge agent; and
3. A copy of the desired properties currently running on the device.

This last piece of information, a copy of the current desired properties, is useful to tell whether the device has applied the latest desired properties or is still running a previous deployment manifest.

> [!NOTE]
> The reported properties of the IoT Edge agent are useful as they can be queried with the [IoT Hub query language](../iot-hub/iot-hub-devguide-query-language.md) to investigate the status of deployments at scale. For more information on how to use the IoT Edge agent properties for status, see [Understand IoT Edge deployments for single devices or at scale](module-deployment-monitoring.md).

The following table does not include the information that is copied from the desired properties.

| Propriedade | Descrição |
| -------- | ----------- |
| lastDesiredVersion | This integer refers to the last version of the desired properties processed by the IoT Edge agent. |
| lastDesiredStatus.code | This status code refers to the last desired properties seen by the IoT Edge agent. Allowed values: `200` Success, `400` Invalid configuration, `412` Invalid schema version, `417` the desired properties are empty, `500` Failed |
| lastDesiredStatus.description | Text description of the status |
| deviceHealth | `healthy` if the runtime status of all modules is either `running` or `stopped`, `unhealthy` otherwise |
| configurationHealth.{deploymentId}.health | `healthy` if the runtime status of all modules set by the deployment {deploymentId} is either `running` or `stopped`, `unhealthy` otherwise |
| runtime.platform.OS | Reporting the OS running on the device |
| runtime.platform.architecture | Reporting the architecture of the CPU on the device |
| systemModules.edgeAgent.runtimeStatus | The reported status of IoT Edge agent: {"running" \| "unhealthy"} |
| systemModules.edgeAgent.statusDescription | Text description of the reported status of the IoT Edge agent. |
| systemModules.edgeHub.runtimeStatus | Status of IoT Edge hub: { "running" \| "stopped" \| "failed" \| "backoff" \| "unhealthy" } |
| systemModules.edgeHub.statusDescription | Text description of the status of IoT Edge hub if unhealthy. |
| systemModules.edgeHub.exitCode | The exit code reported by the IoT Edge hub container if the container exits |
| systemModules.edgeHub.startTimeUtc | Time when IoT Edge hub was last started |
| systemModules.edgeHub.lastExitTimeUtc | Time when IoT Edge hub last exited |
| systemModules.edgeHub.lastRestartTimeUtc | Time when IoT Edge hub was last restarted |
| systemModules.edgeHub.restartCount | Number of times this module was restarted as part of the restart policy. |
| modules.{moduleId}.runtimeStatus | Status of the module: { "running" \| "stopped" \| "failed" \| "backoff" \| "unhealthy" } |
| modules.{moduleId}.statusDescription | Text description of the status of the module if unhealthy. |
| modules.{moduleId}.exitCode | The exit code reported by the module container if the container exits |
| modules.{moduleId}.startTimeUtc | Time when the module was last started |
| modules.{moduleId}.lastExitTimeUtc | Time when the module last exited |
| modules.{moduleId}.lastRestartTimeUtc | Time when the module was last restarted |
| modules.{moduleId}.restartCount | Number of times this module was restarted as part of the restart policy. |

## <a name="edgehub-desired-properties"></a>EdgeHub desired properties

The module twin for the IoT Edge hub is called `$edgeHub` and coordinates the communications between the IoT Edge hub running on a device and IoT Hub. The desired properties are set when applying a deployment manifest on a specific device as part of a single-device or at-scale deployment. 

| Propriedade | Descrição | Required in the deployment manifest |
| -------- | ----------- | -------- |
| schemaVersion | Has to be "1.0" | Sim |
| routes.{routeName} | A string representing an IoT Edge hub route. For more information, see [Declare routes](module-composition.md#declare-routes). | The `routes` element can be present but empty. |
| storeAndForwardConfiguration.timeToLiveSecs | The time in seconds that IoT Edge hub keeps messages if disconnected from routing endpoints, whether IoT Hub or a local module. The value can be any positive integer. | Sim |

## <a name="edgehub-reported-properties"></a>EdgeHub reported properties

| Propriedade | Descrição |
| -------- | ----------- |
| lastDesiredVersion | This integer refers to the last version of the desired properties processed by the IoT Edge hub. |
| lastDesiredStatus.code | The status code referring to last desired properties seen by the IoT Edge hub. Allowed values: `200` Success, `400` Invalid configuration, `500` Failed |
| lastDesiredStatus.description | Text description of the status. |
| clients.{device or moduleId}.status | The connectivity status of this device or module. Possible values {"connected" \| "disconnected"}. Only module identities can be in disconnected state. Downstream devices connecting to IoT Edge hub appear only when connected. |
| clients.{device or moduleId}.lastConnectTime | Last time the device or module connected. |
| clients.{device or moduleId}.lastDisconnectTime | Last time the device or module disconnected. |

## <a name="next-steps"></a>Passos seguintes

To learn how to use these properties to build out deployment manifests, see [Understand how IoT Edge modules can be used, configured, and reused](module-composition.md).
