---
title: Como implantar o módulo OPC Twin para Azure de raiz Microsoft Docs
description: Este artigo descreve como implementar o OPC Twin do zero utilizando a lâmina IoT Edge do portal Azure e também usando OZ CLI.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: industrial-iot
ms.custom: devx-track-azurecli
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 1c12b1c7fd393227cb22d011f8b88f914cfded59
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96445493"
---
# <a name="deploy-opc-twin-module-and-dependencies-from-scratch"></a>Implementar módulo OPC Twin e dependências do zero

> [!IMPORTANT]
> Enquanto atualizamos este artigo, consulte [a Azure Industrial IoT](https://azure.github.io/Industrial-IoT/) para obter o conteúdo mais atualizado.

O módulo OPC Twin funciona no IoT Edge e fornece vários serviços de borda para os serviços de twin e registo do dispositivo OPC. 

Existem várias opções para implementar módulos para o seu [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) Gateway, entre eles

- [Implantação a partir da lâmina IoT Edge do portal Azure](../iot-edge/how-to-deploy-modules-portal.md)
- [Implantação usando CLI AZ](../iot-edge/how-to-deploy-cli-at-scale.md)

> [!NOTE]
> Para obter mais informações sobre detalhes e instruções de implantação, consulte o [repositório](https://github.com/Azure/azure-iiot-components)GitHub .

## <a name="deployment-manifest"></a>Manifesto de implementação

Todos os módulos são implantados usando um manifesto de implantação.  Um manifesto de exemplo para implantar tanto [a OPC Publisher](https://github.com/Azure/iot-edge-opc-publisher) como [a OPC Twin](https://github.com/Azure/azure-iiot-opc-twin-module) é mostrado abaixo.

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
                "createOptions": "{\"Hostname\":\"publisher\",\"Cmd\":[\"publisher\",\"--pf=./pn.json\",\"--di=60\",\"--tm\",\"--aa\",\"--si=0\",\"--ms=0\"],\"ExposedPorts\":{\"62222/tcp\":{}},\"NetworkingConfig\":{\"EndpointsConfig\":{\"host\":{}}},\"HostConfig\":{\"NetworkMode\":\"host\",\"PortBindings\":{\"62222/tcp\":[{\"HostPort\":\"62222\"}]}}}"
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

## <a name="deploying-from-azure-portal"></a>Implantação a partir do portal Azure

A forma mais fácil de implantar os módulos num dispositivo de gateway Azure IoT Edge é através do portal Azure.  

### <a name="prerequisites"></a>Pré-requisitos

1. Implemente as [dependências](howto-opc-twin-deploy-dependencies.md) OPC Twin e obtenha o `.env` ficheiro resultante. Note a implantação `hub name` da `PCS_IOTHUBREACT_HUB_NAME` variável no `.env` ficheiro resultante.

2. Registe-se e inicie um gateway [Linux](../iot-edge/how-to-install-iot-edge.md) ou [Windows](../iot-edge/how-to-install-iot-edge.md) IoT Edge e note o seu `device id` .

### <a name="deploy-to-an-edge-device"></a>Implementar para um dispositivo de borda

1. Inscreva-se no [portal Azure](https://portal.azure.com/) e navegue até ao seu hub IoT.

2. Selecione **IoT Edge** no menu da mão esquerda.

3. Clique no ID do dispositivo alvo a partir da lista de dispositivos.

4. Selecione **módulos de conjunto**.

5. Na secção **de módulos** de implementação da página, selecione **Add** e **IoT Edge Module.**

6. No diálogo do **módulo personalizado IoT Edge** como nome para o `opctwin` módulo, em seguida, especifique o recipiente *Imagem URI* como

   ```bash
   mcr.microsoft.com/iotedge/opc-twin:latest
   ```

   Como *Opções de Criação de Recipientes,* utilize o seguinte JSON:

   ```json
   {"NetworkingConfig": {"EndpointsConfig": {"host": {}}}, "HostConfig": {"NetworkMode": "host" }}
   ```

   Preencha os campos opcionais, se necessário. Para obter mais informações sobre o contentor, crie opções, reinicie a política e o estado desejado consulte [as propriedades desejadas pela EdgeAgent](../iot-edge/module-edgeagent-edgehub.md#edgeagent-desired-properties). Para obter mais informações sobre o módulo twin consulte [Definir ou atualizar as propriedades desejadas.](../iot-edge/module-composition.md#define-or-update-desired-properties)

7. **Selecione Guardar** e repetir passo **5**.  

8. No diálogo do módulo personalizado IoT Edge, utilize `opcpublisher` como nome para o módulo e para a imagem do recipiente *URI* 

   ```bash
   mcr.microsoft.com/iotedge/opc-publisher:latest
   ```

   Como *Opções de Criação de Recipientes,* utilize o seguinte JSON:

   ```json
   {"Hostname":"publisher","Cmd":["publisher","--pf=./pn.json","--di=60","--tm","--aa","--si=0","--ms=0"],"ExposedPorts":{"62222/tcp":{}},"HostConfig":{"PortBindings":{"62222/tcp":[{"HostPort":"62222"}] }}}
   ```

9. **Selecione Guardar** e, em seguida, **continuar** para a secção de rotas.

10. No separador rotas, cole o seguinte 

    ```json
    {
      "routes": {
        "opctwinToIoTHub": "FROM /messages/modules/opctwin/* INTO $upstream",
        "opcpublisherToIoTHub": "FROM /messages/modules/opcpublisher/* INTO $upstream"
      }
    }
    ```

    e selecionar **Next**

11. Reveja as suas informações de implantação e manifeste..  Deve parecer o manifesto de implantação acima.  Selecione **Submeter**.

12. Depois de ter implantado módulos no seu dispositivo, pode vê-los todos na página de detalhes do **Dispositivo** do portal. Esta página apresenta o nome de cada módulo implantado, bem como informações úteis como o estado de implantação e o código de saída.

## <a name="deploying-using-azure-cli"></a>Implantação usando O Azure CLI

### <a name="prerequisites"></a>Pré-requisitos

1. Instale a versão mais recente da interface da linha de [comando Azure (AZ)](/cli/azure/?view=azure-cli-latest) a partir [daqui](/cli/azure/install-azure-cli?view=azure-cli-latest).

### <a name="quickstart"></a>Início Rápido

1. Guarde o manifesto de implantação acima num `deployment.json` ficheiro.  

2. Utilize o seguinte comando para aplicar a configuração num dispositivo IoT Edge:

   ```azurecli
   az iot edge set-modules --device-id [device id] --hub-name [hub name] --content ./deployment.json
   ```

   O `device id` parâmetro é sensível a casos. O parâmetro de conteúdo aponta para o ficheiro manifesto de implantação que guardou. 
    ![saída de módulos de set-modules IoT Edge az](/azure/iot-edge/media/how-to-deploy-cli/set-modules.png)

3. Uma vez implantados módulos no seu dispositivo, pode vê-los todos com o seguinte comando:

   ```azurecli
   az iot hub module-identity list --device-id [device id] --hub-name [hub name]
   ```

   O parâmetro de identificação do dispositivo é sensível a maiôs. ![az iot hub módulo-identidade saída](/azure/iot-edge/media/how-to-deploy-cli/list-modules.png)

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a implementar o OPC Twin do zero, eis o próximo passo sugerido:

> [!div class="nextstepaction"]
> [Implementar o OPC Twin para um projeto existente](howto-opc-twin-deploy-existing.md)