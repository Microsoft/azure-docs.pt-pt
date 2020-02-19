---
title: Comunicar para dispositivo C# app em com streams de dispositivos Azure IoT Hub
description: Neste arranque rápido, executa C# duas aplicações de amostra que comunicam através de um fluxo de dispositivos estabelecido através do IoT Hub.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: cf182e55e172b308a288c741640538ab680e1103
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77462093"
---
# <a name="quickstart-communicate-to-a-device-application-in-c-via-iot-hub-device-streams-preview"></a>Quickstart: Comunicar a uma C# aplicação do dispositivo através de streams de dispositivos IoT Hub (pré-visualização)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

O Azure IoT Hub suporta atualmente os streams do dispositivo como recurso de [pré-visualização](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Os streams de [dispositivos IoT Hub](./iot-hub-device-streams-overview.md) permitem que as aplicações de serviço e dispositivo saem de forma segura e amiga da firewall. Este quickstart envolve C# duas aplicações que aproveitam os fluxos do dispositivo para enviar dados para trás e para a frente (eco).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* A visualização dos fluxos de dispositivos é atualmente suportada apenas para centros IoT que são criados nas seguintes regiões:
  * E.U.A. Central
  * EUA Centrais EUA
  * Europa do Norte
  * Ásia Sudeste

* As duas aplicações de amostra que executa C#neste arranque rápido estão escritas em . Você precisa do .NET Core SDK 2.1.0 ou mais tarde na sua máquina de desenvolvimento.
  * Descarregue o [.NET Core SDK para várias plataformas a partir de .NET](https://www.microsoft.com/net/download/all).
  * Verifique a versão C# atual da sua máquina de desenvolvimento utilizando o seguinte comando:

   ```
   dotnet --version
   ```

* Adicione a extensão Azure IoT para Azure CLI à sua instância Cloud Shell executando o seguinte comando. A extensão IOT adiciona comandos específicos do IoT Hub, IoT Edge e IoT Device Provisioning Service (DPS) específicos para o Azure CLI.

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    ```

* [Descarregue as amostras Azure IoT C# ](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) e extrai a arquivação ZIP. Precisa dele tanto no lado do dispositivo como no lado do serviço.

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registar um dispositivo

É necessário registar um dispositivo no hub IoT antes de o mesmo se poder ligar. Nesta secção, utiliza-se a Azure Cloud Shell para registar um dispositivo simulado.

1. Para criar a identidade do dispositivo, execute o seguinte comando na Cloud Shell:

   > [!NOTE]
   > * Substitua o espaço reservado *YourIoTHubName* pelo nome que escolheu para o seu hub IoT.
   > * Para o nome do dispositivo que está a registar, é aconselhável utilizar o *MyDevice* como mostrado. Se escolher um nome diferente para o seu dispositivo, utilize esse nome ao longo deste artigo e atualize o nome do dispositivo nas aplicações da amostra antes de os executar.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

1. Para obter a *cadeia de ligação* do dispositivo para o dispositivo que acabou de registar, execute o seguinte comando na Cloud Shell:

   > [!NOTE]
   > Substitua o espaço reservado *YourIoTHubName* pelo nome que escolheu para o seu hub IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyDevice --output table
    ```

    Note a cadeia de ligação do dispositivo devolvido para posterior utilização neste arranque rápido. O aspeto é igual ao do exemplo abaixo:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

3. Também precisa da cadeia de *ligação* de serviço do seu hub IoT para permitir que a aplicação do lado do serviço se ligue ao seu hub IoT e estabeleça um fluxo de dispositivos. O seguinte comando recupera este valor para o seu hub IoT:

   > [!NOTE]
   > Substitua o espaço reservado *YourIoTHubName* pelo nome que escolheu para o seu hub IoT.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
    ```

    Note a cadeia de ligação de serviço devolvida para posterior utilização neste arranque rápido. O aspeto é igual ao do exemplo abaixo:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="communicate-between-the-device-and-the-service-via-device-streams"></a>Comunicar entre o dispositivo e o serviço através de fluxos de dispositivos

Nesta secção, executa tanto a aplicação do lado do dispositivo como a aplicação do lado do serviço e comunica entre os dois.

### <a name="run-the-service-side-application"></a>Executar a aplicação do lado do serviço

Numa janela de terminal local, navegue para o diretório `iot-hub/Quickstarts/device-streams-echo/service` na sua pasta de projeto desapertada. Mantenha as seguintes informações à mão:

| Nome do parâmetro | Valor do parâmetro |
|----------------|-----------------|
| `ServiceConnectionString` | A cadeia de ligação de serviço do seu hub IoT. |
| `MyDevice` | O identificador do dispositivo que criou anteriormente. |

Compile e execute o código com os seguintes comandos:

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
A aplicação aguardará a disponibilização da aplicação do dispositivo.

> [!NOTE]
> Ocorre um tempo de tempo se a aplicação do lado do dispositivo não responder a tempo.

### <a name="run-the-device-side-application"></a>Executar a aplicação do lado do dispositivo

Noutra janela de terminal local, navegue para o diretório `iot-hub/Quickstarts/device-streams-echo/device` na sua pasta de projeto desapertada. Mantenha as seguintes informações à mão:

| Nome do parâmetro | Valor do parâmetro |
|----------------|-----------------|
| `DeviceConnectionString` | A cadeia de ligação do dispositivo do seu Hub IoT. |

Compile e execute o código com os seguintes comandos:

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

Saída da consola no lado do dispositivo:

![Saída da consola no lado do dispositivo](./media/quickstart-device-streams-echo-csharp/device-console-output.png)

Saída da consola no lado do serviço:

![Saída da consola no lado do serviço](./media/quickstart-device-streams-echo-csharp/service-console-output.png)

O tráfego que está a ser enviado sobre o riacho é túnel através do centro ioT em vez de enviado diretamente. Os benefícios fornecidos são detalhados nos benefícios dos streams do [Dispositivo.](./iot-hub-device-streams-overview.md#benefits)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou um hub IoT, registou um C# dispositivo, estabeleceu um fluxo de dispositivos entre aplicações no dispositivo e laterais de serviço, e utilizou o fluxo para enviar dados para trás e para a frente entre as aplicações.

Para saber mais sobre os fluxos de dispositivos, consulte:

> [!div class="nextstepaction"]
> [Visão geral dos fluxos de dispositivos](./iot-hub-device-streams-overview.md)
