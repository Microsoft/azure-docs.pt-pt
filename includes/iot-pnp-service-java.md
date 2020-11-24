---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 788aa0ebe9df91caba2ee279df96cbea175975e4
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2020
ms.locfileid: "95510614"
---
O IoT Plug and Play simplifica o IoT, permitindo-lhe interagir com as capacidades de um dispositivo sem ter conhecimento da implementação do dispositivo subjacente. Este quickstart mostra-lhe como usar Java para ligar e controlar um dispositivo IoT Plug and Play que está ligado à sua solução.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

Para completar este arranque rápido no Windows, instale o seguinte software no ambiente local do Windows:

* Kit de Desenvolvimento Java SE 8. Em [Java, suporte a longo prazo para Azure e Azure Stack,](/java/azure/jdk/?preserve-view=true&view=azure-java-stable)sob **suporte a longo prazo,** selecione Java **8**.
* [Apache Maven 3](https://maven.apache.org/download.cgi).

### <a name="clone-the-sdk-repository-with-the-sample-code"></a>Clone o repositório SDK com o código de amostra

Se tiver concluído [o Quickstart: Ligue uma aplicação de dispositivo IoT Plug e Play de amostra que está a decorrer no Windows to IoT Hub (Java)](../articles/iot-pnp/quickstart-connect-device.md), já clonou o repositório.

Abra um pedido de comando no diretório à sua escolha. Execute o seguinte comando para clonar os [SDKs IoT da Microsoft Azure para o](https://github.com/Azure/azure-iot-sdk-java) repositório Java GitHub neste local:

```cmd
git clone https://github.com/Azure/azure-iot-sdk-java.git
```

## <a name="build-and-run-the-sample-device"></a>Construa e execute o dispositivo de amostra

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

Para saber mais sobre a configuração da amostra, consulte o [produto de leitura](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/readme.md)da amostra .

Neste arranque rápido, utilize um dispositivo termóstato de amostra que está escrito em Java como o dispositivo IoT Plug and Play. Para executar o dispositivo de amostragem:

1. Abra uma janela de terminal e navegue para a pasta local que contém o Microsoft Azure IoT SDK para o repositório java que clonou do GitHub.

1. Esta janela de terminal é utilizada como terminal do seu **dispositivo.** Vá para a pasta de raiz do seu repositório clonado. Instale todas as dependências executando o seguinte comando:

1. Executar o seguinte comando para construir a aplicação do dispositivo de amostra:

    ```cmd
    mvn install -T 2C -DskipTests
    ```

1. Para executar a aplicação do dispositivo de amostra, navegue para o *dispositivo\iot-device-samples\pnp-device-sample\thermostat-device-sampleer* e executar o seguinte comando:

    ```cmd
    mvn exec:java -Dexec.mainClass="samples.com.microsoft.azure.sdk.iot.device.Thermostat"
    ```

O dispositivo está agora pronto para receber comandos e atualizações de propriedade, e começou a enviar dados de telemetria para o hub. Mantenha o dispositivo de amostra em funcionamento à medida que completar os próximos passos.

## <a name="run-the-sample-solution"></a>Executar a solução de amostra

Em [Configurar o seu ambiente para os quickstarts e tutoriais IoT Plug and Play](../articles/iot-pnp/set-up-environment.md) criou duas variáveis ambientais para configurar a amostra para ligar ao seu hub e dispositivo IoT:

* **IOTHUB_CONNECTION_STRING**: a cadeia de ligação do hub IoT que fez uma nota anterior.
* **IOTHUB_DEVICE_ID:** `"my-pnp-device"` .

Neste arranque rápido, utilize uma solução IoT de amostra escrita em Java para interagir com o dispositivo de amostra que acabou de configurar.

> [!NOTE]
> Esta amostra utiliza o **com.microsoft.azure.sdk.iot.service** namespace do cliente de **serviço IoT Hub**. Para saber mais sobre as APIs, incluindo as gémeas digitais API, consulte o [guia de desenvolvedores de serviços.](../articles/iot-pnp/concepts-developer-guide-service.md)

1. Abra outra janela do terminal para utilizar como terminal **de serviço.**

1. No repositório Java SDK clonado, navegue para o *serviço\iot-service-samples\pnp-service-sample\thermostat-service-sample.*

1. Para executar a aplicação de serviço de amostra, executar o seguinte comando:

    ```cmd
    mvm exec:java -Dexec.mainClass="samples.com.microsoft.azure.sdk.iot.service.Thermostat"
    ```

### <a name="get-device-twin"></a>Obter dispositivo twin

O seguinte corte de código mostra como recuperar o dispositivo gémeo no serviço:

```java
 // Get the twin and retrieve model Id set by Device client.
DeviceTwinDevice twin = new DeviceTwinDevice(deviceId);
twinClient.getTwin(twin);
System.out.println("Model Id of this Twin is: " + twin.getModelId());
```

### <a name="update-a-device-twin"></a>Atualizar um dispositivo gémeo

O seguinte corte de código mostra-lhe como usar um *patch* para atualizar propriedades através do dispositivo twin:

```java
String propertyName = "targetTemperature";
double propertyValue = 60.2;
twin.setDesiredProperties(Collections.singleton(new Pair(propertyName, propertyValue)));
twinClient.updateTwin(twin);
```

A saída do dispositivo mostra como o dispositivo responde a esta atualização da propriedade.

### <a name="invoke-a-command"></a>Invocar um comando

O seguinte corte de código mostra que você chama um comando no dispositivo:

```java
// The method to invoke for a device without components should be "methodName" as defined in the DTDL.
String methodToInvoke = "getMaxMinReport";
System.out.println("Invoking method: " + methodToInvoke);

Long responseTimeout = TimeUnit.SECONDS.toSeconds(200);
Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);

// Invoke the command.
String commandInput = ZonedDateTime.now(ZoneOffset.UTC).minusMinutes(5).format(DateTimeFormatter.ISO_DATE_TIME);
MethodResult result = methodClient.invoke(deviceId, methodToInvoke, responseTimeout, connectTimeout, commandInput);
if(result == null)
{
    throw new IOException("Method result is null");
}

System.out.println("Method result status is: " + result.getStatus());
```

A saída do dispositivo mostra como o dispositivo responde a este comando.
