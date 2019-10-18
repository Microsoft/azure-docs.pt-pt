---
title: Controlar um dispositivo do início rápido do Hub IoT do Azure (Android) | Microsoft Docs
description: Neste guia de início rápido, irá executar duas aplicações Java de exemplo. Um aplicativo é um aplicativo de serviço que pode controlar remotamente os dispositivos conectados ao seu hub. O outro aplicativo é executado em um dispositivo físico ou simulado conectado ao Hub que pode ser controlado remotamente.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: quickstart
ms.custom: mvc
ms.date: 06/21/2019
ms.author: wesmc
ms.openlocfilehash: d607608167e1287c7df35157ccb9870f40f22943
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72516721"
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub-android"></a>Início rápido: controlar um dispositivo conectado a um hub IoT (Android)

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

O Hub IoT é um serviço do Azure que permite gerenciar seus dispositivos IoT na nuvem e ingerir grandes volumes de telemetria de dispositivo para a nuvem para armazenamento ou processamento. Neste guia de início rápido, irá utilizar um *método direto* para controlar um dispositivo simulado ligado ao seu hub IoT. Pode utilizar métodos diretos para alterar remotamente o comportamento de um dispositivo ligado ao seu hub IoT.

O guia de início rápido utiliza duas aplicações Java pré-escritas:

* Um aplicativo de dispositivo simulado que responde a métodos diretos chamados de um aplicativo de serviço de back-end. Para receber as chamadas de método direto, esta aplicação liga-se a um ponto final específico do dispositivo no seu hub IoT.

* Um aplicativo de serviço que chama o método direto no dispositivo Android. Para chamar um método direto num dispositivo, esta aplicação liga-se a um ponto final do lado do serviço no seu hub IoT.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Android Studio do https://developer.android.com/studio/. Para obter mais informações sobre a instalação do Android Studio, consulte [Android-Installation](https://developer.android.com/studio/install).

* O SDK do Android 27 é usado pelo exemplo neste artigo.

* Execute o comando a seguir para adicionar a extensão de IoT Microsoft Azure para CLI do Azure à sua instância de Cloud Shell. A extensão de IOT adiciona comandos específicos do serviço de provisionamento de dispositivos IOT, IoT Edge e do Hub IoT a CLI do Azure.

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

