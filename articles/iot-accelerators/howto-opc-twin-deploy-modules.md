---
title: Como implementar o módulo duplo de OPC para o Azure a partir do zero | Documentos da Microsoft
description: Como implementar OPC duplo a partir do zero.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: f470beb79e69b5a4a3febeb6a433c48490b96cf7
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59491361"
---
# <a name="deploy-opc-twin-module-and-dependencies-from-scratch"></a>Implementar o módulo duplo de OPC e as dependências do zero

O módulo duplo de OPC é executado no IoT Edge e fornece vários serviços de borda para o dispositivo duplo OPC e serviços de registo. 

Existem várias opções para implementar módulos para sua [do Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) Gateway, entre eles

- [Implementar a partir do painel do portal do Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-modules-portal)
- [Implementar com a CLI de AZ](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-monitor-cli)

> [!NOTE]
> Para obter mais informações sobre os detalhes da implementação e instruções, consulte o GitHub [repositório](https://github.com/Azure/azure-iiot-components).

## <a name="deployment-manifest"></a>Manifesto de implementação

Todos os módulos são implementados através de um manifesto de implantação.  Um manifesto de exemplo para implantar ambas [o publicador OPC](https://github.com/Azure/iot-edge-opc-publisher) e [OPC duplo](https://github.com/Azure/azure-iiot-opc-twin-module) é mostrado abaixo.

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
            "restartPolicy": "never",
            "settings": {
              "image": "mcr.microsoft.com/iotedge/opc-twin:latest",
              "createOptions": "{\"HostConfig\": { \"NetworkMode\": \"host\", \"CapAdd\": [\"NET_ADMIN\"] } }"
            }
          },
          "opcpublisher": {
            "version": "2.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "never",
            "settings": {
              "image": "mcr.microsoft.com/iotedge/opc-publisher:latest",
              "createOptions": "{\"Hostname\": \"publisher\", \"Cmd\": [ \"publisher\", \"--pf=./pn.json\", \"--di=60\", \"--to\", \"--aa\", \"--si=0\", \"--ms=0\" ], \"ExposedPorts\": { \"62222/tcp\": {} }, \"HostConfig\": { \"PortBindings\": { \"62222/tcp\": [{ \"HostPort\": \"62222\" }] } } }"
            }
          }
        }
      }
    },
    "$edgeHub": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "routes": {
          "opctwinToIoTHub": "FROM /messages/modules/opctwin/outputs/* INTO $upstream",
          "opcpublisherToIoTHub": "FROM /messages/modules/opcpublisher/outputs/* INTO $upstream"
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 7200
        }
      }
    }
  }
}
```

## <a name="deploying-from-azure-portal"></a>Implementar a partir do portal do Azure

É a maneira mais fácil de implantar os módulos para um dispositivo de gateway do Azure IoT Edge através do portal do Azure.  

### <a name="prerequisites"></a>Pré-requisitos

1. Implementar o duplo de OPC [dependências](howto-opc-twin-deploy-dependencies.md) e de obteve resultante `.env` ficheiro. Tenha em atenção o implementada `hub name` das `PCS_IOTHUBREACT_HUB_NAME` variável no resultante `.env` ficheiro.

2. Registre-se e iniciar um [Linux](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) ou [Windows](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-windows) gateway do IoT Edge bem como observar seu `device id`.

### <a name="deploy-to-an-edge-device"></a>Implementar um dispositivo do edge

1. Inicie sessão para o [portal do Azure](https://portal.azure.com/) e navegue até ao seu hub IoT.

2. Selecione **IoT Edge** no menu esquerdo.

3. Clique no ID de dispositivo de destino na lista de dispositivos.

4. Selecione **Definir Módulos**.

5. Na **módulos de implementação** secção da página, selecione **Add** e **módulo do IoT Edge.**

6. Na **módulo do IoT Edge personalizada** utilização de caixa de diálogo `opctwin` como nome para o módulo, em seguida, especifique o contentor *URI da imagem* como

   ```bash
   mcr.microsoft.com/iotedge/opc-twin:latest
   ```

   Como *criar opções* utilize o seguinte JSON:

   ```json
   {"HostConfig":{"NetworkMode":"host","CapAdd":["NET_ADMIN"]}}
   ```

   Preencha os campos opcionais, se necessário. Para obter mais informações sobre o contentor criar uma política de reinício de opções e ver o estado pretendido [propriedades pretendidas do EdgeAgent](https://docs.microsoft.com/azure/iot-edge/module-edgeagent-edgehub#edgeagent-desired-properties). Para obter mais informações sobre o duplo do módulo, consulte [definir ou atualizar propriedades pretendidas](https://docs.microsoft.com/azure/iot-edge/module-composition#define-or-update-desired-properties).

7. Selecione **salvar** e repita o passo **5**.  

8. Na caixa de diálogo módulo do IoT Edge personalizada, utilize `opcpublisher` como nome para o módulo e o contentor *URI da imagem* como 

   ```bash
   mcr.microsoft.com/iotedge/opc-publisher:latest
   ```

   Como *criar opções* utilize o seguinte JSON:

   ```json
   {"Hostname":"publisher","Cmd":["publisher","--pf=./pn.json","--di=60","--to","--aa","--si=0","--ms=0"],"ExposedPorts":{"62222/tcp":{}},"HostConfig":{"PortBindings":{"62222/tcp":[{"HostPort":"62222"}] }}}
   ```

9. Selecione **salvar** e, em seguida **próxima** para continuar para a secção de rotas.

10. No separador de rotas, cole o seguinte 

    ```json
    {
      "routes": {
        "opctwinToIoTHub": "FROM /messages/modules/opctwin/outputs/* INTO $upstream",
        "opcpublisherToIoTHub": "FROM /messages/modules/opcpublisher/outputs/* INTO $upstream"
      }
    }
    ```

    e selecione **seguinte**

11. Reveja as suas informações de implementação e de manifesto.  Deve ser semelhante o manifesto de implantação acima.  Selecione **submeter**.

12. Após a implantação de módulos para o seu dispositivo, pode ver todos eles do **detalhes do dispositivo** página do portal. Esta página apresenta o nome de cada módulo implementado, bem como informações úteis, como o código de estado e de saída de implementação.

## <a name="deploying-using-azure-cli"></a>Implementar com a CLI do Azure

### <a name="prerequisites"></a>Pré-requisitos

1. Instale a versão mais recente dos [interface de linha de comandos do Azure (AZ)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) partir [aqui](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

### <a name="quickstart"></a>Início Rápido

1. Guardar o manifesto de implantação acima num `deployment.json` ficheiro.  

2. Utilize o seguinte comando para aplicar a configuração para um dispositivo IoT Edge:

   ```bash
   az iot edge set-modules --device-id [device id] --hub-name [hub name] --content ./deployment.json
   ```

   O `device id` parâmetro diferencia maiúsculas de minúsculas. Os pontos de parâmetro de conteúdo para a implementação do manifestam do ficheiro que guardou. 
    ![az IoT Edge set-modules output](https://docs.microsoft.com/azure/iot-edge/media/how-to-deploy-cli/set-modules.png)

3. Após a implantação de módulos para o seu dispositivo, pode ver todos eles com o seguinte comando:

   ```bash
   az iot hub module-identity list --device-id [device id] --hub-name [hub name]
   ```

   O parâmetro de ID de dispositivo diferencia maiúsculas de minúsculas. ![saída da lista de identidade do módulo AZ iot hub](https://docs.microsoft.com/azure/iot-edge/media/how-to-deploy-cli/list-modules.png)

## <a name="run-and-debug-locally"></a>Executar e depurar localmente

Para problemas de resolução e depurá-la são útil para executar os módulos de extremidade localmente, utilizando o [simulador de desenvolvimento do IoT Edge](https://github.com/Azure/iotedgehubdev).  Ele fornece uma experiência de desenvolvimento local com um simulador para criar, desenvolver, testar, em execução e depuração de módulos do Azure IoT Edge e soluções com o mesmo código/bits, que são utilizadas na produção.

### <a name="prerequisites"></a>Pré-requisitos

1. Implementar o duplo de OPC [dependências](howto-opc-twin-deploy-dependencies.md).

2. Instale [(18.02.0+) do Docker CE](https://www.docker.com/community-edition) nos [Windows](https://docs.docker.com/docker-for-windows/install/), [macOS](https://docs.docker.com/docker-for-mac/install/) ou [Linux](https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-docker-ce).

3. Instale [Docker Compose (1.20.0+)](https://docs.docker.com/compose/install/#install-compose) (apenas necessário para **Linux**. Compor já foi incluído na instalação do Windows/macOS Docker CE)

4. Instalar [Python (2.7 / 3.5+) e o Pip](https://www.python.org/)

5. Instalar iotedgehubdev executando o comando no seu terminal abaixo

   ```bash
   pip install --upgrade iotedgehubdev
   ```

> [!NOTE]
> Instale `iotedgehubdev` para **raiz** em Linux/macOS (*não utilize "– utilizador" opção no comando 'instalação do pip'*).
> Certifique-se de que não existe nenhum tempo de execução do Azure IoT Edge em execução na mesma máquina com iotedgehubdev, já que exigem que as mesmas portas.

### <a name="quickstart"></a>Início Rápido

1. Siga as instruções para [criar um dispositivo do Edge no portal do Azure](https://docs.microsoft.com/azure/iot-edge/how-to-register-device-portal).  Copie a cadeia de ligação de dispositivo do edge.

2. Configure o simulador utilizando a cadeia de ligação do edge.

    ```bash
    iotedgehubdev setup -c <edge-device-connection-string>
    ```

3. Cópia acima manifesto num `deployment.json` ficheiro na mesma pasta.  Iniciar a implementação no simulador com

    ```bash
    iotedgehubdev start -d deployment.json
    ```

4. Parar de utilizar o simulador

   ```bash
   iotedgehubdev stop
   ```

## <a name="next-steps"></a>Passos Seguintes

Agora que sabe como implementar OPC duplo a partir do zero, este é o passo seguinte sugerido:

> [!div class="nextstepaction"]
> [Implementar duplo de OPC para um projeto existente](howto-opc-twin-deploy-existing.md)