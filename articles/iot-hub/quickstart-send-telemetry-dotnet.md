---
title: Quickstart - Envie telemetria para Azure IoT Hub quickstart (C#) | Microsoft Docs
description: Neste início rápido, irá executar duas aplicações C# de exemplo para enviar telemetria simulada para um hub IoT e ler telemetria do mesmo para processamento na cloud.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom:
- mvc
- mqtt
- 'Role: Cloud Development'
- devx-track-azurecli
ms.date: 06/01/2020
ms.openlocfilehash: 1a681cd3f1c624268fdc9bc62cf9841e9ac28827
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106057512"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-it-with-a-service-application-net"></a>Quickstart: Enviar telemetria de um dispositivo para um hub IoT e lê-lo com uma aplicação de serviço (.NET)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

O Hub IoT é um serviço do Azure que lhe permite ingerir elevados volumes de telemetria dos seus dispositivos IoT para a cloud para armazenamento ou processamento. Neste arranque rápido, envia telemetria de uma aplicação de dispositivo simulado, através do IoT Hub, para uma aplicação de serviço para processamento.

Este guia de início rápido utiliza duas aplicações C# pré-escritas, uma para enviar a telemetria e outra para ler a telemetria a partir do hub. Antes de executar estas duas aplicações, tem de criar um hub IoT e registar um dispositivo no hub.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* As duas aplicações de exemplo que executa neste guia de início rápido são escritas com C#. Precisa do .NET Core SDK 3.1 ou superior na sua máquina de desenvolvimento.

    Pode transferir o SDK de .NET Core para múltiplas plataformas em [.NET](https://www.microsoft.com/net/download/all).

    Pode verificar qual a versão atual do C# no seu computador de desenvolvimento através do seguinte comando:

    ```cmd/sh
    dotnet --version
    ```

    > [!NOTE]
    > Recomenda-se que .NET Core SDK 3.1 ou mais para compilar o código de serviço Do Event Hubs utilizado para ler telemetria neste arranque rápido.


* Descarregue as amostras Azure IoT C# [https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) e extraia o arquivo ZIP.

* Certifique-se de que a porta 8883 está aberta na sua firewall. A amostra do dispositivo neste arranque rápido utiliza o protocolo MQTT, que comunica sobre a porta 8883. Este porto pode ser bloqueado em alguns ambientes de rede corporativa e educacional. Para obter mais informações e formas de contornar esta questão, consulte [Connecting to IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registar um dispositivo

É necessário registar um dispositivo no hub IoT antes de o mesmo se poder ligar. Neste início rápido, vai utilizar o Azure Cloud Shell para registar um dispositivo simulado.

1. Executar o seguinte comando em Azure Cloud Shell para criar a identidade do dispositivo.

   **Seu NomeIoTHubName**: Substitua este espaço reservado abaixo pelo nome que escolheu para o seu hub IoT.

   **MyDotnetDevice:** Este é o nome do dispositivo que está a registar. Recomenda-se a utilização **do MyDotnetDevice** como mostrado. Se escolher um nome diferente para o seu dispositivo, também terá de usar esse nome ao longo deste artigo e atualizar o nome do dispositivo nas aplicações da amostra antes de executá-los.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDotnetDevice
    ```

2. Executar o seguinte comando em Azure Cloud Shell para obter a _cadeia de ligação_ do dispositivo para o dispositivo que acabou de registar:

   **Seu NomeIoTHubName**: Substitua este espaço reservado abaixo pelo nome que escolheu para o seu hub IoT.

    ```azurecli-interactive
    az iot hub device-identity connection-string show --hub-name {YourIoTHubName} --device-id MyDotnetDevice --output table
    ```

    Anote a cadeia de ligação do dispositivo, que se assemelha a:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDotnetDevice;SharedAccessKey={YourSharedAccessKey}`

    Usará este valor mais tarde no arranque rápido.

3. Também precisa do _ponto final compatível com Os Centros de Eventos,_ _caminho compatível com Os Hubs de Eventos_ e _da chave primária_ de serviço do seu hub IoT para permitir que a aplicação de serviço se conecte ao seu hub IoT e recupere as mensagens. Os seguintes comandos obtêm estes valores para o hub IoT:

   **Seu NomeIoTHubName**: Substitua este espaço reservado abaixo pelo nome que escolher para o seu hub IoT.

    ```azurecli-interactive
    az iot hub show --query properties.eventHubEndpoints.events.endpoint --name {YourIoTHubName}

    az iot hub show --query properties.eventHubEndpoints.events.path --name {YourIoTHubName}

    az iot hub policy show --name service --query primaryKey --hub-name {YourIoTHubName}
    ```

    Tome nota destes três valores, que utilizará mais tarde no arranque rápido.

## <a name="send-simulated-telemetry"></a>Enviar telemetria simulada

A aplicação de dispositivo simulado liga-se a um ponto final específico do dispositivo no seu hub IoT e envia telemetria simulada de humidade e de temperatura.

1. Numa janela de terminal local, navegue para a pasta raiz do projeto C# de exemplo. Em seguida, navegue para a pasta **iot-hub\Quickstarts\SimulatedDevice.**

2. Na janela de terminal local, execute os seguintes comandos para instalar os pacotes exigidos para a aplicação de dispositivo simulado:

    ```cmd/sh
    dotnet restore
    ```

3. Na janela do terminal local, executar o seguinte comando para construir e executar a aplicação simulada do dispositivo com a cadeia de ligação do dispositivo que fez uma nota de anterior:

    ```cmd/sh
    dotnet run -- {DeviceConnectionString}
    ```

    A seguinte captura de ecrã mostra a saída à medida que a aplicação de dispositivo simulado envia telemetria para o seu hub IoT:

    ![Executar o dispositivo simulado](media/quickstart-send-telemetry-dotnet/simulated-device.png)

## <a name="read-the-telemetry-from-your-hub"></a>Ler a telemetria a partir do seu hub

A aplicação de serviço conecta-se ao ponto final do lado do serviço **Eventos** no seu Hub IoT. A aplicação recebe as mensagens do dispositivo para a cloud enviadas a partir do seu dispositivo simulado. Uma aplicação de serviço IoT Hub normalmente corre na nuvem para receber e processar mensagens de dispositivo para nuvem.

1. Noutra janela de terminal local, navegue para a pasta raiz do projeto C# de exemplo. Em seguida, navegue para a pasta **iot-hub\Quickstarts\ReadD2cMessages.**

2. Na janela do terminal local, executar o seguinte comando para instalar as bibliotecas necessárias para a aplicação:

    ```cmd/sh
    dotnet restore
    ```

3. Na janela do terminal local, executar o seguinte comando para ver as opções de parâmetros.

    ```cmd/sh
    dotnet run
    ```

4. Na janela do terminal local, executar um dos seguintes comandos para construir e executar a aplicação com o :

    ```cmd/sh
    dotnet run -- -c {EventHubConnectionString}
    ```

    ou

    ```cmd/sh
    dotnet run -- -e {EventHubCompatibleEndpoint} -n {EventHubName} -s {SharedAccessKey}
    ```

    A imagem que se segue mostra a saída, uma vez que a aplicação de serviço recebe telemetria enviada pelo dispositivo simulado para o hub:

    ![Executar a aplicação de serviço](media/quickstart-send-telemetry-dotnet/read-device-to-cloud.png)

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, você montou um hub IoT, registou um dispositivo, enviou telemetria simulada para o hub usando uma aplicação C# e leu a telemetria do hub usando uma simples aplicação de serviço.

Para aprender a controlar o seu dispositivo simulado a partir de uma aplicação de serviço, continue até ao próximo quickstart.

> [!div class="nextstepaction"]
> [Guia de Início Rápido: controlar um dispositivo ligado a um hub IoT](quickstart-control-device-dotnet.md)
