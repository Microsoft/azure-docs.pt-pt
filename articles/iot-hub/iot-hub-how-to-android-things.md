---
title: Desenvolva para plataforma Android Things usando Azure IoT SDKs | Microsoft Docs
description: Developer guide - Saiba como desenvolver-se em Android Things usando Azure IoT Hub SDKs.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: robinsh
ms.openlocfilehash: 6008ca1549629bcbb582f38de2ab1b453cd73a99
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94843762"
---
# <a name="develop-for-android-things-platform-using-azure-iot-sdks"></a>Desenvolver para plataforma Android Things usando Azure IoT SDKs

[Os Azure IoT Hub SDKs](./iot-hub-devguide-sdks.md) fornecem suporte de primeira linha para plataformas populares como Windows, Linux, OSX, MBED e plataformas móveis como Android e iOS.  Como parte do nosso compromisso de permitir uma maior escolha e flexibilidade nas implementações de IoT, o Java SDK também suporta a plataforma [Android Things.](https://developer.android.com/things/)  Os desenvolvedores podem aproveitar os benefícios do sistema operativo Android Things no lado do dispositivo, enquanto usam o [Azure IoT Hub](about-iot-hub.md) como o centro de mensagens central que escala para milhões de dispositivos simultaneamente conectados.

Este tutorial descreve os passos para construir uma aplicação lateral do dispositivo no Android Things usando o Azure IoT Java SDK.

## <a name="prerequisites"></a>Pré-requisitos

* Um Android Things suportava hardware com o Android Things OS em execução.  Pode seguir a [documentação do Android Things](https://developer.android.com/things/get-started/kits#flash-at) sobre como flash Android Things OS.  Certifique-se de que o seu dispositivo Android Things está ligado à internet com periféricos essenciais, como teclado, ecrã e rato ligados.  Este tutorial usa Raspberry Pi 3.

* Última versão do [Android Studio](https://developer.android.com/studio/)

* Última versão de [Git](https://git-scm.com/)

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registar um dispositivo

É necessário registar um dispositivo no hub IoT antes de o mesmo se poder ligar. Neste início rápido, vai utilizar o Azure Cloud Shell para registar um dispositivo simulado.

1. Execute os seguintes comandos no Azure Cloud Shell para adicionar a extensão da CLI do Hub IoT e para criar a identidade do dispositivo.

   **YourIoTHubName**: substitua o marcador de posição abaixo pelo nome que escolher para o seu hub IoT.

   **MyAndroidThingsDevice** : Este é o nome dado para o dispositivo registado. Use MyAndroidThingsDevice como mostrado. Se escolher um nome diferente para o seu dispositivo, também irá precisar de utilizar esse nome através deste artigo, e atualize o nome do dispositivo em aplicações de exemplo antes de as executar.

    ```azurecli-interactive
    az extension add --name azure-iot
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyAndroidThingsDevice
    ```

2. Execute os seguintes comandos em Azure Cloud Shell para obter a *cadeia de ligação* do dispositivo para o dispositivo que acabou de registar. Substitua `YourIoTHubName` abaixo pelo nome que escolher para o seu hub IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyAndroidThingsDevice --output table
    ```

    Anote a cadeia de ligação do dispositivo, que se assemelha a:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidThingsDevice;SharedAccessKey={YourSharedAccessKey}`

    Irá utilizar este valor mais adiante no guia de início rápido.

## <a name="building-an-android-things-application"></a>Construção de uma aplicação Android Things

1. O primeiro passo para a construção de uma aplicação Android Things é ligar-se aos seus dispositivos Android Things. Ligue o seu dispositivo Android Things a um ecrã e conecte-o à internet. Android Things fornece [documentação](https://developer.android.com/things/get-started/kits) sobre como ligar-se ao Wi-Fi. Depois de ter ligado à internet, tome nota do endereço IP listado nas Redes.

2. Utilize a ferramenta [adb](https://developer.android.com/studio/command-line/adb) para ligar ao seu dispositivo Android Things com o endereço IP acima indicado. Verifique duas vezes a ligação utilizando este comando a partir do seu terminal. Deve ver os seus dispositivos listados como "conectados".

   ```
   adb devices
   ```

3. Descarregue a nossa amostra para Android/Android Things a partir deste [repositório](https://github.com/Azure-Samples/azure-iot-samples-java) ou use Git.

   ```
   git clone https://github.com/Azure-Samples/azure-iot-samples-java.git
   ```

4. No Android Studio, abra o Android Project em "\azure-iot-samples-java\iot-hub\Samples\device\AndroidSample".

5. Abra o ficheiro gradle.properties e substitua "Device_connection_string" pela cadeia de ligação do dispositivo anteriormente anotada.
 
6. Clique em Executar - Debug e selecione o seu dispositivo para implementar este código nos seus dispositivos Android Things.

7. Quando a aplicação é iniciada com sucesso, pode ver uma aplicação a correr no seu dispositivo Android Things. Esta aplicação de amostra envia leituras de temperatura geradas aleatoriamente.

## <a name="read-the-telemetry-from-your-hub"></a>Ler a telemetria a partir do seu hub

Pode ver os dados através do seu hub IoT à medida que estes são recebidos. A extensão da CLI do Hub IoT pode ligar ao ponto final de **Eventos** do lado do serviço no seu Hub IoT. A extensão recebe as mensagens do dispositivo para a cloud enviadas a partir do seu dispositivo simulado. Uma aplicação back-end do Hub IoT é normalmente executada na cloud para receber e processar mensagens do dispositivo para a cloud.

Execute os seguintes comandos no Azure Cloud Shell, ao substituir `YourIoTHubName` pelo nome do hub IoT:

```azurecli-interactive
az iot hub monitor-events --device-id MyAndroidThingsDevice --hub-name YourIoTHubName
```

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Passos seguintes

* Saiba [como gerir a conectividade e mensagens fiáveis](iot-hub-reliability-features-in-sdks.md) usando os IoT Hub SDKs.
* Saiba como [se desenvolver para plataformas móveis](iot-hub-how-to-develop-for-mobile-devices.md) como iOS e Android.
* [Suporte à plataforma Azure IoT SDK](iot-hub-device-sdk-platform-support.md)