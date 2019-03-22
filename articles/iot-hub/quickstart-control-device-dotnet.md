---
title: 'Guia de Início Rápido: Controlar um dispositivo a partir de um Hub IoT do Azure (.NET) | Microsoft Docs'
description: Neste guia de início rápido, irá executar duas aplicações C# de exemplo. Existe uma aplicação back-end que pode controlar remotamente dispositivos ligados ao seu hub. A outra aplicação simula um dispositivo ligado ao seu hub que pode ser controlado remotamente.
author: robinsh
manager: philmea
ms.author: robin.shahan
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc
ms.date: 02/22/2019
ms.openlocfilehash: a24f0810a5b785a57a8a255f3f762f2d2a8e6ee4
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58170824"
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub-net"></a>Início rápido: Controlar um dispositivo ligado a um hub IoT (.NET)

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

O Hub IoT é um serviço do Azure que lhe permite ingerir elevados volumes de telemetria dos seus dispositivos IoT para a cloud e gerir os seus dispositivos a partir da cloud. Neste guia de início rápido, irá utilizar um *método direto* para controlar um dispositivo simulado ligado ao seu hub IoT. Pode utilizar métodos diretos para alterar remotamente o comportamento de um dispositivo ligado ao seu hub IoT.

O guia de início rápido utiliza duas aplicações .NET pré-escritas:

* Uma aplicação de dispositivo simulado que responde aos métodos diretos chamados a partir de uma aplicação back-end. Para receber as chamadas de método direto, esta aplicação liga-se a um ponto final específico do dispositivo no seu hub IoT.

* Uma aplicação back-end que chama os métodos diretos no dispositivo simulado. Para chamar um método direto num dispositivo, esta aplicação liga-se a um ponto final do lado do serviço no seu hub IoT.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

As duas aplicações de exemplo que executa neste guia de início rápido são escritas com C#. Precisa do SDK de .NET Core 2.1.0 ou posterior no seu computador de desenvolvimento.

