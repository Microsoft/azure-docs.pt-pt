---
title: Ligue o código do dispositivo componente IoT Plug e Play Preview À IoT Hub / Microsoft Docs
description: Construa e execute o código do dispositivo IoT Plug e Play Preview que utiliza vários componentes e se conecta a um hub IoT. Utilize a ferramenta exploradora Azure IoT para visualizar as informações enviadas pelo dispositivo para o hub.
author: ericmitt
ms.author: ericmitt
ms.date: 07/14/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: cf2662bef194bc2b7afdb07f55809ba63f40fd6e
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87352859"
---
# <a name="tutorial-connect-an-iot-plug-and-play-multiple-component-device-application-running-on-windows-to-iot-hub-c"></a>Tutorial: Ligue uma aplicação de dispositivo ioT plug e play múltiplo componente em execução no Windows ao IoT Hub (C#)

[!INCLUDE [iot-pnp-tutorials-device-selector.md](../../includes/iot-pnp-tutorials-device-selector.md)]

Este tutorial mostra-lhe como construir uma aplicação de dispositivo IoT Plug e Play com componentes e interface de raiz, conectá-la ao seu hub IoT e utilizar a ferramenta exploradora Azure IoT para visualizar a informação que envia para o centro. A aplicação da amostra está escrita em C# e está incluída no dispositivo Azure IoT SDK para C#. Um construtor de soluções pode utilizar a ferramenta exploradorA Azure IoT para compreender as capacidades de um dispositivo IoT Plug e Play sem a necessidade de visualizar qualquer código do dispositivo.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para completar este tutorial no Windows, instale o seguinte software no seu ambiente local do Windows:

* [Estúdio Visual (Comunidade, Profissional ou Empresa)](https://visualstudio.microsoft.com/downloads/).
* [Git.](https://git-scm.com/download/)
* [CMake.](https://cmake.org/download/)

### <a name="azure-iot-explorer"></a>Explorador de Azure IoT

Para interagir com o dispositivo de amostra na segunda parte deste tutorial, utilize a ferramenta **exploradora Azure IoT.** [Descarregue e instale a mais recente versão do explorador Azure IoT](./howto-use-iot-explorer.md) para o seu sistema operativo.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Executar o seguinte comando para obter a _cadeia de ligação do hub IoT_ para o seu hub. Tome nota desta cadeia de ligação, use-a mais tarde neste tutorial:

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

> [!TIP]
> Também pode utilizar a ferramenta exploradora Azure IoT para encontrar a cadeia de ligação do hub IoT.

Executar o seguinte comando para obter a _cadeia de ligação_ do dispositivo para o dispositivo que adicionou ao hub. Tome nota desta cadeia de ligação, use-a mais tarde neste tutorial:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```

[!INCLUDE [iot-pnp-download-models.md](../../includes/iot-pnp-download-models.md)]

## <a name="download-the-code"></a>Transferir o código

Neste tutorial, você prepara um ambiente de desenvolvimento que você pode usar para clonar e construir o Azure IoT Hub Device C# SDK.

Abra um pedido de comando no diretório à sua escolha. Execute o seguinte comando para clonar o [repositório Azure IoT C# SDKs e Bibliotecas](https://github.com/Azure/azure-iot-sdk-csharp) GitHub neste local:

```cmd
git clone https://github.com/Azure/azure-iot-sdk-c.git
```

## <a name="build-the-code"></a>Compilar o código

Abra o ficheiro de solução **azureiot.sln** no Visual Studio 2019 e coloque o projeto **TemperatureController** como o projeto de arranque. No **Solution Explorer,** pode encontrar este ficheiro de projeto no **dispositivo iothub > > amostras.**

Agora pode construir a amostra no Visual Studio e executá-la em modo de depurar.

## <a name="run-the-device-sample"></a>Executar a amostra do dispositivo

Crie uma variável ambiental chamada **IOTHUB_DEVICE_CONNECTION_STRING** para armazenar a cadeia de ligação do dispositivo que fez anteriormente.

Para rastrear a execução de código no Estúdio Visual no Windows, adicione um ponto de rutura à `main` função no ficheiro program.cs.

O dispositivo está agora pronto para receber comandos e atualizações de propriedade, e começou a enviar dados de telemetria para o hub. Mantenha a amostra a funcionar à medida que completar os próximos passos.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Use o explorador Azure IoT para validar o código

Depois de iniciar a amostra do cliente do dispositivo, utilize a ferramenta exploradora Azure IoT para verificar se está a funcionar.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>Rever o código

Esta amostra implementa um dispositivo de controlador de temperatura IoT Plug e Play. O modelo que esta amostra implementa utiliza [vários componentes.](concepts-components.md) O ficheiro de definição de linguagem de [definição de Gémeos Digitais (DTDL) para o dispositivo de temperatura](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) define a telemetria, propriedades e comanda os dispositivos.

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

Depois de o dispositivo ligar ao seu hub IoT, o código regista os manipuladores de comando. O `reboot` comando é definido na interface raiz. O `getMaxMinReport` comando é definido em cada um dos dois componentes do termóstato:

```csharp
await s_deviceClient.SetMethodHandlerAsync("reboot", HandleRebootCommandAsync, s_deviceClient);
await s_deviceClient.SetMethodHandlerAsync("thermostat1*getMaxMinReport", HandleMaxMinReportCommandAsync, Thermostat1);
await s_deviceClient.SetMethodHandlerAsync("thermostat2*getMaxMinReport", HandleMaxMinReportCommandAsync, Thermostat2);
```

Existem manipuladores separados para as atualizações de propriedade desejadas nos dois componentes do termóstato:

```csharp
s_desiredPropertyUpdateCallbacks.Add(Thermostat1, TargetTemperatureUpdateCallbackAsync);
s_desiredPropertyUpdateCallbacks.Add(Thermostat2, TargetTemperatureUpdateCallbackAsync);
```

O código de amostra envia telemetria de cada componente do termóstato:

```csharp
await SendTemperatureAsync(Thermostat1);
await SendTemperatureAsync(Thermostat2);
```

O `SendTemperature` método utiliza a classe para criar `PnpHhelper` mensagens para cada componente:

```csharp
Message msg = PnpHelper.CreateIothubMessageUtf8(telemetryName, JsonConvert.SerializeObject(currentTemperature), componentName);
```

A `PnpHelper` classe contém outros métodos de amostra que pode utilizar com um modelo de múltiplos componentes.

Utilize a ferramenta exploradora Azure IoT para visualizar a telemetria e as propriedades dos dois componentes do termóstato:

:::image type="content" source="media/tutorial-multiple-components-csharp/multiple-component.png" alt-text="Dispositivo de vários componentes no explorador Azure IoT":::

Também pode utilizar a ferramenta exploradora Azure IoT para chamar comandos em qualquer um dos dois componentes do termóstato ou na interface raiz.

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Próximos passos

Neste tutorial, aprendeu a ligar um dispositivo IoT Plug and Play com componentes a um hub IoT. Para saber mais sobre os modelos ioT Plug e Play, consulte:

> [!div class="nextstepaction"]
> [Guia de modelação IoT Plug e Play Preview](concepts-developer-guide.md)
