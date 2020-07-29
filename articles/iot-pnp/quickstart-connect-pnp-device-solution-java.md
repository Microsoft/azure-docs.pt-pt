---
title: Interaja com o dispositivo IoT Plug e Play Preview ligado à sua solução - Java Microsoft Docs
description: Utilize o Java para ligar e interagir com um dispositivo IoT Plug e Reprodução que esteja ligado à sua solução Azure IoT.
author: dominicbetts
ms.author: dobett
ms.date: 12/27/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc, devx-track-java
ms.openlocfilehash: 752d1d925c55ed79c7eb1673c6602adb9c2371fb
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87320991"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-java"></a>Quickstart: Interaja com um dispositivo IoT Plug e Play Preview que esteja ligado à sua solução (Java)

[!INCLUDE [iot-pnp-quickstarts-3-selector.md](../../includes/iot-pnp-quickstarts-3-selector.md)]

IoT Plug e Play Preview simplifica o IoT, permitindo-lhe interagir com as capacidades de um dispositivo sem conhecimento da implementação do dispositivo subjacente. Este quickstart mostra-lhe como usar Java para ligar e controlar um dispositivo IoT Plug and Play que está ligado à sua solução.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este arranque rápido, precisa de Java SE 8 na sua máquina de desenvolvimento. Também precisa instalar o Maven 3.

