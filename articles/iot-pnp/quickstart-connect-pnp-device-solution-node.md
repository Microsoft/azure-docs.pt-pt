---
title: Interaja com um dispositivo de pré-visualização IoT Plug e Play Preview ligado à sua solução Azure IoT [ Microsoft Docs
description: Utilize o Node.js para se conectar e interagir com um dispositivo IoT Plug e Play Preview ligado à sua solução Azure IoT.
author: miagdp
ms.author: miag
ms.date: 12/27/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 9962763e647faddc5a2179f304aeb3fa8ca256e8
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "75550745"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-nodejs"></a>Quickstart: Interaja com um dispositivo de pré-visualização IoT Plug e Play que está ligado à sua solução (Node.js)

[!INCLUDE [iot-pnp-quickstarts-3-selector.md](../../includes/iot-pnp-quickstarts-3-selector.md)]

IoT Plug e Play Preview simplifica o IoT permitindo-lhe interagir com as capacidades de um dispositivo sem conhecer a implementação do dispositivo subjacente. Este quickstart mostra-lhe como usar o Node.js para ligar e controlar um dispositivo IoT Plug and Play que está ligado à sua solução.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este arranque rápido, precisa de Node.js na sua máquina de desenvolvimento. Pode descarregar a versão mais recente recomendada para várias plataformas a partir de [nodejs.org](https://nodejs.org).

Pode verificar qual a versão atual do Node.js no seu computador de desenvolvimento através do seguinte comando:

```cmd/sh
node --version
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Executar o seguinte comando para obter a cadeia de ligação do _hub IoT_ para o seu hub (nota para utilização posterior):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="run-the-sample-device"></a>Executar o dispositivo de amostra

Neste arranque rápido, você usa um sensor ambiental de amostra que está escrito no Node.js como o dispositivo IoT Plug and Play. As seguintes instruções mostram-lhe como instalar e executar o dispositivo:

1. Abra uma janela terminal no diretório à sua escolha. Execute o seguinte comando para clonar as [amostras Azure IoT para](https://github.com/azure-samples/azure-iot-samples-node) o repositório Node.js GitHub neste local:

    ```cmd/sh
    git clone https://github.com/azure-samples/azure-iot-samples-node
    ```

1. Esta janela do terminal será agora utilizada como terminal do _dispositivo._ Vá para a pasta do seu repositório clonado e navegue para a pasta **/azure-iot-samples-node/digital-twins/Quickstarts/Device.** Instale todas as dependências executando o seguinte comando:

    ```cmd/sh
    npm install
    ```

1. Configure a cadeia de ligação do _dispositivo:_

    ```cmd/sh
    set DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. Executar a amostra com o seguinte comando:

    ```cmd/sh
    node sample_device.js
    ```

1. Vê mensagens a dizer que o dispositivo enviou algumas informações e reportou-se online. Isto indica que o dispositivo começou a enviar dados de telemetria para o centro, e está agora pronto para receber comandos e atualizações de propriedade. Não feche este terminal, precisará dele mais tarde para confirmar que as amostras de serviço também funcionaram.

## <a name="run-the-sample-solution"></a>Executar a solução da amostra

Neste arranque rápido, utilize uma solução IoT de amostra no Node.js para interagir com o dispositivo de amostra.

1. Abra outra janela terminal (este será o seu terminal de _serviço)._ Vá para a pasta do seu repositório clonado e navegue para a **pasta /azure-iot-samples-node/digital-twins/Quickstarts/Service** folder. Instale todas as dependências executando o seguinte comando:

    ```cmd/sh
    npm install
    ```

1. Configure a cadeia de ligação do _hub IoT_ para permitir que o serviço se conectem a ele:

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIoTHubConnectionString>
    ```

### <a name="read-a-property"></a>Ler uma propriedade

1. Quando ligou o _dispositivo_ no seu terminal, viu a seguinte mensagem indicando o seu estado online:

    ```cmd/sh
    reported state property as online
    ```

1. Na pasta **/azure-iot-samples-node/digital-twins/Quickstarts/Service,** abra o ficheiro **get_digital_twin.js**. Substitua `<DEVICE_ID_GOES_HERE>` o espaço reservado pelo ID do dispositivo e guarde o ficheiro.

1. Dirija-se ao terminal de _serviço_ e utilize o seguinte comando para executar a amostra para obter informações sobre o dispositivo de leitura:

    ```cmd/sh
    node get_digital_twin.js
    ```

1. Na saída do terminal de `environmentalSensor` _serviço,_ desloque-se para o componente. Você vê `state` que a propriedade foi reportada como _on-line:_

    ```JSON
    "environmentalSensor": {
      "name": "environmentalSensor",
      "properties": {
        "state": {
          "reported": {
            "value": "online"
          }
        }
      }
    }
    ```

### <a name="update-a-writable-property"></a>Atualizar uma propriedade writable

1. Abra o ficheiro **update_digital_twin_property.js.**

1. No início do ficheiro, há um conjunto de constantes definidas com os espaços reservados. Substitua `<DEVICE_ID_GOES_HERE>` o espaço reservado pelo ID do seu dispositivo real, atualize as constantes restantes com os seguintes valores e guarde o ficheiro:

    ```javascript
    const interfaceInstanceName = 'environmentalSensor';
    const propertyName = 'brightness';
    const propertyValue = 42;
    ```

1. Dirija-se ao terminal de _serviço_ e utilize o seguinte comando para executar a amostra para atualizar a propriedade:

    ```cmd/sh
    node update_digital_twin_property.js
    ```

1. A saída do terminal de _serviço_ mostra as informações atualizadas do dispositivo. Percorra `environmentalSensor` o componente para ver o novo valor de luminosidade de 42.

    ```json
    "environmentalSensor": {
      "name": "environmentalSensor",
      "properties": {
        "brightness": {
          "desired": {
            "value": "42"
          }
        },
        "state": {
          "reported": {
            "value": "online"
          }
        }
      }
    }
    ```

1. Vá ao terminal do _seu dispositivo,_ veja que o dispositivo recebeu a atualização:

    ```cmd/sh
    Received an update for brightness: 42
    updated the property
    ```
2. Volte ao seu terminal de _serviço_ e execute o comando abaixo para obter novamente a informação do dispositivo, para confirmar que a propriedade foi atualizada.
    
    ```cmd/sh
    node get_digital_twin.js
    ```

3. Na saída do terminal `environmentalSensor` de _serviço,_ sob o componente, é referido o valor de luminosidade atualizado. Nota: pode demorar algum tempo até que o dispositivo termine a atualização. Pode repetir este passo até que o dispositivo tenha realmente processado a atualização da propriedade.
    
    ```json
    "environmentalSensor": {
      "name": "environmentalSensor",
      "properties": {
        "brightness": {
          "reported": {
            "value": "42",
            "desiredState": {
              "code": 200,
              "version": 2,
              "description": "helpful descriptive text"
            }
          },
          "desired": {
            "value": "42"
          }
        },
        "state": {
          "reported": {
            "value": "online"
          }
        }
      }
    }
    ```

### <a name="invoke-a-command"></a>Invocar um comando

1. Abra o ficheiro **invoke_command.js.**

1. No início do ficheiro, `<DEVICE_ID_GOES_HERE>` substitua o espaço reservado por identificação do seu dispositivo real. Atualize as constantes restantes com os seguintes valores e, em seguida, guarde o ficheiro:

    ```javascript
    const interfaceInstanceName = 'environmentalSensor';
    const commandName = 'blink';
    const commandArgument = '<For the environmental sensor, this value does not matter. Any string will do.>'; 
    ```

1. Vá para o terminal de _serviço._ Utilize o seguinte comando para executar a amostra para invocar o comando:

    ```cmd/sh
    node invoke_command.js
    ```

1. A saída no terminal de _serviço_ deve apresentar a seguinte confirmação:

    ```cmd/sh
    invoking command blink on interface instanceenvironmentalSensor for device <device ID>...
    {
      "result": "helpful response text",
      "statusCode": 200,
      "requestId": "<some ID value>",
      "_response": "helpful response text"
    }
    ```

1. Vá ao terminal do _dispositivo,_ veja que o comando foi reconhecido:

    ```cmd/sh
    received command: blink for interfaceInstance: environmentalSensor
    acknowledgement succeeded.
    ```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a ligar um dispositivo IoT Plug and Play a uma solução IoT. Para saber mais sobre como construir uma solução que interaja com os seus dispositivos IoT Plug and Play, consulte:

> [!div class="nextstepaction"]
> [Como: Ligar e interagir com um dispositivo](howto-develop-solution.md)
