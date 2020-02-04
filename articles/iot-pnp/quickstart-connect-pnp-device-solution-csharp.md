---
title: Interagir com um dispositivo de visualização de Plug and Play IoT conectado à solução de IoT do Azure | Microsoft Docs
description: Use C# o (.net) para se conectar e interagir com um dispositivo de visualização de plug and Play IOT que está conectado à sua solução de IOT do Azure.
author: dominicbetts
ms.author: dobett
ms.date: 12/30/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 0953f68839217c1c75eb86f8399ce023f3863ab4
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/02/2020
ms.locfileid: "76963976"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-c"></a>Início rápido: interagir com um dispositivo de visualização de Plug and Play IoT que está conectado àC#sua solução ()

[!INCLUDE [iot-pnp-quickstarts-3-selector.md](../../includes/iot-pnp-quickstarts-3-selector.md)]

O IoT Plug and Play Preview simplifica a IoT, permitindo que você interaja com os recursos de um dispositivo sem conhecer a implementação do dispositivo subjacente. Este guia de início rápido mostra como C# usar o (com o .net) para se conectar e controlar um dispositivo de plug and Play de IOT que está conectado à sua solução.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido, você precisa instalar o .NET Core (2. x. x ou 3. x. x) em seu computador de desenvolvimento. Você pode baixar sua versão preferida do SDK do .NET Core para que várias plataformas [Baixem o .NET Core](https://dotnet.microsoft.com/download/dotnet-core/).

Você pode verificar a versão do .NET que está em seu computador de desenvolvimento executando o seguinte comando em uma janela de terminal local: 

```cmd/sh
dotnet --version
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Execute o seguinte comando para obter a _cadeia de conexão do Hub IOT_ para o Hub (observação para uso posterior):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="run-the-sample-device"></a>Executar o dispositivo de exemplo

Neste guia de início rápido, você usa um sensor ambiental de exemplo que C# é escrito como o dispositivo de plug and Play de IOT. As instruções a seguir mostram como instalar e executar o dispositivo:

1. Abra uma janela de terminal no diretório de sua escolha. Execute o seguinte comando para clonar os [exemplos do Azure C# IOT para](https://github.com/Azure-Samples/azure-iot-samples-csharp) o repositório do GitHub (.net) neste local:

    ```cmd/sh
    git clone https://github.com/Azure-Samples/azure-iot-samples-csharp
    ```

1. Esta janela do terminal agora será usada como terminal do _dispositivo_ . Vá para a pasta do repositório clonado e navegue até a pasta **/Azure-IOT-Samples-Csharp/digitaltwin/Samples/Device/EnvironmentalSensorSample** .

1. Configure a _cadeia de conexão do dispositivo_:

    ```cmd/sh
    set DIGITAL_TWIN_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. Crie os pacotes necessários e execute o exemplo com o seguinte comando:

    ```cmd\sh
        dotnet run
    ```

1. Você vê mensagens dizendo que o dispositivo foi registrado com êxito e está aguardando atualizações da nuvem. Isso indica que o dispositivo agora está pronto para receber comandos e atualizações de propriedade e começou a enviar dados de telemetria para o Hub. Não feche este terminal, você precisará dele mais tarde para confirmar se os exemplos de serviço também funcionaram.

## <a name="run-the-sample-solution"></a>Executar a solução de exemplo

Neste guia de início rápido, você usa uma solução de C# IOT de exemplo no para interagir com o dispositivo de exemplo.

1. Abra outra janela de terminal (esse será o terminal de _serviço_ ). Vá para a pasta do repositório clonado e navegue até a pasta **/Azure-IOT-Samples-Csharp/digitaltwin/Samples/Service** .

