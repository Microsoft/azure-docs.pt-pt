---
title: Controlar um dispositivo a partir do guia de introdução do IoT Hub do Azure (Android) | Documentos da Microsoft
description: Neste guia de início rápido, irá executar duas aplicações Java de exemplo. Uma aplicação é uma aplicação de serviço que pode controlar remotamente os dispositivos ligados ao seu hub. O outro aplicativo é executado num dispositivo físico ou simulado ligado ao seu hub que pode ser controlado remotamente.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: quickstart
ms.custom: mvc
ms.date: 11/19/2018
ms.author: wesmc
ms.openlocfilehash: a9e5b00ad84a57f49303f2e6f9389b4791fd0f60
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58166951"
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub-android"></a>Início rápido: Controlar um dispositivo ligado a um IoT hub (Android)

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

O Hub IoT é um serviço do Azure que lhe permite ingerir elevados volumes de telemetria dos seus dispositivos IoT para a cloud e gerir os seus dispositivos a partir da cloud. Neste guia de início rápido, irá utilizar um *método direto* para controlar um dispositivo simulado ligado ao seu hub IoT. Pode utilizar métodos diretos para alterar remotamente o comportamento de um dispositivo ligado ao seu hub IoT.

O guia de início rápido utiliza duas aplicações Java pré-escritas:

* Um aplicativo de dispositivo simulado que responde aos métodos diretos chamado a partir de uma aplicação de serviço de back-end. Para receber as chamadas de método direto, esta aplicação liga-se a um ponto final específico do dispositivo no seu hub IoT.

* Uma aplicação de serviço que chama o método direto num dispositivo Android. Para chamar um método direto num dispositivo, esta aplicação liga-se a um ponto final do lado do serviço no seu hub IoT.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos


* Android studio a partir de https://developer.android.com/studio/. Para obter mais informações sobre a instalação do Android Studio, consulte [instalação do android](https://developer.android.com/studio/install).

* Android SDK 27 é utilizado pelo exemplo neste artigo.

