---
title: Implementar módulos à escala utilizando o Código do Estúdio Visual - Azure IoT Edge
description: Utilize a extensão IoT para o Código do Estúdio Visual para criar implementações automáticas para grupos de dispositivos IoT Edge.
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 1/8/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 7f6e90edc0503326dc9dbb06abfcf59fa2d51e1e
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92043821"
---
# <a name="deploy-iot-edge-modules-at-scale-using-visual-studio-code"></a>Implementar módulos IoT Edge em escala usando Código de Estúdio Visual

Pode criar uma **implementação automática IoT Edge** utilizando o Código do Estúdio Visual para gerir as implementações em curso para muitos dispositivos ao mesmo tempo. As implementações automáticas para ioT Edge fazem parte da funcionalidade automática de [gestão](../iot-hub/iot-hub-automatic-device-management.md) do dispositivo do IoT Hub. As implementações são processos dinâmicos que permitem implantar vários módulos em vários dispositivos. Também pode acompanhar o estado e a saúde dos módulos e fazer alterações quando necessário.

Para obter mais informações, consulte [as configurações automáticas Understand IoT Edge para dispositivos individuais ou à escala.](module-deployment-monitoring.md)

Neste artigo, configura o Código do Estúdio Visual e a extensão IoT. Em seguida, aprende-se a implantar módulos num conjunto de dispositivos IoT Edge.

## <a name="prerequisites"></a>Pré-requisitos

* Um [hub IoT](../iot-hub/iot-hub-create-through-portal.md) na sua assinatura Azure.
* Um ou mais dispositivos IoT Edge.

  Se não tiver um dispositivo IoT Edge configurado, pode criar um numa máquina virtual Azure. Siga os passos num dos artigos de arranque rápido para [criar um dispositivo Linux virtual](quickstart-linux.md) ou criar um dispositivo Virtual [Windows](quickstart.md).

