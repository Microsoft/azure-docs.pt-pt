---
title: Interagir com um dispositivo de visualização de Plug and Play IoT conectado à solução de IoT do Azure | Microsoft Docs
description: Use o Node. js para se conectar e interagir com um dispositivo de visualização de Plug and Play IoT que está conectado à sua solução de IoT do Azure.
author: miagdp
ms.author: miag
ms.date: 08/02/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 3275c01059a61e4eb8591948695656f4e4b722ed
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74152119"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-nodejs"></a>Início rápido: interagir com um dispositivo de visualização de Plug and Play IoT que está conectado à sua solução (Node. js)

O IoT Plug and Play Preview simplifica a IoT, permitindo que você interaja com os recursos de um dispositivo sem conhecer a implementação do dispositivo subjacente. Este guia de início rápido mostra como usar o Node. js para se conectar e controlar um dispositivo de Plug and Play de IoT que está conectado à sua solução.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido, você precisa do node. js em seu computador de desenvolvimento. Você pode baixar a versão mais recente recomendada para várias plataformas do [NodeJS.org](https://nodejs.org).

Pode verificar qual a versão atual do Node.js no seu computador de desenvolvimento através do seguinte comando:

```cmd/sh
node --version
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub-windows.md](../../includes/iot-pnp-prepare-iot-hub-windows.md)]

## <a name="connect-your-device"></a>Ligar o dispositivo

Neste guia de início rápido, você usa um sensor ambiental de exemplo que é escrito em node. js como o dispositivo de Plug and Play de IoT. As instruções a seguir mostram como instalar e executar o dispositivo:

1. Abra uma janela de terminal no diretório de sua escolha. Execute o seguinte comando para clonar os exemplos de IoT do Azure para o repositório GitHub [do node. js](https://github.com/azure-samples/azure-iot-samples-node) neste local:

    ```cmd/sh
    git clone https://github.com/azure-samples/azure-iot-samples-node
    ```

1. Esta janela do terminal agora será usada como terminal do _dispositivo_ . Acesse o repositório clonado e navegue até a pasta **/Azure-IOT-Samples-node/digital-Twins/QuickStarts/Device** Instale todas as dependências executando o seguinte comando:

    ```cmd/sh
    npm install
    ```

1. Configure a _cadeia de conexão do dispositivo_:

    ```cmd/sh
    set DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. Execute o exemplo com o seguinte comando:

    ```cmd/sh
    node sample_device.js
    ```

1. Você vê mensagens dizendo que o dispositivo enviou algumas informações e se reportou online. Isso indica que o dispositivo começou a enviar dados de telemetria para o Hub e agora está pronto para receber comandos e atualizações de propriedade. Não feche este terminal, você precisará dele mais tarde para confirmar se os exemplos de serviço também funcionaram.

## <a name="build-the-solution"></a>Compilar a solução

Neste guia de início rápido, você usa uma solução de IoT de exemplo no node. js para interagir com o dispositivo de exemplo.

1. Abra outra janela de terminal (esse será o terminal de _serviço_ ). Vá para a pasta do repositório clonado e navegue até a pasta **/Azure-IOT-Samples-node/digital-Twins/QuickStarts/Service** . Instale todas as dependências executando o seguinte comando:

    ```cmd/sh
    npm install
    ```

1. Configure a _cadeia de conexão do Hub IOT_ para permitir que o serviço se conecte a ela:

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIoTHubConnectionString>
    ```

### <a name="read-a-property"></a>Ler uma propriedade

1. Quando você conectou o _dispositivo_ em seu terminal, você viu a seguinte mensagem indicando seu status online:

    ```cmd/sh
    reported state property as online
    ```

1. Na pasta **/Azure-IOT-Samples-node/digital-Twins/QuickStarts/Service** , abra o arquivo **get_digital_twin. js**. Substitua o espaço reservado `<DEVICE_ID_GOES_HERE>` pela ID do dispositivo e salve o arquivo.

1. Vá para o terminal do _serviço_ e use o comando a seguir para executar o exemplo de leitura de informações do dispositivo:

    ```cmd/sh
    node get_digital_twin.js
    ```

1. Na saída de terminal do _serviço_ , role até o componente `environmentalSensor`. Você verá que a propriedade `state` foi relatada como _online_:

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

### <a name="update-a-writable-property"></a>Atualizar uma propriedade gravável

1. Abra o arquivo **update_digital_twin_property. js**.

1. No início do arquivo, há um conjunto de constantes definidas com espaços reservados em maiúsculas. Substitua o espaço reservado `<DEVICE_ID_GOES_HERE>` pela sua ID de dispositivo real, atualize as constantes restantes com os seguintes valores e salve o arquivo:

    ```javascript
    const interfaceInstanceName = 'environmentalSensor';
    const propertyName = 'brightness';
    const propertyValue = 42;
    ```

1. Vá para o terminal do _serviço_ e use o seguinte comando para executar o exemplo de atualização da propriedade:

    ```cmd/sh
    node update_digital_twin_property.js
    ```

1. A saída do terminal do _serviço_ mostra as informações atualizadas do dispositivo. Role até o componente `environmentalSensor` para ver o novo valor de brilho de 42.

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

1. Vá para o terminal do _dispositivo_ , você verá que o dispositivo recebeu a atualização:

    ```cmd/sh
    Received an update for brightness: 42
    updated the property
    ```
2. Volte para o terminal do _serviço_ e execute o comando abaixo para obter as informações do dispositivo novamente, para confirmar se a propriedade foi atualizada.
    
    ```cmd/sh
    node get_digital_twin.js
    ```

3. Na saída de terminal do _serviço_ , no componente `environmentalSensor`, você verá que o valor de brilho atualizado foi relatado. Observação: pode levar algum tempo para que o dispositivo conclua a atualização. Você pode repetir essa etapa até que o dispositivo realmente processe a atualização de propriedade.
    
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

1. Abra o arquivo **invoke_command. js**.

1. No início do arquivo, substitua o espaço reservado `<DEVICE_ID_GOES_HERE>` pela sua ID de dispositivo real. Atualize as constantes restantes com os seguintes valores e salve o arquivo:

    ```javascript
    const interfaceInstanceName = 'environmentalSensor';
    const commandName = 'blink';
    const commandArgument = '<For the environmental sensor, this value does not matter. Any string will do.>'; 
    ```

1. Vá para o terminal do _serviço_ . Use o seguinte comando para executar o exemplo para invocar o comando:

    ```cmd/sh
    node invoke_command.js
    ```

1. A saída no terminal do _serviço_ deve mostrar a seguinte confirmação:

    ```cmd/sh
    invoking command blink on interface instanceenvironmentalSensor for device <device ID>...
    {
      "result": "helpful response text",
      "statusCode": 200,
      "requestId": "<some ID value>",
      "_response": "helpful response text"
    }
    ```

1. Vá para o terminal do _dispositivo_ , você verá que o comando foi confirmado:

    ```cmd/sh
    received command: blink for interfaceInstance: environmentalSensor
    acknowledgement succeeded.
    ```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você aprendeu a conectar um dispositivo de Plug and Play IoT a uma solução de IoT. Para saber mais sobre como criar uma solução que interage com seus dispositivos de Plug and Play de IoT, confira:

> [!div class="nextstepaction"]
> [Como: conectar-se e interagir com um dispositivo](howto-develop-solution.md)
