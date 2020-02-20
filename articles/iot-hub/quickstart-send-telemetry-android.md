---
title: Envie telemetria para O Hub Azure IoT (Android) / Microsoft Docs
description: Neste arranque rápido, você executa uma aplicação Android de amostra para enviar telemetria simulada para um hub IoT e ler telemetria do hub IoT para processamento na nuvem.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/15/2019
ms.author: wesmc
ms.openlocfilehash: 6c7428a4f34f0be64423c42efc06667cb18aa025
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77471283"
---
# <a name="quickstart-send-iot-telemetry-from-an-android-device"></a>Quickstart: Envie telemetria IoT a partir de um dispositivo Android

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

Neste arranque rápido, envia-se telemetria para um Hub Azure IoT a partir de uma aplicação Android que funciona num dispositivo físico ou simulado. O Hub IoT é um serviço do Azure que lhe permite ingerir elevados volumes de telemetria dos seus dispositivos IoT para a cloud para armazenamento ou processamento. Este quickstart usa uma aplicação Android pré-escrita para enviar a telemetria. A telemetria será lida a partir do Hub IoT usando a Casca de Nuvem Azure. Antes de executar a aplicação, cria um hub IoT e regista um dispositivo com o hub.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta Azure com uma subscrição ativa. [Crie um de graça.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

* [Android Studio com Android SDK 27](https://developer.android.com/studio/). Para mais informações, consulte a [instalação do Android.](https://developer.android.com/studio/install) O Android SDK 27 é utilizado pela amostra neste artigo.

* [Uma aplicação Android de amostra.](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/device/AndroidSample) Esta amostra faz parte do repositório [azure-iot-amostras-java.](https://github.com/Azure-Samples/azure-iot-samples-java)

* Porta 8883 aberta na sua firewall. A amostra do dispositivo neste quickstart utiliza o protocolo MQTT, que comunica através da porta 8883. Este porto pode estar bloqueado em alguns ambientes de rede corporativa e educativa. Para obter mais informações e formas de resolver este problema, consulte [A Ligação ao IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

### <a name="add-azure-iot-extension"></a>Adicionar extensão Azure IoT

Execute o seguinte comando para adicionar a extensão Microsoft Azure IoT para Azure CLI à sua instância Cloud Shell. A extensão IOT adiciona comandos específicos do IoT Hub, IoT Edge e IoT Device Provisioning Service (DPS) ao Azure CLI.

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registar um dispositivo

É necessário registar um dispositivo no hub IoT antes de o mesmo se poder ligar. Neste início rápido, vai utilizar o Azure Cloud Shell para registar um dispositivo simulado.

1. Executar o seguinte comando em Azure Cloud Shell para criar a identidade do dispositivo.

   **YourIoTHubName**: Substitua este espaço reservado abaixo com o nome que escolheu para o seu hub IoT.

   **MyAndroidDevice**: Este é o nome do dispositivo que está a registar. É aconselhável utilizar o **MyAndroidDevice** como mostrado. Se escolher um nome diferente para o seu dispositivo, também terá de usar esse nome ao longo deste artigo e atualizar o nome do dispositivo nas aplicações da amostra antes de os executar.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyAndroidDevice
    ```

2. Execute o seguinte comando em Azure Cloud Shell para obter a _cadeia de ligação_ do dispositivo para o dispositivo que acabou de registar:

    **YourIoTHubName**: Substitua este espaço reservado abaixo com o nome que escolheu para o seu hub IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyAndroidDevice --output table
    ```

    Anote a cadeia de ligação do dispositivo, que se parece com:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}`

    Você usará este valor mais tarde neste quickstart para enviar telemetria.

## <a name="send-simulated-telemetry"></a>Enviar telemetria simulada

1. Abra o projeto Android da amostra GitHub no Android Studio. O projeto está localizado no seguinte diretório da sua cópia clonada ou descarregada do repositório [azure-iot-sample-java.](https://github.com/Azure-Samples/azure-iot-samples-java)

        \azure-iot-samples-java\iot-hub\Samples\device\AndroidSample

2. No Android Studio, abra *gradle.properties* para o projeto da amostra e substitua o **Device_Connection_String** espaço reservado pela cadeia de ligação ao dispositivo que fez uma nota anterior.

    ```
    DeviceConnectionString=HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}
    ```

3. No Android Studio, clique em **File** > **Sync Project com Ficheiros Gradle**. Verifique se a construção completa.

   > [!NOTE]
   > Se a sincronização do projeto falhar, pode ser por uma das seguintes razões:
   >
   > * As versões do plugin E Gradle do Android Gradle referenciados no projeto estão desatualizadas para a sua versão do Android Studio. Siga [estas instruções](https://developer.android.com/studio/releases/gradle-plugin) para referência e instale as versões corretas do plugin e gradle para a sua instalação.
   > * O contrato de licença para o Android SDK não foi assinado. Siga as instruções na saída Build para assinar o contrato de licença e baixar o SDK.

4. Uma vez concluída a construção, clique em **Executar** > **Executar 'app'** . Configure a aplicação para executar num dispositivo Android físico ou num emulador Android. Para obter mais informações sobre a execução de uma aplicação Android num dispositivo físico ou emulador, consulte [Executar a sua aplicação](https://developer.android.com/training/basics/firstapp/running-app).

5. Assim que a aplicação estiver carregada, clique no botão **Iniciar** para começar a enviar telemetria para o seu Hub IoT:

    ![Aplicação](media/quickstart-send-telemetry-android/sample-screenshot.png)


## <a name="read-the-telemetry-from-your-hub"></a>Ler a telemetria a partir do seu hub

Nesta secção, utilizará a Shell Nuvem Azure com a [extensão IoT](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) para monitorizar as mensagens do dispositivo que são enviadas pelo dispositivo Android.

1. Com o Azure Cloud Shell, execute o seguinte comando para se ligar e ler mensagens do hub IoT:

   **YourIoTHubName**: Substitua este espaço reservado abaixo com o nome que escolheu para o seu hub IoT.

    ```azurecli-interactive
    az iot hub monitor-events --hub-name {YourIoTHubName} --output table
    ```

    A seguinte imagem mostra a saída à medida que o hub IoT recebe telemetria enviada pelo dispositivo Android:

      ![Ler as mensagens do dispositivo com a CLI do Azure](media/quickstart-send-telemetry-android/read-data.png)
## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, você montou um hub IoT, registou um dispositivo, enviou telemetria simulada para o hub usando uma aplicação Android, e leu a telemetria do hub usando a Azure Cloud Shell.

Para saber como controlar o seu dispositivo simulado a partir de uma aplicação back-end, continue para o guia de início rápido seguinte.

> [!div class="nextstepaction"]
> [Guia de Início Rápido: controlar um dispositivo ligado a um hub IoT](quickstart-control-device-android.md)