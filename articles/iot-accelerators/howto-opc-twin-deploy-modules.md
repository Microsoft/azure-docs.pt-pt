---
title: Como implantar o módulo OPC Mypara Azure do zero | Microsoft Docs
description: Este artigo descreve como implantar o OPC "s" no zero usando a folha de IoT Edge do portal do Azure e também usando AZ CLI.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 96a4afff3e58bfa1ebf661909f380aa525fea76e
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73820142"
---
# <a name="deploy-opc-twin-module-and-dependencies-from-scratch"></a>Implantar o módulo OPC e as dependências do zero

O módulo OPC myé executado em IoT Edge e fornece vários serviços de borda para o dispositivo OPC e os serviços de registro. 

Há várias opções para implantar módulos em seu gateway de [Azure IOT Edge](https://azure.microsoft.com/services/iot-edge/) , entre eles

- [Implantando da folha IoT Edge do portal do Azure](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-modules-portal)
- [Implantando usando AZ CLI](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-monitor-cli)

> [!NOTE]
> Para obter mais informações sobre detalhes de implantação e instruções, consulte o [repositório](https://github.com/Azure/azure-iiot-components)github.

## <a name="deployment-manifest"></a>Manifesto de implementação

Todos os módulos são implantados usando um manifesto de implantação.  Um exemplo de manifesto para implantar o [Editor OPC](https://github.com/Azure/iot-edge-opc-publisher) e o [OPC](https://github.com/Azure/azure-iiot-opc-twin-module) myQuery é mostrado abaixo.

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

## <a name="deploying-from-azure-portal"></a>Implantando do portal do Azure

A maneira mais fácil de implantar os módulos em um dispositivo Azure IoT Edge gateway é por meio do portal do Azure.  

### <a name="prerequisites"></a>Pré-requisitos

1. Implante as [dependências](howto-opc-twin-deploy-dependencies.md) do OPC bivisor e obteve o arquivo de `.env` resultante. Observe o `hub name` implantado da variável `PCS_IOTHUBREACT_HUB_NAME` no arquivo de `.env` resultante.

2. Registre e inicie um gateway de IoT Edge do [Linux](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) ou [Windows](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-windows) e anote seu `device id`.

### <a name="deploy-to-an-edge-device"></a>Implantar em um dispositivo de borda

1. Entre no [portal do Azure](https://portal.azure.com/) e navegue até o Hub IOT.

2. Selecione **IOT Edge** no menu à esquerda.

3. Clique na ID do dispositivo de destino na lista de dispositivos.

4. Selecione **Definir Módulos**.

5. Na seção **módulos de implantação** da página, selecione **Adicionar** e **IOT Edge módulo.**

6. Na caixa de diálogo **IOT Edge módulo personalizado** , use `opctwin` como nome para o módulo e, em seguida, ESPECIFIQUE o *URI da imagem* de contêiner como

   ```bash
   mcr.microsoft.com/iotedge/opc-twin:latest
   ```

   Como *Opções de criação de contêiner*, use o seguinte JSON:

   ```json
   {"NetworkingConfig": {"EndpointsConfig": {"host": {}}}, "HostConfig": {"NetworkMode": "host" }}
   ```

   Preencha os campos opcionais, se necessário. Para obter mais informações sobre opções de criação de contêiner, política de reinicialização e status desejado, consulte [EdgeAgent Desired Properties](https://docs.microsoft.com/azure/iot-edge/module-edgeagent-edgehub#edgeagent-desired-properties). Para obter mais informações sobre o módulo número de atualizações, consulte [definir ou atualizar as propriedades desejadas](https://docs.microsoft.com/azure/iot-edge/module-composition#define-or-update-desired-properties).

7. Selecione **salvar** e repita a etapa **5**.  

8. Na caixa de diálogo IoT Edge módulo personalizado, use `opcpublisher` como nome para o módulo e o *URI da imagem* de contêiner como 

   ```bash
   mcr.microsoft.com/iotedge/opc-publisher:latest
   ```

   Como *Opções de criação de contêiner*, use o seguinte JSON:

   ```json
   {"Hostname":"publisher","Cmd":["publisher","--pf=./pn.json","--di=60","--to","--aa","--si=0","--ms=0"],"ExposedPorts":{"62222/tcp":{}},"HostConfig":{"PortBindings":{"62222/tcp":[{"HostPort":"62222"}] }}}
   ```

9. Selecione **salvar** e **Avançar** para continuar na seção rotas.

10. Na guia rotas, Cole o seguinte 

    ```json
    {
      "routes": {
        "opctwinToIoTHub": "FROM /messages/modules/opctwin/* INTO $upstream",
        "opcpublisherToIoTHub": "FROM /messages/modules/opcpublisher/* INTO $upstream"
      }
    }
    ```

    e selecione **Avançar**

11. Examine as informações de implantação e o manifesto.  Ele deve ser semelhante ao manifesto de implantação acima.  Selecione **Enviar**.

12. Depois de implantar módulos no seu dispositivo, você pode exibir todos eles na página de **detalhes do dispositivo** do Portal. Esta página exibe o nome de cada módulo implantado, bem como informações úteis, como o status da implantação e o código de saída.

## <a name="deploying-using-azure-cli"></a>Implantando usando o CLI do Azure

### <a name="prerequisites"></a>Pré-requisitos

1. Instale a versão mais recente da [interface de linha de comando do Azure (AZ)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) [aqui](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

### <a name="quickstart"></a>Início Rápido

1. Salve o manifesto de implantação acima em um arquivo `deployment.json`.  

2. Use o seguinte comando para aplicar a configuração a um dispositivo IoT Edge:

   ```bash
   az iot edge set-modules --device-id [device id] --hub-name [hub name] --content ./deployment.json
   ```

   O parâmetro `device id` diferencia maiúsculas de minúsculas. O parâmetro de conteúdo aponta para o arquivo de manifesto de implantação que você salvou. 
    ![AZ IoT Edge Set-modules output](https://docs.microsoft.com/azure/iot-edge/media/how-to-deploy-cli/set-modules.png)

3. Depois de implantar módulos no seu dispositivo, você pode exibir todos eles com o seguinte comando:

   ```bash
   az iot hub module-identity list --device-id [device id] --hub-name [hub name]
   ```

   O parâmetro de ID do dispositivo diferencia maiúsculas de minúsculas. ![módulo AZ IOT Hub-saída da lista de identidades](https://docs.microsoft.com/azure/iot-edge/media/how-to-deploy-cli/list-modules.png)

## <a name="next-steps"></a>Passos seguintes

Agora que você aprendeu a implantar o OPC mydo zero, aqui está a próxima etapa sugerida:

> [!div class="nextstepaction"]
> [Implantar o OPC "an" em um projeto existente](howto-opc-twin-deploy-existing.md)
