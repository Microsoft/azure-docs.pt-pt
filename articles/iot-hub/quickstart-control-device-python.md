---
title: Quickstart - Controle um dispositivo a partir de Azure IoT Hub quickstart (Python) | Microsoft Docs
description: Neste guia de início rápido, irá executar duas aplicações Python de exemplo. Existe uma aplicação back-end que pode controlar remotamente dispositivos ligados ao seu hub. A outra aplicação simula um dispositivo ligado ao seu hub que pode ser controlado remotamente.
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
- devx-track-azurecli
ms.date: 09/14/2020
ms.openlocfilehash: 2ecc1ceb624f2f98bca47d4d1331f67c52fa54b4
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566916"
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub-python"></a>Guia de Início Rápido: Controlar um dispositivo ligado a um hub IoT (Python)

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

Neste arranque rápido, utiliza-se um método direto para controlar um dispositivo simulado ligado ao Azure IoT Hub. O IoT Hub é um serviço Azure que lhe permite gerir os seus dispositivos IoT a partir da nuvem e ingerir grandes volumes de telemetria do dispositivo para a nuvem para armazenamento ou processamento. Pode utilizar métodos diretos para alterar remotamente o comportamento de um dispositivo ligado ao seu hub IoT. Este quickstart utiliza duas aplicações Python: uma aplicação simulada de dispositivo que responde a métodos diretos chamados a partir de uma aplicação de back-end e uma aplicação back-end que chama os métodos diretos no dispositivo simulado.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta Azure com uma subscrição ativa. [Crie um de graça.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

* [Python 3.7+](https://www.python.org/downloads/). Para outras versões de Python suportadas, consulte [as funcionalidades do dispositivo Azure IoT](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device#azure-iot-device-features).

* [Um projeto python](https://github.com/Azure-Samples/azure-iot-samples-python/) de amostra de Github. Faça o download ou clone das amostras utilizando o botão **Código** no repositório de Github.

* Porta 8883 aberta na sua firewall. A amostra do dispositivo neste arranque rápido utiliza o protocolo MQTT, que comunica sobre a porta 8883. Este porto pode ser bloqueado em alguns ambientes de rede corporativa e educacional. Para obter mais informações e formas de contornar esta questão, consulte [Connecting to IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Criar um hub IoT

Se concluiu o anterior [Guia de Início Rápido: Enviar telemetria a partir de um dispositivo para um hub IoT](quickstart-send-telemetry-python.md), pode ignorar este passo.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registar um dispositivo

Se concluiu o anterior [Guia de Início Rápido: Enviar telemetria a partir de um dispositivo para um hub IoT](quickstart-send-telemetry-python.md), pode ignorar este passo.

É necessário registar um dispositivo no hub IoT antes de o mesmo se poder ligar. Neste início rápido, vai utilizar o Azure Cloud Shell para registar um dispositivo simulado.

1. Executar o seguinte comando em Azure Cloud Shell para criar a identidade do dispositivo.

    **Seu NomeIoTHubName**: Substitua este espaço reservado abaixo pelo nome que escolheu para o seu hub IoT.

    **MyPythonDevice:** Este é o nome do dispositivo que está a registar. Recomenda-se que use **MyPythonDevice** como mostrado. Se escolher um nome diferente para o seu dispositivo, também precisa de usar esse nome ao longo deste artigo e atualizar o nome do dispositivo nas aplicações da amostra antes de executá-los.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyPythonDevice
    ```

2. Execute o seguinte comando no Azure Cloud Shell para obter a _cadeia de ligação do dispositivo_ que acabou de registar:

    **Seu NomeIoTHubName**: Substitua este espaço reservado abaixo pelo nome que escolheu para o seu hub IoT.

    ```azurecli-interactive
    az iot hub device-identity connection-string show --hub-name {YourIoTHubName} --device-id MyPythonDevice --output table
    ```

    Anote a cadeia de ligação do dispositivo, que se assemelha a:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Irá utilizar este valor mais adiante no guia de início rápido.

3. Também precisa de uma _cadeia de ligação do serviço_ para permitir que a aplicação back-end se ligue ao seu hub IoT e obtenha as mensagens. O seguinte comando obtém a cadeia de ligação do serviço do seu hub IoT:

    **Seu NomeIoTHubName**: Substitua este espaço reservado abaixo pelo nome que escolher para o seu hub IoT.

    ```azurecli-interactive
    az iot hub connection-string show \
      --policy-name service \
      --name {YourIoTHubName} \
      --output table
    ```

    Anote a cadeia de ligação do serviço, que se parece com:

   `HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}`

    Irá utilizar este valor mais adiante no guia de início rápido. Esta cadeia de ligação de serviço é diferente da cadeia de ligação do dispositivo que notou no passo anterior.

## <a name="listen-for-direct-method-calls"></a>Aguardar chamadas de método direto

A aplicação de dispositivo simulado liga-se a um ponto final específico do dispositivo no seu hub IoT, envia telemetria simulada e aguarda chamadas de método direto do seu hub. Neste guia de início rápido, a chamada de método direto do hub indica ao dispositivo para alterar o intervalo em que envia telemetria. O dispositivo simulado envia um reconhecimento de volta ao seu centro depois de executar o método direto.

1. Numa janela de terminal local, navegue para a pasta raiz do projeto Python de exemplo. Em seguida, navegue para a pasta **iot-hub\Quickstarts\simulated-device-2**.

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

    ![Executar o dispositivo simulado](./media/quickstart-control-device-python/simulated-device-1.png)

## <a name="call-the-direct-method"></a>Chamar o método direto

A aplicação back-end liga-se a um ponto final do lado do serviço no seu Hub IoT. A aplicação faz chamadas de método direto para um dispositivo através do seu hub IoT e ouve para reconhecimentos. Normalmente, as aplicações back-end do Hub IoT são executadas na cloud.

1. Noutra janela de terminal local, navegue para a pasta raiz do projeto Python de exemplo. Em seguida, navegue para a pasta **iot-hub\Quickstarts\back-end-application**.

1. Abra o ficheiro **BackEndApplication.py** num editor de texto à sua escolha.

    Substitua o valor da `CONNECTION_STRING` variável pela cadeia de ligação de serviço que fez uma nota anterior. Em seguida, guarde as suas alterações para **BackEndApplication.py**.

1. Na janela de terminal local, execute os seguintes comandos para instalar as bibliotecas exigidas para a aplicação de dispositivo simulado:

    ```cmd/sh
    pip install azure-iot-hub
    ```

1. Na janela de terminal local, execute os seguintes comandos para executar a aplicação back-end:

    ```cmd/sh
    python BackEndApplication.py
    ```

    A imagem que se segue mostra a saída à medida que a aplicação faz uma chamada de método direto para o dispositivo e recebe um reconhecimento:

    ![Executar a aplicação back-end](./media/quickstart-control-device-python/backend-application.png)

    Depois de executar a aplicação back-end, verá uma mensagem na janela da consola a executar o dispositivo simulado e a velocidade à qual a aplicação envia mensagens muda:

    ![Alteração no cliente simulado](./media/quickstart-control-device-python/simulated-device-2.png)

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, chamou um método direto num dispositivo a partir de uma aplicação back-end e respondeu a uma chamada de método direto numa aplicação de dispositivo simulado.

Para saber como encaminhar mensagens do dispositivo para a cloud para diferentes destinos na cloud, avance para o tutorial seguinte.

> [!div class="nextstepaction"]
> [Tutorial: encaminhar telemetria para pontos finais diferentes para processamento](tutorial-routing.md)
