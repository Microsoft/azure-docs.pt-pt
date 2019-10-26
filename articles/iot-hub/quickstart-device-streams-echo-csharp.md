---
title: Comunicar-se com um aplicativo C# de dispositivo por meio de fluxos de dispositivo do Hub IOT do Azure (visualização) | Microsoft Docs
description: Neste guia de início rápido, você executa C# dois aplicativos de exemplo que se comunicam por meio de um fluxo de dispositivo estabelecido por meio do Hub IOT.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: c994b77105fe94eef418c0befc4c135ec09ada14
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900937"
---
# <a name="quickstart-communicate-to-a-device-application-in-c-via-iot-hub-device-streams-preview"></a>Início rápido: comunicar-se com um C# aplicativo de dispositivo por meio de fluxos de dispositivo do Hub IOT (versão prévia)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

O Hub IoT do Azure atualmente dá suporte a fluxos de dispositivo como um [recurso de visualização](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Os [fluxos de dispositivo do Hub IOT](./iot-hub-device-streams-overview.md) permitem que aplicativos de serviço e de dispositivo se comuniquem de maneira segura e amigável ao firewall. Este guia de início C# rápido envolve dois aplicativos que aproveitam os fluxos de dispositivo para enviar dados de volta e para trás (eco).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Atualmente, há suporte para a visualização de fluxos de dispositivo apenas para os hubs IoT criados nas seguintes regiões:
  * Centro dos E.U.A.
  * EUA Central EUAP
  * Europa do Norte
  * Sudeste Asiático

* Os dois aplicativos de exemplo que você executa neste guia de início rápido C#são escritos em. Você precisa do SDK do .NET Core 2.1.0 ou posterior em seu computador de desenvolvimento.
  * Baixe o [SDK do .NET Core para várias plataformas do .net](https://www.microsoft.com/net/download/all).
  * Verifique a versão atual do C# no computador de desenvolvimento usando o seguinte comando:

   ```
   dotnet --version
   ```

* Adicione a extensão do Azure IoT para CLI do Azure à instância do Cloud Shell executando o comando a seguir. A extensão de IOT adiciona comandos específicos do serviço de provisionamento de dispositivos IOT, IoT Edge e do Hub IoT ao CLI do Azure.

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    ```

* [Baixe o projeto C# de exemplo](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) e extraia o arquivo zip. Você precisa dele tanto no lado do dispositivo quanto no lado do serviço.

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub-device-streams](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>Registar um dispositivo

É necessário registar um dispositivo no hub IoT antes de o mesmo se poder ligar. Nesta seção, você usará Azure Cloud Shell para registrar um dispositivo simulado.

1. Para criar a identidade do dispositivo, execute o seguinte comando no Cloud Shell:

   > [!NOTE]
   > * Substitua o espaço reservado *nomedoseuhubiot* pelo nome que você escolheu para o Hub IOT.
   > * Para o nome do dispositivo que você está registrando, é recomendável usar *MyDevice* , conforme mostrado. Se você escolher um nome diferente para seu dispositivo, use esse nome em todo este artigo e atualize o nome do dispositivo nos aplicativos de exemplo antes de executá-los.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

1. Para obter a *cadeia de conexão do dispositivo* que você acabou de registrar, execute o seguinte comando no Cloud Shell:

   > [!NOTE]
   > Substitua o espaço reservado *nomedoseuhubiot* pelo nome que você escolheu para o Hub IOT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyDevice --output table
    ```

    Observe a cadeia de conexão do dispositivo retornado para uso posterior neste guia de início rápido. O aspeto é igual ao do exemplo abaixo:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

3. Você também precisa da *cadeia de conexão de serviço* do Hub IOT para permitir que o aplicativo do lado do serviço se conecte ao seu hub IOT e estabeleça um fluxo de dispositivo. O comando a seguir recupera esse valor para o Hub IoT:

   > [!NOTE]
   > Substitua o espaço reservado *nomedoseuhubiot* pelo nome que você escolheu para o Hub IOT.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
    ```

    Observe a cadeia de conexão de serviço retornada para uso posterior neste guia de início rápido. O aspeto é igual ao do exemplo abaixo:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="communicate-between-the-device-and-the-service-via-device-streams"></a>Comunicar entre o dispositivo e o serviço por meio de fluxos de dispositivo

Nesta seção, você executa o aplicativo do lado do dispositivo e o aplicativo do lado do serviço e se comunica entre os dois.

### <a name="run-the-service-side-application"></a>Executar o aplicativo do lado do serviço

Em uma janela de terminal local, navegue até o diretório `iot-hub/Quickstarts/device-streams-echo/service` em sua pasta de projeto descompactada. Mantenha as seguintes informações à mão:

| Nome do parâmetro | Valor do parâmetro |
|----------------|-----------------|
| `ServiceConnectionString` | A cadeia de conexão de serviço do Hub IoT. |
| `MyDevice` | O identificador do dispositivo que você criou anteriormente. |

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
O aplicativo aguardará que o aplicativo do dispositivo fique disponível.

> [!NOTE]
> Um tempo limite ocorre se o aplicativo do lado do dispositivo não responder no tempo.

### <a name="run-the-device-side-application"></a>Executar o aplicativo do lado do dispositivo

Em outra janela do terminal local, navegue até o diretório `iot-hub/Quickstarts/device-streams-echo/device` em sua pasta de projeto descompactada. Mantenha as seguintes informações à mão:

| Nome do parâmetro | Valor do parâmetro |
|----------------|-----------------|
| `DeviceConnectionString` | A cadeia de conexão do dispositivo do Hub IoT. |

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

No final da última etapa, o aplicativo do lado do serviço inicia um fluxo para seu dispositivo. Depois que o fluxo é estabelecido, o aplicativo envia um buffer de cadeia de caracteres para o serviço por meio do fluxo. Neste exemplo, o aplicativo do lado do serviço simplesmente ecoa os mesmos dados para o dispositivo, o que demonstra uma comunicação bidirecional bem-sucedida entre os dois aplicativos.

Saída do console no lado do dispositivo:

![Saída do console no lado do dispositivo](./media/quickstart-device-streams-echo-csharp/device-console-output.png)

Saída do console no lado do serviço:

![Saída do console no lado do serviço](./media/quickstart-device-streams-echo-csharp/service-console-output.png)

O tráfego que está sendo enviado pelo fluxo é encapsulado por meio do Hub IoT em vez de enviado diretamente. Os benefícios fornecidos são detalhados em [benefícios de fluxos de dispositivo](./iot-hub-device-streams-overview.md#benefits).

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você configura um hub IoT, registrou um dispositivo, estabeleceu um C# fluxo de dispositivo entre aplicativos nos lados do dispositivo e do serviço e usou o fluxo para enviar dados entre os aplicativos.

Para saber mais sobre fluxos de dispositivo, consulte:

> [!div class="nextstepaction"]
> [Visão geral dos fluxos de dispositivo](./iot-hub-device-streams-overview.md)
