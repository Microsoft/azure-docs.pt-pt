---
title: Quickstart - Comunique à aplicação do dispositivo em C# com streams de dispositivoS Azure IoT Hub
description: Neste quickstart, você executou duas aplicações C# de amostra que comunicam através de um fluxo de dispositivo estabelecido através do IoT Hub.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc, devx-track-azurecli
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: 3eb65db27e5b96f4b12973154bc860a2ab3df020
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98624613"
---
# <a name="quickstart-communicate-to-a-device-application-in-c-via-iot-hub-device-streams-preview"></a>Quickstart: Comunicar a uma aplicação do dispositivo em C# via streams de dispositivo ioT Hub (pré-visualização)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

O Azure IoT Hub suporta atualmente os fluxos de dispositivos como [uma funcionalidade de pré-visualização](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[As correntes de dispositivos IoT Hub](./iot-hub-device-streams-overview.md) permitem que as aplicações de serviço e dispositivos se comuniquem de forma segura e amiga da firewall. Este quickstart envolve duas aplicações C# que aproveitam os fluxos do dispositivo para enviar dados de um lado para o outro (eco).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* A pré-visualização dos fluxos de dispositivos é atualmente suportada apenas para centros IoT que são criados nas seguintes regiões:
  * E.U.A. Central
  * EUA Central EUAP
  * Europa do Norte
  * Sudeste Asiático

* As duas aplicações de amostra que você executou neste quickstart estão escritas em C#. Precisa do .NET Core SDK 2.1.0 ou mais tarde na sua máquina de desenvolvimento.

    Descarregue o [.NET Core SDK para várias plataformas a partir de .NET](https://www.microsoft.com/net/download/all).

    Verifique a versão atual de C# na sua máquina de desenvolvimento utilizando o seguinte comando:

    ```
    dotnet --version
    ```

* [Descarregue as amostras Azure IoT C#](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) e extraia o arquivo ZIP. Precisa dele tanto no lado do dispositivo como no lado de serviço.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registar um dispositivo

É necessário registar um dispositivo no hub IoT antes de o mesmo se poder ligar. Nesta secção, utilize a Azure Cloud Shell para registar um dispositivo simulado.

1. Para criar a identidade do dispositivo, executar o seguinte comando em Cloud Shell:

   > [!NOTE]
   > * Substitua o espaço reservado *YourIoTHubName* pelo nome que escolheu para o seu hub IoT.
   > * Para o nome do dispositivo que está a registar, é aconselhável utilizar *o MyDevice* como mostrado. Se escolher um nome diferente para o seu dispositivo, use esse nome ao longo deste artigo e atualize o nome do dispositivo nas aplicações da amostra antes de executá-los.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

1. Para obter a *cadeia de ligação* do dispositivo para o dispositivo que acabou de registar, execute o seguinte comando em Cloud Shell:

   > [!NOTE]
   > Substitua o espaço reservado *YourIoTHubName* pelo nome que escolheu para o seu hub IoT.

    ```azurecli-interactive
    az iot hub device-identity connection-string show --hub-name {YourIoTHubName} --device-id MyDevice --output table
    ```

    Note a cadeia de ligação do dispositivo devolvido para posterior utilização neste arranque rápido. O aspeto é igual ao do exemplo abaixo:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

3. Também precisa da cadeia de *ligação* de serviço do seu hub IoT para permitir que a aplicação do lado do serviço se conecte ao seu hub IoT e estabeleça um fluxo de dispositivo. O seguinte comando recupera este valor para o seu hub IoT:

   > [!NOTE]
   > Substitua o espaço reservado *YourIoTHubName* pelo nome que escolheu para o seu hub IoT.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
    ```

    Note o fio de ligação de serviço devolvido para posterior utilização neste arranque rápido. O aspeto é igual ao do exemplo abaixo:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="communicate-between-the-device-and-the-service-via-device-streams"></a>Comunicar entre o dispositivo e o serviço através de fluxos de dispositivos

Nesta secção, execute a aplicação do lado do dispositivo e a aplicação do lado do serviço e comunique entre os dois.

### <a name="run-the-service-side-application"></a>Executar a aplicação do lado do serviço

Numa janela de terminal local, navegue para o `iot-hub/Quickstarts/device-streams-echo/service` diretório na sua pasta de projeto desapertado. Mantenha as seguintes informações à mão:

| Nome do parâmetro | Valor do parâmetro |
|----------------|-----------------|
| `ServiceConnectionString` | A cadeia de ligação de serviço do seu hub IoT. |
| `MyDevice` | O identificador do dispositivo que criou anteriormente. |

Compilar e executar o código com os seguintes comandos:

```
cd ./iot-hub/Quickstarts/device-streams-echo/service/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run "{ServiceConnectionString}" "MyDevice"

# In Windows
dotnet run {ServiceConnectionString} MyDevice
```
A aplicação aguarda a disponibilização da aplicação do dispositivo.

> [!NOTE]
> Ocorre um intervalo se a aplicação do lado do dispositivo não responder a tempo.

### <a name="run-the-device-side-application"></a>Executar a aplicação do lado do dispositivo

Em outra janela do terminal local, navegue para o `iot-hub/Quickstarts/device-streams-echo/device` diretório na sua pasta de projeto desapertado. Mantenha as seguintes informações à mão:

| Nome do parâmetro | Valor do parâmetro |
|----------------|-----------------|
| `DeviceConnectionString` | A cadeia de ligação do dispositivo do seu Hub IoT. |

Compilar e executar o código com os seguintes comandos:

```
cd ./iot-hub/Quickstarts/device-streams-echo/device/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run "{DeviceConnectionString}"

# In Windows
dotnet run {DeviceConnectionString}
```

No final do último passo, a aplicação do lado do serviço inicia um fluxo para o seu dispositivo. Após a criação do fluxo, a aplicação envia um tampão de corda para o serviço sobre o fluxo. Nesta amostra, a aplicação do lado do serviço simplesmente ecoa os mesmos dados para o dispositivo, o que demonstra uma comunicação bidirecional bem sucedida entre as duas aplicações.

Saída da consola do lado do dispositivo:

![Saída da consola no lado do dispositivo](./media/quickstart-device-streams-echo-csharp/device-console-output.png)

Saída da consola no lado do serviço:

![Saída da consola no lado do serviço](./media/quickstart-device-streams-echo-csharp/service-console-output.png)

O tráfego que está a ser enviado através do riacho é escavado através do centro de IoT em vez de ser enviado diretamente. Os benefícios fornecidos são detalhados nos [benefícios dos fluxos de dispositivos.](./iot-hub-device-streams-overview.md#benefits)

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, criou um hub IoT, registou um dispositivo, estabeleceu um fluxo de dispositivos entre aplicações C# no dispositivo e laterais de serviço, e utilizou o fluxo para enviar dados entre as aplicações.

Para saber mais sobre os fluxos de dispositivos, consulte:

> [!div class="nextstepaction"]
> [Visão geral dos fluxos de dispositivos](./iot-hub-device-streams-overview.md)
