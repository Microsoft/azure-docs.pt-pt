---
title: Implantar módulos em escala usando o Visual Studio Code-Azure IoT Edge
description: Use a extensão de IoT para Visual Studio Code para criar implantações automáticas para grupos de dispositivos IoT Edge.
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 1/8/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 08299a589dc6e8f768cba7ef976e109ef1fb69d7
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774136"
---
# <a name="deploy-iot-edge-modules-at-scale-using-visual-studio-code"></a>Implantar módulos IoT Edge em escala usando Visual Studio Code

Você pode criar uma **IOT Edge implantação automática** usando Visual Studio Code para gerenciar implantações em andamento para vários dispositivos de uma vez. As implantações automáticas para IoT Edge fazem parte do recurso de [gerenciamento automático de dispositivo](/azure/iot-hub/iot-hub-automatic-device-management) do Hub IOT. As implantações são processos dinâmicos que permitem implantar vários módulos em vários dispositivos. Você também pode acompanhar o status e a integridade dos módulos e fazer alterações quando necessário.

Para obter mais informações, consulte [entender IOT Edge implantações automáticas para dispositivos únicos ou em escala](module-deployment-monitoring.md).

Neste artigo, você configura Visual Studio Code e a extensão de IoT. Em seguida, você aprenderá a implantar módulos em um conjunto de dispositivos IoT Edge.

## <a name="prerequisites"></a>Pré-requisitos

