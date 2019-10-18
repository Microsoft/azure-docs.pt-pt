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
ms.openlocfilehash: 13652b287da94adff5bdf2235900734e5908c56f
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72516652"
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub-python"></a>Guia de Início Rápido: Controlar um dispositivo ligado a um hub IoT (Python)

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

O Hub IoT é um serviço do Azure que permite gerenciar seus dispositivos IoT na nuvem e ingerir grandes volumes de telemetria de dispositivo para a nuvem para armazenamento ou processamento. Neste guia de início rápido, irá utilizar um *método direto* para controlar um dispositivo simulado ligado ao seu hub IoT. Pode utilizar métodos diretos para alterar remotamente o comportamento de um dispositivo ligado ao seu hub IoT.

O guia de início rápido utiliza duas aplicações Python pré-escritas:

* Uma aplicação de dispositivo simulado que responde aos métodos diretos chamados a partir de uma aplicação back-end. Para receber as chamadas de método direto, esta aplicação liga-se a um ponto final específico do dispositivo no seu hub IoT.

* Uma aplicação back-end que chama os métodos diretos no dispositivo simulado. Para chamar um método direto num dispositivo, esta aplicação liga-se a um ponto final do lado do serviço no seu hub IoT.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Execute o comando a seguir para adicionar a extensão de IoT Microsoft Azure para CLI do Azure à sua instância de Cloud Shell. A extensão de IOT adiciona comandos específicos do serviço de provisionamento de dispositivos IOT, IoT Edge e do Hub IoT a CLI do Azure.

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Se ainda não o fez, transfira o projeto Python de exemplo do https://github.com/Azure-Samples/azure-iot-samples-python/archive/master.zip e extraia o arquivo ZIP.

## <a name="create-an-iot-hub"></a>Criar um hub IoT

Se concluiu o anterior [Guia de Início Rápido: Enviar telemetria a partir de um dispositivo para um hub IoT](quickstart-send-telemetry-python.md), pode ignorar este passo.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registar um dispositivo

Se concluiu o anterior [Guia de Início Rápido: Enviar telemetria a partir de um dispositivo para um hub IoT](quickstart-send-telemetry-python.md), pode ignorar este passo.

É necessário registar um dispositivo no hub IoT antes de o mesmo se poder ligar. Neste início rápido, vai utilizar o Azure Cloud Shell para registar um dispositivo simulado.

1. Execute o comando a seguir em Azure Cloud Shell para criar a identidade do dispositivo.

    **Nomedoseuhubiot**: Substitua esse espaço reservado abaixo pelo nome escolhido para o Hub IOT.

    **MyPythonDevice**: esse é o nome do dispositivo que você está registrando. É recomendável usar **MyPythonDevice** conforme mostrado. Se você escolher um nome diferente para seu dispositivo, também precisará usar esse nome em todo este artigo e atualizar o nome do dispositivo nos aplicativos de exemplo antes de executá-los.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyPythonDevice
    ```

2. Execute o seguinte comando no Azure Cloud Shell para obter a _cadeia de ligação do dispositivo_ que acabou de registar:

    **Nomedoseuhubiot**: Substitua esse espaço reservado abaixo pelo nome escolhido para o Hub IOT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyPythonDevice --output table
    ```

    Anote a cadeia de ligação do dispositivo, que se parece com:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Irá utilizar este valor mais adiante no guia de início rápido.

3. Também precisa de uma _cadeia de ligação do serviço_ para permitir que a aplicação back-end se ligue ao seu hub IoT e obtenha as mensagens. O seguinte comando obtém a cadeia de ligação do serviço do seu hub IoT:

    **Nomedoseuhubiot**: Substitua esse espaço reservado abaixo pelo nome que você escolher para o Hub IOT.

    ```azurecli-interactive
    az iot hub show-connection-string \
      --policy-name service \
      --name {YourIoTHubName} \
      --output table
    ```

    Anote a cadeia de ligação do serviço, que se parece com:

   `HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}`

    Irá utilizar este valor mais adiante no guia de início rápido. Essa cadeia de conexão de serviço é diferente da cadeia de conexão do dispositivo que você anotou na etapa anterior.

## <a name="listen-for-direct-method-calls"></a>Aguardar chamadas de método direto

A aplicação de dispositivo simulado liga-se a um ponto final específico do dispositivo no seu hub IoT, envia telemetria simulada e aguarda chamadas de método direto do seu hub. Neste guia de início rápido, a chamada de método direto do hub indica ao dispositivo para alterar o intervalo em que envia telemetria. O dispositivo simulado envia uma confirmação de volta ao seu hub depois de executar o método direto.

1. Numa janela de terminal local, navegue para a pasta raiz do projeto Python de exemplo. Em seguida, navegue para a pasta **iot-hub\Quickstarts\simulated-device-2**.

1. Abra o ficheiro **SimulatedDevice.py** num editor de texto à sua escolha.

    Substitua o valor da variável `CONNECTION_STRING` pela cadeia de conexão do dispositivo anotada anteriormente. Em seguida, salve as alterações em **SimulatedDevice.py**.

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

A aplicação back-end liga-se a um ponto final do lado do serviço no seu Hub IoT. O aplicativo faz chamadas de método diretas para um dispositivo por meio do Hub IoT e ouve as confirmações. Normalmente, as aplicações back-end do Hub IoT são executadas na cloud.

1. Noutra janela de terminal local, navegue para a pasta raiz do projeto Python de exemplo. Em seguida, navegue para a pasta **iot-hub\Quickstarts\back-end-application**.

1. Abra o ficheiro **BackEndApplication.py** num editor de texto à sua escolha.

    Substitua o valor da variável `CONNECTION_STRING` pela cadeia de conexão de serviço anotada anteriormente. Em seguida, salve as alterações em **BackEndApplication.py**.

1. Na janela de terminal local, execute os seguintes comandos para instalar as bibliotecas exigidas para a aplicação de dispositivo simulado:

    ```cmd/sh
    pip install azure-iothub-service-client future
    ```

1. Na janela de terminal local, execute os seguintes comandos para executar a aplicação back-end:

    ```cmd/sh
    python BackEndApplication.py
    ```

    A captura de tela a seguir mostra a saída, pois o aplicativo faz uma chamada de método direto para o dispositivo e recebe uma confirmação:

    ![Executar a aplicação back-end](./media/quickstart-control-device-python/BackEndApplication.png)

    Depois de executar a aplicação back-end, verá uma mensagem na janela da consola a executar o dispositivo simulado e a velocidade à qual a aplicação envia mensagens muda:

    ![Alteração no cliente simulado](./media/quickstart-control-device-python/SimulatedDevice-2.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, chamou um método direto num dispositivo a partir de uma aplicação back-end e respondeu a uma chamada de método direto numa aplicação de dispositivo simulado.

Para saber como encaminhar mensagens do dispositivo para a cloud para diferentes destinos na cloud, avance para o tutorial seguinte.

> [!div class="nextstepaction"]
> [Tutorial: encaminhar telemetria para pontos finais diferentes para processamento](tutorial-routing.md)