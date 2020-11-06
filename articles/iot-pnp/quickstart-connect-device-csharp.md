---
title: Quickstart - Connect IoT Plug and Play sample C# device code to Azure IoT Hub [ Connect IoT Plug and Play sample C# device code to Azure IoT Hub ] Microsoft Docs
description: Quickstart - Construa e execute o código do dispositivo de amostra IoT Plug e Play no Windows que se conecta a um hub IoT. Utilize a ferramenta exploradora Azure IoT para visualizar as informações enviadas pelo dispositivo para o hub.
author: ericmitt
ms.author: ericmitt
ms.date: 07/14/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 01e87b08f8d0abcf3659e0d7bfe081fd3516ff08
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93421622"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-device-application-running-on-windows-to-iot-hub-c"></a>Quickstart: Conecte uma aplicação de dispositivo IoT Plug e Play de amostra que está a decorrer no Windows ao IoT Hub (C#)

[!INCLUDE [iot-pnp-quickstarts-device-selector.md](../../includes/iot-pnp-quickstarts-device-selector.md)]

Este quickstart mostra-lhe como construir uma aplicação de dispositivo IoT Plug e Play de amostra, conectá-la ao seu hub IoT e utilizar a ferramenta exploradora Azure IoT para visualizar a telemetria que envia. O pedido de amostra está escrito em C# e está incluído nas amostras Azure IoT para C#. Um construtor de soluções pode utilizar a ferramenta exploradorA Azure IoT para compreender as capacidades de um dispositivo IoT Plug e Play sem a necessidade de visualizar qualquer código do dispositivo.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

Para completar este arranque rápido no Windows, necessita do seguinte software instalado na sua máquina de desenvolvimento:

* [Estúdio Visual (Comunidade, Profissional ou Empresa)](https://visualstudio.microsoft.com/downloads/).
* [Git.](https://git-scm.com/download/)

## <a name="download-the-code"></a>Transferir o código

Neste quickstart, você prepara um ambiente de desenvolvimento que você pode usar para clonar e construir as amostras Azure IoT para o repositório C#.

Abra um pedido de comando numa pasta à sua escolha. Executar o seguinte comando para clonar as [amostras de IoT microsoft Azure para c# (.NET)](https://github.com/Azure-Samples/azure-iot-samples-csharp) GitHub repositório neste local:

```cmd
git clone  https://github.com/Azure-Samples/azure-iot-samples-csharp.git
```

## <a name="build-the-code"></a>Compilar o código

Agora pode construir a amostra no Visual Studio e executá-la em modo de depurar.

1. Abra o ficheiro *de projeto azure-iot-samples-csharp\iot-hub\Samples\device\PnpDeviceSamples\Thermostat\Thermostat.csproj* project file in Visual Studio 2019.

1. No Estúdio Visual, navegue para **Project > Thermostat Properties > Debug**. Em seguida, adicione as seguintes variáveis ambientais ao projeto:

    | Name | Valor |
    | ---- | ----- |
    | IOTHUB_DEVICE_SECURITY_TYPE | DPS |
    | IOTHUB_DEVICE_DPS_ENDPOINT | global.azure-devices-provisioning.net |
    | IOTHUB_DEVICE_DPS_ID_SCOPE | O valor que fez notar quando completou [Configurar o seu ambiente](set-up-environment.md) |
    | IOTHUB_DEVICE_DPS_DEVICE_ID | my-pnp-dispositivo |
    | IOTHUB_DEVICE_DPS_DEVICE_KEY | O valor que fez notar quando completou [Configurar o seu ambiente](set-up-environment.md) |

Agora pode construir a amostra no Visual Studio e executá-la em modo de depurar.

## <a name="run-the-device-sample"></a>Executar a amostra do dispositivo

Para rastrear a execução de código no Estúdio Visual no Windows, adicione um ponto de rutura à `main` função no ficheiro program.cs.

O dispositivo está agora pronto para receber comandos e atualizações de propriedade, e começou a enviar dados de telemetria para o hub. Mantenha a amostra a funcionar à medida que completar os próximos passos.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Use o explorador Azure IoT para validar o código

Depois de iniciar a amostra do cliente do dispositivo, utilize a ferramenta exploradora Azure IoT para verificar se está a funcionar.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>Rever o código

Esta amostra implementa um simples dispositivo de termóstato IoT Plug e Play. O modelo que esta amostra implementa não utiliza [componentes](concepts-components.md)IoT Plug e Play . O [ficheiro de modelo de definição de Gémeos Digitais (DTDL) para o dispositivo termóstato](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) define a telemetria, propriedades e comanda os dispositivos.

O código do dispositivo liga-se ao seu hub IoT utilizando o `CreateFromConnectionString` método padrão. O dispositivo envia o ID modelo do modelo DTDL que implementa no pedido de ligação. Um dispositivo que envia um ID modelo é um dispositivo IoT Plug and Play:

```csharp
private static void InitializeDeviceClientAsync()
{
  var options = new ClientOptions
  {
    ModelId = ModelId,
  };
  s_deviceClient = DeviceClient.CreateFromConnectionString(s_deviceConnectionString, TransportType.Mqtt, options);
  s_deviceClient.SetConnectionStatusChangesHandler((status, reason) =>
  {
     s_logger.LogDebug($"Connection status change registered - status={status}, reason={reason}.");
  });
}
```

O ID do modelo é armazenado no código tal como mostrado no seguinte corte:

```csharp
private const string ModelId = "dtmi:com:example:Thermostat;1";
```

O código que atualiza propriedades, lida com comandos e envia telemetria é idêntico ao código de um dispositivo que não utiliza as convenções IoT Plug and Play.

A amostra utiliza uma biblioteca JSON para analisar objetos JSON nas cargas enviadas do seu hub IoT:

```csharp
using Newtonsoft.Json;

...

DateTime since = JsonConvert.DeserializeObject<DateTime>(request.DataAsJson);
```

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a ligar um dispositivo IoT Plug e Play a um hub IoT. Para saber mais sobre como construir uma solução que interage com os seus dispositivos IoT Plug e Play, consulte:

> [!div class="nextstepaction"]
> [Como fazer: Ligar e interagir com um dispositivo](./quickstart-service-node.md)