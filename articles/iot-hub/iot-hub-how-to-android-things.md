---
title: Desenvolver para a plataforma de itens do Android usando SDKs do Azure IoT | Microsoft Docs
description: Guia do desenvolvedor-saiba mais sobre como desenvolver itens do Android usando SDKs do Hub IoT do Azure.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: robinsh
ms.openlocfilehash: 82f6da54aec7aee94c19fd75a06d2850ca0db8b6
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68883118"
---
# <a name="develop-for-android-things-platform-using-azure-iot-sdks"></a>Desenvolver para a plataforma de itens do Android usando SDKs de IoT do Azure

Os [SDKs do Hub IOT do Azure](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks) fornecem suporte de primeira camada para plataformas populares, como Windows, Linux, OSX, MBED e plataformas móveis, como Android e Ios.  Como parte do nosso compromisso de permitir maior escolha e flexibilidade em implantações de IoT, o SDK do Java também dá suporte à plataforma de [coisas do Android](https://developer.android.com/things/) .  Os desenvolvedores podem aproveitar os benefícios do sistema operacional de coisas do Android no lado do dispositivo, ao usar o [Hub IOT do Azure](about-iot-hub.md) como o Hub de mensagens central que pode ser dimensionado para milhões de dispositivos conectados simultaneamente.

Este tutorial descreve as etapas para criar um aplicativo do lado do dispositivo em itens do Android usando o SDK do Java do Azure IoT.

## <a name="prerequisites"></a>Pré-requisitos

* Um Android tem suporte para hardware com sistema operacional de coisas Android em execução.  Você pode seguir a [documentação de coisas do Android](https://developer.android.com/things/get-started/kits#flash-at) sobre como atualizar o so de coisas do Android.  Verifique se o dispositivo de coisas do Android está conectado à Internet com periféricos essenciais, como teclado, exibição e mouse anexados.  Este tutorial usa o Raspberry Pi 3.

* Versão mais recente do [Android Studio](https://developer.android.com/studio/)

* Versão mais recente do [git](https://git-scm.com/)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registar um dispositivo

É necessário registar um dispositivo no hub IoT antes de o mesmo se poder ligar. Neste início rápido, vai utilizar o Azure Cloud Shell para registar um dispositivo simulado.

1. Execute os seguintes comandos no Azure Cloud Shell para adicionar a extensão da CLI do Hub IoT e para criar a identidade do dispositivo.

   **Nomedoseuhubiot** : Substitua esse espaço reservado abaixo pelo nome que você escolher para o Hub IoT.

   **MyAndroidThingsDevice** : Este é o nome fornecido para o dispositivo registrado. Use MyAndroidThingsDevice conforme mostrado. Se escolher um nome diferente para o seu dispositivo, também irá precisar de utilizar esse nome através deste artigo, e atualize o nome do dispositivo em aplicações de exemplo antes de as executar.

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyAndroidThingsDevice
    ```

2. Execute os seguintes comandos no Azure Cloud Shell para obter a *cadeia de conexão do dispositivo* para o dispositivo que você acabou de registrar. Substitua `YourIoTHubName` abaixo pelo nome que você escolher para o Hub IOT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyAndroidThingsDevice --output table
    ```

    Anote a cadeia de ligação do dispositivo, que se parece com:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidThingsDevice;SharedAccessKey={YourSharedAccessKey}`

    Irá utilizar este valor mais adiante no guia de início rápido.

## <a name="building-an-android-things-application"></a>Criando um aplicativo de coisas do Android

1. A primeira etapa para criar um aplicativo de coisas do Android está se conectando aos dispositivos de itens do Android. Conecte seu dispositivo de coisas do Android a uma exibição e conecte-o à Internet. As coisas do Android fornecem [documentação](https://developer.android.com/things/get-started/kits) sobre como se conectar ao wifi. Depois de se conectar à Internet, anote o endereço IP listado em redes.

2. Use a ferramenta [ADB](https://developer.android.com/studio/command-line/adb) para se conectar ao dispositivo de coisas do Android com o endereço IP indicado acima. Verifique a conexão novamente usando este comando do seu terminal. Você deve ver seus dispositivos listados como "conectado".

   ```
   adb devices
   ```

3. Baixe nosso exemplo para coisas Android/Android deste [repositório](https://github.com/Azure-Samples/azure-iot-samples-java) ou use o git.

   ```
   git clone https://github.com/Azure-Samples/azure-iot-samples-java.git
   ```

4. No Android Studio, abra o projeto do Android no localizado em "\azure-iot-samples-java\iot-hub\Samples\device\AndroidSample".

5. Abra o arquivo gradle. Properties e substitua "Device_connection_string" pela cadeia de conexão do dispositivo observada anteriormente.
 
6. Clique em Run-Debug e selecione seu dispositivo para implantar esse código em seus dispositivos Android de itens.

7. Quando o aplicativo for iniciado com êxito, você poderá ver um aplicativo em execução no dispositivo de coisas do Android. Este aplicativo de exemplo envia leituras de temperatura geradas aleatoriamente.

## <a name="read-the-telemetry-from-your-hub"></a>Ler a telemetria a partir do seu hub

Você pode exibir os dados por meio do Hub IoT conforme ele é recebido. A extensão da CLI do Hub IoT pode ligar ao ponto final de **Eventos** do lado do serviço no seu Hub IoT. A extensão recebe as mensagens do dispositivo para a cloud enviadas a partir do seu dispositivo simulado. Uma aplicação back-end do Hub IoT é normalmente executada na cloud para receber e processar mensagens do dispositivo para a cloud.

Execute os seguintes comandos no Azure Cloud Shell, ao substituir `YourIoTHubName` pelo nome do hub IoT:

```azurecli-interactive
az iot hub monitor-events --device-id MyAndroidThingsDevice --hub-name YourIoTHubName
```

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre [como gerenciar a conectividade e as mensagens confiáveis](iot-hub-reliability-features-in-sdks.md) usando os SDKs do Hub IOT.
* Saiba mais sobre como [desenvolver para plataformas móveis](iot-hub-how-to-develop-for-mobile-devices.md) , como Ios e Android.
* [Suporte à plataforma SDK do Azure IoT](iot-hub-device-sdk-platform-support.md)
