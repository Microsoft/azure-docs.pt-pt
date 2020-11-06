---
title: Tutorial - Ligar o código do dispositivo componente IoT Plug e Play C# ao Azure IoT Hub [ Microsoft Docs
description: Tutorial - Construa e execute o código do dispositivo IoT Plug e Play, que utiliza vários componentes e se conecta a um hub IoT. Utilize a ferramenta exploradora Azure IoT para visualizar as informações enviadas pelo dispositivo para o hub.
author: ericmitt
ms.author: ericmitt
ms.date: 07/14/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: f7a1aa85c4456bdcf7ed50e42382fc440618ec7d
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93421452"
---
# <a name="tutorial-connect-an-iot-plug-and-play-multiple-component-device-application-running-on-windows-to-iot-hub-c"></a>Tutorial: Ligue uma aplicação de dispositivo ioT plug e play múltiplo componente em execução no Windows ao IoT Hub (C#)

[!INCLUDE [iot-pnp-tutorials-device-selector.md](../../includes/iot-pnp-tutorials-device-selector.md)]

Este tutorial mostra-lhe como construir uma aplicação de dispositivo IoT Plug e Play com componentes, conectá-la ao seu hub IoT e utilizar a ferramenta exploradora Azure IoT para visualizar a informação que envia para o centro. A aplicação da amostra está escrita em C# e está incluída no dispositivo Azure IoT SDK para C#. Um construtor de soluções pode utilizar a ferramenta exploradorA Azure IoT para compreender as capacidades de um dispositivo IoT Plug e Play sem a necessidade de visualizar qualquer código do dispositivo.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

Para completar este tutorial no Windows, instale o seguinte software no seu ambiente local do Windows:

* [Estúdio Visual (Comunidade, Profissional ou Empresa)](https://visualstudio.microsoft.com/downloads/).
* [Git.](https://git-scm.com/download/)

### <a name="clone-the-sdk-repository-with-the-sample-code"></a>Clone o repositório SDK com o código de amostra

Se tiver concluído [o Quickstart: Ligue uma aplicação de dispositivo IoT Plug e Play de amostra que está a decorrer no Windows to IoT Hub (C#),](quickstart-connect-device-csharp.md)já clonou o repositório.

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
    | IOTHUB_DEVICE_DPS_ID_SCOPE | O valor que fez notar quando completou [Configurar o seu ambiente](set-up-environment.md) |
    | IOTHUB_DEVICE_DPS_DEVICE_ID | my-pnp-dispositivo |
    | IOTHUB_DEVICE_DPS_DEVICE_KEY | O valor que fez notar quando completou [Configurar o seu ambiente](set-up-environment.md) |


1. Agora pode construir a amostra no Visual Studio e executá-la em modo de depurar.

1. Vê mensagens a dizer que o dispositivo enviou algumas informações e reportou-se online. Estas mensagens indicam que o dispositivo começou a enviar dados de telemetria para o hub, estando agora pronto para receber comandos e atualizações de propriedade. Não feche este caso de Visual Studio, precisa dele para confirmar que a amostra de serviço está a funcionar.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Use o explorador Azure IoT para validar o código

Depois de iniciar a amostra do cliente do dispositivo, utilize a ferramenta exploradora Azure IoT para verificar se está a funcionar.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>Rever o código

Esta amostra implementa um dispositivo de controlador de temperatura IoT Plug e Play. O modelo que esta amostra implementa utiliza [vários componentes.](concepts-components.md) O ficheiro de definição de linguagem de [definição de Gémeos Digitais (DTDL) para o dispositivo de temperatura](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) define a telemetria, propriedades e comanda os dispositivos.

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

:::image type="content" source="media/tutorial-multiple-components-csharp/multiple-component.png" alt-text="Dispositivo de vários componentes no explorador Azure IoT":::

Também pode utilizar a ferramenta exploradorA Azure IoT para chamar comandos em qualquer um dos dois componentes do termóstato ou no componente predefinido.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a ligar um dispositivo IoT Plug and Play com componentes a um hub IoT. Para saber mais sobre os modelos ioT Plug e Play, consulte:

> [!div class="nextstepaction"]
> [Guia de desenvolvedores de modelação IoT Plug e Play](concepts-developer-guide-device-csharp.md)
