---
title: Interaja com um dispositivo de pré-visualização IoT Plug e Play Preview ligado à sua solução Azure IoT [ Microsoft Docs
description: Utilize C# (.NET) para se conectar e interagir com um dispositivo IoT Plug e Play Preview ligado à sua solução Azure IoT.
author: dominicbetts
ms.author: dobett
ms.date: 12/30/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 0953f68839217c1c75eb86f8399ce023f3863ab4
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76963976"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-c"></a>Quickstart: Interaja com um dispositivo de pré-visualização IoT Plug e Play que está ligado à sua solução (C#)

[!INCLUDE [iot-pnp-quickstarts-3-selector.md](../../includes/iot-pnp-quickstarts-3-selector.md)]

IoT Plug e Play Preview simplifica o IoT permitindo-lhe interagir com as capacidades de um dispositivo sem conhecer a implementação do dispositivo subjacente. Este quickstart mostra-lhe como usar C# (com .NET) para ligar e controlar um dispositivo IoT Plug and Play que está ligado à sua solução.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este arranque rápido, é necessário instalar .NET Core (2.x.x ou 3.x)na sua máquina de desenvolvimento. Pode descarregar a sua versão preferida do .NET Core SDK para várias plataformas a partir de [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core/).

Pode verificar a versão de .NET que está na sua máquina de desenvolvimento executando o seguinte comando numa janela de terminal local: 

```cmd/sh
dotnet --version
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Executar o seguinte comando para obter a cadeia de ligação do _hub IoT_ para o seu hub (nota para utilização posterior):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="run-the-sample-device"></a>Executar o dispositivo de amostra

Neste arranque rápido, você usa um sensor ambiental de amostra que está escrito em C# como o dispositivo IoT Plug and Play. As seguintes instruções mostram-lhe como instalar e executar o dispositivo:

1. Abra uma janela terminal no diretório à sua escolha. Execute o seguinte comando para clonar as [amostras Azure IoT para c# (.NET)](https://github.com/Azure-Samples/azure-iot-samples-csharp) Repositório GitHub neste local:

    ```cmd/sh
    git clone https://github.com/Azure-Samples/azure-iot-samples-csharp
    ```

1. Esta janela do terminal será agora utilizada como terminal do _dispositivo._ Vá para a pasta do seu repositório clonado e navegue para a pasta **/azure-iot-samples-csharp/digitaltwin/Samples/device/EnvironmentalSensorSample.**

1. Configure a cadeia de ligação do _dispositivo:_

    ```cmd/sh
    set DIGITAL_TWIN_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. Construa as embalagens necessárias e execute a amostra com o seguinte comando:

    ```cmd\sh
        dotnet run
    ```

1. Vê mensagens a dizer que o dispositivo se registou com sucesso e está à espera de atualizações a partir da nuvem. Isto indica que o dispositivo está agora pronto para receber comandos e atualizações de propriedade, e começou a enviar dados de telemetria para o centro. Não feche este terminal, precisará dele mais tarde para confirmar que as amostras de serviço também funcionaram.

## <a name="run-the-sample-solution"></a>Executar a solução da amostra

Neste arranque rápido, utiliza-se uma solução IoT de amostra em C# para interagir com o dispositivo de amostra.

1. Abra outra janela terminal (este será o seu terminal de _serviço)._ Vá para a pasta do seu repositório clonado e navegue para a **/azure-iot-samples-csharp/digitaltwin/Samples/service** folder.

1. Configure a cadeia de ligação do _hub IoT_ e o ID do _dispositivo_ para permitir que o serviço se conectem a ambos:

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIoTHubConnectionString>
    set DEVICE_ID=<YourDeviceID>
    ```

### <a name="read-a-property"></a>Ler uma propriedade

1. Quando ligou o _dispositivo_ no seu terminal, viu a seguinte mensagem indicando o seu estado online:

    ```cmd/sh
    Waiting to receive updates from cloud...
    ```

1. Dirija-se ao terminal de _serviço_ e utilize os seguintes comandos para executar a amostra para obter informações sobre o dispositivo de leitura:

    ```cmd/sh
    cd GetDigitalTwin
    dotnet run
    ```

1. Na saída do terminal de `environmentalSensor` _serviço,_ desloque-se para o componente. Você vê `state` que a propriedade, que é usada para indicar se o dispositivo está ou não on-line, foi reportada como _verdadeira:_

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

### <a name="update-a-writable-property"></a>Atualizar uma propriedade writable

1. Vá ao terminal de _serviço_ e defina as seguintes variáveis para definir qual imóvel atualizar:
    ```cmd/sh
    set INTERFACE_INSTANCE_NAME=environmentalSensor
    set PROPERTY_NAME=brightness
    set PROPERTY_VALUE=42
    ```

1. Utilize os seguintes comandos para executar a amostra para atualizar a propriedade:

    ```cmd/sh
    cd ..\UpdateProperty
    dotnet run
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
            "value": true
          }
        }
      }
    }
    ```

1. Vá ao terminal do _seu dispositivo,_ veja que o dispositivo recebeu a atualização:

    ```cmd/sh
    Received updates for property 'brightness'
    Desired brightness = '"42"'.
    Reported brightness = ''.
    Version is '2'.
    Sent pending status for brightness property.
    Sent completed status for brightness property.
    ```
2. Volte ao seu terminal de _serviço_ e execute os comandos abaixo para obter novamente a informação do dispositivo, para confirmar que a propriedade foi atualizada.
    
    ```cmd/sh
    cd ..\GetDigitalTwin
    dotnet run
    ```
3. Na saída do terminal `environmentalSensor` de _serviço,_ sob o componente, é referido o valor de luminosidade atualizado. Nota: pode demorar algum tempo até que o dispositivo termine a atualização. Pode repetir este passo até que o dispositivo tenha realmente processado a atualização da propriedade.
    
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

1. Vá ao terminal de _serviço_ e defina as seguintes variáveis para definir qual o comando invocar:
    ```cmd/sh
    set INTERFACE_INSTANCE_NAME=environmentalSensor
    set COMMAND_NAME=blink
    ```

1. Utilize os seguintes comandos para executar a amostra para invocar o comando:

    ```cmd/sh
    cd ..\InvokeCommand
    dotnet run
    ```

1. A saída no terminal de _serviço_ deve apresentar a seguinte confirmação:

    ```cmd/sh
    Invoking blink on device <YourDeviceID> with interface instance name environmentalSensor
    Command blink invoked on the device successfully, the returned status was 200 and the request id was <some ID value>
    The returned payload was
    {"description": "abc"}
    Enter any key to finish
    ```

1. Vá ao terminal do _dispositivo,_ veja que o comando foi reconhecido:

    ```cmd/sh
    Command - blink was invoked from the service
    Data - null
    Request Id - <some ID value>.
    ```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a ligar um dispositivo IoT Plug and Play a uma solução IoT. Para saber mais sobre como construir uma solução que interaja com os seus dispositivos IoT Plug and Play, consulte:

> [!div class="nextstepaction"]
> [Como: Ligar e interagir com um dispositivo](howto-develop-solution.md)