* [Visual Studio Code](https://code.visualstudio.com/).
* [Ferramentas Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools#overview) para Código de Estúdio Visual.

## <a name="sign-in-to-access-your-iot-hub"></a>Inscreva-se para aceder ao seu hub IoT

Pode utilizar as extensões Azure IoT para o Código do Estúdio Visual para fazer operações com o seu Hub. Para que estas operações funcionem, tem de se inscrever na sua conta Azure e selecionar o hub IoT em que está a trabalhar.

1. No Código do Estúdio Visual, abra a vista **explorer.**

1. Na parte inferior do Explorer, expanda a secção **Azure IoT Hub.**

1. Clique no **...** no cabeçalho da secção **Azure IoT Hub.** Se não vir a elipse, paire sobre o cabeçalho.

1. Escolha **Select IoT Hub**.

1. Se não tiver assinado na sua conta Azure, siga as instruções para o fazer.

1. Selecione a sua subscrição do Azure.

1. Selecione o seu hub IoT.

## <a name="configure-a-deployment-manifest"></a>Configure um manifesto de implantação

Um manifesto de implantação é um documento JSON que descreve quais os módulos a implementar. Também descreve como os dados fluem entre os módulos e as propriedades desejadas dos gémeos módulos. Para obter mais informações, consulte [Saiba como implementar módulos e estabelecer rotas no IoT Edge.](module-composition.md)

Para implementar módulos utilizando o Código do Estúdio Visual, guarde o manifesto de implantação localmente como um . Arquivo JSON. Terá de fornecer a sua localização quando executar o comando para aplicar a configuração no seu dispositivo.

Aqui está um manifesto básico de implantação com um módulo como exemplo:

>[!NOTE]
>Este manifesto de implantação da amostra utiliza a versão 1.1 do esquema para o agente e o hub IoT Edge. A versão 1.1 do Schema foi lançada juntamente com a versão 1.0.10 do IoT Edge e permite funcionalidades como a ordem de arranque do módulo e a priorização da rota.

```json
{
  "content": {
    "modulesContent": {
      "$edgeAgent": {
        "properties.desired": {
          "schemaVersion": "1.1",
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
              "version": "1.1",
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

## <a name="identify-devices-with-target-conditions"></a>Identificar dispositivos com condições-alvo

Para identificar os dispositivos IoT Edge que devem receber a implantação, tem de especificar uma condição de destino. Uma condição de destino é cumprida quando critérios especificados são acompanhados por um dispositivoId, valor de etiqueta ou um valor de propriedade reportado.

Configuras etiquetas no dispositivo gémeo. Aqui está um exemplo de um dispositivo gémeo que tem etiquetas:

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

Este dispositivo receberá uma implantação se a condição alvo da implantação contiver uma expressão que corresponda a um dos valores da etiqueta, tais como `tag.location.building = '20'` .

Se pretender direcionar um dispositivo específico independentemente das suas etiquetas ou outros valores, basta especificar a `deviceId` condição de destino.

Aqui estão mais alguns exemplos:

* deviceId ='linuxprod1'
* deviceId = 'linuxprod1' OR deviceId = 'linuxprod2' OR deviceId = 'linuxprod3'
* tags.environment ='prod'
* tags.environment = 'prod' E tags.location = 'westus2'
* tags.environment = 'prod' OR tags.location = 'westus2'
* tags.operator = 'John' AND tags.environment = 'prod' AND NOT deviceId = 'linuxprod1'

Consulte [a condição do alvo](module-deployment-monitoring.md#target-condition) para mais detalhes. Para obter mais informações sobre os gémeos e tags do dispositivo, consulte [Compreender e utilizar gémeos do dispositivo no IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md).

### <a name="edit-the-device-twin"></a>Editar o dispositivo twin

Pode editar o dispositivo twin no Código do Estúdio Visual para configurar as etiquetas. No menu **Ver,** selecione **Paleta de Comando** e execute o comando **IoT Edge: Editar o comando Device Twin.** Selecione o seu dispositivo IoT Edge e o twin do dispositivo aparece.

Neste exemplo, não foram definidas tags. Substitua a secção vazia atual `"tags": {}` pela definição de etiquetas próprias.

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

## <a name="create-deployment-at-scale"></a>Criar implantação à escala

Depois de configurar o manifesto de implantação e configurar as etiquetas no dispositivo twin, está pronto para ser implantado.

1. No menu **Ver,** selecione **a Paleta de Comando** e selecione o **Aresta IoT Azure: Criar implantação no** comando escala.

1. Navegue para o ficheiro JSON manifesto de implementação que pretende utilizar e clique em **Select Edge Deployment Manifest**.

1. Fornecer valores conforme solicitado, começando com o **ID de implementação**.

   ![Especificar um ID de implementação](./media/how-to-deploy-monitor-vscode/create-deployment-at-scale.png)

   Especificar os valores para estes parâmetros:

  | Parâmetro | Descrição |
  | --- | --- |
  | ID de implantação | O nome da implantação que será criada no hub IoT. Dê à sua implantação um nome único que seja até 128 letras minúsculas. Evite espaços e os seguintes caracteres inválidos: `& ^ [ ] { } \ | " < > /` . |
  | Condição do alvo | Introduza uma condição de destino para determinar quais os dispositivos que serão alvo com esta implementação.A condição baseia-se em etiquetas gémeas do dispositivo ou no dispositivo que as propriedades reportadas por gémeos e devem corresponder ao formato de expressão.Por exemplo, `tags.environment='test' and properties.reported.devicemodel='4000x'` . |
  | Prioridade |  Um inteiro positivo. Se duas ou mais implementações forem direcionadas para o mesmo dispositivo, aplicar-se-á a implantação com o valor numérico mais elevado de Prioridade. |

  Depois de especificar a prioridade, o terminal deve apresentar uma saída semelhante à seguinte representação:

   ```cmd
   [Edge] Start deployment with deployment id [{specified-value}] and target condition [{specified-value}]
   [Edge] Deployment with deployment id [{specified-value}] succeeded.
   ```

## <a name="monitoring-and-modifying-deployments"></a>Monitorização e modificação de implementações

Utilize o [portal Azure](how-to-monitor-iot-edge-deployments.md#monitor-a-deployment-in-the-azure-portal) ou o [CLI Azure](how-to-monitor-iot-edge-deployments.md#monitor-a-deployment-with-azure-cli) para monitorizar, modificar e eliminar as implementações. Ambos fornecem métricas nas suas implementações.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [a implementação de módulos para dispositivos IoT Edge](module-deployment-monitoring.md).