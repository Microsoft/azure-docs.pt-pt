---
title: 'Guia de Início Rápido: Controlar um dispositivo a partir de um Hub IoT do Azure (.NET) | Microsoft Docs'
description: Neste guia de início rápido, irá executar duas aplicações C# de exemplo. Existe uma aplicação back-end que pode controlar remotamente dispositivos ligados ao seu hub. A outra aplicação simula um dispositivo ligado ao seu hub que pode ser controlado remotamente.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc
ms.date: 06/21/2019
ms.openlocfilehash: 3e01f02026bfd788c398e580a78812d1a69f8f1d
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/09/2020
ms.locfileid: "77108943"
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub-net"></a>Guia de Início Rápido: Controlar um dispositivo ligado a um hub IoT (.NET)

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

O IoT Hub é um serviço Azure que lhe permite gerir os seus dispositivos IoT a partir da nuvem, e ingerir grandes volumes de telemetria do dispositivo para a nuvem para armazenamento ou processamento. Neste guia de início rápido, irá utilizar um *método direto* para controlar um dispositivo simulado ligado ao seu hub IoT. Pode utilizar métodos diretos para alterar remotamente o comportamento de um dispositivo ligado ao seu hub IoT.

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

Execute o seguinte comando para adicionar a extensão Microsoft Azure IoT para Azure CLI à sua instância Cloud Shell. A extensão IOT adiciona comandos específicos do IoT Hub, IoT Edge e IoT Device Provisioning Service (DPS) ao Azure CLI.

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Se ainda não o fez, baixe as amostras C# Do Azure IoT de https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip e extrai o arquivo ZIP.

Certifique-se de que a porta 8883 está aberta na sua firewall. A amostra do dispositivo neste quickstart utiliza o protocolo MQTT, que comunica através da porta 8883. Este porto pode estar bloqueado em alguns ambientes de rede corporativa e educativa. Para obter mais informações e formas de resolver este problema, consulte [A Ligação ao IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Criar um hub IoT

Se concluiu o anterior [Guia de Início Rápido: Enviar telemetria a partir de um dispositivo para um hub IoT](quickstart-send-telemetry-dotnet.md), pode ignorar este passo.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registar um dispositivo

Se concluiu o anterior [Guia de Início Rápido: Enviar telemetria a partir de um dispositivo para um hub IoT](quickstart-send-telemetry-dotnet.md), pode ignorar este passo.

É necessário registar um dispositivo no hub IoT antes de o mesmo se poder ligar. Neste início rápido, vai utilizar o Azure Cloud Shell para registar um dispositivo simulado.

1. Executar o seguinte comando em Azure Cloud Shell para criar a identidade do dispositivo.

   **YourIoTHubName**: Substitua este espaço reservado abaixo com o nome que escolheu para o seu hub IoT.

   **MyDotnetDevice**: Este é o nome do dispositivo que está a registar. É aconselhável utilizar o **MyDotnetDevice** como mostrado. Se escolher um nome diferente para o seu dispositivo, também precisa de usar esse nome ao longo deste artigo e atualizar o nome do dispositivo nas aplicações da amostra antes de os executar.

    ```azurecli-interactive
    az iot hub device-identity create \
      --hub-name {YourIoTHubName} --device-id MyDotnetDevice
    ```

2. Execute o seguinte comando no Azure Cloud Shell para obter a _cadeia de ligação do dispositivo_ que acabou de registar:

   **YourIoTHubName**: Substitua este espaço reservado abaixo com o nome que escolheu para o seu hub IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string \
      --hub-name {YourIoTHubName} \
      --device-id MyDotnetDevice \
      --output table
    ```

    Anote a cadeia de ligação do dispositivo, que se parece com:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Irá utilizar este valor mais adiante no guia de início rápido.

## <a name="retrieve-the-service-connection-string"></a>Obter a cadeia de ligação do serviço

Também precisa de uma _cadeia de ligação do serviço_ do seu hub IoT para permitir que a aplicação back-end se ligue ao seu hub e obtenha as mensagens. O seguinte comando obtém a cadeia de ligação do serviço do seu hub IoT:

```azurecli-interactive
az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
```

Anote a cadeia de ligação do serviço, que se parece com:

   `HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}`

Irá utilizar este valor mais adiante no guia de início rápido. Esta cadeia de ligação de serviço é diferente da cadeia de ligação do dispositivo que observou no passo anterior.

## <a name="listen-for-direct-method-calls"></a>Aguardar chamadas de método direto

A aplicação de dispositivo simulado liga-se a um ponto final específico do dispositivo no seu hub IoT, envia telemetria simulada e aguarda chamadas de método direto do seu hub. Neste guia de início rápido, a chamada de método direto do hub indica ao dispositivo para alterar o intervalo em que envia telemetria. O dispositivo simulado envia um reconhecimento de volta para o seu hub depois de executar o método direto.

1. Numa janela de terminal local, navegue para a pasta raiz do projeto C# de exemplo. Em seguida, navegue para a pasta **iot-hub\Quickstarts\simulated-device-2**.

2. Abra o ficheiro **SimulatedDevice.cs** num editor de texto à sua escolha.

    Substitua o valor da variável `s_connectionString` com a cadeia de ligação do dispositivo de que fez uma nota anterior. Em seguida, guarde as suas alterações para **SimulatedDevice.cs**.

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

A aplicação back-end liga-se a um ponto final do lado do serviço no seu Hub IoT. A aplicação faz chamadas de método sinuoso para um dispositivo através do seu hub IoT e ouve reconhecimentos. Normalmente, as aplicações back-end do Hub IoT são executadas na cloud.

1. Noutra janela de terminal local, navegue para a pasta raiz do projeto C# de exemplo. Em seguida, navegue para a pasta **iot-hub\Quickstarts\back-end-application**.

2. Abra o ficheiro **BackEndApplication.cs** num editor de texto à sua escolha.

    Substitua o valor da variável `s_connectionString` com a cadeia de ligação de serviço de que fez uma nota anterior. Em seguida, guarde as suas alterações para **BackEndApplication.cs**.

3. Na janela de terminal local, execute os seguintes comandos para instalar as bibliotecas exigidas para a aplicação back-end:

    ```cmd/sh
    dotnet restore
    ```

4. Na janela de terminal local, execute os seguintes comandos para compilar e executar a aplicação back-end:

    ```cmd/sh
    dotnet run
    ```

    A imagem seguinte mostra a saída à medida que a aplicação faz uma chamada de método direto para o dispositivo e recebe um reconhecimento:

    ![Executar a aplicação back-end](./media/quickstart-control-device-dotnet/BackEndApplication.png)

    Depois de executar a aplicação back-end, verá uma mensagem na janela da consola a executar o dispositivo simulado e a velocidade à qual a aplicação envia mensagens muda:

    ![Alteração no cliente simulado](./media/quickstart-control-device-dotnet/SimulatedDevice-2.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, chamou um método direto num dispositivo a partir de uma aplicação de back-end, e respondeu à chamada do método direto numa aplicação simulada do dispositivo.

Para saber como encaminhar mensagens do dispositivo para a cloud para diferentes destinos na cloud, avance para o tutorial seguinte.

> [!div class="nextstepaction"]
> [Tutorial: encaminhar telemetria para pontos finais diferentes para processamento](tutorial-routing.md)
