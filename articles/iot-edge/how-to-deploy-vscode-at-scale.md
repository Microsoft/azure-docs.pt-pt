---
title: Implementar módulos em escala utilizando o Código do Estúdio Visual - Borda Azure IoT
description: Utilize a extensão IoT para o Código do Estúdio Visual para criar implementações automáticas para grupos de dispositivos IoT Edge.
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 1/8/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 92540c57179ae0198f78b588681167fe48097362
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82134367"
---
# <a name="deploy-iot-edge-modules-at-scale-using-visual-studio-code"></a>Implementar módulos IoT Edge em escala usando o Código do Estúdio Visual

Pode criar uma **implementação automática IoT Edge** utilizando o Visual Studio Code para gerir as implementações em curso para muitos dispositivos ao mesmo tempo. As implementações automáticas para IoT Edge fazem parte da funcionalidade [de gestão automática](/azure/iot-hub/iot-hub-automatic-device-management) do dispositivo do IoT Hub. As implementações são processos dinâmicos que permitem implementar vários módulos em vários dispositivos. Também pode rastrear o estado e a saúde dos módulos e fazer alterações quando necessário.

Para obter mais informações, consulte [as implementações automáticas De IoT Edge para dispositivos individuais ou em escala](module-deployment-monitoring.md).

Neste artigo, crias o Código do Estúdio Visual e a extensão IoT. Em seguida, aprende a implementar módulos para um conjunto de dispositivos IoT Edge.

## <a name="prerequisites"></a>Pré-requisitos

