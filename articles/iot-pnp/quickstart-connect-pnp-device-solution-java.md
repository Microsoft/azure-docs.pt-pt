---
title: Interaja com um dispositivo de pré-visualização IoT Plug e Play Preview ligado à sua solução Azure IoT [ Microsoft Docs
description: Utilize a Java para se conectar e interagir com um dispositivo ioT Plug e Play Preview ligado à sua solução Azure IoT.
author: dominicbetts
ms.author: dobett
ms.date: 12/27/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 53812e68fe397b81f29869565e0e4a0640a9ef23
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "76964639"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-java"></a>Quickstart: Interaja com um dispositivo de pré-visualização IoT Plug e Play que está ligado à sua solução (Java)

[!INCLUDE [iot-pnp-quickstarts-3-selector.md](../../includes/iot-pnp-quickstarts-3-selector.md)]

IoT Plug e Play Preview simplifica o IoT permitindo-lhe interagir com as capacidades de um dispositivo sem conhecer a implementação do dispositivo subjacente. Este quickstart mostra-lhe como usar o Java para ligar e controlar um dispositivo IoT Plug and Play que está ligado à sua solução.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este arranque rápido, precisa de Java SE 8 na sua máquina de desenvolvimento. Também precisa instalar maven 3.

Para mais detalhes sobre como se configurar com estes, consulte [Prepare o seu ambiente](https://github.com/Azure/azure-iot-sdk-java/blob/preview/doc/java-devbox-setup.md) de desenvolvimento no dispositivo Microsoft Azure IoT SDK para Java.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Executar o seguinte comando para obter a cadeia de ligação do _hub IoT_ para o seu hub (nota para utilização posterior):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="run-the-sample-device"></a>Executar o dispositivo de amostra

Neste arranque rápido, você usa um sensor ambiental de amostra que está escrito em Java como o dispositivo IoT Plug and Play. As seguintes instruções mostram-lhe como instalar e executar o dispositivo:

1. Abra uma janela terminal no diretório à sua escolha. Execute o seguinte comando para clonar as [amostras Azure IoT para o](https://github.com/Azure-Samples/azure-iot-samples-java) repositório Java GitHub neste local:

    ```cmd/sh
    git clone https://github.com/Azure-Samples/azure-iot-samples-java
    ```

1. Esta janela do terminal será agora utilizada como terminal do _dispositivo._ Vá para a pasta do seu repositório clonado e navegue para a pasta **/azure-iot-samples-java/digital-twin/Samples/device/JdkSample.** Instale as bibliotecas necessárias e construa a aplicação simulada do dispositivo executando o seguinte comando:

    ```cmd/sh
    mvn clean install -DskipTests
    ```

1. Configure a cadeia de ligação do _dispositivo:_

    ```cmd/sh
    set DIGITAL_TWIN_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. Executar o seguinte comando para executar a amostra a partir da pasta do dispositivo.

    ```cmd/sh
    java -jar environmental-sensor-sample\target\environmental-sensor-sample-with-deps.jar
    ```

1. Vê mensagens a dizer que o dispositivo está conectado, a executar várias etapas de configuração e à espera de atualizações de serviço, seguidas de registos de telemetria. Isto indica que o dispositivo está agora pronto para receber comandos e atualizações de propriedade, e começou a enviar dados de telemetria para o centro. Mantenha a amostra a funcionar à medida que completar os próximos passos. Não feche este terminal, precisará dele mais tarde para confirmar que as amostras de serviço também funcionaram.

## <a name="run-the-sample-solution"></a>Executar a solução da amostra

Neste arranque rápido, utiliza-se uma solução IoT de amostra em Java para interagir com o dispositivo de amostra.

1. Abra outra janela terminal (este será o seu terminal de _serviço)._ Vá para a pasta do seu repositório clonado e navegue para a pasta **/azure-iot-samples-java\digital-twin\Samples\service\JdkSample.**

1. Instale as bibliotecas necessárias e construa a amostra de serviço executando o seguinte comando:

    ```cmd/sh
    mvn clean install -DskipTests
    ```

1. Configure a cadeia de ligação do _hub IoT_ e o ID do _dispositivo_ para permitir que o serviço se conectem a ambos:

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIoTHubConnectionString>
    set DEVICE_ID=<YourDeviceID>
    ```

### <a name="read-a-property"></a>Ler uma propriedade

1. Quando ligou o _dispositivo_ no seu terminal, uma das mensagens de saída foi a seguinte mensagem a indicar o seu estado online. A `state` propriedade, que é usada para indicar se o dispositivo está ou não on-line, é _verdade:_

    ```cmd/sh
    State of environmental sensor was set to true
    ```

1. Dirija-se ao terminal de _serviço_ e utilize o seguinte comando para executar a amostra de serviço para informações do dispositivo de leitura:

    ```cmd/sh
    java -jar get-digital-twin/target/get-digital-twin-with-deps.jar
    ```

1. Na saída do terminal de `environmentalSensor` _serviço,_ desloque-se para o componente. Você vê `state` que a propriedade foi reportada como _verdadeira:_
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

1. Vá ao terminal de _serviço_ e defina as seguintes variáveis para definir qual imóvel atualizar:
    ```cmd/sh
    set INTERFACE_INSTANCE_NAME=environmentalSensor
    set PROPERTY_NAME=brightness
    set PROPERTY_VALUE=42
    ```

1. Utilize o seguinte comando para executar a amostra de serviço para atualizar a propriedade:

    ```cmd/sh
    java -jar update-digital-twin/target/update-digital-twin-with-deps.jar
    ```

1. A saída do terminal de _serviço_ mostra as informações atualizadas do dispositivo. Percorra `environmentalSensor` o componente para ver o novo valor de luminosidade de 42.

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

1. Vá ao terminal do _seu dispositivo,_ veja que o dispositivo recebeu a atualização:

    ```cmd/sh
    OnPropertyUpdate called: propertyName=brightness, reportedValue=null, desiredVersion=2, desiredValue={"value":"42"}
    Report property: propertyName=brightness, reportedValue={"value":"42"}, desiredVersion=2 was DIGITALTWIN_CLIENT_OK
    ```
2. Volte ao seu terminal de _serviço_ e execute o comando abaixo para obter novamente a informação do dispositivo, para confirmar que a propriedade foi atualizada.
    
    ```cmd/sh
    java -jar get-digital-twin/target/get-digital-twin-with-deps.jar
    ```
3. Na saída do terminal `environmentalSensor` de _serviço,_ sob o componente, é referido o valor de luminosidade atualizado. Nota: pode demorar algum tempo até que o dispositivo termine a atualização. Pode repetir este passo até que o dispositivo tenha realmente processado a atualização da propriedade.
    
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

1. Vá ao terminal de _serviço_ e defina as seguintes variáveis para definir qual o comando invocar:
    ```cmd/sh
    set INTERFACE_INSTANCE_NAME=environmentalSensor
    set COMMAND_NAME=blink
    set PAYLOAD=10
    ```

1. Utilize o seguinte comando para executar a amostra de serviço para invocar o comando:

    ```cmd/sh
    java -jar invoke-digital-twin-command/target/invoke-digital-twin-command-with-deps.jar
    ```

1. A saída no terminal de _serviço_ deve apresentar a seguinte confirmação:

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

Neste arranque rápido, aprendeu a ligar um dispositivo IoT Plug and Play a uma solução IoT. Para saber mais sobre como construir uma solução que interaja com os seus dispositivos IoT Plug and Play, consulte:

> [!div class="nextstepaction"]
> [Como: Ligar e interagir com um dispositivo](howto-develop-solution.md)
