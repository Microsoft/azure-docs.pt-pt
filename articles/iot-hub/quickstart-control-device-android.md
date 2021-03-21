---
title: Quickstart - Controle um dispositivo a partir do Azure IoT Hub quickstart (Android) | Microsoft Docs
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
- devx-track-java
- devx-track-azurecli
ms.date: 06/21/2019
ms.author: wesmc
ms.openlocfilehash: 361317729ecf868465d498cf3fcedd45d8c979db
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102201010"
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub-android"></a>Quickstart: Controle um dispositivo ligado a um hub IoT (Android)

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

Neste arranque rápido, utiliza-se um método direto para controlar um dispositivo simulado ligado ao Azure IoT Hub. O IoT Hub é um serviço Azure que lhe permite gerir os seus dispositivos IoT a partir da nuvem e ingerir grandes volumes de telemetria do dispositivo para a nuvem para armazenamento ou processamento. Pode utilizar métodos diretos para alterar remotamente o comportamento de um dispositivo ligado ao seu hub IoT. Este quickstart utiliza duas aplicações: uma aplicação simulada de dispositivo que responde a métodos diretos chamados a partir de uma aplicação de serviço back-end e uma aplicação de serviço que chama o método direto no dispositivo Android.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta Azure com uma subscrição ativa. [Crie um de graça.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

* [Android Studio com Android SDK 27](https://developer.android.com/studio/). Para mais informações, consulte [instalar o Android Studio.](https://developer.android.com/studio/install)

* [Git.](https://git-scm.com/download/)

* [Aplicação Android de amostra de dispositivo SDK,](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/device/AndroidSample)incluída em [amostras Azure IoT (Java)](https://github.com/Azure-Samples/azure-iot-samples-java).

* [Aplicação Android de amostra de serviço SDK,](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/service/AndroidSample)incluída em amostras Azure IoT (Java).

* Porta 8883 aberta na sua firewall. A amostra do dispositivo neste arranque rápido utiliza o protocolo MQTT, que comunica sobre a porta 8883. Este porto pode ser bloqueado em alguns ambientes de rede corporativa e educacional. Para obter mais informações e formas de contornar esta questão, consulte [Connecting to IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Criar um hub IoT

Se tiver completado o [Quickstart anterior: Enviar telemetria de um dispositivo para um hub IoT,](quickstart-send-telemetry-android.md)pode saltar este passo e utilizar o hub IoT que já criou.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registar um dispositivo

Se tiver completado o [Quickstart anterior: Enviar telemetria de um dispositivo para um hub IoT,](quickstart-send-telemetry-android.md)pode saltar este passo e utilizar o mesmo dispositivo registado no arranque rápido anterior.

É necessário registar um dispositivo no hub IoT antes de o mesmo se poder ligar. Neste início rápido, vai utilizar o Azure Cloud Shell para registar um dispositivo simulado.

1. Executar o seguinte comando em Azure Cloud Shell para criar a identidade do dispositivo.

   **Seu NomeIoTHubName**: Substitua este espaço reservado abaixo pelo nome que escolheu para o seu hub IoT.

   **MyAndroidDevice:** Este é o nome do dispositivo que está a registar. Recomenda-se usar **MyAndroidDevice** como mostrado. Se escolher um nome diferente para o seu dispositivo, também precisa de usar esse nome ao longo deste artigo e atualizar o nome do dispositivo nas aplicações da amostra antes de executá-los.

    ```azurecli-interactive
    az iot hub device-identity create \
      --hub-name {YourIoTHubName} --device-id MyAndroidDevice
    ```

2. Execute o seguinte comando no Azure Cloud Shell para obter a _cadeia de ligação do dispositivo_ que acabou de registar:

   **Seu NomeIoTHubName**: Substitua este espaço reservado abaixo pelo nome que escolher para o seu hub IoT.

    ```azurecli-interactive
    az iot hub device-identity connection-string show\
      --hub-name {YourIoTHubName} \
      --device-id MyAndroidDevice \
      --output table
    ```

    Anote a cadeia de ligação do dispositivo, que se assemelha a:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}`

    Irá utilizar este valor mais adiante no guia de início rápido.

## <a name="retrieve-the-service-connection-string"></a>Obter a cadeia de ligação do serviço

Também precisa de um fio de _ligação_ de serviço para permitir que as aplicações de serviço back-end se conectem ao seu hub IoT de modo a executar métodos e recuperar mensagens. O seguinte comando obtém a cadeia de ligação do serviço do seu hub IoT:

**Seu NomeIoTHubName**: Substitua este espaço reservado abaixo pelo nome que escolheu para o seu hub IoT.

```azurecli-interactive
az iot hub connection-string show --policy-name service --name {YourIoTHubName} --output table
```

Anote a cadeia de ligação do serviço, que se parece com:

`HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}`

Irá utilizar este valor mais adiante no guia de início rápido. Esta cadeia de ligação de serviço é diferente da cadeia de ligação do dispositivo que notou no passo anterior.

## <a name="listen-for-direct-method-calls"></a>Aguardar chamadas de método direto

Ambas as amostras para este arranque rápido fazem parte do repositório azure-iot-amostras-java no GitHub. Faça o download ou clone do repositório [azure-iot-samples-java.](https://github.com/Azure-Samples/azure-iot-samples-java)

A aplicação de amostra SDK do dispositivo pode ser executada num dispositivo Android físico ou num emulador Android. A amostra liga-se a um ponto final específico do dispositivo no seu hub IoT, envia telemetria simulada e ouve chamadas de métodos diretos do seu hub. Neste guia de início rápido, a chamada de método direto do hub indica ao dispositivo para alterar o intervalo em que envia telemetria. O dispositivo simulado envia um reconhecimento de volta ao seu centro depois de executar o método direto.

1. Abra o projeto Android de amostra do GitHub no Android Studio. O projeto está localizado no seguinte diretório do seu repositório clonado ou descarregado da [amostra-iot-java:](https://github.com/Azure-Samples/azure-iot-samples-java) *\azure-iot-samples-java\iot-hub\Samples\device\AndroidSample*.

2. No Android Studio, abra *as propriedades gradle.properties* para o projeto de amostra e substitua o espaço reservado **Device_Connection_String** pela cadeia de ligação do dispositivo que fez uma nota de anterior.

    ```
    DeviceConnectionString=HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}
    ```

3. No Estúdio Android, clique em **File**  >  **Sync Project com Gradle Files**. Verifique se a construção está concluída.

   > [!NOTE]
   > Se a sincronização do projeto falhar, pode ser por uma das seguintes razões:
   >
   > * As versões do plugin Android Gradle e Gradle referenciadas no projeto estão desatualizadas para a sua versão do Android Studio. Siga [estas instruções](https://developer.android.com/studio/releases/gradle-plugin) para fazer referência e instalar as versões corretas do plugin e gradle para a sua instalação.
   > * O contrato de licença para o Android SDK não foi assinado. Siga as instruções na saída Build para assinar o contrato de licença e descarregar o SDK.

4. Uma vez concluída a construção, clique em  >  **'app'** Run Run . Configurar a aplicação para executar num dispositivo Android físico ou num emulador Android. Para obter mais informações sobre a execução de uma aplicação Android num dispositivo físico ou emulador, consulte [Executar a sua aplicação.](https://developer.android.com/training/basics/firstapp/running-app)

5. Uma vez que a aplicação carrega, clique no botão **Iniciar** para começar a enviar telemetria para o seu IoT Hub:

    ![Screenshot da amostra do aplicativo android dispositivo cliente](media/quickstart-control-device-android/sample-screenshot.png)

Esta aplicação precisa de ser deixada em execução num dispositivo físico ou emulador enquanto executa a amostra SDK de serviço para atualizar o intervalo de telemetria durante o tempo de funcionamento.

## <a name="read-the-telemetry-from-your-hub"></a>Ler a telemetria a partir do seu hub

Nesta secção, utilizará o Azure Cloud Shell com a [extensão IoT](/cli/azure/ext/azure-iot/iot) para monitorizar as mensagens que são enviadas pelo dispositivo Android.

1. Com o Azure Cloud Shell, execute o seguinte comando para se ligar e ler mensagens do hub IoT:

   **Seu NomeIoTHubName**: Substitua este espaço reservado abaixo pelo nome que escolher para o seu hub IoT.

    ```azurecli-interactive
    az iot hub monitor-events --hub-name {YourIoTHubName} --output table
    ```

    A imagem que se segue mostra a saída, uma vez que o hub IoT recebe telemetria enviada pelo dispositivo Android:

      ![Ler as mensagens do dispositivo com a CLI do Azure](media/quickstart-control-device-android/read-data.png)

Por padrão, a aplicação de telemetria envia telemetria a partir do dispositivo Android a cada cinco segundos. Na secção seguinte, utilizará uma chamada de método direto para atualizar o intervalo de telemetria para o dispositivo Android IoT.

## <a name="call-the-direct-method"></a>Chamar o método direto

A aplicação de serviço liga-se a um ponto final do lado do serviço no seu Hub IoT. A aplicação faz chamadas de método direto para um dispositivo através do seu hub IoT e ouve para reconhecimentos.

Execute esta aplicação num dispositivo Android físico separado ou emulador Android.

Uma aplicação de serviço back-end IoT Hub normalmente funciona na nuvem, onde é mais fácil atenuar os riscos associados à cadeia de ligação sensível que controla todos os dispositivos num Hub IoT. Neste exemplo, estamos a executá-lo como uma aplicação Android apenas para fins de demonstração. As versões de outra linguagem deste quickstart fornecem exemplos que se alinham mais de perto com uma aplicação típica de serviço back-end.

1. Abra o projeto Android de amostra de serviço GitHub no Android Studio. O projeto está localizado no seguinte diretório do seu repositório clonado ou descarregado da [amostra-iot-java:](https://github.com/Azure-Samples/azure-iot-samples-java) *\azure-iot-samples-java\iot-hub\Samples\service\AndroidSample*.

2. No Android Studio, open *gradle.properties* para o projeto de amostragem. Atualize os valores das propriedades **ConnectionString** e **DeviceId** com a cadeia de ligação de serviço que observou anteriormente e o ID do dispositivo Android que registou.

    ```
    ConnectionString=HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}
    DeviceId=MyAndroidDevice
    ```

3. No Estúdio Android, clique em **File**  >  **Sync Project com Gradle Files**. Verifique se a construção está concluída.

   > [!NOTE]
   > Se a sincronização do projeto falhar, pode ser por uma das seguintes razões:
   >
   > * As versões do plugin Android Gradle e Gradle referenciadas no projeto estão desatualizadas para a sua versão do Android Studio. Siga [estas instruções](https://developer.android.com/studio/releases/gradle-plugin) para fazer referência e instalar as versões corretas do plugin e gradle para a sua instalação.
   > * O contrato de licença para o Android SDK não foi assinado. Siga as instruções na saída Build para assinar o contrato de licença e descarregar o SDK.

4. Uma vez concluída a construção, clique em  >  **'app'** Run Run . Configure a aplicação para executar em um dispositivo Físico Android separado ou um emulador Android. Para obter mais informações sobre a execução de uma aplicação Android num dispositivo físico ou emulador, consulte [Executar a sua aplicação.](https://developer.android.com/training/basics/firstapp/running-app)

5. Uma vez carregada a aplicação, atualize o valor **de intervalo de mensagens definida** para **1000** e clique em **Invocar**.

    O intervalo de mensagens de telemetria está em milissegundos. O intervalo de telemetria predefinido da amostra do dispositivo é definido durante 5 segundos. Esta alteração irá atualizar o dispositivo IoT Android para que a telemetria seja enviada a cada segundo.

    ![Insira o intervalo de telemetria](media/quickstart-control-device-android/enter-telemetry-interval.png)

6. A aplicação receberá um reconhecimento indicando se o método executado com sucesso ou não.

    ![Reconhecimento do método direto](media/quickstart-control-device-android/direct-method-ack.png)

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, chamou um método direto num dispositivo a partir de uma aplicação de back-end, e respondeu à chamada de método direto numa aplicação simulada do dispositivo.

Para saber como encaminhar mensagens do dispositivo para a cloud para diferentes destinos na cloud, avance para o tutorial seguinte.

> [!div class="nextstepaction"]
> [Tutorial: encaminhar telemetria para pontos finais diferentes para processamento](tutorial-routing.md)
