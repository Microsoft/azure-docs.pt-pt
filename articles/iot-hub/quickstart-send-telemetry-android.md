---
title: Enviar telemetria para o início rápido do Hub IoT do Azure (Android) | Microsoft Docs
description: Neste guia de início rápido, você executa um aplicativo Android de exemplo para enviar telemetria simulada para um hub IoT e para ler a telemetria do Hub IoT para processamento na nuvem.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/15/2019
ms.author: wesmc
ms.openlocfilehash: e213a92397240f2646ceda30688ecef422cdf29c
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72166362"
---
# <a name="quickstart-send-iot-telemetry-from-an-android-device"></a>Início rápido: enviar telemetria IoT de um dispositivo Android

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

O Hub IoT é um serviço do Azure que lhe permite ingerir elevados volumes de telemetria dos seus dispositivos IoT para a cloud para armazenamento ou processamento. Neste guia de início rápido, você envia a telemetria para um hub IoT de um aplicativo Android em execução em um dispositivo físico ou simulado.

O guia de início rápido usa um aplicativo Android previamente escrito para enviar a telemetria. A telemetria será lida do Hub IoT usando o Azure Cloud Shell. Antes de executar o aplicativo, você cria um hub IoT e registra um dispositivo com o Hub.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Android Studio do https://developer.android.com/studio/. Para obter mais informações sobre a instalação do Android Studio, consulte [Android-Installation](https://developer.android.com/studio/install).

* O SDK do Android 27 é usado pelo exemplo neste artigo.

* Execute o comando a seguir para adicionar a extensão de IoT Microsoft Azure para CLI do Azure à sua instância de Cloud Shell. A extensão de IOT adiciona comandos específicos do serviço de provisionamento de dispositivos IOT, IoT Edge e do Hub IoT a CLI do Azure.

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

* O [aplicativo Android de exemplo](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/device/AndroidSample) executado neste guia de início rápido faz parte do repositório Azure-IOT-samples-java no github. Baixe ou clone o repositório [Azure-IOT-samples-java](https://github.com/Azure-Samples/azure-iot-samples-java) .

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registar um dispositivo

É necessário registar um dispositivo no hub IoT antes de o mesmo se poder ligar. Neste início rápido, vai utilizar o Azure Cloud Shell para registar um dispositivo simulado.

1. Execute o comando a seguir em Azure Cloud Shell para criar a identidade do dispositivo.

   **Nomedoseuhubiot**: Substitua esse espaço reservado abaixo pelo nome escolhido para o Hub IOT.

   **MyAndroidDevice**: esse é o nome do dispositivo que você está registrando. É recomendável usar **MyAndroidDevice** conforme mostrado. Se você escolher um nome diferente para seu dispositivo, também precisará usar esse nome em todo este artigo e atualizar o nome do dispositivo nos aplicativos de exemplo antes de executá-los.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyAndroidDevice
    ```

2. Execute o seguinte comando no Azure Cloud Shell para obter a _cadeia de conexão do dispositivo_ para o dispositivo que você acabou de registrar:

    **Nomedoseuhubiot**: Substitua esse espaço reservado abaixo pelo nome escolhido para o Hub IOT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyAndroidDevice --output table
    ```

    Anote a cadeia de ligação do dispositivo, que se parece com:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}`

    Você usará esse valor posteriormente neste guia de início rápido para enviar telemetria.

## <a name="send-simulated-telemetry"></a>Enviar telemetria simulada

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

    ![Candidatura](media/quickstart-send-telemetry-android/sample-screenshot.png)


## <a name="read-the-telemetry-from-your-hub"></a>Ler a telemetria a partir do seu hub

Nesta seção, você usará o Azure Cloud Shell com a [extensão de IOT](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) para monitorar as mensagens do dispositivo que são enviadas pelo dispositivo Android.

1. Com o Azure Cloud Shell, execute o seguinte comando para se ligar e ler mensagens do hub IoT:

   **Nomedoseuhubiot**: Substitua esse espaço reservado abaixo pelo nome escolhido para o Hub IOT.

    ```azurecli-interactive
    az iot hub monitor-events --hub-name {YourIoTHubName} --output table
    ```

    A captura de tela a seguir mostra a saída à medida que o Hub IoT recebe telemetria enviada pelo dispositivo Android:

      ![Ler as mensagens do dispositivo com a CLI do Azure](media/quickstart-send-telemetry-android/read-data.png)
## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você configura um hub IoT, registrou um dispositivo, enviou telemetria simulada para o Hub usando um aplicativo Android e leu a telemetria do Hub usando o Azure Cloud Shell.

Para saber como controlar o seu dispositivo simulado a partir de uma aplicação back-end, continue para o guia de início rápido seguinte.

> [!div class="nextstepaction"]
> [Guia de Início Rápido: controlar um dispositivo ligado a um hub IoT](quickstart-control-device-android.md)