* Um [hub IoT](../iot-hub/iot-hub-create-through-portal.md) na sua assinatura Azure.
* Um [dispositivo IoT Edge](how-to-register-device.md#register-with-visual-studio-code) com o tempo de execução do IoT Edge instalado.
* [Código de estúdio visual.](https://code.visualstudio.com/)
* [Ferramentas Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools#overview) para Código de Estúdio Visual.

## <a name="sign-in-to-access-your-iot-hub"></a>Inscreva-se para aceder ao seu hub IoT

Pode utilizar as extensões Azure IoT para O Código do Estúdio Visual para fazer operações com o seu Hub. Para que estas operações funcionem, tem de assinar na sua conta Azure e selecionar o hub IoT em que está a trabalhar.

1. No Visual Studio Code, abra a vista **Explorer.**

1. Na parte inferior do Explorer, expanda a secção **Azure IoT Hub.**

1. Clique no **...** no cabeçalho da secção **Azure IoT Hub.** Se não vires a elipse, paira sobre o cabeceamento.

1. Escolha **Selecione Selecione IoT Hub**.

1. Se não estiver inscrito na sua conta Azure, siga as instruções para o fazer.

1. Selecione a sua subscrição do Azure.

1. Selecione o seu hub ioT.

## <a name="configure-a-deployment-manifest"></a>Configurar um manifesto de implantação

Um manifesto de implantação é um documento JSON que descreve quais os módulos a implantar. Também descreve como os dados fluem entre os módulos, e as propriedades desejadas dos gémeos módulos. Para mais informações, consulte [Saiba como implementar módulos e estabelecer rotas em IoT Edge](module-composition.md).

Para implementar módulos utilizando o Código do Estúdio Visual, guarde o manifesto de implantação localmente como a . Ficheiro JSON. Terá de fornecer a sua localização quando executar o comando para aplicar a configuração no seu dispositivo.

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

Se precisar de determinar quais os dispositivos IoT Edge que pode configurar atualmente, execute o comando **IoT Edge: Get Device Info.**

## <a name="identify-devices-with-target-conditions"></a>Identificar dispositivos com condições de destino

Para identificar os dispositivos IoT Edge que devem receber a implementação, deve especificar uma condição de destino. Uma condição-alvo é satisfeita quando critérios especificados são comparados por um dispositivoId, valor de etiqueta ou um valor de propriedade reportado.

Configurar etiquetas no dispositivo twin. Aqui está um exemplo de um dispositivo twin que tem tags:

```json
"tags":{
  "location":{
    "building": "20",
    "floor": "2"
  },
  "roomtype": "conference",
  "environment": "prod"
}
```

Este dispositivo receberá uma implementação se a condição-alvo para a implementação contiver `tag.location.building = '20'`uma expressão que corresponda a um dos valores da etiqueta, como .

Se pretender direcionar um dispositivo específico independentemente das suas etiquetas `deviceId` ou outros valores, apenas especifique o estado de destino.

Aqui estão mais alguns exemplos:

* dispositivoId ='linuxprod1'
* dispositivoId = 'linuxprod1' OR dispositivoId = 'linuxprod2' OR dispositivoId = 'linuxprod3'
* tags.environment ='prod'
* tags.environment = 'prod' And tags.location = 'westus2'
* tags.environment = 'prod' OR tags.location = 'westus2'
* tags.operator = 'John' And tags.environment = 'prod' AND NOT deviceId = 'linuxprod1'

Consulte a [condição do alvo](module-deployment-monitoring.md#target-condition) para obter detalhes. Para obter mais informações sobre gémeos e tags de dispositivos, consulte [Compreender e utilizar gémeos dispositivos no IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md).

### <a name="edit-the-device-twin"></a>Editar o dispositivo twin

Pode editar o dispositivo twin no Visual Studio Code para configurar as etiquetas. A partir do menu **'Ver',** selecione 'Paleta de **Comando'** e executa o comando **IoT Edge: Editar dispositivo twin.** Selecione o seu dispositivo IoT Edge e aparece o dispositivo twin.

Neste exemplo, não foram definidas etiquetas. Substitua a `"tags": {}` secção vazia atual com a definição de etiquetas próprias.

```json
{
    "deviceId": "myEdgeDevice",
    "etag": "AAAAAAAAAAE=",
    "deviceEtag": "NTgwMDg5MDAz",
    "status": "enabled",
    "statusUpdateTime": "0001-01-01T00:00:00Z",
    "connectionState": "Disconnected",
    "lastActivityTime": "0001-01-01T00:00:00Z",
    "cloudToDeviceMessageCount": 0,
    "authenticationType": "sas",
    "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
    },
    "version": 2,
    "properties": {
        "desired": {
            "$metadata": {
                "$lastUpdated": "2019-12-29T00:58:49.9315265Z"
            },
            "$version": 1
        },
        "reported": {
            "$metadata": {
                "$lastUpdated": "2019-12-29T00:58:49.9315265Z"
            },
            "$version": 1
        }
    },
    "capabilities": {
        "iotEdge": true
    },
    "deviceScope": "ms-azure-iot-edge://myEdgeDevice-637131779299315265",
    "tags": {}
}
```

Depois de guardar o ficheiro local, execute o comando **IoT Edge: Update Device Twin.**

## <a name="create-deployment-at-scale"></a>Criar implantação em escala

Depois de configurar o manifesto de implementação e as etiquetas configuradas no dispositivo twin, está pronto para ser implementado.

1. A partir do menu **'Ver',** selecione 'Paleta de **Comando'** e selecione o **Edge Azure IoT: Criar a implantação à escala.**

1. Navegue para o ficheiro JSON manifesto de implementação que pretende utilizar e clique em **Select Edge Deployment Manifest**.

1. Forneça valores conforme solicitado, começando com o ID de **implantação**.

   ![Especificar um ID de implementação](./media/how-to-deploy-monitor-vscode/create-deployment-at-scale.png)

   Especificar valores para estes parâmetros:

  | Parâmetro | Descrição |
  | --- | --- |
  | ID de implantação | O nome da implantação que será criado no centro ioT. Dê à sua implantação um nome único que seja até 128 letras minúsculas. Evite espaços e os seguintes caracteres inválidos: `& ^ [ ] { } \ | " < > /`. |
  | Condição do alvo | Introduza uma condição de destino para determinar quais os dispositivos que serão alvo com esta implementação.A condição baseia-se em etiquetas duplas do dispositivo ou propriedades reportadas de dispositivos twin e deve corresponder ao formato de expressão.Por exemplo, `tags.environment='test' and properties.reported.devicemodel='4000x'`. |
  | Prioridade |  Um inteiro positivo. Se duas ou mais implementações forem direcionadas para o mesmo dispositivo, aplicar-se-á a implantação com o valor numérico mais elevado para a Priority. |

  Depois de especificar a prioridade, o terminal deve apresentar saída semelhante à seguinte representação:

   ```cmd
   [Edge] Start deployment with deployment id [{specified-value}] and target condition [{specified-value}]
   [Edge] Deployment with deployment id [{specified-value}] succeeded.
   ```

## <a name="monitoring-and-modifying-deployments"></a>Monitorização e modificação de implantações

Utilize o [portal Azure](how-to-monitor-iot-edge-deployments.md#monitor-a-deployment-in-the-azure-portal) ou o [Azure CLI](how-to-monitor-iot-edge-deployments.md#monitor-a-deployment-with-azure-cli) para monitorizar, modificar e eliminar as implementações. Ambos fornecem métricas nas suas implementações.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [a implementação de módulos para dispositivos IoT Edge](module-deployment-monitoring.md).
