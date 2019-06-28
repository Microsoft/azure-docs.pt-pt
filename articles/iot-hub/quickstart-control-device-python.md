---
title: 'Guia de Início Rápido: Controlar um dispositivo a partir de um Hub IoT do Azure (Python) | Microsoft Docs'
description: Neste guia de início rápido, irá executar duas aplicações Python de exemplo. Existe uma aplicação back-end que pode controlar remotamente dispositivos ligados ao seu hub. A outra aplicação simula um dispositivo ligado ao seu hub que pode ser controlado remotamente.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: quickstart
ms.custom: mvc
ms.date: 06/21/2019
ms.openlocfilehash: 9c9b892f7d1f992210a18f2290bb199d12aeaf49
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/22/2019
ms.locfileid: "67330501"
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub-python"></a>Início rápido: Controlar um dispositivo ligado a um IoT hub (Python)

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

O Hub IoT é um serviço do Azure que lhe permite ingerir elevados volumes de telemetria dos seus dispositivos IoT para a cloud e gerir os seus dispositivos a partir da cloud. Neste guia de início rápido, irá utilizar um *método direto* para controlar um dispositivo simulado ligado ao seu hub IoT. Pode utilizar métodos diretos para alterar remotamente o comportamento de um dispositivo ligado ao seu hub IoT.

O guia de início rápido utiliza duas aplicações Python pré-escritas:

* Uma aplicação de dispositivo simulado que responde aos métodos diretos chamados a partir de uma aplicação back-end. Para receber as chamadas de método direto, esta aplicação liga-se a um ponto final específico do dispositivo no seu hub IoT.

* Uma aplicação back-end que chama os métodos diretos no dispositivo simulado. Para chamar um método direto num dispositivo, esta aplicação liga-se a um ponto final do lado do serviço no seu hub IoT.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

