---
title: 'Quickstart: Envie telemetria para Azure IoT (Node.js)'
description: Neste início rápido, irá executar duas aplicações Node.js de exemplo para enviar telemetria simulada para um hub IoT e ler telemetria do mesmo para processamento na cloud.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom:
- mvc
- seo-javascript-september2019
- mqtt
ms.date: 06/21/2019
ms.openlocfilehash: 24b6d2eca2eaa12e3e04647d403a015bdbf24ec6
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770035"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-it-with-a-back-end-application-nodejs"></a>Quickstart: Envie a telemetria de um dispositivo para um hub IoT e leia-a com uma aplicação de back-end (Node.js)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

 Neste arranque rápido, envia-se telemetria de uma aplicação simulada de dispositivo através do Azure IoT Hub para uma aplicação de back-end para processamento. O Hub IoT é um serviço do Azure que lhe permite ingerir elevados volumes de telemetria dos seus dispositivos IoT para a cloud para armazenamento ou processamento. Este quickstart usa duas aplicações node.js pré-escritas: uma para enviar a telemetria e outra para ler a telemetria do centro. Antes de executar estas duas aplicações, tem de criar um hub IoT e registar um dispositivo no hub.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta Azure com uma subscrição ativa. [Crie um de graça.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

* [Node.js 10+](https://nodejs.org). Se estiver a utilizar o Azure Cloud Shell, não atualize a versão instalada do Node.js. O Azure Cloud Shell já tem a versão mais recente do Node.js.

* [Um projeto de node.js de amostra.](https://github.com/Azure-Samples/azure-iot-samples-node/archive/master.zip)

* Porta 8883 aberta na sua firewall. A amostra do dispositivo neste quickstart utiliza o protocolo MQTT, que comunica através da porta 8883. Este porto pode estar bloqueado em alguns ambientes de rede corporativa e educativa. Para obter mais informações e formas de resolver este problema, consulte [A Ligação ao IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

Pode verificar qual a versão atual do Node.js no seu computador de desenvolvimento através do seguinte comando:

```cmd/sh
node --version
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

### <a name="add-azure-iot-extension"></a>Adicionar extensão Azure IoT

Execute o seguinte comando para adicionar a extensão Microsoft Azure IoT para Azure CLI à sua instância Cloud Shell. A extensão IoT adiciona comandos específicos do IoT Hub, IoT Edge e IoT Device Provisioning Service (DPS) ao Azure CLI.

```azurecli-interactive
az extension add --name azure-iot
```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registar um dispositivo

É necessário registar um dispositivo no hub IoT antes de o mesmo se poder ligar. Neste início rápido, vai utilizar o Azure Cloud Shell para registar um dispositivo simulado.

1. Executar o seguinte comando em Azure Cloud Shell para criar a identidade do dispositivo.

   **YourIoTHubName**: Substitua este espaço reservado abaixo com o nome que escolheu para o seu hub IoT.

   **MyNodeDevice**: Este é o nome do dispositivo que está a registar. É aconselhável utilizar o **MyNodeDevice** como mostrado. Se escolher um nome diferente para o seu dispositivo, também terá de usar esse nome ao longo deste artigo e atualizar o nome do dispositivo nas aplicações da amostra antes de os executar.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyNodeDevice
    ```

1. Execute o seguinte comando em Azure Cloud Shell para obter a _cadeia de ligação_ do dispositivo para o dispositivo que acabou de registar:

   **YourIoTHubName**: Substitua este espaço reservado abaixo com o nome que escolheu para o seu hub IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyNodeDevice --output table
    ```

    Anote a cadeia de ligação do dispositivo, que se parece com:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Usará este valor mais tarde no início.

1. Também precisa de uma _cadeia de ligação do serviço_ para permitir que a aplicação back-end se ligue ao seu hub IoT e obtenha as mensagens. O seguinte comando obtém a cadeia de ligação do serviço do seu hub IoT:

   **YourIoTHubName**: Substitua este espaço reservado abaixo com o nome que escolheu para o seu hub IoT.

    ```azurecli-interactive
    az iot hub show-connection-string --name {YourIoTHubName} --policy-name service --output table
    ```

    Anote a cadeia de ligação do serviço, que se parece com:

   `HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}`

    Usará este valor mais tarde no início. Esta cadeia de ligação de serviço é diferente da cadeia de ligação do dispositivo que observou no passo anterior.

## <a name="send-simulated-telemetry"></a>Enviar telemetria simulada

A aplicação de dispositivo simulado liga-se a um ponto final específico do dispositivo no seu hub IoT e envia telemetria simulada de humidade e de temperatura.

1. Abra a janela do terminal local, navegue para a pasta raiz do projeto Node.js de exemplo. Em seguida, navegue para a pasta **iot-hub\Quickstarts\simulated-device**.

1. Abra o ficheiro **SimulatedDevice.js** num editor de texto à sua escolha.

    Substitua o `connectionString` valor da variável pela cadeia de ligação do dispositivo de que fez uma nota anterior. Em seguida, guarde as alterações para **SimulatedDevice.js**.

1. Na janela do terminal local, execute os seguintes comandos para instalar as bibliotecas necessárias e execute a aplicação de dispositivo simulado:

    ```cmd/sh
    npm install
    node SimulatedDevice.js
    ```

    A seguinte captura de ecrã mostra a saída à medida que a aplicação de dispositivo simulado envia telemetria para o seu hub IoT:

    ![Executar o dispositivo simulado](media/quickstart-send-telemetry-node/SimulatedDevice.png)

## <a name="read-the-telemetry-from-your-hub"></a>Ler a telemetria a partir do seu hub

A aplicação back-end liga-se ao ponto final de **eventos** do lado do serviço no seu Hub IoT. A aplicação recebe as mensagens do dispositivo para a cloud enviadas a partir do seu dispositivo simulado. Uma aplicação back-end do Hub IoT é normalmente executada na cloud para receber e processar mensagens do dispositivo para a cloud.

1. Abra outra janela do terminal local, navegue para a pasta raiz do projeto Node.js de exemplo. Em seguida, navegue para a pasta **iot-hub\Quickstarts\read-d2c-messages**.

1. Abra o ficheiro **ReadDeviceToCloudMessages.js** num editor de texto à sua escolha.

    Substitua o `connectionString` valor da variável pela cadeia de ligação de serviço de que fez uma nota anterior. Em seguida, guarde as suas alterações para **ReadDeviceToCloudMessages.js**.

1. Na janela do terminal local, execute os seguintes comandos para instalar as bibliotecas exigidas e executar a aplicação back-end:

    ```cmd/sh
    npm install
    node ReadDeviceToCloudMessages.js
    ```

    A seguinte captura de ecrã mostra a saída à medida que a aplicação back-end recebe telemetria enviada pelo dispositivo simulado para o hub:

    ![Executar a aplicação back-end](media/quickstart-send-telemetry-node/ReadDeviceToCloud.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, você montou um hub IoT, registou um dispositivo, enviou telemetria simulada para o hub usando uma aplicação Node.js, e leu a telemetria do hub usando uma aplicação simples de back-end.

Para saber como controlar o seu dispositivo simulado a partir de uma aplicação back-end, continue para o guia de início rápido seguinte.

> [!div class="nextstepaction"]
> [Guia de Início Rápido: controlar um dispositivo ligado a um hub IoT](quickstart-control-device-node.md)
