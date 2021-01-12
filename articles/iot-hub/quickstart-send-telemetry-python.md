---
title: Guia de início rápido sobre como enviar telemetria para o Hub IoT do Azure (Python) | Microsoft Docs
description: Neste início rápido, irá executar um exemplo de aplicação Python para enviar telemetria simulada para um hub IoT e utilizar um utilitário para ler telemetria do mesmo.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: quickstart
ms.custom:
- mvc
- mqtt
- devx-track-python
- 'Role: Cloud Development'
- devx-track-azurecli
ms.date: 06/16/2020
ms.openlocfilehash: 71d91ceba48158d2abc688af572dabfc258a2e94
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98121444"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-it-with-a-back-end-application-python"></a>Quickstart: Enviar telemetria de um dispositivo para um hub IoT e lê-lo com uma aplicação de back-end (Python)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

Neste arranque rápido, envia telemetria de uma aplicação de dispositivo simulado através do Azure IoT Hub para uma aplicação de back-end para processamento. O Hub IoT é um serviço do Azure que lhe permite ingerir elevados volumes de telemetria dos seus dispositivos IoT para a cloud para armazenamento ou processamento. Este quickstart utiliza duas aplicações Python pré-escritas: uma para enviar a telemetria e outra para ler a telemetria a partir do centro. Antes de executar estas duas aplicações, tem de criar um hub IoT e registar um dispositivo no hub.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta Azure com uma subscrição ativa. [Crie um de graça.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

* [Python 3.7+](https://www.python.org/downloads/). Para outras versões de Python suportadas, consulte [as funcionalidades do dispositivo Azure IoT](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device#azure-iot-device-features).

* [Um projeto Python de amostra.](https://github.com/Azure-Samples/azure-iot-samples-python/archive/master.zip)

* Porta 8883 aberta na sua firewall. A amostra do dispositivo neste arranque rápido utiliza o protocolo MQTT, que comunica sobre a porta 8883. Este porto pode ser bloqueado em alguns ambientes de rede corporativa e educacional. Para obter mais informações e formas de contornar esta questão, consulte [Connecting to IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registar um dispositivo

É necessário registar um dispositivo no hub IoT antes de o mesmo se poder ligar. Neste início rápido, vai utilizar o Azure Cloud Shell para registar um dispositivo simulado.

1. Executar o seguinte comando em Azure Cloud Shell para criar a identidade do dispositivo.

    **Seu NomeIoTHubName**: Substitua este espaço reservado abaixo pelo nome que escolheu para o seu hub IoT.

    **MyPythonDevice:** Este é o nome do dispositivo que está a registar. Recomenda-se que use **MyPythonDevice** como mostrado. Se escolher um nome diferente para o seu dispositivo, também terá de usar esse nome ao longo deste artigo e atualizar o nome do dispositivo nas aplicações da amostra antes de executá-los.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyPythonDevice
    ```

1. Executar o seguinte comando em Azure Cloud Shell para obter a _cadeia de ligação_ do dispositivo para o dispositivo que registou:

    **Seu NomeIoTHubName**: Substitua este espaço reservado abaixo pelo nome que escolheu para o seu hub IoT.

    ```azurecli-interactive
    az iot hub device-identity connection-string show --hub-name {YourIoTHubName} --device-id MyPythonDevice --output table
    ```

    Anote a cadeia de ligação do dispositivo, que se assemelha a:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyPythonDevice;SharedAccessKey={YourSharedAccessKey}`

    Usará este valor mais tarde no arranque rápido.

1. Também precisa do _ponto final compatível com Os Centros de Eventos,_ _caminho compatível com Os Hubs de Eventos_ e _da chave primária_ de serviço do seu hub IoT para permitir que a aplicação back-end se conecte ao seu hub IoT e recupere as mensagens. Os seguintes comandos obtêm estes valores para o hub IoT:

   **Seu NomeIoTHubName**: Substitua este espaço reservado abaixo pelo nome que escolher para o seu hub IoT.

    ```azurecli-interactive
    az iot hub show --query properties.eventHubEndpoints.events.endpoint --name {YourIoTHubName}

    az iot hub show --query properties.eventHubEndpoints.events.path --name {YourIoTHubName}

    az iot hub policy show --name service --query primaryKey --hub-name {YourIoTHubName}
    ```

    Tome nota destes três valores, que utilizará mais tarde no arranque rápido.

## <a name="send-simulated-telemetry"></a>Enviar telemetria simulada

A aplicação de dispositivo simulado liga-se a um ponto final específico do dispositivo no seu hub IoT e envia telemetria simulada de humidade e de temperatura.

1. Numa janela de terminal local, navegue para a pasta raiz do projeto Python de exemplo. Em seguida, navegue para a pasta **iot-hub\Quickstarts\simulated-device**.

1. Abra o ficheiro **SimulatedDevice.py** num editor de texto à sua escolha.

    Substitua o valor da `CONNECTION_STRING` variável pela cadeia de ligação do dispositivo que fez uma nota anterior. Em seguida, guarde as suas alterações para **SimulatedDevice.py**.

1. Na janela de terminal local, execute os seguintes comandos para instalar as bibliotecas exigidas para a aplicação de dispositivo simulado:

    ```cmd/sh
    pip install azure-iot-device
    ```

1. Na janela de terminal local, execute os seguintes comandos para executar a aplicação de dispositivo simulado:

    ```cmd/sh
    python SimulatedDevice.py
    ```

    A seguinte captura de ecrã mostra a saída à medida que a aplicação de dispositivo simulado envia telemetria para o seu hub IoT:

    ![Executar o dispositivo simulado](media/quickstart-send-telemetry-python/simulated-device.png)

## <a name="read-the-telemetry-from-your-hub"></a>Ler a telemetria a partir do seu hub

A aplicação back-end liga-se ao ponto final de **eventos** do lado do serviço no seu Hub IoT. A aplicação recebe as mensagens do dispositivo para a cloud enviadas a partir do seu dispositivo simulado. Uma aplicação back-end do Hub IoT é normalmente executada na cloud para receber e processar mensagens do dispositivo para a cloud.

> [!NOTE]
> Os seguintes passos utilizem a amostra sincronizada, **read_device_to_cloud_messages_sync.py**. Pode executar os mesmos passos com a amostra assíncronea, **read_device_to_cloud_messages_async.py**.

1. Noutra janela de terminal local, navegue para a pasta raiz do projeto Python de exemplo. Em seguida, navegue para a pasta **iot-hub\Quickstarts\read-d2c-messages**.

2. Abra o ficheiro **read_device_to_cloud_messages_sync.py** num editor de texto à sua escolha. Atualize as seguintes variáveis e guarde as alterações no ficheiro.

    | Variável | Valor |
    | -------- | ----------- |
    | `EVENTHUB_COMPATIBLE_ENDPOINT` | Substitua o valor da variável pelo ponto final compatível com Os Centros de Eventos que fez uma nota anterior. |
    | `EVENTHUB_COMPATIBLE_PATH`     | Substitua o valor da variável pelo caminho compatível com Os Centros de Eventos que fez uma nota anterior. |
    | `IOTHUB_SAS_KEY`                | Substitua o valor da variável pela chave primária de serviço que fez uma nota de anterior. |

3. Na janela de terminal local, execute os seguintes comandos para instalar as bibliotecas exigidas para a aplicação back-end:

    ```cmd/sh
    pip install azure-eventhub
    ```

4. Na janela de terminal local, execute os seguintes comandos para compilar e executar a aplicação back-end:

    ```cmd/sh
    python read_device_to_cloud_messages_sync.py
    ```

    A seguinte captura de ecrã mostra a saída à medida que a aplicação back-end recebe telemetria enviada pelo dispositivo simulado para o hub:

    ![Executar a aplicação back-end](media/quickstart-send-telemetry-python/read-device-to-cloud.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, você montou um hub IoT, registou um dispositivo, enviou telemetria simulada para o hub usando uma aplicação Python, e leu a telemetria do hub usando uma simples aplicação de back-end.

Para saber como controlar o seu dispositivo simulado a partir de uma aplicação back-end, continue para o guia de início rápido seguinte.

> [!div class="nextstepaction"]
> [Guia de Início Rápido: controlar um dispositivo ligado a um hub IoT](quickstart-control-device-python.md)
