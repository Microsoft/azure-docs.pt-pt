---
title: Controle um dispositivo do Azure IoT Hub quickstart (Android) [ Microsoft Docs
description: Neste guia de início rápido, irá executar duas aplicações Java de exemplo. Uma aplicação é uma aplicação de serviço que pode controlar remotamente dispositivos ligados ao seu hub. A outra aplicação funciona num dispositivo físico ou simulado ligado ao seu hub que pode ser controlado remotamente.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: quickstart
ms.custom:
- mvc
- mqtt
ms.date: 06/21/2019
ms.author: wesmc
ms.openlocfilehash: b9afd1c4ca5e4c652a03bc2ef652b8e43ea12787
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83727204"
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub-android"></a>Quickstart: Controle um dispositivo ligado a um hub IoT (Android)

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

Neste arranque rápido, utiliza-se um método direto para controlar um dispositivo simulado ligado ao Hub Azure IoT. O IoT Hub é um serviço Azure que lhe permite gerir os seus dispositivos IoT a partir da nuvem e ingerir grandes volumes de telemetria do dispositivo para a nuvem para armazenamento ou processamento. Pode utilizar métodos diretos para alterar remotamente o comportamento de um dispositivo ligado ao seu hub IoT. Este quickstart utiliza duas aplicações: uma aplicação simulada de dispositivo que responde a métodos diretos chamados a partir de uma aplicação de serviço back-end e uma aplicação de serviço que chama o método direto no dispositivo Android.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta Azure com uma subscrição ativa. [Crie um de graça.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

* [Android Studio com Android SDK 27](https://developer.android.com/studio/). Para mais informações, consulte [Instalar o Android Studio.](https://developer.android.com/studio/install)

* [Git.](https://git-scm.com/download/)

* [Aplicação Android amostra de dispositivo SDK](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/device/AndroidSample), incluída em [Amostras Azure IoT (Java)](https://github.com/Azure-Samples/azure-iot-samples-java).

* [Aplicação Android de amostra de serviço SDK,](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/service/AndroidSample)incluída em Amostras Azure IoT (Java).

* Porta 8883 aberta na sua firewall. A amostra do dispositivo neste quickstart utiliza o protocolo MQTT, que comunica através da porta 8883. Este porto pode estar bloqueado em alguns ambientes de rede corporativa e educativa. Para obter mais informações e formas de resolver este problema, consulte [A Ligação ao IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

### <a name="add-azure-iot-extension"></a>Adicionar extensão Azure IoT

Execute o seguinte comando para adicionar a extensão Microsoft Azure IoT para Azure CLI à sua instância Cloud Shell. A extensão IoT adiciona comandos específicos do IoT Hub, IoT Edge e IoT Device Provisioning Service (DPS) ao Azure CLI.

```azurecli-interactive
az extension add --name azure-iot
```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Criar um hub IoT

Se tiver concluído o [Quickstart anterior: Envie a telemetria de um dispositivo para um hub IoT,](quickstart-send-telemetry-android.md)pode saltar este passo e utilizar o hub IoT que já criou.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registar um dispositivo

Se tiver concluído o [Quickstart anterior: Envie a telemetria de um dispositivo para um hub IoT,](quickstart-send-telemetry-android.md)pode saltar este passo e utilizar o mesmo dispositivo registado no quickstart anterior.

É necessário registar um dispositivo no hub IoT antes de o mesmo se poder ligar. Neste início rápido, vai utilizar o Azure Cloud Shell para registar um dispositivo simulado.

1. Executar o seguinte comando em Azure Cloud Shell para criar a identidade do dispositivo.

   **YourIoTHubName**: Substitua este espaço reservado abaixo com o nome que escolheu para o seu hub IoT.

   **MyAndroidDevice**: Este é o nome do dispositivo que está a registar. É aconselhável utilizar o **MyAndroidDevice** como mostrado. Se escolher um nome diferente para o seu dispositivo, também precisa de usar esse nome ao longo deste artigo e atualizar o nome do dispositivo nas aplicações da amostra antes de os executar.

    ```azurecli-interactive
    az iot hub device-identity create \
      --hub-name {YourIoTHubName} --device-id MyAndroidDevice
    ```

2. Execute o seguinte comando no Azure Cloud Shell para obter a _cadeia de ligação do dispositivo_ que acabou de registar:

   **YourIoTHubName**: Substitua este espaço reservado abaixo com o nome que escolher para o seu hub IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string \
      --hub-name {YourIoTHubName} \
      --device-id MyAndroidDevice \
      --output table
    ```

    Anote a cadeia de ligação do dispositivo, que se parece com:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}`

    Irá utilizar este valor mais adiante no guia de início rápido.

## <a name="retrieve-the-service-connection-string"></a>Obter a cadeia de ligação do serviço

Também precisa de uma cadeia de _ligação_ de serviço para permitir que as aplicações de serviço back-end se conectem ao seu hub IoT para executar métodos e recuperar mensagens. O seguinte comando obtém a cadeia de ligação do serviço do seu hub IoT:

**YourIoTHubName**: Substitua este espaço reservado abaixo com o nome que escolheu para o seu hub IoT.

```azurecli-interactive
az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
```

Anote a cadeia de ligação do serviço, que se parece com:

`HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}`

Irá utilizar este valor mais adiante no guia de início rápido. Esta cadeia de ligação de serviço é diferente da cadeia de ligação do dispositivo que observou no passo anterior.

## <a name="listen-for-direct-method-calls"></a>Aguardar chamadas de método direto

Ambas as amostras para este arranque rápido fazem parte do repositório azure-iot-amostras-java no GitHub. Descarregue ou clone o repositório [azure-iot-amostras-java.](https://github.com/Azure-Samples/azure-iot-samples-java)

A aplicação de amostras SDK do dispositivo pode ser executada num dispositivo Android físico ou num emulador Android. A amostra liga-se a um ponto final específico do dispositivo no seu hub IoT, envia telemetria simulada e ouve chamadas de métodos diretos do seu hub. Neste guia de início rápido, a chamada de método direto do hub indica ao dispositivo para alterar o intervalo em que envia telemetria. O dispositivo simulado envia um reconhecimento de volta para o seu hub depois de executar o método direto.

1. Abra o projeto Android da amostra GitHub no Android Studio. O projeto está localizado no seguinte diretório da sua cópia clonada ou descarregada do repositório [azure-iot-sample-java.](https://github.com/Azure-Samples/azure-iot-samples-java)

        \azure-iot-samples-java\iot-hub\Samples\device\AndroidSample

2. No Android Studio, abra *gradle.properties* para o projeto da amostra e substitua o **Device_Connection_String** espaço reservado pela cadeia de ligação ao dispositivo que fez uma nota anterior.

    ```
    DeviceConnectionString=HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}
    ```

3. No Android Studio, clique em **File**  >  **Sync Project com Ficheiros Gradle**. Verifique se a construção completa.

   > [!NOTE]
   > Se a sincronização do projeto falhar, pode ser por uma das seguintes razões:
   >
   > * As versões do plugin E Gradle do Android Gradle referenciados no projeto estão desatualizadas para a sua versão do Android Studio. Siga [estas instruções](https://developer.android.com/studio/releases/gradle-plugin) para referência e instale as versões corretas do plugin e gradle para a sua instalação.
   > * O contrato de licença para o Android SDK não foi assinado. Siga as instruções na saída Build para assinar o contrato de licença e baixar o SDK.

4. Uma vez concluída a construção, clique em **Executar**  >  **Executar 'app'.** Configure a aplicação para executar num dispositivo Android físico ou num emulador Android. Para obter mais informações sobre a execução de uma aplicação Android num dispositivo físico ou emulador, consulte [Executar a sua aplicação](https://developer.android.com/training/basics/firstapp/running-app).

5. Assim que a aplicação estiver carregada, clique no botão **Iniciar** para começar a enviar telemetria para o seu Hub IoT:

    ![Screenshot da amostra da aplicação android dispositivo cliente](media/quickstart-control-device-android/sample-screenshot.png)

Esta aplicação precisa de ser deixada em funcionamento num dispositivo físico ou emulador enquanto executa a amostra SDK de serviço para atualizar o intervalo de telemetria durante o tempo de funcionamento.

## <a name="read-the-telemetry-from-your-hub"></a>Ler a telemetria a partir do seu hub

Nesta secção, utilizará a Shell Nuvem Azure com a [extensão IoT](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot?view=azure-cli-latest) para monitorizar as mensagens que são enviadas pelo dispositivo Android.

1. Com o Azure Cloud Shell, execute o seguinte comando para se ligar e ler mensagens do hub IoT:

   **YourIoTHubName**: Substitua este espaço reservado abaixo com o nome que escolher para o seu hub IoT.

    ```azurecli-interactive
    az iot hub monitor-events --hub-name {YourIoTHubName} --output table
    ```

    A seguinte imagem mostra a saída à medida que o hub IoT recebe telemetria enviada pelo dispositivo Android:

      ![Ler as mensagens do dispositivo com a CLI do Azure](media/quickstart-control-device-android/read-data.png)

Por padrão, a aplicação de telemetria envia telemetria do dispositivo Android a cada cinco segundos. Na secção seguinte, utilizará uma chamada de método direto para atualizar o intervalo de telemetria para o dispositivo Android IoT.

## <a name="call-the-direct-method"></a>Chamar o método direto

A aplicação de serviço liga-se a um ponto final do lado do serviço no seu Hub IoT. A aplicação faz chamadas de método sinuoso para um dispositivo através do seu hub IoT e ouve reconhecimentos.

Execute esta aplicação num dispositivo Android físico separado ou emulador Android.

Uma aplicação de serviço back-end IoT Hub normalmente funciona na nuvem, onde é mais fácil mitigar os riscos associados à cadeia de ligação sensível que controla todos os dispositivos num Hub IoT. Neste exemplo, estamos a executá-lo como uma aplicação Android apenas para fins de demonstração. As versões de outros idiomas deste quickstart fornecem exemplos que se alinham mais estreitamente com uma aplicação de serviço típica de back-end.

1. Abra o projeto Android da amostra de serviço GitHub no Android Studio. O projeto está localizado no seguinte diretório da sua cópia clonada ou descarregada do repositório [azure-iot-sample-java.](https://github.com/Azure-Samples/azure-iot-samples-java)

        \azure-iot-samples-java\iot-hub\Samples\service\AndroidSample

2. No Android Studio, abra *gradle.properties* para o projeto de amostra. Atualize os valores das propriedades **ConnectionString** e **DeviceId** com a cadeia de ligação de serviço que observou anteriormente e o ID do dispositivo Android que registou.

    ```
    ConnectionString=HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}
    DeviceId=MyAndroidDevice
    ```

3. No Android Studio, clique em **File**  >  **Sync Project com Ficheiros Gradle**. Verifique se a construção completa.

   > [!NOTE]
   > Se a sincronização do projeto falhar, pode ser por uma das seguintes razões:
   >
   > * As versões do plugin E Gradle do Android Gradle referenciados no projeto estão desatualizadas para a sua versão do Android Studio. Siga [estas instruções](https://developer.android.com/studio/releases/gradle-plugin) para referência e instale as versões corretas do plugin e gradle para a sua instalação.
   > * O contrato de licença para o Android SDK não foi assinado. Siga as instruções na saída Build para assinar o contrato de licença e baixar o SDK.

4. Uma vez concluída a construção, clique em **Executar**  >  **Executar 'app'.** Configure a aplicação para executar num dispositivo Android físico separado ou num emulador Android. Para obter mais informações sobre a execução de uma aplicação Android num dispositivo físico ou emulador, consulte [Executar a sua aplicação](https://developer.android.com/training/basics/firstapp/running-app).

5. Assim que a aplicação estiver carregada, atualize o valor do intervalo de **mensagens para** **1000** e clique **em Invocar**.

    O intervalo de mensagens de telemetria é em milissegundos. O intervalo de telemetria predefinido da amostra do dispositivo está definido durante 5 segundos. Esta alteração irá atualizar o dispositivo Android IoT para que a telemetria seja enviada a cada segundo.

    ![Introduza o intervalo da telemetria](media/quickstart-control-device-android/enter-telemetry-interval.png)

6. A aplicação receberá um reconhecimento indicando se o método foi executado com sucesso ou não.

    ![Reconhecimento do método direto](media/quickstart-control-device-android/direct-method-ack.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Próximos passos

Neste arranque rápido, chamou um método direto num dispositivo a partir de uma aplicação de back-end, e respondeu à chamada do método direto numa aplicação simulada do dispositivo.

Para saber como encaminhar mensagens do dispositivo para a cloud para diferentes destinos na cloud, avance para o tutorial seguinte.

> [!div class="nextstepaction"]
> [Tutorial: encaminhar telemetria para pontos finais diferentes para processamento](tutorial-routing.md)