* Dois aplicativos de exemplo são exigidos por este guia de início rápido: o [aplicativo Android de exemplo do SDK do dispositivo](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/device/AndroidSample) e o [aplicativo Android de exemplo do SDK do serviço](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/service/AndroidSample). Ambos os exemplos fazem parte do repositório Azure-IOT-samples-java no GitHub. Baixe ou clone o repositório [Azure-IOT-samples-java](https://github.com/Azure-Samples/azure-iot-samples-java) .

## <a name="create-an-iot-hub"></a>Criar um hub IoT

Se você concluiu o [início rápido anterior: enviar telemetria de um dispositivo para um hub IOT](quickstart-send-telemetry-android.md), você pode ignorar esta etapa e usar o Hub IOT que você já criou.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registar um dispositivo

Se você concluiu o [início rápido anterior: enviar telemetria de um dispositivo para um hub IOT](quickstart-send-telemetry-android.md), você pode ignorar esta etapa e usar o mesmo dispositivo registrado no guia de início rápido anterior.

É necessário registar um dispositivo no hub IoT antes de o mesmo se poder ligar. Neste início rápido, vai utilizar o Azure Cloud Shell para registar um dispositivo simulado.

1. Execute o comando a seguir em Azure Cloud Shell para criar a identidade do dispositivo.

   **Nomedoseuhubiot**: Substitua esse espaço reservado abaixo pelo nome escolhido para o Hub IOT.

   **MyAndroidDevice**: esse é o nome do dispositivo que você está registrando. É recomendável usar **MyAndroidDevice** conforme mostrado. Se você escolher um nome diferente para seu dispositivo, também precisará usar esse nome em todo este artigo e atualizar o nome do dispositivo nos aplicativos de exemplo antes de executá-los.

    ```azurecli-interactive
    az iot hub device-identity create \
      --hub-name {YourIoTHubName} --device-id MyAndroidDevice
    ```

2. Execute o seguinte comando no Azure Cloud Shell para obter a _cadeia de ligação do dispositivo_ que acabou de registar:

   **Nomedoseuhubiot**: Substitua esse espaço reservado abaixo pelo nome que você escolher para o Hub IOT.

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

Você também precisa de uma _cadeia de conexão de serviço_ para permitir que os aplicativos de serviço de back-end se conectem ao seu hub IOT para executar métodos e recuperar mensagens. O seguinte comando obtém a cadeia de ligação do serviço do seu hub IoT:

**Nomedoseuhubiot**: Substitua esse espaço reservado abaixo pelo nome escolhido para o Hub IOT.

```azurecli-interactive
az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
```

Anote a cadeia de ligação do serviço, que se parece com:

`HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}`

Irá utilizar este valor mais adiante no guia de início rápido. Essa cadeia de conexão de serviço é diferente da cadeia de conexão do dispositivo que você anotou na etapa anterior.

## <a name="listen-for-direct-method-calls"></a>Aguardar chamadas de método direto

O aplicativo de exemplo do SDK do dispositivo pode ser executado em um dispositivo Android físico ou em um emulador do Android. O exemplo se conecta a um ponto de extremidade específico do dispositivo em seu hub IoT, envia telemetria simulada e escuta chamadas de método diretas do seu hub. Neste guia de início rápido, a chamada de método direto do hub indica ao dispositivo para alterar o intervalo em que envia telemetria. O dispositivo simulado envia uma confirmação de volta ao seu hub depois de executar o método direto.

1. Abra o projeto Android de exemplo do GitHub no Android Studio. O projeto está localizado no seguinte diretório de sua cópia clonada ou baixada do repositório [Azure-IOT-Sample-Java](https://github.com/Azure-Samples/azure-iot-samples-java) .

        \azure-iot-samples-java\iot-hub\Samples\device\AndroidSample

2. No Android Studio, abra *gradle. Properties* para o projeto de exemplo e substitua o espaço reservado **Device_Connection_String** pela cadeia de conexão do dispositivo anotada anteriormente.

    ```
    DeviceConnectionString=HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}
    ```

3. Em Android Studio, clique em **arquivo** > **sincronizar projeto com arquivos gradle**. Verifique se a compilação foi concluída.

   > [!NOTE]
   > Se a sincronização do projeto falhar, pode ser por um dos seguintes motivos:
   >
   > * As versões do plug-in do Android gradle e do gradle referenciados no projeto estão desatualizadas para sua versão do Android Studio. Siga [estas instruções](https://developer.android.com/studio/releases/gradle-plugin) para fazer referência e instalar as versões corretas do plug-in e do gradle para sua instalação.
   > * O contrato de licença para o SDK do Android não foi assinado. Siga as instruções na saída da compilação para assinar o contrato de licença e baixar o SDK.

4. Depois que a compilação for concluída, clique em **executar** > **Executar ' aplicativo '** . Configure o aplicativo para ser executado em um dispositivo Android físico ou em um emulador do Android. Para obter mais informações sobre como executar um aplicativo Android em um emulador ou dispositivo físico, consulte [executar seu aplicativo](https://developer.android.com/training/basics/firstapp/running-app).

5. Depois que o aplicativo for carregado, clique no botão **Iniciar** para começar a enviar telemetria para o Hub IOT:

    ![Captura de tela de exemplo do aplicativo Android do dispositivo cliente](media/quickstart-control-device-android/sample-screenshot.png)

Esse aplicativo precisa ser deixado em execução em um dispositivo físico ou emulador enquanto você executa o exemplo do SDK do serviço para atualizar o intervalo de telemetria durante o tempo de execução.

## <a name="read-the-telemetry-from-your-hub"></a>Ler a telemetria a partir do seu hub

Nesta seção, você usará o Azure Cloud Shell com a [extensão de IOT](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) para monitorar as mensagens que são enviadas pelo dispositivo Android.

1. Com o Azure Cloud Shell, execute o seguinte comando para se ligar e ler mensagens do hub IoT:

   **Nomedoseuhubiot**: Substitua esse espaço reservado abaixo pelo nome que você escolher para o Hub IOT.

    ```azurecli-interactive
    az iot hub monitor-events --hub-name {YourIoTHubName} --output table
    ```

    A captura de tela a seguir mostra a saída à medida que o Hub IoT recebe telemetria enviada pelo dispositivo Android:

      ![Ler as mensagens do dispositivo com a CLI do Azure](media/quickstart-control-device-android/read-data.png)

Por padrão, o aplicativo de telemetria envia a telemetria do dispositivo Android a cada cinco segundos. Na próxima seção, você usará uma chamada de método direto para atualizar o intervalo de telemetria para o dispositivo IoT Android.

## <a name="call-the-direct-method"></a>Chamar o método direto

O aplicativo de serviço se conecta a um ponto de extremidade do lado do serviço em seu hub IoT. O aplicativo faz chamadas de método diretas para um dispositivo por meio do Hub IoT e ouve as confirmações.

Execute este aplicativo em um dispositivo Android físico separado ou no emulador do Android.

Um aplicativo de serviço de back-end do Hub IoT normalmente é executado na nuvem, onde é mais fácil reduzir os riscos associados à cadeia de conexão confidencial que controla todos os dispositivos em um hub IoT. Neste exemplo, estamos executando-o como um aplicativo Android apenas para fins de demonstração. As versões de outros idiomas deste guia de início rápido fornecem exemplos que se alinham mais com um aplicativo de serviço de back-end típico.

1. Abra o projeto do Android de exemplo de serviço do GitHub no Android Studio. O projeto está localizado no seguinte diretório de sua cópia clonada ou baixada do repositório [Azure-IOT-Sample-Java](https://github.com/Azure-Samples/azure-iot-samples-java) .

        \azure-iot-samples-java\iot-hub\Samples\service\AndroidSample

2. Em Android Studio, abra *gradle. Properties* para o projeto de exemplo. Atualize os valores para as propriedades **ConnectionString** e **DeviceID** com a cadeia de conexão de serviço que você ANOTOU anteriormente e a ID do dispositivo Android que você registrou.

    ```
    ConnectionString=HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}
    DeviceId=MyAndroidDevice
    ```

3. Em Android Studio, clique em **arquivo** > **sincronizar projeto com arquivos gradle**. Verifique se a compilação foi concluída.

   > [!NOTE]
   > Se a sincronização do projeto falhar, pode ser por um dos seguintes motivos:
   >
   > * As versões do plug-in do Android gradle e do gradle referenciados no projeto estão desatualizadas para sua versão do Android Studio. Siga [estas instruções](https://developer.android.com/studio/releases/gradle-plugin) para fazer referência e instalar as versões corretas do plug-in e do gradle para sua instalação.
   > * O contrato de licença para o SDK do Android não foi assinado. Siga as instruções na saída da compilação para assinar o contrato de licença e baixar o SDK.

4. Depois que a compilação for concluída, clique em **executar** > **Executar ' aplicativo '** . Configure o aplicativo para ser executado em um dispositivo Android físico separado ou em um emulador do Android. Para obter mais informações sobre como executar um aplicativo Android em um emulador ou dispositivo físico, consulte [executar seu aplicativo](https://developer.android.com/training/basics/firstapp/running-app).

5. Depois que o aplicativo for carregado, atualize o valor do **intervalo de mensagens definido** como **1000** e clique em **invocar**.

    O intervalo de mensagens de telemetria está em milissegundos. O intervalo de telemetria padrão do exemplo de dispositivo é definido para 5 segundos. Essa alteração atualizará o dispositivo IoT Android para que a telemetria seja enviada a cada segundo.

    ![Inserir intervalo de telemetria](media/quickstart-control-device-android/enter-telemetry-interval.png)

6. O aplicativo receberá uma confirmação indicando se o método foi executado com êxito ou não.

    ![Confirmação de método direto](media/quickstart-control-device-android/direct-method-ack.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você chamou um método direto em um dispositivo de um aplicativo de back-end e respondeu à chamada de método direto em um aplicativo de dispositivo simulado.

Para saber como encaminhar mensagens do dispositivo para a cloud para diferentes destinos na cloud, avance para o tutorial seguinte.

> [!div class="nextstepaction"]
> [Tutorial: encaminhar telemetria para pontos finais diferentes para processamento](tutorial-routing.md)
