---
title: Como implantar o módulo OPC Twin para OPc Twin do zero [ Microsoft Docs
description: Este artigo descreve como implantar o OPC Twin de raiz utilizando a lâmina IoT Edge do portal Azure e também usando a AZ CLI.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 6c8ceeaf49d8ebfa15a83118e8b518190f6ff85e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241063"
---
# <a name="deploy-opc-twin-module-and-dependencies-from-scratch"></a>Implementar módulo OPC Twin e dependências do zero

O módulo OPC Twin funciona no IoT Edge e fornece vários serviços de borda para os serviços de twin e registo do dispositivo OPC. 

Existem várias opções para implementar módulos para o seu Portal de [Borda Azure IoT,](https://azure.microsoft.com/services/iot-edge/) entre eles

- [Implantação da lâmina IoT Edge do portal Azure](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-modules-portal)
- [Implantação utilizando AZ CLI](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-monitor-cli)

> [!NOTE]
> Para obter mais informações sobre detalhes e instruções de implementação, consulte o [repositório](https://github.com/Azure/azure-iiot-components)GitHub .

## <a name="deployment-manifest"></a>Manifesto de implementação

Todos os módulos são implantados usando um manifesto de implantação.  Um manifesto de exemplo para a implantação tanto da [OPC Publisher](https://github.com/Azure/iot-edge-opc-publisher) como [da OPC Twin](https://github.com/Azure/azure-iiot-opc-twin-module) é mostrado abaixo.

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
                "createOptions": ""
              }
            },
            "edgeHub": {
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
                "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}], \"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
              }
            }
          },
          "modules": {
            "opctwin": {
              "version": "1.0",
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/iotedge/opc-twin:latest",
                "createOptions": "{\"NetworkingConfig\": {\"EndpointsConfig\": {\"host\": {}}}, \"HostConfig\": {\"NetworkMode\": \"host\" }}"
              }
            },
            "opcpublisher": {
              "version": "2.0",
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/iotedge/opc-publisher:latest",
                "createOptions": "{\"Hostname\":\"publisher\",\"Cmd\":[\"publisher\",\"--pf=./pn.json\",\"--di=60\",\"--to\",\"--aa\",\"--si=0\",\"--ms=0\"],\"ExposedPorts\":{\"62222/tcp\":{}},\"NetworkingConfig\":{\"EndpointsConfig\":{\"host\":{}}},\"HostConfig\":{\"NetworkMode\":\"host\",\"PortBindings\":{\"62222/tcp\":[{\"HostPort\":\"62222\"}]}}}"
              }
            }
          }
        }
      },
      "$edgeHub": {
        "properties.desired": {
          "schemaVersion": "1.0",
          "routes": {
            "opctwinToIoTHub": "FROM /messages/modules/opctwin/* INTO $upstream",
            "opcpublisherToIoTHub": "FROM /messages/modules/opcpublisher/* INTO $upstream"
          },
          "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
          }
        }
      }
    }
  }
}
```

## <a name="deploying-from-azure-portal"></a>Implantação do portal Azure

A forma mais fácil de implantar os módulos num dispositivo de gateway Azure IoT Edge é através do portal Azure.  

### <a name="prerequisites"></a>Pré-requisitos

1. Implante as [dependências Gémeas](howto-opc-twin-deploy-dependencies.md) OPC `.env` e obteve o ficheiro resultante. Note a `hub name` implementação `PCS_IOTHUBREACT_HUB_NAME` da variável no ficheiro resultante. `.env`

2. Registe-se e inicie um gateway [Linux](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) ou `device id` [Windows](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-windows) IoT Edge e note o seu .

### <a name="deploy-to-an-edge-device"></a>Implementar para um dispositivo de borda

1. Inscreva-se no [portal Azure](https://portal.azure.com/) e navegue até ao seu hub IoT.

2. Selecione **IoT Edge** do menu à esquerda.

3. Clique na identificação do dispositivo alvo a partir da lista de dispositivos.

4. Selecione **Módulos de Conjunto**.

5. Na secção de **módulos** de implantação da página, selecione **Adicionar** e Módulo De **Borda IoT.**

6. No módulo **personalizado IoT** Edge `opctwin` usar como nome para o módulo, em seguida, especificar o recipiente *Imagem URI* como

   ```bash
   mcr.microsoft.com/iotedge/opc-twin:latest
   ```

   Como *Recipiente Criar Opções,* utilize o seguinte JSON:

   ```json
   {"NetworkingConfig": {"EndpointsConfig": {"host": {}}}, "HostConfig": {"NetworkMode": "host" }}
   ```

   Preencha os campos opcionais, se necessário. Para obter mais informações sobre o recipiente, criar opções, reiniciar a política e o estado desejado ver [propriedades desejadas pelo EdgeAgent](https://docs.microsoft.com/azure/iot-edge/module-edgeagent-edgehub#edgeagent-desired-properties). Para obter mais informações sobre o módulo twin consulte [Definir ou atualizar as propriedades desejadas](https://docs.microsoft.com/azure/iot-edge/module-composition#define-or-update-desired-properties).

7. Selecione **Guardar** e repetir passo **5**.  

8. No diálogo ioT Edge Custom `opcpublisher` Module, use como nome para o módulo e a imagem do recipiente *URI* como 

   ```bash
   mcr.microsoft.com/iotedge/opc-publisher:latest
   ```

   Como *Recipiente Criar Opções,* utilize o seguinte JSON:

   ```json
   {"Hostname":"publisher","Cmd":["publisher","--pf=./pn.json","--di=60","--to","--aa","--si=0","--ms=0"],"ExposedPorts":{"62222/tcp":{}},"HostConfig":{"PortBindings":{"62222/tcp":[{"HostPort":"62222"}] }}}
   ```

9. Selecione **Guardar** **e, em** seguida, ao lado para continuar na secção rotas.

10. No separador de rotas, colar o seguinte 

    ```json
    {
      "routes": {
        "opctwinToIoTHub": "FROM /messages/modules/opctwin/* INTO $upstream",
        "opcpublisherToIoTHub": "FROM /messages/modules/opcpublisher/* INTO $upstream"
      }
    }
    ```

    e selecionar **Seguinte**

11. Reveja as suas informações de implantação e manifeste-se.  Deve parecer o manifesto de implantação acima.  Selecione **Submeter**.

12. Depois de ter implantado módulos no seu dispositivo, pode ver todos na página de detalhes do **Dispositivo** do portal. Esta página exibe o nome de cada módulo implantado, bem como informações úteis como o estado de implementação e o código de saída.

## <a name="deploying-using-azure-cli"></a>Implantação utilizando o Azure CLI

### <a name="prerequisites"></a>Pré-requisitos

1. Instale a mais recente versão da interface da linha de [comando Azure (AZ)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) a partir [daqui](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

### <a name="quickstart"></a>Início Rápido

1. Guarde o manifesto `deployment.json` de implantação acima num ficheiro.  

2. Utilize o seguinte comando para aplicar a configuração num dispositivo IoT Edge:

   ```azurecli
   az iot edge set-modules --device-id [device id] --hub-name [hub name] --content ./deployment.json
   ```

   O `device id` parâmetro é sensível a casos. O parâmetro de conteúdo aponta para o ficheiro manifesto de implantação que guardou. 
    ![saída de módulos de conjunto az IoT Edge](https://docs.microsoft.com/azure/iot-edge/media/how-to-deploy-cli/set-modules.png)

3. Depois de ter implantado módulos no seu dispositivo, pode vê-los todos com o seguinte comando:

   ```azurecli
   az iot hub module-identity list --device-id [device id] --hub-name [hub name]
   ```

   O parâmetro ID do dispositivo é sensível a casos. ![az iot hub saída de lista de identidade de módulo](https://docs.microsoft.com/azure/iot-edge/media/how-to-deploy-cli/list-modules.png)

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a implantar o OPC Twin do zero, aqui está o próximo passo sugerido:

> [!div class="nextstepaction"]
> [Implementar o OPC Twin para um projeto existente](howto-opc-twin-deploy-existing.md)
