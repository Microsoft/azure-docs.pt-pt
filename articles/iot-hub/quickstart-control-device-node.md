---
title: 'Início rápido: controlar um dispositivo do Azure IoT (Node. js)'
description: Neste guia de início rápido, irá executar duas aplicações Node.js de exemplo. Existe uma aplicação back-end que pode controlar remotamente dispositivos ligados ao seu hub. A outra aplicação simula um dispositivo ligado ao seu hub que pode ser controlado remotamente.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019
ms.date: 06/21/2019
ms.openlocfilehash: ed3946c40d8e3eff665e4454b81f408c2033c09a
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72529605"
---
# <a name="quickstart-use-nodejs-to-control-a-device-connected-to-an-azure-iot-hub"></a>Início rápido: usar o Node. js para controlar um dispositivo conectado a um hub IoT do Azure

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

O Hub IoT é um serviço do Azure que permite gerenciar seus dispositivos IoT na nuvem e ingerir grandes volumes de telemetria de dispositivo para a nuvem para armazenamento ou processamento. Neste guia de início rápido, irá utilizar um *método direto* para controlar um dispositivo simulado ligado ao seu hub IoT. Pode utilizar métodos diretos para alterar remotamente o comportamento de um dispositivo ligado ao seu hub IoT.

O guia de início rápido utiliza duas aplicações Node.js pré-escritas:

* Uma aplicação de dispositivo simulado que responde aos métodos diretos chamados a partir de uma aplicação back-end. Para receber as chamadas de método direto, esta aplicação liga-se a um ponto final específico do dispositivo no seu hub IoT.

* Uma aplicação back-end que chama os métodos diretos no dispositivo simulado. Para chamar um método direto num dispositivo, esta aplicação liga-se a um ponto final do lado do serviço no seu hub IoT.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

As duas aplicações de exemplo que executa neste guia de início rápido são escritas com Node.js. Você precisa do node. js v10. x. x ou posterior em seu computador de desenvolvimento.

