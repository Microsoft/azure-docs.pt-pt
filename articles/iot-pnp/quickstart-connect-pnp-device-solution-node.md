---
title: Interaja com o dispositivo IoT Plug e Play Preview ligado à sua solução - Node.js Microsoft Docs
description: Utilize Node.js para ligar e interagir com um dispositivo IoT Plug e Play Preview que esteja ligado à sua solução Azure IoT.
author: miagdp
ms.author: miag
ms.date: 12/27/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 7f86ae8ffefa88fe1cdfa429b7de4671422839ff
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87044004"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-nodejs"></a>Quickstart: Interaja com um dispositivo IoT Plug e Play Preview que esteja ligado à sua solução (Node.js)

[!INCLUDE [iot-pnp-quickstarts-3-selector.md](../../includes/iot-pnp-quickstarts-3-selector.md)]

IoT Plug e Play Preview simplifica o IoT, permitindo-lhe interagir com as capacidades de um dispositivo sem conhecimento da implementação do dispositivo subjacente. Este quickstart mostra-lhe como usar Node.js para ligar e controlar um dispositivo IoT Plug and Play que está ligado à sua solução.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este arranque rápido, precisa de Node.js na sua máquina de desenvolvimento. Pode descarregar a versão mais recente recomendada para várias plataformas a partir de [nodejs.org](https://nodejs.org).

Pode verificar qual a versão atual do Node.js no seu computador de desenvolvimento através do seguinte comando:

```cmd/sh
node --version
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Executar o seguinte comando para obter a _cadeia de ligação do hub IoT_ para o seu hub (nota para utilização posterior):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="run-the-sample-device"></a>Executar o dispositivo de amostra

Neste arranque rápido, utiliza-se um sensor ambiental de amostra que está escrito em Node.js como o dispositivo IoT Plug and Play. As seguintes instruções mostram-lhe como instalar e executar o dispositivo:

1. Abra uma janela terminal no diretório à sua escolha. Execute o seguinte comando para clonar as [amostras Azure IoT para Node.js](https://github.com/azure-samples/azure-iot-samples-node) repositório GitHub neste local:

    ```cmd/sh
    git clone https://github.com/azure-samples/azure-iot-samples-node
    ```

1. Esta janela de terminal será agora utilizada como terminal do seu _dispositivo._ Vá à pasta do seu repositório clonado e navegue para a pasta **/azure-iot-samples-node/digital-twins/Quickstarts/Device.** Instale todas as dependências executando o seguinte comando:

    ```cmd/sh
    npm install
    ```

1. Configure a _cadeia de ligação_do dispositivo:

    ```cmd/sh
    set DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. Executar a amostra com o seguinte comando:

    ```cmd/sh
    node sample_device.js
    ```

1. Vê mensagens a dizer que o dispositivo enviou algumas informações e reportou-se online. Isto indica que o dispositivo começou a enviar dados de telemetria para o hub, estando agora pronto para receber comandos e atualizações de propriedade. Não feche este terminal, vai precisar dele mais tarde para confirmar que as amostras de serviço também funcionaram.

## <a name="run-the-sample-solution"></a>Executar a solução de amostra

Neste arranque rápido, utilize uma solução IoT de amostra em Node.js para interagir com o dispositivo de amostra.

1. Abra outra janela do terminal (este será o seu terminal _de serviço)._ Vá à pasta do seu repositório clonado e navegue para a pasta **/azure-iot-samples-node/digital-twins/Quickstarts/Service.** Instale todas as dependências executando o seguinte comando:

    ```cmd/sh
    npm install
    ```

1. Configure a _cadeia de ligação do hub IoT_ para permitir que o serviço se conecte a ele:

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIoTHubConnectionString>
    ```

### <a name="read-a-property"></a>Ler uma propriedade

1. Quando ligou o _dispositivo_ no seu terminal, viu a seguinte mensagem indicando o seu estado online:

    ```cmd/sh
    reported state property as online
    ```

1. Na pasta **/azure-iot-samples-node/digital-twins/Quickstarts/Service,** abra o ficheiro **get_digital_twin.js**. Substitua o `<DEVICE_ID_GOES_HERE>` espaço reservado pelo ID do dispositivo e guarde o ficheiro.

1. Vá ao terminal _de serviço_ e use o seguinte comando para executar a amostra para obter informações do dispositivo de leitura:

    ```cmd/sh
    node get_digital_twin.js
    ```

1. Na saída do terminal _de serviço,_ percorra para o `environmentalSensor` componente. Você vê que a `state` propriedade foi reportada como _on-line_:

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

1. Abra o ficheiro **update_digital_twin_property.js**.

1. No início do ficheiro, há um conjunto de constantes definidas com espaços reservados maiúsculas. Substitua o `<DEVICE_ID_GOES_HERE>` espaço reservado pelo seu ID do dispositivo real, atualize as constantes restantes pelos seguintes valores e guarde o ficheiro:

    ```javascript
    const interfaceInstanceName = 'environmentalSensor';
    const propertyName = 'brightness';
    const propertyValue = 42;
    ```

1. Vá ao terminal _de serviço_ e use o seguinte comando para executar a amostra para atualizar o imóvel:

    ```cmd/sh
    node update_digital_twin_property.js
    ```

1. A saída do terminal de _serviço_ mostra as informações atualizadas do dispositivo. Percorra o `environmentalSensor` componente para ver o novo valor de luminosidade de 42.

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

1. Vá ao terminal do seu _dispositivo,_ veja que o dispositivo recebeu a atualização:

    ```cmd/sh
    Received an update for brightness: 42
    updated the property
    ```
2. Volte ao seu terminal _de serviço_ e execute o comando abaixo para obter novamente a informação do dispositivo, para confirmar que a propriedade foi atualizada.
    
    ```cmd/sh
    node get_digital_twin.js
    ```

3. Na saída do terminal _de serviço,_ sob o `environmentalSensor` componente, vê-se o valor de luminosidade atualizado relatado. Nota: pode demorar algum tempo até que o dispositivo termine a atualização. Pode repetir este passo até que o dispositivo tenha processado a atualização da propriedade.
    
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

1. Abra o ficheiro **invoke_command.js**.

1. No início do ficheiro, substitua o `<DEVICE_ID_GOES_HERE>` espaço reservado pelo seu ID do dispositivo real. Atualize as constantes restantes com os seguintes valores e, em seguida, guarde o ficheiro:

    ```javascript
    const interfaceInstanceName = 'environmentalSensor';
    const commandName = 'blink';
    const commandArgument = '<For the environmental sensor, this value does not matter. Any string will do.>'; 
    ```

1. Vai para o terminal _de serviço._ Utilize o seguinte comando para executar a amostra para invocar o comando:

    ```cmd/sh
    node invoke_command.js
    ```

1. A saída no terminal _de serviço_ deve apresentar a seguinte confirmação:

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

Neste arranque rápido, aprendeu a ligar um dispositivo IoT Plug e Play a uma solução IoT. Para saber mais sobre como construir uma solução que interage com os seus dispositivos IoT Plug e Play, consulte:

> [!div class="nextstepaction"]
> [Como fazer: Ligar e interagir com um dispositivo](howto-develop-solution.md)
