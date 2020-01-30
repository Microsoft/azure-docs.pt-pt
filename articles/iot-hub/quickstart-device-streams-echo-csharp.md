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
ms.openlocfilehash: ee709919f550a8b9afaf56e5e071522a494348ac
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2020
ms.locfileid: "76773815"
---
# <a name="quickstart-communicate-to-a-device-application-in-c-via-iot-hub-device-streams-preview"></a>Quickstart: Comunicar a uma C# aplicação do dispositivo através de streams de dispositivos IoT Hub (pré-visualização)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

O Hub IoT do Azure atualmente dá suporte a fluxos de dispositivo como um [recurso de visualização](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Os [fluxos de dispositivo do Hub IOT](./iot-hub-device-streams-overview.md) permitem que aplicativos de serviço e de dispositivo se comuniquem de maneira segura e amigável ao firewall. Este quickstart envolve C# duas aplicações que aproveitam os fluxos do dispositivo para enviar dados para trás e para a frente (eco).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Atualmente, há suporte para a visualização de fluxos de dispositivo apenas para os hubs IoT criados nas seguintes regiões:
  * Centro dos E.U.A.
  * EUA Central EUAP
  * Europa do Norte
  * Sudeste Asiático

* Os dois aplicativos de exemplo que você executa neste guia de início rápido C#são escritos em. Você precisa do SDK do .NET Core 2.1.0 ou posterior em seu computador de desenvolvimento.
  * Descarregue o [.NET Core SDK para várias plataformas a partir de .NET](https://www.microsoft.com/net/download/all).
  * Verifique a versão atual do C# no computador de desenvolvimento usando o seguinte comando:

   ```
   dotnet --version
   ```

* Adicione a extensão Azure IoT para Azure CLI à sua instância Cloud Shell executando o seguinte comando. A extensão de IOT adiciona comandos específicos do serviço de provisionamento de dispositivos IOT, IoT Edge e do Hub IoT ao CLI do Azure.

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    ```

* [Descarregue as amostras Azure IoT C# ](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) e extrai a arquivação ZIP. Precisa dele tanto no lado do dispositivo como no lado do serviço.

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub-device-streams](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>Registar um dispositivo

É necessário registar um dispositivo no hub IoT antes de o mesmo se poder ligar. Nesta secção, utiliza-se a Azure Cloud Shell para registar um dispositivo simulado.

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

3. Também precisa da cadeia de *ligação* de serviço do seu hub IoT para permitir que a aplicação do lado do serviço se ligue ao seu hub IoT e estabeleça um fluxo de dispositivos. O comando a seguir recupera esse valor para o Hub IoT:

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

Numa janela de terminal local, navegue para o diretório `iot-hub/Quickstarts/device-streams-echo/service` na sua pasta de projeto desapertada. Mantenha as seguintes informações à mão:

| Nome do parâmetro | Valor do parâmetro |
|----------------|-----------------|
| `ServiceConnectionString` | A cadeia de ligação de serviço do seu hub IoT. |
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
A aplicação aguardará a disponibilização da aplicação do dispositivo.

> [!NOTE]
> Ocorre um tempo de tempo se a aplicação do lado do dispositivo não responder a tempo.

### <a name="run-the-device-side-application"></a>Executar o aplicativo do lado do dispositivo

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

Para saber mais sobre fluxos de dispositivo, consulte:

> [!div class="nextstepaction"]
> [Visão geral dos fluxos de dispositivo](./iot-hub-device-streams-overview.md)