* Uma [IoT hub](../iot-hub/iot-hub-create-through-portal.md) na sua subscrição do Azure.
* Uma [dispositivo IoT Edge](how-to-register-device.md#register-with-visual-studio-code) com o runtime do IoT Edge instalado.
* [Visual Studio Code](https://code.visualstudio.com/).
* [Ferramentas de IOT do Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools#overview) para Visual Studio Code.

## <a name="sign-in-to-access-your-iot-hub"></a>Entre para acessar o seu hub IoT

Você pode usar as extensões do Azure IoT para Visual Studio Code para realizar operações com seu hub. Para que essas operações funcionem, você precisa entrar em sua conta do Azure e selecionar o Hub IoT no qual você está trabalhando.

1. No Visual Studio Code, abra a **Explorer** vista.

1. Na parte inferior do Explorer, expanda a seção **Hub IOT do Azure** .

1. Clique no cabeçalho da seção **...** no **Hub IOT do Azure** . Se não vir o botão de reticências, Paire o rato sobre o cabeçalho.

1. Escolher **selecione o IoT Hub**.

1. Se você não estiver conectado à sua conta do Azure, siga os prompts para fazer isso.

1. Selecione a sua subscrição do Azure.

1. Selecione o seu hub IoT.

## <a name="configure-a-deployment-manifest"></a>Configurar um manifesto de implantação

Um manifesto de implantação é um documento JSON que descreve quais módulos serão implantados. Ele também descreve como os dados fluem entre os módulos e as propriedades desejadas do módulo gêmeos. Para obter mais informações, consulte [saiba como implantar módulos e estabelecer rotas no IOT Edge](module-composition.md).

Para implementar módulos com o Visual Studio Code, guarde o manifesto de implantação localmente como um. Ficheiro JSON. Você precisará fornecer seu local ao executar o comando para aplicar a configuração ao seu dispositivo.

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

Se você precisar determinar quais dispositivos IoT Edge você pode configurar no momento, execute o comando **IOT Edge: obter informações do dispositivo** .

## <a name="identify-devices-with-target-conditions"></a>Identificar dispositivos com condições de destino

Para identificar os dispositivos IoT Edge que devem receber a implantação, você deve especificar uma condição de destino. Uma condição de destino é atendida quando os critérios especificados são correspondidos por um DeviceID, um valor de marca ou um valor de propriedade relatado.

Você configura marcas no dispositivo. Aqui está um exemplo de um dispositivo de r com marcas:

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

Esse dispositivo receberá uma implantação se a condição de destino da implantação contiver uma expressão que corresponda a um dos valores da marca, como `tag.location.building = '20'`.

Se você quiser direcionar um dispositivo específico independentemente de suas marcas ou outros valores, basta especificar o `deviceId` para a condição de destino.

Aqui estão alguns exemplos:

* deviceId = 'linuxprod1'
* DeviceID = ' linuxprod1 ' ou DeviceID = ' linuxprod2 ' ou DeviceID = ' linuxprod3 '
* tags.Environment = 'prod'
* Tags. Environment = ' prod ' e Tags. Location = ' westus2 '
* Tags. Environment = ' prod ' ou Tags. Location = ' westus2 '
* Tags. Operator = ' John ' e Tags. Environment = ' prod ' e NOT DeviceID = ' linuxprod1 '

Consulte [condição de destino](module-deployment-monitoring.md#target-condition) para obter detalhes. Para obter mais informações sobre dispositivos duplos e etiquetas, consulte [compreender e utilizar dispositivos duplos no IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md).

### <a name="edit-the-device-twin"></a>Editar o dispositivo.

Você pode editar as configurações de dispositivo no Visual Studio Code para configurar as marcas. No menu **Exibir** , selecione **paleta de comandos** e execute o comando **IOT Edge: editar dispositivo** . Selecione o dispositivo de IoT Edge e o dispositivo de dispositivos aparecerá.

Neste exemplo, nenhuma marca foi definida. Substitua a seção vazia atual `"tags": {}` pela sua própria definição de marcações.

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

Depois de salvar o arquivo local, execute o comando **IOT Edge: atualizar o dispositivo** .

## <a name="create-deployment-at-scale"></a>Criar implantação em escala

Depois de configurar o manifesto de implantação e as marcas configuradas no dispositivo "d", você estará pronto para implantar.

1. No menu **Exibir** , selecione **paleta de comandos** e selecione o comando **Azure IOT Edge: criar implantação em escala** .

1. Navegue para o manifesto JSON ficheiro de implementação que pretende utilizar e clique em **selecionar o manifesto de implantação do Edge**.

1. Forneça valores conforme solicitado, começando com a **ID de implantação**.

   ![Especificar uma ID de implantação](./media/how-to-deploy-monitor-vscode/create-deployment-at-scale.png)

   Especifique valores para estes parâmetros:

  | Parâmetro | Descrição |
  | --- | --- |
  | ID da implantação | O nome da implantação que será criada no Hub IoT. Dê um nome exclusivo que é até 128 minúsculas de sua implementação. Evite espaços e os seguintes carateres inválidos: `& ^ [ ] { } \ | " < > /`. |
  | Condição de destino | Insira uma condição de destino para determinar quais dispositivos serão direcionados a essa implantação. A condição é baseada nas marcas de dispositivo ou nas propriedades relatadas do dispositivo e deve corresponder ao formato da expressão. Por exemplo, `tags.environment='test' and properties.reported.devicemodel='4000x'`. |
  | Prioridade |  Um inteiro positivo. Se duas ou mais implantações forem direcionadas ao mesmo dispositivo, a implantação com o maior valor numérico para prioridade será aplicada. |

  Depois de especificar a prioridade, o terminal deve exibir uma saída semelhante à seguinte descrição:

   ```cmd
   [Edge] Start deployment with deployment id [{specified-value}] and target condition [{specified-value}]
   [Edge] Deployment with deployment id [{specified-value}] succeeded.
   ```

## <a name="monitoring-and-modifying-deployments"></a>Monitorando e modificando implantações

Use o [CLI do Azure](how-to-deploy-monitor-cli.md#monitor-a-deployment) ou o [portal do Azure](how-to-deploy-monitor.md#monitor-a-deployment) para monitorar, modificar e excluir implantações. Ambos fornecem métricas sobre suas implantações.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [a implantação de módulos em dispositivos IOT Edge](module-deployment-monitoring.md).