1. Configure a _cadeia de conexão do Hub IOT_ e a _ID do dispositivo_ para permitir que o serviço se conecte a ambos:

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIoTHubConnectionString>
    set DEVICE_ID=<YourDeviceID>
    ```

### <a name="read-a-property"></a>Ler uma propriedade

1. Quando você conectou o _dispositivo_ em seu terminal, você viu a seguinte mensagem indicando seu status online:

    ```cmd/sh
    Waiting to receive updates from cloud...
    ```

1. Vá para o terminal do _serviço_ e use os seguintes comandos para executar o exemplo de leitura de informações do dispositivo:

    ```cmd/sh
    cd GetDigitalTwin
    dotnet run
    ```

1. Na saída de terminal do _serviço_ , role até o componente `environmentalSensor`. Você verá que a propriedade `state`, que é usada para indicar se o dispositivo está online ou não, foi relatado como _verdadeiro_:

    ```JSON
    "environmentalSensor": {
      "name": "environmentalSensor",
      "properties": {
        "state": {
          "reported": {
            "value": true
          }
        }
      }
    }
    ```

### <a name="update-a-writable-property"></a>Atualizar uma propriedade gravável

1. Vá para o terminal do _serviço_ e defina as seguintes variáveis para definir qual propriedade deve ser atualizada:
    ```cmd/sh
    set INTERFACE_INSTANCE_NAME=environmentalSensor
    set PROPERTY_NAME=brightness
    set PROPERTY_VALUE=42
    ```

1. Use os comandos a seguir para executar o exemplo de atualização da propriedade:

    ```cmd/sh
    cd ..\UpdateProperty
    dotnet run
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
            "value": true
          }
        }
      }
    }
    ```

1. Vá para o terminal do _dispositivo_ , você verá que o dispositivo recebeu a atualização:

    ```cmd/sh
    Received updates for property 'brightness'
    Desired brightness = '"42"'.
    Reported brightness = ''.
    Version is '2'.
    Sent pending status for brightness property.
    Sent completed status for brightness property.
    ```
2. Volte para o terminal do _serviço_ e execute os comandos abaixo para obter as informações do dispositivo novamente, para confirmar se a propriedade foi atualizada.
    
    ```cmd/sh
    cd ..\GetDigitalTwin
    dotnet run
    ```
3. Na saída de terminal do _serviço_ , no componente `environmentalSensor`, você verá que o valor de brilho atualizado foi relatado. Observação: pode levar algum tempo para que o dispositivo conclua a atualização. Você pode repetir essa etapa até que o dispositivo realmente processe a atualização de propriedade.
    
    ```json
    "environmentalSensor": {
      "name": "environmentalSensor",
      "properties": {
        "brightness": {
          "desired": {
            "value": "42"
          },
          "reported": {
            "value": "42",
            "desiredState": {
              "code": 200,
              "description": "Request completed",
              "version": 2
            }
          }
        },
        "state": {
          "reported": {
            "value": true
          }
        }
      }
    },
    ```

### <a name="invoke-a-command"></a>Invocar um comando

1. Vá para o terminal do _serviço_ e defina as seguintes variáveis para definir qual comando invocar:
    ```cmd/sh
    set INTERFACE_INSTANCE_NAME=environmentalSensor
    set COMMAND_NAME=blink
    ```

1. Use os comandos a seguir para executar o exemplo para invocar o comando:

    ```cmd/sh
    cd ..\InvokeCommand
    dotnet run
    ```

1. A saída no terminal do _serviço_ deve mostrar a seguinte confirmação:

    ```cmd/sh
    Invoking blink on device <YourDeviceID> with interface instance name environmentalSensor
    Command blink invoked on the device successfully, the returned status was 200 and the request id was <some ID value>
    The returned payload was
    {"description": "abc"}
    Enter any key to finish
    ```

1. Vá para o terminal do _dispositivo_ , você verá que o comando foi confirmado:

    ```cmd/sh
    Command - blink was invoked from the service
    Data - null
    Request Id - <some ID value>.
    ```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você aprendeu a conectar um dispositivo de Plug and Play IoT a uma solução de IoT. Para saber mais sobre como criar uma solução que interage com seus dispositivos de Plug and Play de IoT, confira:

> [!div class="nextstepaction"]
> [Como: conectar-se e interagir com um dispositivo](howto-develop-solution.md)
