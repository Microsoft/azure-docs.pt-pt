---
title: Deploy modules from command line - Azure IoT Edge | Microsoft Docs
description: Use the IoT extension for Azure CLI to deploy modules to an IoT Edge device
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/16/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 72535b69c81aee880eb16bf5d10e11dedb36f3a7
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457472"
---
# <a name="deploy-azure-iot-edge-modules-with-azure-cli"></a>Deploy Azure IoT Edge modules with Azure CLI

Once you create IoT Edge modules with your business logic, you want to deploy them to your devices to operate at the edge. If you have multiple modules that work together to collect and process data, you can deploy them all at once and declare the routing rules that connect them.

[Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) is an open-source cross platform command-line tool for managing Azure resources such as IoT Edge. It enables you to manage Azure IoT Hub resources, device provisioning service instances, and linked-hubs out of the box. The new IoT extension enriches Azure CLI with features such as device management and full IoT Edge capability.

This article shows how to create a JSON deployment manifest, then use that file to push the deployment to an IoT Edge device. For information about creating a deployment that targets multiple devices based on their shared tags, see [Deploy and monitor IoT Edge modules at scale](how-to-deploy-monitor-cli.md)

## <a name="prerequisites"></a>Pré-requisitos

* An [IoT hub](../iot-hub/iot-hub-create-using-cli.md) in your Azure subscription.
* An [IoT Edge device](how-to-register-device.md#register-with-the-azure-cli) with the IoT Edge runtime installed.
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) in your environment. At a minimum, your Azure CLI version must be 2.0.24 or above. Utilize `az --version` para validar. Esta versão suporta comandos de extensão az e apresenta a arquitetura de comandos Knack.
* The [IoT extension for Azure CLI](https://github.com/Azure/azure-iot-cli-extension).

## <a name="configure-a-deployment-manifest"></a>Configure a deployment manifest

A deployment manifest is a JSON document that describes which modules to deploy, how data flows between the modules, and desired properties of the module twins. For more information about how deployment manifests work and how to create them, see [Understand how IoT Edge modules can be used, configured, and reused](module-composition.md).

To deploy modules using the Azure CLI, save the deployment manifest locally as a .json file. You will use the file path in the next section when you run the command to apply the configuration to your device.

Here's a basic deployment manifest with one module as an example:

   ```json
   {
     "modulesContent": {
       "$edgeAgent": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "runtime": {
             "type": "docker",
             "settings": {
               "minDockerVersion": "v1.25",
               "loggingOptions": "",
               "registryCredentials": {}
             }
           },
           "systemModules": {
             "edgeAgent": {
               "type": "docker",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
                 "createOptions": "{}"
               }
             },
             "edgeHub": {
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
                 "createOptions": "{}"
               }
             }
           },
           "modules": {
             "SimulatedTemperatureSensor": {
               "version": "1.0",
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
                 "createOptions": "{}"
               }
             }
           }
         }
       },
       "$edgeHub": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "routes": {
               "route": "FROM /* INTO $upstream"
           },
           "storeAndForwardConfiguration": {
             "timeToLiveSecs": 7200
           }
         }
       },
       "SimulatedTemperatureSensor": {
         "properties.desired": {}
       }
     }
   }
   ```

## <a name="deploy-to-your-device"></a>Implementar no seu novo dispositivo

You deploy modules to your device by applying the deployment manifest that you configured with the module information.

Change directories into the folder where your deployment manifest is saved. If you used one of the VS Code IoT Edge templates, use the `deployment.json` file in the **config** folder of your solution directory and not the `deployment.template.json` file.

Use the following command to apply the configuration to an IoT Edge device:

   ```cli
   az iot edge set-modules --device-id [device id] --hub-name [hub name] --content [file path]
   ```

The device ID parameter is case-sensitive. The content parameter points to the deployment manifest file that you saved.

   ![az iot edge set-modules output](./media/how-to-deploy-cli/set-modules.png)

## <a name="view-modules-on-your-device"></a>View modules on your device

Once you've deployed modules to your device, you can view all of them with the following command:

Veja os módulos no seu dispositivo do IoT Edge:

   ```cli
   az iot hub module-identity list --device-id [device id] --hub-name [hub name]
   ```

The device ID parameter is case-sensitive.

   ![az iot hub module-identity list output](./media/how-to-deploy-cli/list-modules.png)

## <a name="next-steps"></a>Passos seguintes

Learn how to [Deploy and monitor IoT Edge modules at scale](how-to-deploy-monitor.md)