* Duas aplicações de exemplo são necessários por este início rápido: O [aplicação Android de exemplo de SDK de dispositivo](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/device/AndroidSample) e o [aplicação Android de exemplo de SDK do serviço](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/service/AndroidSample). Ambos estes exemplos são parte do repositório azure-iot-exemplos-java no GitHub. Transfira ou clone a [azure-iot-exemplos-java](https://github.com/Azure-Samples/azure-iot-samples-java) repositório.


## <a name="create-an-iot-hub"></a>Criar um hub IoT

Se concluiu o anterior [início rápido: Enviar telemetria a partir de um dispositivo para um hub IoT](quickstart-send-telemetry-android.md), pode ignorar este passo e utilizar o hub IoT que já criou.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registar um dispositivo

Se concluiu o anterior [início rápido: Enviar telemetria a partir de um dispositivo para um hub IoT](quickstart-send-telemetry-android.md), pode ignorar este passo e utilizar o mesmo dispositivo registado no início rápido anterior.

É necessário registar um dispositivo no hub IoT antes de o mesmo se poder ligar. Neste início rápido, vai utilizar o Azure Cloud Shell para registar um dispositivo simulado.

1. Execute os seguintes comandos no Azure Cloud Shell para adicionar a extensão da CLI do Hub IoT e para criar a identidade do dispositivo.

   **YourIoTHubName**: Substitua este marcador de posição abaixo com o nome que escolheu para o seu hub IoT.

   **MyAndroidDevice**: Este valor é o nome fornecido para o dispositivo registado. Utilize MyAndroidDevice, conforme mostrado. Se escolher um nome diferente para o seu dispositivo, também poderá usar esse nome ao longo deste artigo e atualizar o nome do dispositivo em amostras de aplicativos antes de executá-los.

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    az iot hub device-identity create \
      --hub-name YourIoTHubName --device-id MyAndroidDevice
    ```

2. Execute o seguinte comando no Azure Cloud Shell para obter a _cadeia de ligação do dispositivo_ que acabou de registar:

   **YourIoTHubName**: Substitua este marcador de posição abaixo com o nome que escolher para o seu hub IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string \
      --hub-name YourIoTHubName \
      --device-id MyAndroidDevice \
      --output table
    ```

    Anote a cadeia de ligação do dispositivo, que se parece com:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}`

    Irá utilizar este valor mais adiante no guia de início rápido.

## <a name="retrieve-the-service-connection-string"></a>Obter a cadeia de ligação do serviço

Também é necessário um _cadeia de ligação de serviço_ para permitir que os aplicativos de serviço de back-end ligar ao seu hub IoT para poder executar métodos e obter mensagens. O seguinte comando obtém a cadeia de ligação do serviço do seu hub IoT:

**YourIoTHubName**: Substitua este marcador de posição abaixo com o nome que escolheu para o seu hub IoT.

```azurecli-interactive
az iot hub show-connection-string --name YourIoTHubName --output table
```

Anote a cadeia de ligação do serviço, que se parece com:

`HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={YourSharedAccessKey}`

Irá utilizar este valor mais adiante no guia de início rápido. A cadeia de ligação do serviço é diferente da cadeia de ligação de dispositivo.

## <a name="listen-for-direct-method-calls"></a>Aguardar chamadas de método direto

O aplicativo de exemplo do SDK de dispositivo pode ser executado num dispositivo Android físico ou o emulador do Android. O exemplo liga a um ponto de extremidade específicos de dispositivos no IoT hub, envia telemetria simulada e está à escuta de chamadas de método direto do seu hub. Neste guia de início rápido, a chamada de método direto do hub indica ao dispositivo para alterar o intervalo em que envia telemetria. O dispositivo simulado envia uma confirmação para o seu hub depois de executar o método direto.

1. Abra o projeto Android de exemplo do GitHub no Android Studio. O projeto está localizado no diretório seguinte da sua cópia clonada ou transferido [azure-iot-exemplo-java](https://github.com/Azure-Samples/azure-iot-samples-java) repositório.

        \azure-iot-samples-java\iot-hub\Samples\device\AndroidSample

2. No Android Studio, abra *gradle.properties* para o projeto de exemplo e substitua o **Device_Connection_String** marcador de posição pela cadeia de ligação de dispositivo que anotou anteriormente.

    ```
    DeviceConnectionString=HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}
    ```

3. No Android Studio, clique em **arquivo** > **sincronizar projeto com os ficheiros Gradle**. Certifique-se de que a compilação for concluída.

   > [!NOTE]
   > Se a sincronização de projeto falhar, pode ser para um dos seguintes motivos:
   >
   > * As versões do plug-in do Android Gradle e referenciado no projeto do Gradle estão Desatualizadas para a sua versão do Android Studio. Siga [estas instruções](https://developer.android.com/studio/releases/gradle-plugin) para referenciar e instalar as versões corretas do plug-in e Gradle para a sua instalação.
   > * O contrato de licença para o Android SDK não assinado. Siga as instruções na saída da compilação para assinar o contrato de licença e transferir o SDK.


4. Depois de concluída a compilação, clique em **execute** > **executar "aplicação"**. Configure a aplicação seja executada num dispositivo Android físico ou o emulador do Android. Para obter mais informações sobre como executar uma aplicação Android num dispositivo físico ou o emulador, consulte [executar a sua aplicação](https://developer.android.com/training/basics/firstapp/running-app).

5. Assim que a aplicação for carregada, clique nas **iniciar** botão para começar a enviar telemetria ao seu IoT Hub:

    ![Aplicação](media/quickstart-send-telemetry-android/sample-screenshot.png)

Esta aplicação tem de ser deixada em execução num phycial dispositivo ou emulador enquanto executar o exemplo de SDK do serviço para atualizar o intervalo de telemetria durante o tempo de execução.


## <a name="read-the-telemetry-from-your-hub"></a>Ler a telemetria a partir do seu hub

Nesta secção, irá utilizar o Azure Cloud Shell com o [extensão de IoT](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) para monitorizar as mensagens de dispositivo que são enviadas pelo dispositivo Android.

1. Com o Azure Cloud Shell, execute o seguinte comando para se ligar e ler mensagens do hub IoT:

   **YourIoTHubName**: Substitua este marcador de posição abaixo com o nome que escolher para o seu hub IoT.

    ```azurecli-interactive
    az iot hub monitor-events --hub-name YourIoTHubName --output table
    ```
    Captura de ecrã seguinte mostra a saída, como o IoT hub recebe a telemetria enviada pelo dispositivo Android:

      ![Ler as mensagens do dispositivo com a CLI do Azure](media/quickstart-send-telemetry-android/read-data.png)

Por predefinição a aplicação de telemetria está a enviar telemetria do dispositivo Android 5 em 5 segundos. Na secção seguinte, irá utilizar uma chamada de método direto para atualizar o intervalo de telemetria para o dispositivo de Android IoT.


## <a name="call-the-direct-method"></a>Chamar o método direto

A aplicação de serviço liga-se para um ponto de extremidade do lado do serviço no seu IoT Hub. A aplicação faz chamadas de método direto para um dispositivo através do seu hub IoT e aguarda confirmações.

Execute esta aplicação num dispositivo Android físico separado ou emulador Android.

Normalmente, executa uma aplicação de serviço de back-end do IoT Hub na cloud em que é mais fácil atenuar os riscos associados a cadeia de ligação confidenciais que controla todos os dispositivos num IoT Hub. Neste exemplo, estamos executando-la como uma aplicação Android para apenas a fins de demonstração. As outras versões de idioma deste início rápido fornecem outros exemplos que se alinham mais de perto com uma aplicação de serviço de back-end.

1. Abra o projeto de Android de exemplo do GitHub serviço no Android Studio. O projeto está localizado no diretório seguinte da sua cópia clonada ou transferido [azure-iot-exemplo-java](https://github.com/Azure-Samples/azure-iot-samples-java) repositório.

        \azure-iot-samples-java\iot-hub\Samples\service\AndroidSample

2. No Android Studio, abra *gradle.properties* para o exemplo de projeto e atualize o valor para **ConnectionString** e **DeviceId** propriedades com a ligação de serviço cadeia de caracteres que anotou anteriormente e o ID de dispositivo Android que registou.

    ```
    ConnectionString=HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={YourSharedAccessKey}
    DeviceId=MyAndroidDevice
    ```

3. No Android Studio, clique em **arquivo** > **sincronizar projeto com os ficheiros Gradle**. Certifique-se de que a compilação for concluída.

   > [!NOTE]
   > Se a sincronização de projeto falhar, pode ser para um dos seguintes motivos:
   >
   > * As versões do plug-in do Android Gradle e referenciado no projeto do Gradle estão Desatualizadas para a sua versão do Android Studio. Siga [estas instruções](https://developer.android.com/studio/releases/gradle-plugin) para referenciar e instalar as versões corretas do plug-in e Gradle para a sua instalação.
   > * O contrato de licença para o Android SDK não assinado. Siga as instruções na saída da compilação para assinar o contrato de licença e transferir o SDK.


4. Depois de concluída a compilação, clique em **execute** > **executar "aplicação"**. Configure a aplicação seja executada num dispositivo Android físico separado ou um emulador Android. Para obter mais informações sobre como executar uma aplicação Android num dispositivo físico ou o emulador, consulte [executar a sua aplicação](https://developer.android.com/training/basics/firstapp/running-app).

5. Assim que a aplicação for carregada, atualizar o **defina o intervalo de mensagens** valor **1000** e clique em **Invoke**.

    Intervalo de mensagens de telemetria de TH é em milissegundos. O intervalo de telemetria de predefinido da amostra de dispositivos está definido para 5 segundos. Esta alteração irá atualizar o dispositivo de Android IoT, para que a telemetria é enviada a cada segundo.

    ![Introduza o intervalo de telemetria](media/quickstart-control-device-android/enter-telemetry-interval.png)

6. A aplicação irá receber uma confirmação que indica se o método executado com êxito ou não.

    ![Confirmação de método direto](media/quickstart-control-device-android/direct-method-ack.png)



## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, chamado um método direto num dispositivo a partir de uma aplicação de back-end e respondeu à chamada de método direto num aplicativo de dispositivo simulado.

Para saber como encaminhar mensagens do dispositivo para a cloud para diferentes destinos na cloud, avance para o tutorial seguinte.

> [!div class="nextstepaction"]
> [Tutorial: Telemetria de rota para diferentes pontos de extremidade para processamento](tutorial-routing.md)
