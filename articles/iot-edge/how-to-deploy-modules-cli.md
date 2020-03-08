---
title: Desloque os módulos da linha de comando Azure CLI - Azure IoT Edge
description: Utilize o Azure CLI com a extensão Azure IoT para empurrar um módulo IoT Edge do seu Hub IoT para o seu dispositivo IoT Edge, configurado por um manifesto de implantação.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/16/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: e93360d4045f9c97d45abe2af489804a4c3c85f0
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78673511"
---
# <a name="deploy-azure-iot-edge-modules-with-azure-cli"></a>Implementar módulos do Azure IoT Edge com a CLI do Azure

Depois de criar do IoT Edge módulos com a sua lógica de negócios, pretende implementá-las para os seus dispositivos para operar na periferia. Se tiver vários módulos que funcionam em conjunto para coletar e processar dados, pode implementá-las ao mesmo tempo e declarar as regras de encaminhamento que ligam-os.

[O Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) é uma ferramenta de linha de comando de plataforma cruzada de código aberto para gerir recursos Azure, como o IoT Edge. Permite-lhe gerir recursos, instâncias de serviço aprovisionamento de dispositivos e hubs ligados a caixa do IoT Hub do Azure. A nova extensão de IoT otimiza a CLI do Azure com funcionalidades como a gestão de dispositivos e a capacidade total do IoT Edge.

Este artigo mostra como criar um manifesto de implantação de JSON, em seguida, utilizar esse ficheiro para emitir a implementação para um dispositivo IoT Edge. Para obter informações sobre a criação de uma implementação que visa vários dispositivos com base nas suas tags partilhadas, consulte [módulos IoT Edge em escala](how-to-deploy-monitor-cli.md)

## <a name="prerequisites"></a>Pré-requisitos

* Um [hub IoT](../iot-hub/iot-hub-create-using-cli.md) na sua assinatura Azure.
* Um [dispositivo IoT Edge](how-to-register-device.md#register-with-the-azure-cli) com o tempo de execução do IoT Edge instalado.
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) no seu ambiente. No mínimo, a sua versão Azure CLI deve ser de 2.0.70 ou superior. Utilize `az --version` para validar. Esta versão suporta comandos de extensão az e apresenta a arquitetura de comandos Knack.
* A [extensão IoT para Azure CLI](https://github.com/Azure/azure-iot-cli-extension).

## <a name="configure-a-deployment-manifest"></a>Configurar um manifesto de implantação

Um manifesto de implantação é um documento JSON que descreve quais os módulos para implementar, como os dados fluem entre os módulos e propriedades pretendidas do duplos de módulo. Para obter mais informações sobre como os manifestos de implantação funcionam e como criá-los, consulte [Entenda como os módulos IoT Edge podem ser usados, configurados e reutilizados](module-composition.md).

Para implementar módulos com a CLI do Azure, guarde o manifesto de implantação localmente como um ficheiro. JSON. Irá utilizar o caminho do ficheiro na próxima seção ao executar o comando para aplicar a configuração para o seu dispositivo.

Aqui está um manifesto de implantação básico com um módulo como exemplo:

```json
{
  "content": {
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
                "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
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
            "upstream": "FROM /messages/* INTO $upstream"
          },
          "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
          }
        }
      },
      "SimulatedTemperatureSensor": {
        "properties.desired": {
          "SendData": true,
          "SendInterval": 5
        }
      }
    }
  }
}
```

## <a name="deploy-to-your-device"></a>Implementar no seu novo dispositivo

Implementar módulos no seu dispositivo ao aplicar o manifesto de implantação que configurou com as informações de módulo.

Altere os diretórios para a pasta onde o manifesto de implantação é guardado. Se utilizou um dos modelos VS Code IoT Edge, utilize o ficheiro `deployment.json` na pasta **de config** do seu diretório de soluções e não no ficheiro `deployment.template.json`.

Utilize o seguinte comando para aplicar a configuração para um dispositivo IoT Edge:

   ```cli
   az iot edge set-modules --device-id [device id] --hub-name [hub name] --content [file path]
   ```

O parâmetro ID do dispositivo é sensível a casos. Os pontos de parâmetro de conteúdo para a implementação do manifestam do ficheiro que guardou.

   ![saída de conjunto de módulos do AZ iot edge](./media/how-to-deploy-cli/set-modules.png)

## <a name="view-modules-on-your-device"></a>Módulos de ver no seu dispositivo

Após a implantação de módulos para o seu dispositivo, pode ver todos eles com o seguinte comando:

Veja os módulos no seu dispositivo do IoT Edge:

   ```cli
   az iot hub module-identity list --device-id [device id] --hub-name [hub name]
   ```

O parâmetro ID do dispositivo é sensível a casos.

   ![saída da lista de identidade do módulo AZ iot hub](./media/how-to-deploy-cli/list-modules.png)

## <a name="next-steps"></a>Passos seguintes

Saiba como [implantar e monitorizar módulos IoT Edge em escala](how-to-deploy-monitor.md)