As duas aplicações de exemplo que executa neste guia de início rápido são escritas com Python. Atualmente, os SDKs IoT do Microsoft Azure para Python suporta apenas as versões específicas do Python para cada plataforma. Para obter mais informações, consulte a [Leiame do SDK de Python](https://github.com/Azure/azure-iot-sdk-python#important-installation-notes---dealing-with-importerror-issues).

Este guia de introdução pressupõe que está a utilizar uma máquina de desenvolvimento do Windows. Para sistemas Windows, só [Python 3.6.x](https://www.python.org/downloads/release/python-368/) é suportada. O instalador do Python que escolher deve basear-se na arquitetura do sistema com o qual está a trabalhar. Se o seu sistema de arquitetura de CPU é de 32 bits, em seguida, instalador de download x86; para a arquitetura de 64 bits, transfira o instalador de x86 64. Além disso, certifique-se de que o [Microsoft Visual C++ Redistributable para Visual Studio 2015, 2017 e 2019](https://support.microsoft.com/en-us/help/2977003/the-latest-supported-visual-c-downloads) está instalado para a sua arquitetura (x86 ou x64).

Pode transferir o Python para outras plataformas partir [Python.org](https://www.python.org/downloads/).

Pode verificar qual a versão atual do Python no seu computador de desenvolvimento através de um dos seguintes comandos:

```python
python --version
```

```python
python3 --version
```

Execute o seguinte comando para adicionar a extensão de IoT do Microsoft Azure para a CLI do Azure à sua instância do Cloud Shell. A extensão de IOT adiciona comandos específicos de IoT Hub, o IoT Edge e o serviço aprovisionamento de dispositivos IoT (DPS) para a CLI do Azure.

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Se ainda não o fez, transfira o projeto Python de exemplo do https://github.com/Azure-Samples/azure-iot-samples-python/archive/master.zip e extraia o arquivo ZIP.

## <a name="create-an-iot-hub"></a>Criar um hub IoT

Se concluiu o anterior [início rápido: Enviar telemetria a partir de um dispositivo para um hub IoT](quickstart-send-telemetry-python.md), pode ignorar este passo.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registar um dispositivo

Se concluiu o anterior [início rápido: Enviar telemetria a partir de um dispositivo para um hub IoT](quickstart-send-telemetry-python.md), pode ignorar este passo.

É necessário registar um dispositivo no hub IoT antes de o mesmo se poder ligar. Neste início rápido, vai utilizar o Azure Cloud Shell para registar um dispositivo simulado.

1. Execute o seguinte comando no Azure Cloud Shell para criar a identidade de dispositivo.

    **YourIoTHubName** : Substitua este marcador de posição abaixo com o nome que escolheu para o seu hub IoT.

    **MyPythonDevice** : Este é o nome fornecido para o dispositivo registado. Utilize MyPythonDevice, conforme mostrado. Se escolher um nome diferente para o seu dispositivo, também irá precisar de utilizar esse nome através deste artigo, e atualize o nome do dispositivo em aplicações de exemplo antes de as executar.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyPythonDevice
    ```

2. Execute o seguinte comando no Azure Cloud Shell para obter a _cadeia de ligação do dispositivo_ que acabou de registar:

    **YourIoTHubName** : Substitua este marcador de posição abaixo com o nome que escolheu para o seu hub IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyPythonDevice --output table
    ```

    Anote a cadeia de ligação do dispositivo, que se parece com:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Irá utilizar este valor mais adiante no guia de início rápido.

3. Também precisa de uma _cadeia de ligação do serviço_ para permitir que a aplicação back-end se ligue ao seu hub IoT e obtenha as mensagens. O seguinte comando obtém a cadeia de ligação do serviço do seu hub IoT:

    **YourIoTHubName** : Substitua este marcador de posição abaixo com o nome que escolher para o seu hub IoT.

    ```azurecli-interactive
    az iot hub show-connection-string \
      --name YourIoTHubName \
      --policy-name service \
      --output table
    ```

    Anote a cadeia de ligação do serviço, que se parece com:

   `HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}`

    Irá utilizar este valor mais adiante no guia de início rápido. A cadeia de ligação do serviço é diferente da cadeia de ligação de dispositivo.

## <a name="listen-for-direct-method-calls"></a>Aguardar chamadas de método direto

A aplicação de dispositivo simulado liga-se a um ponto final específico do dispositivo no seu hub IoT, envia telemetria simulada e aguarda chamadas de método direto do seu hub. Neste guia de início rápido, a chamada de método direto do hub indica ao dispositivo para alterar o intervalo em que envia telemetria. O dispositivo simulado envia uma confirmação para o seu hub depois de executar o método direto.

1. Numa janela de terminal local, navegue para a pasta raiz do projeto Python de exemplo. Em seguida, navegue para a pasta **iot-hub\Quickstarts\simulated-device-2**.

1. Abra o ficheiro **SimulatedDevice.py** num editor de texto à sua escolha.

    Substitua o valor da variável `CONNECTION_STRING` pela cadeia de ligação do dispositivo que anotou anteriormente. Em seguida, guarde as alterações feitas ao ficheiro **SimulatedDevice.py**.

1. Na janela de terminal local, execute os seguintes comandos para instalar as bibliotecas exigidas para a aplicação de dispositivo simulado:

    ```cmd/sh
    pip install azure-iothub-device-client
    ```

1. Na janela de terminal local, execute os seguintes comandos para executar a aplicação de dispositivo simulado:

    ```cmd/sh
    python SimulatedDevice.py
    ```

    A seguinte captura de ecrã mostra a saída à medida que a aplicação de dispositivo simulado envia telemetria para o seu hub IoT:

    ![Executar o dispositivo simulado](./media/quickstart-control-device-python/SimulatedDevice-1.png)

## <a name="call-the-direct-method"></a>Chamar o método direto

A aplicação back-end liga-se a um ponto final do lado do serviço no seu Hub IoT. A aplicação faz chamadas de método direto para um dispositivo através do seu hub IoT e aguarda confirmações. Normalmente, as aplicações back-end do Hub IoT são executadas na cloud.

1. Noutra janela de terminal local, navegue para a pasta raiz do projeto Python de exemplo. Em seguida, navegue para a pasta **iot-hub\Quickstarts\back-end-application**.

1. Abra o ficheiro **BackEndApplication.py** num editor de texto à sua escolha.

    Substitua o valor da variável `CONNECTION_STRING` pela cadeia de ligação do serviço que anotou anteriormente. Em seguida, guarde as alterações feitas ao ficheiro **BackEndApplication.py**.

1. Na janela de terminal local, execute os seguintes comandos para instalar as bibliotecas exigidas para a aplicação de dispositivo simulado:

    ```cmd/sh
    pip install azure-iothub-service-client future
    ```

1. Na janela de terminal local, execute os seguintes comandos para executar a aplicação back-end:

    ```cmd/sh
    python BackEndApplication.py
    ```

    A seguinte captura de ecrã mostra a saída à medida que a aplicação faz uma chamada de método direto para o dispositivo e obtém uma confirmação:

    ![Executar a aplicação back-end](./media/quickstart-control-device-python/BackEndApplication.png)

    Depois de executar a aplicação back-end, verá uma mensagem na janela da consola a executar o dispositivo simulado e a velocidade à qual a aplicação envia mensagens muda:

    ![Alteração no cliente simulado](./media/quickstart-control-device-python/SimulatedDevice-2.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Passos Seguintes

Neste guia de início rápido, chamou um método direto num dispositivo a partir de uma aplicação back-end e respondeu a uma chamada de método direto numa aplicação de dispositivo simulado.

Para saber como encaminhar mensagens do dispositivo para a cloud para diferentes destinos na cloud, avance para o tutorial seguinte.

> [!div class="nextstepaction"]
> [Tutorial: Telemetria de rota para diferentes pontos de extremidade para processamento](tutorial-routing.md)