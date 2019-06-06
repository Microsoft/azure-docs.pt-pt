---
title: Comunicar com uma aplicação de dispositivo no C# através de fluxos de dispositivo do IoT Hub do Azure (pré-visualização) | Documentos da Microsoft
description: Neste início rápido, executa duas de exemplo C# aplicações que se comunicam através de um fluxo de dispositivo estabelecido através do IoT Hub.
author: rezasherafat
manager: briz
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: rezas
ms.openlocfilehash: 74a8fc40cff12070f7cea99981eb4e8321d7c1ef
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/06/2019
ms.locfileid: "66735143"
---
# <a name="quickstart-communicate-to-a-device-application-in-c-via-iot-hub-device-streams-preview"></a>Início rápido: Comunicar com um aplicativo de dispositivo no C# através de fluxos de dispositivo do IoT Hub (pré-visualização)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Atualmente, o IoT Hub do Azure suporta fluxos de dispositivo como uma [funcionalidade de pré-visualização](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Fluxos de dispositivo do IoT Hub](./iot-hub-device-streams-overview.md) permitem que os aplicativos de serviço e dispositivo comunicar de forma segura e compatível com firewall. Este guia de introdução envolve dois C# aplicações que tiram partido dos fluxos de dispositivo para enviar dados para a frente e para trás (eco).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* A pré-visualização de fluxos de dispositivo é atualmente suportada apenas para os hubs IoT que são criados nas seguintes regiões:
  * EUA Central
  * E.U.A. central EUAP

* As duas aplicações de exemplo que executar neste início rápido são escritas utilizando C#. É necessário o .NET Core SDK 2.1.0 ou posterior no seu computador de desenvolvimento.
  * Transfira o [SDK para .NET Core para várias plataformas do .NET](https://www.microsoft.com/net/download/all).
  * Certifique-se a versão atual do C# no computador de desenvolvimento com o seguinte comando:

   ```
   dotnet --version
   ```

* Adicione a extensão de IoT do Azure para a CLI do Azure à sua instância do Cloud Shell, executando o seguinte comando. A extensão de IOT adiciona o IoT Hub, o IoT Edge e o serviço aprovisionamento de dispositivos IoT (DPS)-comandos específicos para a CLI do Azure.

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    ```

* [Transferir o exemplo C# projeto](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) e extraia o arquivo ZIP. Precisa no lado do dispositivo e no lado do serviço.

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub-device-streams](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>Registar um dispositivo

É necessário registar um dispositivo no hub IoT antes de o mesmo se poder ligar. Nesta secção, utilize o Azure Cloud Shell para registar um dispositivo simulado.

1. Para criar a identidade de dispositivo, execute o seguinte comando no Cloud Shell:

   > [!NOTE]
   > * Substitua a *YourIoTHubName* espaço reservado com o nome que escolher para o seu hub IoT.
   > * Uso *MyDevice*, conforme mostrado. É o nome fornecido para o dispositivo registado. Se escolher um nome diferente para o seu dispositivo, utilize esse nome ao longo deste artigo e atualizar o nome do dispositivo em aplicativos de exemplo, antes de gerá-los.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyDevice
    ```

1. Para obter o *cadeia de ligação do dispositivo* para o dispositivo que acabou de registar, execute o seguinte comando no Cloud Shell:

   > [!NOTE]
   > Substitua a *YourIoTHubName* espaço reservado com o nome que escolher para o seu hub IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyDevice --output table
    ```

    Tenha em atenção a cadeia de ligação do dispositivo para utilizar mais tarde neste início rápido. O aspeto é igual ao do exemplo abaixo:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

3. Também tem do *cadeia de ligação de serviço* do seu hub IoT para permitir que o aplicativo do lado do serviço para se ligar ao seu hub IoT e estabelecer um fluxo de dispositivo. O comando seguinte obtém este valor para o seu hub IoT:

   > [!NOTE]
   > Substitua a *YourIoTHubName* espaço reservado com o nome que escolher para o seu hub IoT.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name YourIoTHubName
    ```

    Tenha em atenção o valor devolvido para utilizar mais tarde neste início rápido. O aspeto é igual ao do exemplo abaixo:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="communicate-between-the-device-and-the-service-via-device-streams"></a>Comunicar entre o dispositivo e o serviço por meio de fluxos de dispositivo

Nesta secção, execute a aplicação do lado do dispositivo e a aplicação do lado do serviço e comunicar entre os dois.

### <a name="run-the-service-side-application"></a>Executar a aplicação do lado do serviço

Vá para o *iot-hub/inícios rápidos/dispositivo-fluxos-eco/serviço* diretório na pasta do projeto descompactado. Mantenha as seguintes informações úteis:

| Nome do parâmetro | Valor do parâmetro |
|----------------|-----------------|
| `ServiceConnectionString` | Indicar a cadeia de ligação de serviço do IoT hub. |
| `DeviceId` | Forneça o ID do dispositivo que criou anteriormente (por exemplo, *MyDevice*). |

Compilar e executar o código da seguinte forma:

```
cd ./iot-hub/Quickstarts/device-streams-echo/service/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run "<ServiceConnectionString>" "<MyDevice>"

# In Windows
dotnet run <ServiceConnectionString> <MyDevice>
```

> [!NOTE]
> Um tempo limite ocorre se a aplicação do lado do dispositivo não responder em tempo.

### <a name="run-the-device-side-application"></a>Executar a aplicação do lado do dispositivo

Vá para o *iot-hub/inícios rápidos/dispositivo fluxos eco/dispositivo* diretório na pasta do projeto descompactado. Mantenha as seguintes informações úteis:

| Nome do parâmetro | Valor do parâmetro |
|----------------|-----------------|
| `DeviceConnectionString` | Indicar a cadeia de ligação do dispositivo do IoT Hub. |

Compilar e executar o código da seguinte forma:

```
cd ./iot-hub/Quickstarts/device-streams-echo/device/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run "<DeviceConnectionString>"

# In Windows
dotnet run <DeviceConnectionString>
```

No final da última etapa, a aplicação do lado do serviço inicia um fluxo para o seu dispositivo. Uma vez estabelecido o fluxo, a aplicação envia um buffer de cadeia de caracteres para o serviço sobre o fluxo. Neste exemplo, a aplicação do lado do serviço simplesmente ecoa de volta os mesmos dados para o dispositivo, que demonstra uma comunicação bidirecional bem-sucedida entre as duas aplicações.

Resultado do lado do dispositivo da consola:

![Resultado da consola do lado do dispositivo](./media/quickstart-device-streams-echo-csharp/device-console-output.png)

Console de saída no lado do serviço:

![Resultado da consola no lado do serviço](./media/quickstart-device-streams-echo-csharp/service-console-output.png)

O que está a ser enviado através do fluxo de tráfego é em túnel através do hub IoT em vez de enviados diretamente. Os benefícios fornecidos estão descritos no [dispositivo transmite os benefícios](./iot-hub-device-streams-overview.md#benefits).

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, configurar um hub IoT, registou um dispositivo, estabelecer um fluxo de dispositivo entre C# aplicações nos lados dispositivos e de serviços e utilizado o fluxo para enviar dados entre os aplicativos.

Para saber mais sobre fluxos de dispositivo, veja:

> [!div class="nextstepaction"]
> [Descrição geral de fluxos de dispositivo](./iot-hub-device-streams-overview.md)