Para obter detalhes sobre como ser configurado com estes, consulte [Prepare o seu ambiente de desenvolvimento](https://github.com/Azure/azure-iot-sdk-java/blob/preview/doc/java-devbox-setup.md) no dispositivo IoT da Microsoft Azure SDK para Java.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Executar o seguinte comando para obter a _cadeia de ligação do hub IoT_ para o seu hub (nota para utilização posterior):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="run-the-sample-device"></a>Executar o dispositivo de amostra

Neste arranque rápido, você usa um sensor ambiental de amostra que está escrito em Java como o dispositivo IoT Plug and Play. As seguintes instruções mostram-lhe como instalar e executar o dispositivo:

1. Abra uma janela terminal no diretório à sua escolha. Execute o seguinte comando para clonar as [amostras Azure IoT para o](https://github.com/Azure-Samples/azure-iot-samples-java) repositório Java GitHub neste local:

    ```cmd/sh
    git clone https://github.com/Azure-Samples/azure-iot-samples-java
    ```

1. Esta janela de terminal será agora utilizada como terminal do seu _dispositivo._ Vá à pasta do seu repositório clonado e navegue para a pasta **/azure-iot-samples-java/digital-twin/samples/device/JdkSample.** Instale as bibliotecas necessárias e construa a aplicação do dispositivo simulado executando o seguinte comando:

    ```cmd/sh
    mvn clean install -DskipTests
    ```

1. Configure a _cadeia de ligação_do dispositivo:

    ```cmd/sh
    set DIGITAL_TWIN_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. Executar o seguinte comando para executar a amostra da pasta do dispositivo.

    ```cmd/sh
    java -jar environmental-sensor-sample\target\environmental-sensor-sample-with-deps.jar
    ```

1. Vê mensagens a dizer que o dispositivo está ligado, a executar vários passos de configuração e à espera de atualizações de serviço, seguidos de registos de telemetria. Isto indica que o dispositivo está agora pronto para receber comandos e atualizações de propriedade, e começou a enviar dados de telemetria para o hub. Mantenha a amostra a funcionar à medida que completar os próximos passos. Não feche este terminal, vai precisar dele mais tarde para confirmar que as amostras de serviço também funcionaram.

## <a name="run-the-sample-solution"></a>Executar a solução de amostra

Neste arranque rápido, utilize uma solução IoT de amostra em Java para interagir com o dispositivo de amostra.

1. Abra outra janela do terminal (este será o seu terminal _de serviço)._ Vá à pasta do seu repositório clonado e navegue para a pasta **/azure-iot-samples-java\digital-twin\Samples\service\JdkSample.**

1. Instale as bibliotecas necessárias e construa a amostra de serviço executando o seguinte comando:

    ```cmd/sh
    mvn clean install -DskipTests
    ```

1. Configure a cadeia de _ligação do hub IoT_ e o _ID_ do dispositivo para permitir que o serviço se conecte a ambos:

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIoTHubConnectionString>
    set DEVICE_ID=<YourDeviceID>
    ```

### <a name="read-a-property"></a>Ler uma propriedade

1. Quando ligou o _dispositivo_ no seu terminal, uma das mensagens de saída foi a seguinte mensagem para indicar o seu estado online. A `state` propriedade, que é usada para indicar se o dispositivo está ou não online, é _verdade:_

    ```cmd/sh
    State of environmental sensor was set to true
    ```

1. Vá ao terminal _de serviço_ e use o seguinte comando para executar a amostra de serviço para obter informações do dispositivo de leitura:

    ```cmd/sh
    java -jar get-digital-twin/target/get-digital-twin-with-deps.jar
    ```

1. Na saída do terminal _de serviço,_ percorra para o `environmentalSensor` componente. Você vê que a `state` propriedade foi relatada como _verdadeira:_
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

### <a name="update-a-writable-property"></a>Atualizar uma propriedade writable

1. Vá ao terminal _de serviço_ e defina as seguintes variáveis para definir que propriedade a atualizar:
    ```cmd/sh
    set INTERFACE_INSTANCE_NAME=environmentalSensor
    set PROPERTY_NAME=brightness
    set PROPERTY_VALUE=42
    ```

1. Utilize o seguinte comando para executar a amostra de serviço para atualizar o imóvel:

    ```cmd/sh
    java -jar update-digital-twin/target/update-digital-twin-with-deps.jar
    ```

1. A saída do terminal de _serviço_ mostra as informações atualizadas do dispositivo. Percorra o `environmentalSensor` componente para ver o novo valor de luminosidade de 42.

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

1. Vá ao terminal do seu _dispositivo,_ veja que o dispositivo recebeu a atualização:

    ```cmd/sh
    OnPropertyUpdate called: propertyName=brightness, reportedValue=null, desiredVersion=2, desiredValue={"value":"42"}
    Report property: propertyName=brightness, reportedValue={"value":"42"}, desiredVersion=2 was DIGITALTWIN_CLIENT_OK
    ```
2. Volte ao seu terminal _de serviço_ e execute o comando abaixo para obter novamente a informação do dispositivo, para confirmar que a propriedade foi atualizada.
    
    ```cmd/sh
    java -jar get-digital-twin/target/get-digital-twin-with-deps.jar
    ```
3. Na saída do terminal _de serviço,_ sob o `environmentalSensor` componente, vê-se o valor de luminosidade atualizado relatado. Nota: pode demorar algum tempo até que o dispositivo termine a atualização. Pode repetir este passo até que o dispositivo tenha processado a atualização da propriedade.
    
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

1. Vá ao terminal _de serviço_ e defina as seguintes variáveis para definir que comando invocar:
    ```cmd/sh
    set INTERFACE_INSTANCE_NAME=environmentalSensor
    set COMMAND_NAME=blink
    set PAYLOAD=10
    ```

1. Utilize o seguinte comando para executar a amostra de serviço para invocar o comando:

    ```cmd/sh
    java -jar invoke-digital-twin-command/target/invoke-digital-twin-command-with-deps.jar
    ```

1. A saída no terminal _de serviço_ deve apresentar a seguinte confirmação:

    ```cmd/sh
    Invoking blink on device <YourDeviceID> with interface instance name environmentalSensor
    Command invoked on the device successfully, the returned status was 200 and the request id was <some ID value>
    The returned PAYLOAD was
    ```

1. Vá ao terminal do _dispositivo,_ veja que o comando foi reconhecido:

    ```cmd/sh
    OnCommandReceived called: commandName=blink, requestId=<some ID value>, commandPayload="10"
    EnvironmentalSensor is blinking every 10 seconds.
    ```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a ligar um dispositivo IoT Plug e Play a uma solução IoT. Para saber mais sobre como construir uma solução que interage com os seus dispositivos IoT Plug e Play, consulte:

> [!div class="nextstepaction"]
> [Como fazer: Ligar e interagir com um dispositivo](howto-develop-solution.md)
