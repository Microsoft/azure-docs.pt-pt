---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 6d507d31680b25932980bdede2c1252348d84803
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/08/2021
ms.locfileid: "99834202"
---
Este tutorial mostra-lhe como construir uma aplicação de dispositivo IoT Plug e Play com componentes, conectá-la ao seu hub IoT e utilizar a ferramenta exploradora Azure IoT para visualizar a informação que envia para o centro. A aplicação da amostra está escrita em C# e está incluída no dispositivo Azure IoT SDK para C#. Um construtor de soluções pode utilizar a ferramenta exploradorA Azure IoT para compreender as capacidades de um dispositivo IoT Plug e Play sem a necessidade de visualizar qualquer código do dispositivo.

Neste tutorial:

> [!div class="checklist"]
> * Descarregue o código de amostra.
> * Construa o código de amostra.
> * Executar a aplicação do dispositivo de amostra e validar que se conecta ao seu hub IoT.
> * Reveja o código fonte.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

Para completar este tutorial no Windows, instale o seguinte software no seu ambiente local do Windows:

* [Estúdio Visual (Comunidade, Profissional ou Empresa)](https://visualstudio.microsoft.com/downloads/).
* [Git.](https://git-scm.com/download/)

### <a name="clone-the-sdk-repository-with-the-sample-code"></a>Clone o repositório SDK com o código de amostra

Se tiver concluído [o Quickstart: Ligue uma aplicação de dispositivo IoT Plug e Play de amostra que está a decorrer no Windows to IoT Hub (C#),](../articles/iot-pnp/quickstart-connect-device.md)já clonou o repositório.

Clone as amostras do repositório Azure IoT para o repositório C# GitHub. Abra um pedido de comando numa pasta à sua escolha. Executar o seguinte comando para clonar as [amostras de IoT do Microsoft Azure para](https://github.com/Azure-Samples/azure-iot-samples-csharp) o repositório .NET GitHub:

```cmd
git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
```

## <a name="run-the-sample-device"></a>Executar o dispositivo de amostra

Neste arranque rápido, utilize um dispositivo de controlador de temperatura de amostra que está escrito em C# como o dispositivo IoT Plug and Play. Para executar o dispositivo de amostragem:

1. Abra o ficheiro *de projeto azure-iot-samples-csharp\iot-hub\Samples\device\PnpDeviceSamples\TemperatureController\TemperatureController.csproj* project file in Visual Studio 2019.

1. No Estúdio Visual, navegue para **Project > TemperatureController Properties > Debug**. Em seguida, adicione as seguintes variáveis ambientais ao projeto:

    | Name | Valor |
    | ---- | ----- |
    | IOTHUB_DEVICE_SECURITY_TYPE | DPS |
    | IOTHUB_DEVICE_DPS_ENDPOINT | global.azure-devices-provisioning.net |
    | IOTHUB_DEVICE_DPS_ID_SCOPE | O valor que fez notar quando completou [Configurar o seu ambiente](../articles/iot-pnp/set-up-environment.md) |
    | IOTHUB_DEVICE_DPS_DEVICE_ID | my-pnp-dispositivo |
    | IOTHUB_DEVICE_DPS_DEVICE_KEY | O valor que fez notar quando completou [Configurar o seu ambiente](../articles/iot-pnp/set-up-environment.md) |


1. Agora pode construir a amostra no Visual Studio e executá-la em modo de depurar.

1. Vê mensagens a dizer que o dispositivo enviou algumas informações e reportou-se online. Estas mensagens indicam que o dispositivo começou a enviar dados de telemetria para o hub, estando agora pronto para receber comandos e atualizações de propriedade. Não feche este caso de Visual Studio, precisa dele para confirmar que a amostra de serviço está a funcionar.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Use o explorador Azure IoT para validar o código

Depois de iniciar a amostra do cliente do dispositivo, utilize a ferramenta exploradora Azure IoT para verificar se está a funcionar.

[!INCLUDE [iot-pnp-iot-explorer.md](iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>Rever o código

Esta amostra implementa um dispositivo de controlador de temperatura IoT Plug e Play. O modelo que esta amostra implementa utiliza [vários componentes.](../articles/iot-pnp/concepts-components.md) O ficheiro de definição de linguagem de [definição de Gémeos Digitais (DTDL) para o dispositivo de temperatura](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) define a telemetria, propriedades e comanda os dispositivos.

O código do dispositivo liga-se ao seu hub IoT utilizando o `CreateFromConnectionString` método padrão. O dispositivo envia o ID modelo do modelo DTDL que implementa no pedido de ligação. Um dispositivo que envia um ID modelo é um dispositivo IoT Plug and Play:

```csharp
private static DeviceClient InitializeDeviceClient(string hostname, IAuthenticationMethod authenticationMethod)
{
    var options = new ClientOptions
    {
        ModelId = ModelId,
    };

    var deviceClient = DeviceClient.Create(hostname, authenticationMethod, TransportType.Mqtt, options);
    deviceClient.SetConnectionStatusChangesHandler((status, reason) =>
    {
        s_logger.LogDebug($"Connection status change registered - status={status}, reason={reason}.");
    });

    return deviceClient;
}
```

O ID do modelo é armazenado no código tal como mostrado no seguinte corte:

```csharp
private const string ModelId = "dtmi:com:example:TemperatureController;1";
```

Depois de o dispositivo ligar ao seu hub IoT, o código regista os manipuladores de comando. O `reboot` comando é definido no componente predefinido. O `getMaxMinReport` comando é definido em cada um dos dois componentes do termóstato:

```csharp
await _deviceClient.SetMethodHandlerAsync("reboot", HandleRebootCommandAsync, _deviceClient, cancellationToken);
await _deviceClient.SetMethodHandlerAsync("thermostat1*getMaxMinReport", HandleMaxMinReportCommandAsync, Thermostat1, cancellationToken);
await _deviceClient.SetMethodHandlerAsync("thermostat2*getMaxMinReport", HandleMaxMinReportCommandAsync, Thermostat2, cancellationToken);

```

Existem manipuladores separados para as atualizações de propriedade desejadas nos dois componentes do termóstato:

```csharp
_desiredPropertyUpdateCallbacks.Add(Thermostat1, TargetTemperatureUpdateCallbackAsync);
_desiredPropertyUpdateCallbacks.Add(Thermostat2, TargetTemperatureUpdateCallbackAsync);

```

O código de amostra envia telemetria de cada componente do termóstato:

```csharp
await SendTemperatureAsync(Thermostat1, cancellationToken);
await SendTemperatureAsync(Thermostat2, cancellationToken);
```

O `SendTemperatureTelemetryAsync` método utiliza a classe para criar `PnpHhelper` mensagens para cada componente:

```csharp
using Message msg = PnpHelper.CreateIothubMessageUtf8(telemetryName, JsonConvert.SerializeObject(currentTemperature), componentName);
```

A `PnpHelper` classe contém outros métodos de amostra que pode utilizar com um modelo de múltiplos componentes.

Utilize a ferramenta exploradora Azure IoT para visualizar a telemetria e as propriedades dos dois componentes do termóstato:

:::image type="content" source="media/iot-pnp-multiple-components-csharp/multiple-component.png" alt-text="Dispositivo de vários componentes no explorador Azure IoT":::

Também pode utilizar a ferramenta exploradorA Azure IoT para chamar comandos em qualquer um dos dois componentes do termóstato ou no componente predefinido.
