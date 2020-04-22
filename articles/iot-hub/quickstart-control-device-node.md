---
title: 'Quickstart: Controle um dispositivo a partir de Azure IoT (Node.js)'
description: Neste guia de início rápido, irá executar duas aplicações Node.js de exemplo. Existe uma aplicação back-end que pode controlar remotamente dispositivos ligados ao seu hub. A outra aplicação simula um dispositivo ligado ao seu hub que pode ser controlado remotamente.
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
- seo-javascript-october2019
- mqtt
ms.date: 06/21/2019
ms.openlocfilehash: 39d136815e3808c907e190773b9303075a5093e6
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81769365"
---
# <a name="quickstart-use-nodejs-to-control-a-device-connected-to-an-azure-iot-hub"></a>Quickstart: Use Node.js para controlar um dispositivo ligado a um hub Azure IoT

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

Neste arranque rápido, utiliza-se um método direto para controlar um dispositivo simulado ligado ao Hub Azure IoT. O IoT Hub é um serviço Azure que lhe permite gerir os seus dispositivos IoT a partir da nuvem, e ingerir grandes volumes de telemetria do dispositivo para a nuvem para armazenamento ou processamento. Pode utilizar métodos diretos para alterar remotamente o comportamento de um dispositivo ligado ao seu hub IoT. Este quickstart utiliza duas aplicações Node.js: uma aplicação simulada do dispositivo que responde a métodos diretos chamados a partir de uma aplicação back-end e uma aplicação back-end que chama os métodos diretos no dispositivo simulado.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta Azure com uma subscrição ativa. [Crie um de graça.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

* [Node.js 10+](https://nodejs.org).

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

Se concluiu o anterior [Guia de Início Rápido: Enviar telemetria a partir de um dispositivo para um hub IoT](quickstart-send-telemetry-node.md), pode ignorar este passo.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registar um dispositivo

Se concluiu o anterior [Guia de Início Rápido: Enviar telemetria a partir de um dispositivo para um hub IoT](quickstart-send-telemetry-node.md), pode ignorar este passo.

É necessário registar um dispositivo no hub IoT antes de o mesmo se poder ligar. Neste início rápido, vai utilizar o Azure Cloud Shell para registar um dispositivo simulado.

1. Executar o seguinte comando em Azure Cloud Shell para criar a identidade do dispositivo.

   **YourIoTHubName**: Substitua este espaço reservado abaixo com o nome que escolheu para o seu hub IoT.

   **MyNodeDevice**: Este é o nome do dispositivo que está a registar. É aconselhável utilizar o **MyNodeDevice** como mostrado. Se escolher um nome diferente para o seu dispositivo, também precisa de usar esse nome ao longo deste artigo e atualizar o nome do dispositivo nas aplicações da amostra antes de os executar.

    ```azurecli-interactive
    az iot hub device-identity create \
      --hub-name {YourIoTHubName} --device-id MyNodeDevice
    ```

2. Execute o seguinte comando no Azure Cloud Shell para obter a _cadeia de ligação do dispositivo_ que acabou de registar:

    **YourIoTHubName**: Substitua este espaço reservado abaixo com o nome que escolheu para o seu hub IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string \
      --hub-name {YourIoTHubName} \
      --device-id MyNodeDevice \
      --output table
    ```

    Anote a cadeia de ligação do dispositivo, que se parece com:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Irá utilizar este valor mais adiante no guia de início rápido.

3. Também precisa de uma _cadeia de ligação do serviço_ para permitir que a aplicação back-end se ligue ao seu hub IoT e obtenha as mensagens. O seguinte comando obtém a cadeia de ligação do serviço do seu hub IoT:

    **YourIoTHubName**: Substitua este espaço reservado abaixo com o nome que escolheu para o seu hub IoT.

    ```azurecli-interactive
    az iot hub show-connection-string \
      --policy-name service --name {YourIoTHubName} --output table

    ```

    Anote a cadeia de ligação do serviço, que se parece com:

   `HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}`

    Irá utilizar este valor mais adiante no guia de início rápido. Esta cadeia de ligação de serviço é diferente da cadeia de ligação do dispositivo que observou no passo anterior.

## <a name="listen-for-direct-method-calls"></a>Aguardar chamadas de método direto

A aplicação de dispositivo simulado liga-se a um ponto final específico do dispositivo no seu hub IoT, envia telemetria simulada e aguarda chamadas de método direto do seu hub. Neste guia de início rápido, a chamada de método direto do hub indica ao dispositivo para alterar o intervalo em que envia telemetria. O dispositivo simulado envia um reconhecimento de volta para o seu hub depois de executar o método direto.

1. Na janela do terminal local, navegue para a pasta raiz do projeto Node.js de exemplo. Em seguida, navegue para a pasta **iot-hub\Quickstarts\simulated-device-2**.

2. Abra o ficheiro **SimulatedDevice.js** num editor de texto à sua escolha.

    Substitua o `connectionString` valor da variável pela cadeia de ligação do dispositivo de que fez uma nota anterior. Em seguida, guarde as alterações para **SimulatedDevice.js**.

3. Na janela do terminal local, execute os seguintes comandos para instalar as bibliotecas necessárias e execute a aplicação de dispositivo simulado:

    ```cmd/sh
    npm install
    node SimulatedDevice.js
    ```

    A seguinte captura de ecrã mostra a saída à medida que a aplicação de dispositivo simulado envia telemetria para o seu hub IoT:

    ![Executar o dispositivo simulado](./media/quickstart-control-device-node/simulated-device-telemetry-iot-hub.png)

## <a name="call-the-direct-method"></a>Chamar o método direto

A aplicação back-end liga-se a um ponto final do lado do serviço no seu Hub IoT. A aplicação faz chamadas de método sinuoso para um dispositivo através do seu hub IoT e ouve reconhecimentos. Normalmente, as aplicações back-end do Hub IoT são executadas na cloud.

1. Noutra janela do terminal local, navegue para a pasta raiz do projeto Node.js de exemplo. Em seguida, navegue para a pasta **iot-hub\Quickstarts\back-end-application**.

2. Abra o ficheiro **BackEndApplication.js** num editor de texto à sua escolha.

    Substitua o `connectionString` valor da variável pela cadeia de ligação de serviço de que fez uma nota anterior. Em seguida, guarde as suas alterações para **BackEndApplication.js**.

3. Na janela do terminal local, execute os seguintes comandos para instalar as bibliotecas exigidas e executar a aplicação back-end:

    ```cmd/sh
    npm install
    node BackEndApplication.js
    ```

    A imagem seguinte mostra a saída à medida que a aplicação faz uma chamada de método direto para o dispositivo e recebe um reconhecimento:

    ![Saída quando a aplicação faz chamada de método direto para o dispositivo](./media/quickstart-control-device-node/direct-method-device-call.png)

    Depois de executar a aplicação back-end, verá uma mensagem na janela da consola a executar o dispositivo simulado e a velocidade à qual a aplicação envia mensagens muda:

    ![Saída quando há uma mudança no cliente simulado](./media/quickstart-control-device-node/simulated-device-message-change.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, chamou um método direto num dispositivo a partir de uma aplicação de back-end, e respondeu à chamada do método direto numa aplicação simulada do dispositivo.

Para saber como encaminhar mensagens do dispositivo para a cloud para diferentes destinos na cloud, avance para o tutorial seguinte.

> [!div class="nextstepaction"]
> [Tutorial: encaminhar telemetria para pontos finais diferentes para processamento](tutorial-routing.md)
