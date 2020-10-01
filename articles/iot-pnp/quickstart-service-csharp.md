---
title: Interaja com um dispositivo IoT Plug and Play ligado à sua solução Azure IoT (C#) Microsoft Docs
description: Utilize C# para ligar e interagir com um dispositivo IoT Plug and Play que esteja ligado à sua solução Azure IoT.
author: ericmitt
ms.author: ericmitt
ms.date: 09/21/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 1a8155c9c0466184a09bc4ff224c9bdf42c173b9
ms.sourcegitcommit: 06ba80dae4f4be9fdf86eb02b7bc71927d5671d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/01/2020
ms.locfileid: "91612562"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-device-thats-connected-to-your-solution-c"></a>Quickstart: Interaja com um dispositivo IoT Plug and Play que está ligado à sua solução (C#)

[!INCLUDE [iot-pnp-quickstarts-service-selector.md](../../includes/iot-pnp-quickstarts-service-selector.md)]

O IoT Plug and Play simplifica o IoT, permitindo-lhe interagir com as capacidades de um dispositivo sem ter conhecimento da implementação do dispositivo subjacente. Este quickstart mostra-lhe como usar C# para ligar e controlar um dispositivo IoT Plug and Play que está ligado à sua solução.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

Para completar este arranque rápido no Windows, necessita do seguinte software instalado na sua máquina de desenvolvimento:

* [Estúdio Visual (Comunidade, Profissional ou Empresa)](https://visualstudio.microsoft.com/downloads/).
* [Git.](https://git-scm.com/download/)

### <a name="clone-the-sdk-repository-with-the-sample-code"></a>Clone o repositório SDK com o código de amostra

Se tiver concluído [o Quickstart: Ligue uma aplicação de dispositivo IoT Plug e Play de amostra que está a decorrer no Windows to IoT Hub (C#),](quickstart-connect-device-csharp.md)já clonou o repositório.

Clone as amostras do repositório Microsoft Azure IoT SDK para .NET GitHub. Abra um pedido de comando numa pasta à sua escolha. Executar o seguinte comando para clonar as [amostras de IoT microsoft Azure para o](https://github.com/Azure-Samples/azure-iot-samples-csharp) repositório .NET GitHub:

```cmd
git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
```

## <a name="run-the-sample-device"></a>Executar o dispositivo de amostra

Neste arranque rápido, utilize um dispositivo termóstato de amostra que está escrito em C# como o dispositivo IoT Plug and Play. Para executar o dispositivo de amostragem:

1. Abra o ficheiro *de projeto azure-iot-samples-csharp\iot-hub\Samples\device\PnpDeviceSamples\Thermostat\Thermostat.csproj* project file in Visual Studio 2019.

1. No Estúdio Visual, navegue para **Project > Thermostat Properties > Debug**. Em seguida, adicione as seguintes variáveis ambientais ao projeto:

    | Nome | Valor |
    | ---- | ----- |
    | IOTHUB_DEVICE_SECURITY_TYPE | DPS |
    | IOTHUB_DEVICE_DPS_ENDPOINT | global.azure-devices-provisioning.net |
    | IOTHUB_DEVICE_DPS_ID_SCOPE | O valor que fez notar quando completou [Configurar o seu ambiente](set-up-environment.md) |
    | IOTHUB_DEVICE_DPS_DEVICE_ID | my-pnp-dispositivo |
    | IOTHUB_DEVICE_DPS_DEVICE_KEY | O valor que fez notar quando completou [Configurar o seu ambiente](set-up-environment.md) |


1. Agora pode construir a amostra no Visual Studio e executá-la em modo de depurar.

1. Vê mensagens a dizer que o dispositivo enviou algumas informações e reportou-se online. Estas mensagens indicam que o dispositivo começou a enviar dados de telemetria para o hub, estando agora pronto para receber comandos e atualizações de propriedade. Não feche este caso de Visual Studio, precisa dele para confirmar que a amostra de serviço está a funcionar.

## <a name="run-the-sample-solution"></a>Executar a solução de amostra

Em [Configurar o seu ambiente para os quickstarts e tutoriais IoT Plug and Play](set-up-environment.md) criou duas variáveis ambientais para configurar a amostra para ligar ao seu hub e dispositivo IoT:

* **IOTHUB_CONNECTION_STRING**: a cadeia de ligação do hub IoT que fez uma nota anterior.
* **DEVICE_ID:** `"my-pnp-device"` .

Neste arranque rápido, utilize uma solução de IoT de amostra em C# para interagir com o dispositivo de amostra que acabou de configurar.

1. Em outro caso de Visual Studio, abra o projeto *azure-iot-samples-csharp\iot-hub\Samples\service\PnpServiceSamples\Thermostat\Thermostat.csproj.*

1. No Estúdio Visual, navegue para **Project > Thermostat Properties > Debug**. Em seguida, adicione as seguintes variáveis ambientais ao projeto:

    | Nome | Valor |
    | ---- | ----- |
    | DEVICE_ID | my-pnp-dispositivo |
    | IOTHUB_CONNECTION_STRING | O valor que fez notar quando completou [Configurar o seu ambiente](set-up-environment.md) |

1. Agora pode construir a amostra no Visual Studio e executá-la em modo de depurar.

### <a name="get-digital-twin"></a>Obter twin digital

O seguinte corte de código mostra como a aplicação de serviço recupera o gémeo digital:

```C#
// Get a Twin and retrieves model Id set by Device client
Twin twin = await s_registryManager.GetTwinAsync(s_deviceId);
s_logger.LogDebug($"Model Id of this Twin is: {twin.ModelId}");
```

> [!NOTE]
> Esta amostra utiliza o **Microsoft.Azure.Devices.Client;** espaço de nome do cliente de **serviço IoT Hub**. Para saber mais sobre como recuperar o ID do modelo, consulte o [guia do desenvolvedor.](concepts-developer-guide-service.md)

Este código gera a seguinte saída:

```cmd
[09/21/2020 11:26:04]dbug: Thermostat.Program[0]
      Model Id of this Twin is: dtmi:com:example:Thermostat;1
```

O seguinte corte de código mostra como usar um *patch* para atualizar propriedades através do twin digital:

```C#
// Update the twin
var twinPatch = new Twin();
twinPatch.Properties.Desired[PropertyName] = PropertyValue;
await s_registryManager.UpdateTwinAsync(s_deviceId, twinPatch, twin.ETag);
```

Este código gera a seguinte saída do dispositivo quando o serviço atualiza a `targetTemperature` propriedade:

```cmd
[09/21/2020 11:26:05]dbug: Thermostat.ThermostatSample[0]
      Property: Received - { "targetTemperature": 60°C }.
[09/21/2020 11:26:05]dbug: Thermostat.ThermostatSample[0]
      Property: Update - {"targetTemperature": 60°C } is InProgress.

...

[09/21/2020 11:26:17]dbug: Thermostat.ThermostatSample[0]
      Property: Update - {"targetTemperature": 60°C } is Completed.
```

### <a name="invoke-a-command"></a>Invocar um comando

O seguinte código snippet mostra como chamar um comando:

```csharp
private static async Task InvokeCommandAsync()
{
    var commandInvocation = new CloudToDeviceMethod(CommandName) { ResponseTimeout = TimeSpan.FromSeconds(30) };

    // Set command payload
    string componentCommandPayload = JsonConvert.SerializeObject(s_dateTime);
    commandInvocation.SetPayloadJson(componentCommandPayload);

    CloudToDeviceMethodResult result = await s_serviceClient.InvokeDeviceMethodAsync(s_deviceId, commandInvocation);

    if (result == null)
    {
        throw new Exception($"Command {CommandName} invocation returned null");
    }

    s_logger.LogDebug($"Command {CommandName} invocation result status is: {result.Status}");
}
```

Este código gera a seguinte saída do dispositivo quando o serviço chama o `getMaxMinReport` comando:

```cmd
[09/21/2020 11:26:05]dbug: Thermostat.ThermostatSample[0]
      Command: Received - Generating max, min and avg temperature report since 21/09/2020 11:25:58.
[09/21/2020 11:26:05]dbug: Thermostat.ThermostatSample[0]
      Command: MaxMinReport since 21/09/2020 11:25:58: maxTemp=32, minTemp=32, avgTemp=32, startTime=21/09/2020 11:25:59, endTime=21/09/2020 11:26:04
```

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a ligar um dispositivo IoT Plug e Play a uma solução IoT. Para saber mais sobre os modelos ioT Plug e Play, consulte:

> [!div class="nextstepaction"]
> [Guia de desenvolvedores de modelação IoT Plug e Play](concepts-developer-guide-device-csharp.md)
