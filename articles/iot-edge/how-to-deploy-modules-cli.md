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
ms.openlocfilehash: fbd0d65624852737c424128e9125b8370b870d4d
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82133933"
---
# <a name="deploy-azure-iot-edge-modules-with-azure-cli"></a>Implementar módulos Azure IoT Edge com Azure CLI

Assim que criar módulos IoT Edge com a sua lógica de negócio, pretende implantá-los nos seus dispositivos para operar na borda. Se tiver vários módulos que trabalham em conjunto para recolher e processar dados, pode implantá-los todos de uma vez e declarar as regras de encaminhamento que os ligam.

[O Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) é uma ferramenta de linha de comando de plataforma cruzada de código aberto para gerir recursos Azure, como o IoT Edge. Permite-lhe gerir os recursos do Azure IoT Hub, as instâncias de serviço de fornecimento de dispositivos e os centros ligados fora da caixa. A nova extensão IoT enriquece o Azure CLI com funcionalidades como a gestão de dispositivos e a capacidade completa do IoT Edge.

Este artigo mostra como criar um manifesto de implantação JSON, em seguida, use esse ficheiro para empurrar a implementação para um dispositivo IoT Edge. Para obter informações sobre a criação de uma implementação que visa vários dispositivos com base nas suas tags partilhadas, consulte [módulos IoT Edge em escala](how-to-deploy-cli-at-scale.md)

## <a name="prerequisites"></a>Pré-requisitos

* Um [hub IoT](../iot-hub/iot-hub-create-using-cli.md) na sua assinatura Azure.
* Um [dispositivo IoT Edge](how-to-register-device.md#register-with-the-azure-cli) com o tempo de execução do IoT Edge instalado.
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) no seu ambiente. No mínimo, a sua versão Azure CLI deve ser de 2.0.70 ou superior. Utilize `az --version` para validar. Esta versão suporta comandos de extensão az e apresenta a arquitetura de comandos Knack.
* A [extensão IoT para Azure CLI](https://github.com/Azure/azure-iot-cli-extension).

## <a name="configure-a-deployment-manifest"></a>Configurar um manifesto de implantação

Um manifesto de implantação é um documento JSON que descreve quais os módulos a implantar, como os dados fluem entre os módulos e as propriedades desejadas dos gémeos módulos. Para obter mais informações sobre como os manifestos de implantação funcionam e como criá-los, consulte [Entenda como os módulos IoT Edge podem ser usados, configurados e reutilizados](module-composition.md).

Para utilizar módulos utilizando o Azure CLI, guarde o manifesto de implantação localmente como um ficheiro .json. Utilizará o caminho do ficheiro na secção seguinte quando executar o comando para aplicar a configuração no seu dispositivo.

Aqui está um manifesto básico de implantação com um módulo como exemplo:

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

Implementa módulos no seu dispositivo aplicando o manifesto de implementação que configuracom a informação do módulo.

Mude os diretórios para a pasta onde o seu manifesto de implantação é guardado. Se utilizou um dos modelos VS Code IoT Edge, utilize o `deployment.json` ficheiro na pasta **config** do seu diretório de soluções e não no `deployment.template.json` ficheiro.

Utilize o seguinte comando para aplicar a configuração num dispositivo IoT Edge:

   ```azurecli
   az iot edge set-modules --device-id [device id] --hub-name [hub name] --content [file path]
   ```

O parâmetro ID do dispositivo é sensível a casos. O parâmetro de conteúdo aponta para o ficheiro manifesto de implantação que guardou.

   ![az iot borda set-modules saída](./media/how-to-deploy-cli/set-modules.png)

## <a name="view-modules-on-your-device"></a>Ver módulos no seu dispositivo

Depois de ter implantado módulos no seu dispositivo, pode vê-los todos com o seguinte comando:

Veja os módulos no seu dispositivo do IoT Edge:

   ```azurecli
   az iot hub module-identity list --device-id [device id] --hub-name [hub name]
   ```

O parâmetro ID do dispositivo é sensível a casos.

   ![az iot hub saída de lista de identidade de módulo](./media/how-to-deploy-cli/list-modules.png)

## <a name="next-steps"></a>Passos seguintes

Saiba como [implantar e monitorizar módulos IoT Edge em escala](how-to-deploy-at-scale.md)
