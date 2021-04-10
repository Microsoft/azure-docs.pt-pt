---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 3bf5ac4e01bca3bfc3cc8720a068bc53830b4747
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104612094"
---
Este tutorial mostra-lhe como construir uma aplicação de dispositivo IoT Plug e Play de múltiplas amostras de componentes, conectá-la ao seu hub IoT e utilizar o CLI Azure para visualizar a telemetria que envia. A aplicação da amostra está escrita em Java e está incluída no dispositivo Azure IoT SDK para Java. Um construtor de soluções pode usar o CLI Azure para entender as capacidades de um dispositivo IoT Plug e Play sem a necessidade de visualizar qualquer código do dispositivo.

Este tutorial mostra-lhe como construir uma aplicação de dispositivo IoT Plug e Play com componentes, conectá-la ao seu hub IoT e utilizar a ferramenta exploradora Azure IoT para visualizar a informação que envia para o centro. A aplicação da amostra está escrita em Java e está incluída no dispositivo Azure IoT SDK para Java. Um construtor de soluções pode utilizar a ferramenta exploradorA Azure IoT para compreender as capacidades de um dispositivo IoT Plug e Play sem a necessidade de visualizar qualquer código do dispositivo.

Neste tutorial:

> [!div class="checklist"]
> * Descarregue o código de amostra.
> * Construa o código de amostra.
> * Executar a aplicação do dispositivo de amostra e validar que se conecta ao seu hub IoT.
> * Reveja o código fonte.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

Para completar este tutorial no Windows, instale o seguinte software no seu ambiente local do Windows:

* Kit de Desenvolvimento Java SE 8. Em [Java, suporte a longo prazo para Azure e Azure Stack,](/java/azure/jdk/)sob **suporte a longo prazo,** selecione Java **8**.
* [Apache Maven 3](https://maven.apache.org/download.cgi).

## <a name="download-the-code"></a>Transferir o código

Se tiver concluído [o Quickstart: Ligue uma aplicação de dispositivo IoT Plug e Play de amostra que está a decorrer no Windows to IoT Hub (Java)](../articles/iot-pnp/quickstart-connect-device.md), já clonou o repositório.

Abra um pedido de comando no diretório à sua escolha. Execute o seguinte comando para clonar o [repositório Azure IoT Java SDKs e Bibliotecas](https://github.com/Azure/azure-iot-sdk-java) GitHub neste local:

```cmd
git clone https://github.com/Azure/azure-iot-sdk-java.git
```

Espere que esta operação leve vários minutos para ser concluída.

## <a name="build-the-code"></a>Compilar o código

No Windows, navegue para a pasta raiz do repositório Java SDK clonado. Executar o seguinte comando para construir as dependências:

```cmd/sh
mvn install -T 2C -DskipTests
```

## <a name="run-the-device-sample"></a>Executar a amostra do dispositivo

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

Para executar a aplicação da amostra, navegue para o *\dispositivo\iot-device-samples\pnp-device-sample\temperature-controller-device-sampleer* e executar o seguinte comando:

```cmd/sh
mvn exec:java -Dexec.mainClass="samples.com.microsoft.azure.sdk.iot.device.TemperatureController"
```

O dispositivo está agora pronto para receber comandos e atualizações de propriedade, e começou a enviar dados de telemetria para o hub. Mantenha a amostra a funcionar à medida que completar os próximos passos.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Use o explorador Azure IoT para validar o código

Depois de iniciar a amostra do cliente do dispositivo, utilize a ferramenta exploradora Azure IoT para verificar se está a funcionar.

[!INCLUDE [iot-pnp-iot-explorer.md](iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>Rever o código

Esta amostra implementa um dispositivo de controlador de temperatura IoT Plug e Play. O modelo que esta amostra implementa utiliza [vários componentes.](../articles/iot-pnp/concepts-modeling-guide.md) O ficheiro de definição de linguagem de [definição de Gémeos Digitais (DTDL) para o dispositivo de temperatura](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) define a telemetria, propriedades e comanda os dispositivos.

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

Depois de o dispositivo ligar ao seu hub IoT, o código regista os manipuladores de comando.

```java
deviceClient.subscribeToDeviceMethod(new MethodCallback(), null, new MethodIotHubEventCallback(), null);
```

Existem manipuladores separados para as atualizações de propriedade desejadas nos dois componentes do termóstato:

```java
deviceClient.startDeviceTwin(new TwinIotHubEventCallback(), null, new GenericPropertyUpdateCallback(), null);
Map<Property, Pair<TwinPropertyCallBack, Object>> desiredPropertyUpdateCallback = Stream.of(
        new AbstractMap.SimpleEntry<Property, Pair<TwinPropertyCallBack, Object>>(
                new Property(THERMOSTAT_1, null),
                new Pair<>(new TargetTemperatureUpdateCallback(), THERMOSTAT_1)),
        new AbstractMap.SimpleEntry<Property, Pair<TwinPropertyCallBack, Object>>(
                new Property(THERMOSTAT_2, null),
                new Pair<>(new TargetTemperatureUpdateCallback(), THERMOSTAT_2))
).collect(Collectors.toMap(AbstractMap.SimpleEntry::getKey, AbstractMap.SimpleEntry::getValue));

deviceClient.subscribeToTwinDesiredProperties(desiredPropertyUpdateCallback);
```

O código de amostra envia telemetria de cada componente do termóstato:

```java
sendTemperatureReading(THERMOSTAT_1);
sendTemperatureReading(THERMOSTAT_2);
```

O `sendTemperatureReading` método utiliza a classe para criar `PnpHhelper` mensagens para cada componente:

```java
Message message = PnpHelper.createIotHubMessageUtf8(telemetryName, currentTemperature, componentName);
```

A `PnpHelper` classe contém outros métodos de amostra que pode utilizar com um modelo de múltiplos componentes.

Utilize a ferramenta exploradora Azure IoT para visualizar a telemetria e as propriedades dos dois componentes do termóstato:

:::image type="content" source="media/iot-pnp-multiple-components-java/multiple-component.png" alt-text="Dispositivo de vários componentes no explorador Azure IoT":::

Também pode utilizar a ferramenta exploradorA Azure IoT para chamar comandos em qualquer um dos dois componentes do termóstato ou no componente predefinido.
