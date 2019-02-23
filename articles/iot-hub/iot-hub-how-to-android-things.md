---
title: Desenvolver para a plataforma Android coisas com os SDKs do Azure | Documentos da Microsoft
description: Desenvolvedor orientá - Saiba mais sobre como programar no Android coisas através de SDKs do Azure IoT Hub.
author: yzhong94
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 1/30/2019
ms.author: yizhon
ms.openlocfilehash: e5b075a457c646eb03810b2c51af1a1181ee96ed
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56670267"
---
# <a name="develop-for-android-things-platform-using-azure-iot-sdks"></a>Desenvolver para a plataforma Android coisas com os SDKs do Azure
[SDKs do IoT Hub do Azure](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks) fornecem suporte de escalão primeiro para plataformas populares, como Windows, Linux, OSX, MBED e plataformas móveis, como Android e iOS.  Como parte do nosso compromisso para permitir maior escolha e flexibilidade nas implementações do IoT, o SDK do Java também suporta [coisas Android](https://developer.android.com/things/) plataforma.  Os desenvolvedores podem aproveitar os benefícios do sistema de operativo Android coisas no lado do dispositivo, ao utilizar [IoT Hub do Azure](https://docs.microsoft.com/azure/iot-hub/about-iot-hub) como a mensagem central hub é dimensionado em simultâneo para milhões de dispositivos de conexão. 

Este tutorial descreve os passos para criar uma aplicação do lado de dispositivo no Android coisas com o SDK de Java do Azure IoT.

## <a name="prerequisites"></a>Pré-requisitos
* Um coisas Android suportado hardware com o Android de coisas de sistema operacional em execução.  Pode seguir [documentação de coisas Android](https://developer.android.com/things/get-started/kits#flash-at) sobre como flash SO Android de coisas.  Certifique-se de que o seu dispositivo Android coisas está ligado à internet com periféricos essenciais, como o teclado, exibição e mouse anexado.  Este tutorial utiliza o Raspberry Pi 3.
* Versão mais recente do [Android Studio](https://developer.android.com/studio/)
* Versão mais recente do [Git](https://git-scm.com/)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registar um dispositivo

É necessário registar um dispositivo no hub IoT antes de o mesmo se poder ligar. Neste início rápido, vai utilizar o Azure Cloud Shell para registar um dispositivo simulado.

1. Execute os seguintes comandos no Azure Cloud Shell para adicionar a extensão da CLI do Hub IoT e para criar a identidade do dispositivo. 

   **YourIoTHubName** : Substitua este marcador de posição abaixo com o nome que escolher para o seu hub IoT.

   **MyAndroidThingsDevice** : Este é o nome fornecido para o dispositivo registado. Utilize MyAndroidThingsDevice, conforme mostrado. Se escolher um nome diferente para o seu dispositivo, também irá precisar de utilizar esse nome através deste artigo, e atualize o nome do dispositivo em aplicações de exemplo antes de as executar.

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyAndroidThingsDevice
    ```

2. Execute o seguinte comando no Azure Cloud Shell para obter a _cadeia de ligação do dispositivo_ que acabou de registar:  **YourIoTHubName** : Substitua este marcador de posição abaixo com o nome que escolher para o seu hub IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyAndroidThingsDevice --output table
    ```

    Anote a cadeia de ligação do dispositivo, que se parece com:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidThingsDevice;SharedAccessKey={YourSharedAccessKey}`

    Irá utilizar este valor mais adiante no guia de início rápido.

## <a name="building-an-android-things-application"></a>Criando um aplicativo de coisas Android
1.  O primeiro passo para criar um aplicativo de coisas Android está a ligar aos seus dispositivos Android coisas.  Ligue o seu dispositivo Android coisas para um ecrã e ligá-la à internet.  Android coisas fornecem [documentação](https://developer.android.com/things/get-started/kits) sobre como ligar ao Wi-Fi.  Depois de ligar à internet, tome nota do endereço IP listado em redes.
2.  Utilize o [adb](https://developer.android.com/studio/command-line/adb) ferramenta para ligar ao seu dispositivo Android coisas com o endereço IP indicado acima.  Verifique novamente a ligação ao utilizar este comando no seu terminal.  Deverá ver os dispositivos listados como "ligado"
    ```
    adb devices
    ```
3.  Baixe o nosso exemplo para Android/Android coisas nesta [repositório](https://github.com/Azure-Samples/azure-iot-samples-java) ou utilize o Git.
    ```
    git clone https://github.com/Azure-Samples/azure-iot-samples-java.git
    ```
4.  No Android Studio, abra o projeto Android em localizado em "\azure-iot-samples-java\iot-hub\Samples\device\AndroidSample".
5.  Abra o ficheiro de gradle.properties e substitua "Device_connection_string" com a cadeia de ligação do dispositivo com o indicado anteriormente.
6.  Clique na execução - depurar e selecione o seu dispositivo para implementar esse código para os seus dispositivos Android coisas.
7.  Quando o aplicativo é iniciado com êxito, pode ver uma aplicação em execução no seu dispositivo Android coisas.  Este aplicativo de exemplo envia leituras de temperatura gerado aleatoriamente.

## <a name="read-the-telemetry-from-your-hub"></a>Ler a telemetria a partir do seu hub

Pode ver os dados através do IoT hub que for recebida. A extensão da CLI do Hub IoT pode ligar ao ponto final de **Eventos** do lado do serviço no seu Hub IoT. A extensão recebe as mensagens do dispositivo para a cloud enviadas a partir do seu dispositivo simulado. Uma aplicação back-end do Hub IoT é normalmente executada na cloud para receber e processar mensagens do dispositivo para a cloud.

Execute os seguintes comandos no Azure Cloud Shell, ao substituir `YourIoTHubName` pelo nome do hub IoT:

```
azurecli-interactive
az iot hub monitor-events --device-id MyAndroidThingsDevice --hub-name YourIoTHubName
```

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre [como gerir o sistema de mensagens confiável e conectividade](iot-hub-reliability-features-in-sdks.md) com os SDKs do IoT Hub.
* Saiba mais sobre como [desenvolver para plataformas móveis](iot-hub-how-to-develop-for-mobile-devices.md) como iOS e Android.
* [Suporte de plataforma do SDK do IoT do Azure](iot-hub-device-sdk-platform-support.md)
