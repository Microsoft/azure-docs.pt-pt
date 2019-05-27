---
title: Comunicar com uma aplicação de dispositivo no C# através de fluxos de dispositivo do IoT Hub do Azure (pré-visualização) | Documentos da Microsoft
description: Neste início rápido, irá executar dois exemplos C# aplicações que se comunicam através de um fluxo de dispositivo estabelecido através do IoT Hub.
author: rezasherafat
manager: briz
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: rezas
ms.openlocfilehash: 8df57d3d36dcae851c9c0e23ea609e200a429605
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/17/2019
ms.locfileid: "65832917"
---
# <a name="quickstart-communicate-to-a-device-application-in-c-via-iot-hub-device-streams-preview"></a>Início rápido: Comunicar com um aplicativo de dispositivo no C# através de fluxos de dispositivo do IoT Hub (pré-visualização)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Atualmente, o IoT Hub do Microsoft Azure suporta fluxos de dispositivo como uma [funcionalidade de pré-visualização](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Fluxos de dispositivo do IoT Hub](./iot-hub-device-streams-overview.md) permitem que os aplicativos de serviço e dispositivo comunicar de forma segura e compatível com firewall. Este guia de introdução envolve dois C# programas que tiram partido dos fluxos de dispositivo para enviar dados para a frente e para trás (eco).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

*  A pré-visualização de fluxos de dispositivo é atualmente suportado apenas para os Hubs IoT criado nas seguintes regiões:

   *  **E.U.A. central**

   *  **E.U.A. central EUAP**

As duas aplicações de exemplo que executa neste guia de início rápido são escritas com C#. Precisa do SDK de .NET Core 2.1.0 ou posterior no seu computador de desenvolvimento.

*  Transfira o [SDK para .NET Core para várias plataformas do .NET](https://www.microsoft.com/net/download/all).

Pode verificar qual a versão atual do C# no seu computador de desenvolvimento através do seguinte comando:

```
dotnet --version
```

*  Execute o seguinte comando para adicionar a extensão de IoT do Microsoft Azure para a CLI do Azure à sua instância do Cloud Shell. A extensão de IOT adiciona comandos específicos de IoT Hub, o IoT Edge e o serviço aprovisionamento de dispositivos IoT (DPS) para a CLI do Azure.

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    ```

* Transfira o projeto C# de exemplo de https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip e extraia o arquivo ZIP. Irá precisar no lado do serviço e dispositivo.

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub-device-streams](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>Registar um dispositivo

É necessário registar um dispositivo no hub IoT antes de o mesmo se poder ligar. Neste início rápido, vai utilizar o Azure Cloud Shell para registar um dispositivo simulado.

1. Execute o seguinte comando no Azure Cloud Shell para criar a identidade de dispositivo.

   **YourIoTHubName**: Substitua este marcador de posição abaixo com o nome que escolher para o seu hub IoT.

   **MyDevice**: Este é o nome fornecido para o dispositivo registado. Utilize MyDevice, conforme mostrado. Se escolher um nome diferente para o seu dispositivo, também irá precisar de utilizar esse nome através deste artigo, e atualize o nome do dispositivo em aplicações de exemplo antes de as executar.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyDevice
    ```

2. Execute o seguinte comando no Azure Cloud Shell para obter a _cadeia de ligação do dispositivo_ que acabou de registar:

   **YourIoTHubName**: Substitua este marcador de posição abaixo com o nome que escolher para o seu hub IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyDevice --output table
    ```

    Anote a cadeia de ligação do dispositivo, que é semelhante ao seguinte exemplo:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

    Irá utilizar este valor mais adiante no guia de início rápido.

3. Também tem do *cadeia de ligação de serviço* do seu hub IoT para permitir que o aplicativo do lado do serviço para se ligar ao seu hub IoT e estabelecer um fluxo de dispositivo. O comando seguinte obtém este valor para o seu hub IoT:

   **YourIoTHubName**: Substitua este marcador de posição abaixo com o nome que escolher para o seu hub IoT.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name YourIoTHubName
    ```

    Anote o valor retornado, o que é semelhante a este:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="communicate-between-device-and-service-via-device-streams"></a>Comunicação entre o serviço por meio de fluxos de dispositivo e dispositivo

Nesta secção, execute a aplicação do lado do dispositivo e a aplicação do lado do serviço e comunicar entre os dois.

### <a name="run-the-service-side-application"></a>Executar a aplicação do lado do serviço

Navegue para `iot-hub/Quickstarts/device-streams-echo/service` na pasta do projeto descompactado. Terá das seguintes informações úteis:

| Nome do parâmetro | Valor do parâmetro |
|----------------|-----------------|
| `ServiceConnectionString` | Indicar a cadeia de ligação de serviço do IoT Hub. |
| `DeviceId` | Forneça o ID do dispositivo que criou anteriormente, por exemplo, MyDevice. |

Compilar e executar o código da seguinte forma:

```
cd ./iot-hub/Quickstarts/device-streams-echo/service/

# Build the application
dotnet build

# Run the application
# In Linux/MacOS
dotnet run "<ServiceConnectionString>" "<MyDevice>"

# In Windows
dotnet run <ServiceConnectionString> <MyDevice>
```

> [!NOTE]
> Um tempo limite ocorre se a aplicação do lado do dispositivo não responder em tempo.

### <a name="run-the-device-side-application"></a>Executar a aplicação do lado do dispositivo

Navegue para `iot-hub/Quickstarts/device-streams-echo/device` diretório na pasta do projeto descompactado. Terá das seguintes informações úteis:

| Nome do parâmetro | Valor do parâmetro |
|----------------|-----------------|
| `DeviceConnectionString` | Indicar a cadeia de ligação do dispositivo do IoT Hub. |

Compilar e executar o código da seguinte forma:

```
cd ./iot-hub/Quickstarts/device-streams-echo/device/

# Build the application
dotnet build

# Run the application
# In Linux/MacOS
dotnet run "<DeviceConnectionString>"

# In Windows
dotnet run <DeviceConnectionString>
```

No final do último passo, o programa do lado do serviço irá iniciar um fluxo para o seu dispositivo e depois de estabelecido irá enviar um buffer de cadeia de caracteres para o serviço sobre o fluxo. Neste exemplo, o programa do lado do serviço simplesmente ecoa de volta os mesmos dados para o dispositivo, demonstrando a comunicação bidirecional bem-sucedida entre as duas aplicações. Veja a figura abaixo.

Resultado do lado do dispositivo da consola:

![Resultado da consola do lado do dispositivo](./media/quickstart-device-streams-echo-csharp/device-console-output.png)

Console de saída no lado do serviço: ![Resultado da consola no lado do serviço](./media/quickstart-device-streams-echo-csharp/service-console-output.png )

O que está a ser enviado através do fluxo de tráfego irá serem em túnel através do IoT Hub, em vez de a ser enviados diretamente. Os benefícios fornecidos estão descritos no [dispositivo transmite os benefícios](./iot-hub-device-streams-overview.md#benefits).

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, tem configurar um hub IoT, registou um dispositivo, estabelecer um fluxo de dispositivo entre C# aplicações no lado do dispositivo e o serviço e utilizado o fluxo para enviar dados entre os aplicativos.

Use os links abaixo para saber mais sobre fluxos de dispositivo:

> [!div class="nextstepaction"]
> [Descrição geral de fluxos de dispositivo](./iot-hub-device-streams-overview.md)
