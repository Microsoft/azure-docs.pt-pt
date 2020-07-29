---
title: Ligue ioT plug e play preview amostra código de dispositivo Java ao IoT Hub / Microsoft Docs
description: Construa e execute o código do dispositivo de amostra IoT Plug e Play Preview que se liga a um hub IoT. Utilize a ferramenta exploradora Azure IoT para visualizar as informações enviadas pelo dispositivo para o hub.
author: ericmitt
ms.author: ericmitt
ms.date: 07/14/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: b89c92e675ab505878f350e9716af95050ce28b2
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87352936"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-running-on-windows-to-iot-hub-java"></a>Quickstart: Conecte uma aplicação de dispositivo IoT Plug e Reprodução de amostras em execução no Windows to IoT Hub (Java)

[!INCLUDE [iot-pnp-quickstarts-device-selector.md](../../includes/iot-pnp-quickstarts-device-selector.md)]

Este quickstart mostra-lhe como construir uma aplicação de dispositivo IoT Plug e Play de amostra, conectá-la ao seu hub IoT e utilizar a ferramenta exploradora Azure IoT para visualizar a telemetria que envia. A aplicação da amostra está escrita em Java e está incluída no dispositivo Azure IoT SDK para Java. Um construtor de soluções pode utilizar a ferramenta exploradorA Azure IoT para compreender as capacidades de um dispositivo IoT Plug e Play sem a necessidade de visualizar qualquer código do dispositivo.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para completar este arranque rápido no Windows, instale o seguinte software no ambiente local do Windows:

* Kit de Desenvolvimento Java SE 8. Em [Java, suporte a longo prazo para Azure e Azure Stack,](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)sob **suporte a longo prazo,** selecione Java **8**.
* [Apache Maven 3](https://maven.apache.org/download.cgi).

### <a name="azure-iot-explorer"></a>Explorador de Azure IoT

Para interagir com o dispositivo de amostra na segunda parte deste arranque rápido, utilize a ferramenta **exploradora Azure IoT.** [Descarregue e instale a mais recente versão do explorador Azure IoT](./howto-use-iot-explorer.md) para o seu sistema operativo.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Executar o seguinte comando para obter a _cadeia de ligação do hub IoT_ para o seu hub. Tome nota desta cadeia de ligação, use-a mais tarde neste arranque rápido:

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

> [!TIP]
> Também pode utilizar a ferramenta exploradora Azure IoT para encontrar a cadeia de ligação do hub IoT.

Executar o seguinte comando para obter a _cadeia de ligação_ do dispositivo para o dispositivo que adicionou ao hub. Tome nota desta cadeia de ligação, use-a mais tarde neste arranque rápido:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```

## <a name="download-the-code"></a>Transferir o código

Neste arranque rápido, você prepara um ambiente de desenvolvimento que você pode usar para clonar e construir o Azure IoT Hub Device Java SDK.

Abra um pedido de comando no diretório à sua escolha. Execute o seguinte comando para clonar o [repositório Azure IoT Java SDKs e Bibliotecas](https://github.com/Azure/azure-iot-sdk-java) GitHub neste local:

```cmd
git clone https://github.com/Azure/azure-iot-sdk-java.git
```

[!INCLUDE [iot-pnp-download-models.md](../../includes/iot-pnp-download-models.md)]

## <a name="build-the-code"></a>Compilar o código

No Windows, navegue para a pasta raiz do repositório Java SDK clonado. Em seguida, navegue para a pasta *\dispositivo\iot-device\pnp-device-sample\\thermostat-device-sampleer.*

Executar o seguinte comando para construir a aplicação da amostra:

```cmd
mvn clean package
```

## <a name="run-the-device-sample"></a>Executar a amostra do dispositivo

Crie uma variável ambiental chamada **IOTHUB_DEVICE_CONNECTION_STRING** para armazenar a cadeia de ligação do dispositivo que fez anteriormente.

Para executar a aplicação da amostra, executar o seguinte comando:

```cmd
mvn exec:java -Dexec.mainClass="samples.com.microsoft.azure.sdk.iot.device.Thermostat"
```

O dispositivo está agora pronto para receber comandos e atualizações de propriedade, e começou a enviar dados de telemetria para o hub. Mantenha a amostra a funcionar à medida que completar os próximos passos.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Use o explorador Azure IoT para validar o código

Depois de iniciar a amostra do cliente do dispositivo, utilize a ferramenta exploradora Azure IoT para verificar se está a funcionar.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>Rever o código

Esta amostra implementa um simples dispositivo de termóstato IoT Plug e Play. O modelo que esta amostra implementa não utiliza [componentes](concepts-components.md)IoT Plug e Play . O [ficheiro do modelo DTDL para o dispositivo termóstato](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) define a telemetria, as propriedades e o comando dos dispositivos.

O código do dispositivo utiliza a classe padrão `DeviceClient` para ligar ao seu hub IoT. O dispositivo envia o ID modelo do modelo DTDL que implementa no pedido de ligação. Um dispositivo que envia um ID modelo é um dispositivo IoT Plug and Play:

```java
private static void initializeDeviceClient() throws URISyntaxException, IOException {
    ClientOptions options = new ClientOptions();
    options.setModelId(MODEL_ID);
    deviceClient = new DeviceClient(deviceConnectionString, protocol, options);

    deviceClient.registerConnectionStatusChangeCallback((status, statusChangeReason, throwable, callbackContext) -> {
        log.debug("Connection status change registered: status={}, reason={}", status, statusChangeReason);

        if (throwable != null) {
            log.debug("The connection status change was caused by the following Throwable: {}", throwable.getMessage());
            throwable.printStackTrace();
        }
    }, deviceClient);

    deviceClient.open();
}
```

O ID do modelo é armazenado no código tal como mostrado no seguinte corte:

```java
private static final String MODEL_ID = "dtmi:com:example:Thermostat;1";
```

O código que atualiza propriedades, lida com comandos e envia telemetria é idêntico ao código de um dispositivo que não utiliza as convenções IoT Plug and Play.

A amostra utiliza uma biblioteca JSON para analisar objetos JSON nas cargas enviadas do seu hub IoT:

```java
import com.google.gson.Gson;

...

Date since = new Gson().fromJson(jsonRequest, Date.class);
```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a ligar um dispositivo IoT Plug e Play a um hub IoT. Para saber mais sobre como construir uma solução que interage com os seus dispositivos IoT Plug e Play, consulte:

> [!div class="nextstepaction"]
> [Como fazer: Ligar e interagir com um dispositivo](howto-develop-solution.md)