Pode transferir o Node.js para múltiplas plataformas em [nodejs.org](https://nodejs.org).

Pode verificar qual a versão atual do Node.js no seu computador de desenvolvimento através do seguinte comando:

```cmd/sh
node --version
```

Execute o comando a seguir para adicionar a extensão de IoT Microsoft Azure para CLI do Azure à sua instância de Cloud Shell. A extensão de IOT adiciona comandos específicos do serviço de provisionamento de dispositivos IOT, IoT Edge e do Hub IoT a CLI do Azure.

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Se ainda não o fez, transfira o projeto Node.js de exemplo do https://github.com/Azure-Samples/azure-iot-samples-node/archive/master.zip e extraia o arquivo ZIP.

## <a name="create-an-iot-hub"></a>Criar um hub IoT

Se concluiu o anterior [Guia de Início Rápido: Enviar telemetria a partir de um dispositivo para um hub IoT](quickstart-send-telemetry-node.md), pode ignorar este passo.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registar um dispositivo

Se concluiu o anterior [Guia de Início Rápido: Enviar telemetria a partir de um dispositivo para um hub IoT](quickstart-send-telemetry-node.md), pode ignorar este passo.

É necessário registar um dispositivo no hub IoT antes de o mesmo se poder ligar. Neste início rápido, vai utilizar o Azure Cloud Shell para registar um dispositivo simulado.

1. Execute o comando a seguir em Azure Cloud Shell para criar a identidade do dispositivo.

   **Nomedoseuhubiot**: Substitua esse espaço reservado abaixo pelo nome escolhido para o Hub IOT.

   **MyNodeDevice**: esse é o nome do dispositivo que você está registrando. É recomendável usar **MyNodeDevice** conforme mostrado. Se você escolher um nome diferente para seu dispositivo, também precisará usar esse nome em todo este artigo e atualizar o nome do dispositivo nos aplicativos de exemplo antes de executá-los.

    ```azurecli-interactive
    az iot hub device-identity create \
      --hub-name {YourIoTHubName} --device-id MyNodeDevice
    ```

2. Execute o seguinte comando no Azure Cloud Shell para obter a _cadeia de ligação do dispositivo_ que acabou de registar:

    **Nomedoseuhubiot**: Substitua esse espaço reservado abaixo pelo nome escolhido para o Hub IOT.

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

    **Nomedoseuhubiot**: Substitua esse espaço reservado abaixo pelo nome escolhido para o Hub IOT.

    ```azurecli-interactive
    az iot hub show-connection-string \
      --policy-name service --name {YourIoTHubName} --output table

    ```

    Anote a cadeia de ligação do serviço, que se parece com:

   `HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}`

    Irá utilizar este valor mais adiante no guia de início rápido. Essa cadeia de conexão de serviço é diferente da cadeia de conexão do dispositivo que você anotou na etapa anterior.

## <a name="listen-for-direct-method-calls"></a>Aguardar chamadas de método direto

A aplicação de dispositivo simulado liga-se a um ponto final específico do dispositivo no seu hub IoT, envia telemetria simulada e aguarda chamadas de método direto do seu hub. Neste guia de início rápido, a chamada de método direto do hub indica ao dispositivo para alterar o intervalo em que envia telemetria. O dispositivo simulado envia uma confirmação de volta ao seu hub depois de executar o método direto.

1. Na janela do terminal local, navegue para a pasta raiz do projeto Node.js de exemplo. Em seguida, navegue para a pasta **iot-hub\Quickstarts\simulated-device-2**.

2. Abra o ficheiro **SimulatedDevice.js** num editor de texto à sua escolha.

    Substitua o valor da variável `connectionString` pela cadeia de conexão do dispositivo anotada anteriormente. Em seguida, salve as alterações em **SimulatedDevice. js**.

3. Na janela do terminal local, execute os seguintes comandos para instalar as bibliotecas exigidas e execute a aplicação de dispositivo simulado:

    ```cmd/sh
    npm install
    node SimulatedDevice.js
    ```

    A seguinte captura de ecrã mostra a saída à medida que a aplicação de dispositivo simulado envia telemetria para o seu hub IoT:

    ![Executar o dispositivo simulado](./media/quickstart-control-device-node/simulated-device-telemetry-iot-hub.png)

## <a name="call-the-direct-method"></a>Chamar o método direto

A aplicação back-end liga-se a um ponto final do lado do serviço no seu Hub IoT. O aplicativo faz chamadas de método diretas para um dispositivo por meio do Hub IoT e ouve as confirmações. Normalmente, as aplicações back-end do Hub IoT são executadas na cloud.

1. Noutra janela do terminal local, navegue para a pasta raiz do projeto Node.js de exemplo. Em seguida, navegue para a pasta **iot-hub\Quickstarts\back-end-application**.

2. Abra o ficheiro **BackEndApplication.js** num editor de texto à sua escolha.

    Substitua o valor da variável `connectionString` pela cadeia de conexão de serviço anotada anteriormente. Em seguida, salve as alterações em **BackEndApplication. js**.

3. Na janela do terminal local, execute os seguintes comandos para instalar as bibliotecas exigidas e executar a aplicação back-end:

    ```cmd/sh
    npm install
    node BackEndApplication.js
    ```

    A captura de tela a seguir mostra a saída, pois o aplicativo faz uma chamada de método direto para o dispositivo e recebe uma confirmação:

    ![Saída quando o aplicativo faz chamada de método direto para o dispositivo](./media/quickstart-control-device-node/direct-method-device-call.png)

    Depois de executar a aplicação back-end, verá uma mensagem na janela da consola a executar o dispositivo simulado e a velocidade à qual a aplicação envia mensagens muda:

    ![Saída quando há uma alteração no cliente simulado](./media/quickstart-control-device-node/simulated-device-message-change.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você chamou um método direto em um dispositivo de um aplicativo de back-end e respondeu à chamada de método direto em um aplicativo de dispositivo simulado.

Para saber como encaminhar mensagens do dispositivo para a cloud para diferentes destinos na cloud, avance para o tutorial seguinte.

> [!div class="nextstepaction"]
> [Tutorial: encaminhar telemetria para pontos finais diferentes para processamento](tutorial-routing.md)
