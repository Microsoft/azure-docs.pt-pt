---
title: Envie telemetria para Azure IoT Hub quickstart (Android) Microsoft Docs
description: Neste quickstart, você executou uma aplicação Android de amostra para enviar telemetria simulada para um hub IoT e para ler telemetria a partir do hub IoT para processamento na nuvem.
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
ms.date: 03/15/2019
ms.author: wesmc
ms.openlocfilehash: ad9c64f2846b59fcc833ad56c4da378f7819dae3
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92747405"
---
# <a name="quickstart-send-iot-telemetry-from-an-android-device"></a>Quickstart: Enviar telemetria IoT a partir de um dispositivo Android

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

Neste arranque rápido, envia telemetria para um Azure IoT Hub a partir de uma aplicação Android em execução num dispositivo físico ou simulado. O Hub IoT é um serviço do Azure que lhe permite ingerir elevados volumes de telemetria dos seus dispositivos IoT para a cloud para armazenamento ou processamento. Este quickstart utiliza uma aplicação Android pré-escrita para enviar a telemetria. A telemetria será lida a partir do Hub IoT usando a Azure Cloud Shell. Antes de executar a aplicação, cria um hub IoT e regista um dispositivo com o hub.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta Azure com uma subscrição ativa. [Crie um de graça.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

* [Android Studio com Android SDK 27](https://developer.android.com/studio/). Para mais informações, consulte [a instalação do Android.](https://developer.android.com/studio/install) O Android SDK 27 é utilizado pela amostra deste artigo.

* [Uma aplicação Android de amostra.](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/device/AndroidSample) Esta amostra faz parte do repositório [azure-iot-samples-java.](https://github.com/Azure-Samples/azure-iot-samples-java)

* Porta 8883 aberta na sua firewall. A amostra do dispositivo neste arranque rápido utiliza o protocolo MQTT, que comunica sobre a porta 8883. Este porto pode ser bloqueado em alguns ambientes de rede corporativa e educacional. Para obter mais informações e formas de contornar esta questão, consulte [Connecting to IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

### <a name="add-azure-iot-extension"></a>Adicionar extensão Azure IoT

Executar o seguinte comando para adicionar a extensão IoT do Microsoft Azure para Azure CLI à sua instância Cloud Shell. A extensão IOT adiciona comandos específicos do IoT Hub, IoT Edge e IoT Device Provisioning Service (DPS) ao Azure CLI.

```azurecli-interactive
az extension add --name azure-iot
```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registar um dispositivo

É necessário registar um dispositivo no hub IoT antes de o mesmo se poder ligar. Neste início rápido, vai utilizar o Azure Cloud Shell para registar um dispositivo simulado.

1. Executar o seguinte comando em Azure Cloud Shell para criar a identidade do dispositivo.

   **Seu NomeIoTHubName** : Substitua este espaço reservado abaixo pelo nome que escolheu para o seu hub IoT.

   **MyAndroidDevice:** Este é o nome do dispositivo que está a registar. Recomenda-se usar **MyAndroidDevice** como mostrado. Se escolher um nome diferente para o seu dispositivo, também terá de usar esse nome ao longo deste artigo e atualizar o nome do dispositivo nas aplicações da amostra antes de executá-los.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyAndroidDevice
    ```

2. Executar o seguinte comando em Azure Cloud Shell para obter a _cadeia de ligação_ do dispositivo para o dispositivo que acabou de registar:

    **Seu NomeIoTHubName** : Substitua este espaço reservado abaixo pelo nome que escolheu para o seu hub IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyAndroidDevice --output table
    ```

    Anote a cadeia de ligação do dispositivo, que se assemelha a:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}`

    Usará este valor mais tarde neste quickstart para enviar telemetria.

## <a name="send-simulated-telemetry"></a>Enviar telemetria simulada

1. Abra o projeto Android de amostra do GitHub no Android Studio. O projeto está localizado no seguinte diretório do seu repositório clonado ou descarregado da [amostra-iot-java:](https://github.com/Azure-Samples/azure-iot-samples-java) *\azure-iot-samples-java\iot-hub\Samples\device\AndroidSample* .

2. No Android Studio, abra *as propriedades gradle.properties* para o projeto de amostra e substitua o espaço reservado **Device_Connection_String** pela cadeia de ligação do dispositivo que fez uma nota de anterior.

    ```
    DeviceConnectionString=HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}
    ```

3. No Estúdio Android, clique em **File**  >  **Sync Project com Gradle Files** . Verifique se a construção está concluída.

   > [!NOTE]
   > Se a sincronização do projeto falhar, pode ser por uma das seguintes razões:
   >
   > * As versões do plugin Android Gradle e Gradle referenciadas no projeto estão desatualizadas para a sua versão do Android Studio. Siga [estas instruções](https://developer.android.com/studio/releases/gradle-plugin) para fazer referência e instalar as versões corretas do plugin e gradle para a sua instalação.
   > * O contrato de licença para o Android SDK não foi assinado. Siga as instruções na saída Build para assinar o contrato de licença e descarregar o SDK.

4. Uma vez concluída a **Run** construção, clique em  >  **'app'** Run Run . Configurar a aplicação para executar num dispositivo Android físico ou num emulador Android. Para obter mais informações sobre a execução de uma aplicação Android num dispositivo físico ou emulador, consulte [Executar a sua aplicação.](https://developer.android.com/training/basics/firstapp/running-app)

5. Uma vez que a aplicação carrega, clique no botão **Iniciar** para começar a enviar telemetria para o seu IoT Hub:

    ![Aplicação](media/quickstart-send-telemetry-android/sample-screenshot.png)


## <a name="read-the-telemetry-from-your-hub"></a>Ler a telemetria a partir do seu hub

Nesta secção, utilizará o Azure Cloud Shell com a [extensão IoT](/cli/azure/ext/azure-iot/iot?view=azure-cli-latest) para monitorizar as mensagens do dispositivo que são enviadas pelo dispositivo Android.

1. Com o Azure Cloud Shell, execute o seguinte comando para se ligar e ler mensagens do hub IoT:

   **Seu NomeIoTHubName** : Substitua este espaço reservado abaixo pelo nome que escolheu para o seu hub IoT.

    ```azurecli-interactive
    az iot hub monitor-events --hub-name {YourIoTHubName} --output table
    ```

    A imagem que se segue mostra a saída, uma vez que o hub IoT recebe telemetria enviada pelo dispositivo Android:

      ![Ler as mensagens do dispositivo com a CLI do Azure](media/quickstart-send-telemetry-android/read-data.png)
## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, você montou um hub IoT, registou um dispositivo, enviou telemetria simulada para o hub usando uma aplicação Android, e leu a telemetria do hub usando o Azure Cloud Shell.

Para saber como controlar o seu dispositivo simulado a partir de uma aplicação back-end, continue para o guia de início rápido seguinte.

> [!div class="nextstepaction"]
> [Guia de Início Rápido: controlar um dispositivo ligado a um hub IoT](quickstart-control-device-android.md)
