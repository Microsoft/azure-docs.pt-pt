---
title: Interagir com um dispositivo de visualização de Plug and Play IoT conectado à solução de IoT do Azure | Microsoft Docs
description: Use o Java para se conectar e interagir com um dispositivo de visualização de Plug and Play IoT que está conectado à sua solução de IoT do Azure.
author: dominicbetts
ms.author: dobett
ms.date: 12/27/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 53812e68fe397b81f29869565e0e4a0640a9ef23
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964639"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-java"></a>Início rápido: interagir com um dispositivo de visualização de Plug and Play IoT que está conectado à sua solução (Java)

[!INCLUDE [iot-pnp-quickstarts-3-selector.md](../../includes/iot-pnp-quickstarts-3-selector.md)]

O IoT Plug and Play Preview simplifica a IoT, permitindo que você interaja com os recursos de um dispositivo sem conhecer a implementação do dispositivo subjacente. Este guia de início rápido mostra como usar o Java para se conectar e controlar um dispositivo de Plug and Play de IoT que está conectado à sua solução.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido, você precisa do Java SE 8 em seu computador de desenvolvimento. Você também precisa instalar o Maven 3.

Para obter detalhes sobre como configurar com eles, consulte [preparar seu ambiente de desenvolvimento](https://github.com/Azure/azure-iot-sdk-java/blob/preview/doc/java-devbox-setup.md) no SDK do dispositivo IOT Microsoft Azure para Java.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Execute o seguinte comando para obter a _cadeia de conexão do Hub IOT_ para o Hub (observação para uso posterior):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="run-the-sample-device"></a>Executar o dispositivo de exemplo

Neste guia de início rápido, você usa um sensor ambiental de exemplo escrito em Java como o dispositivo de Plug and Play de IoT. As instruções a seguir mostram como instalar e executar o dispositivo:

1. Abra uma janela de terminal no diretório de sua escolha. Execute o seguinte comando para clonar os [exemplos do Azure IOT para](https://github.com/Azure-Samples/azure-iot-samples-java) o repositório do GitHub Java neste local:

    ```cmd/sh
    git clone https://github.com/Azure-Samples/azure-iot-samples-java
    ```

1. Esta janela do terminal agora será usada como terminal do _dispositivo_ . Vá para a pasta do repositório clonado e navegue até a pasta **/Azure-IOT-samples-java/Digital-Twin/Samples/Device/JdkSample** . Instale as bibliotecas necessárias e crie o aplicativo de dispositivo simulado executando o seguinte comando:

    ```cmd/sh
    mvn clean install -DskipTests
    ```

1. Configure a _cadeia de conexão do dispositivo_:

    ```cmd/sh
    set DIGITAL_TWIN_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. Execute o comando a seguir para executar o exemplo da pasta do dispositivo.

    ```cmd/sh
    java -jar environmental-sensor-sample\target\environmental-sensor-sample-with-deps.jar
    ```

1. Você vê mensagens dizendo que o dispositivo está conectado, executando várias etapas de configuração e aguardando atualizações de serviço, seguidos pelos logs de telemetria. Isso indica que o dispositivo agora está pronto para receber comandos e atualizações de propriedade e começou a enviar dados de telemetria para o Hub. Mantenha o exemplo em execução enquanto você conclui as próximas etapas. Não feche este terminal, você precisará dele mais tarde para confirmar se os exemplos de serviço também funcionaram.

## <a name="run-the-sample-solution"></a>Executar a solução de exemplo

Neste guia de início rápido, você usa uma solução de IoT de exemplo em Java para interagir com o dispositivo de exemplo.

1. Abra outra janela de terminal (esse será o terminal de _serviço_ ). Vá para a pasta do repositório clonado e navegue até a pasta **/Azure-IOT-Samples-java\digital-twin\Samples\service\JdkSample** .

1. Instale as bibliotecas necessárias e crie o exemplo de serviço executando o seguinte comando:

    ```cmd/sh
    mvn clean install -DskipTests
    ```

1. Configure a _cadeia de conexão do Hub IOT_ e a _ID do dispositivo_ para permitir que o serviço se conecte a ambos:

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIoTHubConnectionString>
    set DEVICE_ID=<YourDeviceID>
    ```

### <a name="read-a-property"></a>Ler uma propriedade

1. Quando você conectou o _dispositivo_ em seu terminal, uma das mensagens de saída foi a seguinte mensagem para indicar seu status online. A propriedade `state`, que é usada para indicar se o dispositivo está online ou não, é _verdadeiro_:

    ```cmd/sh
    State of environmental sensor was set to true
    ```

1. Vá para o terminal do _serviço_ e use o comando a seguir para executar o exemplo de serviço para ler as informações do dispositivo:

    ```cmd/sh
    java -jar get-digital-twin/target/get-digital-twin-with-deps.jar
    ```

1. Na saída de terminal do _serviço_ , role até o componente `environmentalSensor`. Você verá que a propriedade `state` foi relatada como _verdadeira_:
    ```JSON
    "environmentalSensor" : {
      "name" : "environmentalSensor",
      "properties" : {
        "state" : {
          "reported" : {
            "value" : true,
            "desiredState" : null
          },
          "desired" : null
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

1. Use o comando a seguir para executar o exemplo de serviço para atualizar a propriedade:

    ```cmd/sh
    java -jar update-digital-twin/target/update-digital-twin-with-deps.jar
    ```

1. A saída do terminal do _serviço_ mostra as informações atualizadas do dispositivo. Role até o componente `environmentalSensor` para ver o novo valor de brilho de 42.

    ```json
    "environmentalSensor": {
        "name": "environmentalSensor",
        "properties": {
            "brightness": {
                "reported": null,
                "desired": {
                    "value": "42"
                }
            },
    ```

1. Vá para o terminal do _dispositivo_ , você verá que o dispositivo recebeu a atualização:

    ```cmd/sh
    OnPropertyUpdate called: propertyName=brightness, reportedValue=null, desiredVersion=2, desiredValue={"value":"42"}
    Report property: propertyName=brightness, reportedValue={"value":"42"}, desiredVersion=2 was DIGITALTWIN_CLIENT_OK
    ```
2. Volte para o terminal do _serviço_ e execute o comando abaixo para obter as informações do dispositivo novamente, para confirmar se a propriedade foi atualizada.
    
    ```cmd/sh
    java -jar get-digital-twin/target/get-digital-twin-with-deps.jar
    ```
3. Na saída de terminal do _serviço_ , no componente `environmentalSensor`, você verá que o valor de brilho atualizado foi relatado. Observação: pode levar algum tempo para que o dispositivo conclua a atualização. Você pode repetir essa etapa até que o dispositivo realmente processe a atualização de propriedade.
    
    ```json
    "environmentalSensor" : {
      "name" : "environmentalSensor",
      "properties" : {
        "brightness" : {
          "reported" : {
            "value" : {
              "value" : "42"
            },
            "desiredState" : {
              "code" : 200,
              "version" : 2,
              "description" : "OK"
            }
          },
          "desired" : {
            "value" : "42"
          }
        },
        "state" : {
          "reported" : {
            "value" : true,
            "desiredState" : null
          },
          "desired" : null
        }
      }
    },       
    ```

### <a name="invoke-a-command"></a>Invocar um comando

1. Vá para o terminal do _serviço_ e defina as seguintes variáveis para definir qual comando invocar:
    ```cmd/sh
    set INTERFACE_INSTANCE_NAME=environmentalSensor
    set COMMAND_NAME=blink
    set PAYLOAD=10
    ```

1. Use o comando a seguir para executar o exemplo de serviço para invocar o comando:

    ```cmd/sh
    java -jar invoke-digital-twin-command/target/invoke-digital-twin-command-with-deps.jar
    ```

1. A saída no terminal do _serviço_ deve mostrar a seguinte confirmação:

    ```cmd/sh
    Invoking blink on device <YourDeviceID> with interface instance name environmentalSensor
    Command invoked on the device successfully, the returned status was 200 and the request id was <some ID value>
    The returned PAYLOAD was
    ```

1. Vá para o terminal do _dispositivo_ , você verá que o comando foi confirmado:

    ```cmd/sh
    OnCommandReceived called: commandName=blink, requestId=<some ID value>, commandPayload="10"
    EnvironmentalSensor is blinking every 10 seconds.
    ```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você aprendeu a conectar um dispositivo de Plug and Play IoT a uma solução de IoT. Para saber mais sobre como criar uma solução que interage com seus dispositivos de Plug and Play de IoT, confira:

> [!div class="nextstepaction"]
> [Como: conectar-se e interagir com um dispositivo](howto-develop-solution.md)