Pode transferir o SDK de .NET Core para múltiplas plataformas em [.NET](https://www.microsoft.com/net/download/all).

Pode verificar qual a versão atual do C# no seu computador de desenvolvimento através do seguinte comando:

```cmd/sh
dotnet --version
```

Se ainda não o fez, transfira o projeto C# de exemplo do https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip e extraia o arquivo ZIP.

## <a name="create-an-iot-hub"></a>Criar um hub IoT

Se concluiu o anterior [início rápido: Enviar telemetria a partir de um dispositivo para um hub IoT](quickstart-send-telemetry-dotnet.md), pode ignorar este passo.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registar um dispositivo

Se concluiu o anterior [início rápido: Enviar telemetria a partir de um dispositivo para um hub IoT](quickstart-send-telemetry-dotnet.md), pode ignorar este passo.

É necessário registar um dispositivo no hub IoT antes de o mesmo se poder ligar. Neste início rápido, vai utilizar o Azure Cloud Shell para registar um dispositivo simulado.

1. Execute os seguintes comandos no Azure Cloud Shell para adicionar a extensão da CLI do Hub IoT e para criar a identidade do dispositivo.

   **YourIoTHubName**: Substitua este marcador de posição abaixo com o nome que escolheu para o seu hub IoT.

   **MyDotnetDevice**: O nome do dispositivo que está a registar. Uso **MyDotnetDevice** conforme mostrado. Se escolher um nome diferente para o seu dispositivo, terá de usar esse nome ao longo deste artigo e atualizar o nome do dispositivo em amostras de aplicativos antes de executá-los.

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    az iot hub device-identity create \
      --hub-name YourIoTHubName --device-id MyDotnetDevice
    ```

2. Execute o seguinte comando no Azure Cloud Shell para obter a _cadeia de ligação do dispositivo_ que acabou de registar:

   **YourIoTHubName**: Substitua este marcador de posição abaixo com o nome que escolheu para o seu hub IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string \
      --hub-name YourIoTHubName \
      --device-id MyDotnetDevice 
      --output table
    ```

    Anote a cadeia de ligação do dispositivo, que se parece com:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Irá utilizar este valor mais adiante no guia de início rápido.

## <a name="retrieve-the-service-connection-string"></a>Obter a cadeia de ligação do serviço

Também precisa de uma _cadeia de ligação do serviço_ do seu hub IoT para permitir que a aplicação back-end se ligue ao seu hub e obtenha as mensagens. O seguinte comando obtém a cadeia de ligação do serviço do seu hub IoT:

```azurecli-interactive
az iot hub show-connection-string --name YourIoTHubName --output table
```

Anote a cadeia de ligação do serviço, que se parece com:

   `HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={YourSharedAccessKey}`

Irá utilizar este valor mais adiante no guia de início rápido. A cadeia de ligação do serviço é diferente da cadeia de ligação de dispositivo.  

## <a name="listen-for-direct-method-calls"></a>Aguardar chamadas de método direto

A aplicação de dispositivo simulado liga-se a um ponto final específico do dispositivo no seu hub IoT, envia telemetria simulada e aguarda chamadas de método direto do seu hub. Neste guia de início rápido, a chamada de método direto do hub indica ao dispositivo para alterar o intervalo em que envia telemetria. O dispositivo simulado envia uma confirmação para o seu hub depois de executar o método direto.

1. Numa janela de terminal local, navegue para a pasta raiz do projeto C# de exemplo. Em seguida, navegue para a pasta **iot-hub\Quickstarts\simulated-device-2**.

2. Abra o ficheiro **SimulatedDevice.cs** num editor de texto à sua escolha.

    Substitua o valor da variável `s_connectionString` pela cadeia de ligação do dispositivo que anotou anteriormente. Em seguida, guarde as alterações feitas ao ficheiro **SimulatedDevice.cs**.

3. Na janela de terminal local, execute os seguintes comandos para instalar os pacotes exigidos para a aplicação de dispositivo simulado:

    ```cmd/sh
    dotnet restore
    ```

4. Na janela de terminal local, execute o seguinte comando para compilar e executar a aplicação de dispositivo simulado:

    ```cmd/sh
    dotnet run
    ```

    A seguinte captura de ecrã mostra a saída à medida que a aplicação de dispositivo simulado envia telemetria para o seu hub IoT:

    ![Executar o dispositivo simulado](./media/quickstart-control-device-dotnet/SimulatedDevice-1.png)

## <a name="call-the-direct-method"></a>Chamar o método direto

A aplicação back-end liga-se a um ponto final do lado do serviço no seu Hub IoT. A aplicação faz chamadas de método direto para um dispositivo através do seu hub IoT e aguarda confirmações. Normalmente, as aplicações back-end do Hub IoT são executadas na cloud.

1. Noutra janela de terminal local, navegue para a pasta raiz do projeto C# de exemplo. Em seguida, navegue para a pasta **iot-hub\Quickstarts\back-end-application**.

2. Abra o ficheiro **BackEndApplication.cs** num editor de texto à sua escolha.

    Substitua o valor da variável `s_connectionString` pela cadeia de ligação do serviço que anotou anteriormente. Em seguida, guarde as alterações feitas ao ficheiro **BackEndApplication.cs**.

3. Na janela de terminal local, execute os seguintes comandos para instalar as bibliotecas exigidas para a aplicação back-end:

    ```cmd/sh
    dotnet restore
    ```

4. Na janela de terminal local, execute os seguintes comandos para compilar e executar a aplicação back-end:

    ```cmd/sh
    dotnet run
    ```

    A seguinte captura de ecrã mostra a saída à medida que a aplicação faz uma chamada de método direto para o dispositivo e obtém uma confirmação:

    ![Executar a aplicação back-end](./media/quickstart-control-device-dotnet/BackEndApplication.png)

    Depois de executar a aplicação back-end, verá uma mensagem na janela da consola a executar o dispositivo simulado e a velocidade à qual a aplicação envia mensagens muda:

    ![Alteração no cliente simulado](./media/quickstart-control-device-dotnet/SimulatedDevice-2.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, chamado um método direto num dispositivo a partir de uma aplicação de back-end e respondeu à chamada de método direto num aplicativo de dispositivo simulado.

Para saber como encaminhar mensagens do dispositivo para a cloud para diferentes destinos na cloud, avance para o tutorial seguinte.

> [!div class="nextstepaction"]
> [Tutorial: Telemetria de rota para diferentes pontos de extremidade para processamento](tutorial-routing.md)