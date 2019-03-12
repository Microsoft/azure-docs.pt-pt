---
title: Enviar telemetria para o guia de introdução do IoT Hub do Azure (Android) | Documentos da Microsoft
description: Neste início rápido, irá executar uma aplicação Android de exemplo para enviar telemetria simulada para um hub IoT e ler telemetria do hub IoT para processamento na cloud.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: quickstart
ms.custom: mvc
ms.date: 11/05/2018
ms.author: wesmc
ms.openlocfilehash: 8f30918a73e1e7012ad3be151e778eb073627b84
ms.sourcegitcommit: 235cd1c4f003a7f8459b9761a623f000dd9e50ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2019
ms.locfileid: "57726690"
---
# <a name="quickstart-send-iot-telemetry-from-an-android-device"></a>Início rápido: Enviar a telemetria de IoT de um dispositivo Android

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

O Hub IoT é um serviço do Azure que lhe permite ingerir elevados volumes de telemetria dos seus dispositivos IoT para a cloud para armazenamento ou processamento. Neste início rápido, enviar telemetria para um IoT Hub partir de uma aplicação Android em execução num dispositivo físico ou simulado.

O início rápido utiliza uma aplicação Android pré-escritos para enviar a telemetria. A telemetria será lido a partir do IoT Hub com o Azure Cloud Shell. Antes de executar o aplicativo, criar um hub IoT e registar um dispositivo com o hub.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Android studio a partir de https://developer.android.com/studio/. Para obter mais informações sobre a instalação do Android Studio, consulte [instalação do android](https://developer.android.com/studio/install). 

* Android SDK 27 é utilizado pelo exemplo neste artigo. 

* O [exemplo de aplicação Android](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/device/AndroidSample) executar neste início rápido faz parte do repositório azure-iot-exemplos-java no GitHub. Transfira ou clone a [azure-iot-exemplos-java](https://github.com/Azure-Samples/azure-iot-samples-java) repositório.



## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registar um dispositivo

É necessário registar um dispositivo no hub IoT antes de o mesmo se poder ligar. Neste início rápido, vai utilizar o Azure Cloud Shell para registar um dispositivo simulado.

1. Execute os seguintes comandos no Azure Cloud Shell para adicionar a extensão da CLI do Hub IoT e para criar a identidade do dispositivo. 

   **YourIoTHubName**: Substitua este marcador de posição abaixo com o nome que escolher para o seu hub IoT.

   **MyAndroidDevice**: MyAndroidDevice é o nome fornecido para o dispositivo registado. Utilize MyAndroidDevice, conforme mostrado. Se escolher um nome diferente para o seu dispositivo, também irá precisar de utilizar esse nome através deste artigo, e atualize o nome do dispositivo em aplicações de exemplo antes de as executar.

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyAndroidDevice
    ```

2. Execute o seguinte comando no Azure Cloud Shell para obter a _cadeia de ligação do dispositivo_ que acabou de registar:

    **YourIoTHubName**: Substitua este marcador de posição abaixo com o nome que escolher para o seu hub IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyAndroidDevice --output table
    ```

    Anote a cadeia de ligação do dispositivo, que se parece com:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}`

    Irá utilizar este valor posteriormente neste guia de introdução para enviar telemetria.

## <a name="send-telemetry"></a>Enviar telemetria

1. Abra o projeto Android de exemplo do GitHub no Android Studio. O projeto está localizado no diretório seguinte da sua cópia clonada ou transferido [azure-iot-exemplo-java](https://github.com/Azure-Samples/azure-iot-samples-java) repositório.

        \azure-iot-samples-java\iot-hub\Samples\device\AndroidSample

2. No Android Studio, abra *gradle.properties* para o projeto de exemplo e substitua o **Device_Connection_String** marcador de posição pela cadeia de ligação de dispositivo que anotou anteriormente.

    ```
    DeviceConnectionString=HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}
    ```

3. No Android Studio, clique em **arquivo** > **sincronizar projeto com os ficheiros Gradle**. Certifique-se de que a compilação for concluída.

   > [!NOTE]
   > Se a sincronização de projeto falhar, pode ser para um dos seguintes motivos:
   >
   > * As versões do plug-in do Android Gradle e referenciado no projeto do Gradle estão Desatualizadas para a sua versão do Android Studio. Siga [estas instruções](https://developer.android.com/studio/releases/gradle-plugin) para referenciar e instalar as versões corretas do plug-in e Gradle para a sua instalação.
   > * O contrato de licença para o Android SDK não assinado. Siga as instruções na saída da compilação para assinar o contrato de licença e transferir o SDK.

4. Depois de concluída a compilação, clique em **execute** > **executar "aplicação"**. Configure a aplicação seja executada num dispositivo Android físico ou o emulador do Android. Para obter mais informações sobre como executar uma aplicação Android num dispositivo físico ou o emulador, consulte [executar a sua aplicação](https://developer.android.com/training/basics/firstapp/running-app).

5. Assim que a aplicação for carregada, clique nas **iniciar** botão para começar a enviar telemetria ao seu IoT Hub:

    ![Candidatura](media/quickstart-send-telemetry-android/sample-screenshot.png)


## <a name="read-the-telemetry-from-your-hub"></a>Ler a telemetria a partir do seu hub

Nesta secção, irá utilizar o Azure Cloud Shell com o [extensão de IoT](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) para monitorizar as mensagens de dispositivo que são enviadas pelo dispositivo Android.

1. Com o Azure Cloud Shell, execute o seguinte comando para se ligar e ler mensagens do hub IoT:

   **YourIoTHubName**: Substitua este marcador de posição abaixo com o nome que escolher para o seu hub IoT.

    ```azurecli-interactive
    az iot hub monitor-events --hub-name YourIoTHubName --output table
    ```
    Captura de ecrã seguinte mostra a saída, como o IoT hub recebe a telemetria enviada pelo dispositivo Android:

      ![Ler as mensagens do dispositivo com a CLI do Azure](media/quickstart-send-telemetry-android/read-data.png)


## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, configurou um IoT hub, registou um dispositivo, enviou telemetria simulada para o hub a utilizar uma aplicação Android e ler a telemetria do hub de utilizar o Azure Cloud Shell.

Para saber como controlar o seu dispositivo simulado a partir de uma aplicação back-end, continue para o guia de início rápido seguinte.

> [!div class="nextstepaction"]
> [Quickstart: Controlar um dispositivo ligado a um hub IoT](quickstart-control-device-android.md)

