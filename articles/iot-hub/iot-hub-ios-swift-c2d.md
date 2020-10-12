---
title: Mensagens em nuvem-para-dispositivo com Azure IoT Hub (iOS) Microsoft Docs
description: Como enviar mensagens nuvem-para-dispositivo para um dispositivo a partir de um hub Azure IoT usando os SDKs Azure IoT para iOS.
author: kgremban
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/19/2018
ms.author: kgremban
ms.custom: mqtt
ms.openlocfilehash: d8552391e8e8c389a44174595305b8f28224a833
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "81732538"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-ios"></a>Envie mensagens em nuvem para dispositivo com IoT Hub (iOS)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

O Azure IoT Hub é um serviço totalmente gerido que ajuda a permitir comunicações biducionais fiáveis e seguras entre milhões de dispositivos e uma solução traseira. O [envio de telemetria de um dispositivo para um quickstart do hub IoT](quickstart-send-telemetry-ios.md) mostra como criar um hub IoT, fornecê-lo e codificar uma aplicação de dispositivo simulado que envia mensagens dispositivo-a-nuvem.

Este tutorial mostrar-lhe como:

* A partir da parte de trás da sua solução, envie mensagens nuvem-dispositivo para um único dispositivo através do IoT Hub.

* Receba mensagens nuvem-dispositivo num dispositivo.

* A partir da sua solução back end, solicite o reconhecimento de entrega *(feedback)* para mensagens enviadas para um dispositivo do IoT Hub.

Pode encontrar mais informações sobre mensagens nuvem-dispositivo na [secção de mensagens do guia de desenvolvedores do IoT Hub](iot-hub-devguide-messaging.md).

No final deste artigo, você dirige dois projetos Swift iOS:

* **dispositivo de amostra**, a mesma aplicação criada em [Enviar telemetria de um dispositivo para um hub IoT](quickstart-send-telemetry-ios.md), que se conecta ao seu hub IoT e recebe mensagens nuvem-dispositivo.

* **serviço de amostra**, que envia uma mensagem nuvem-para-dispositivo para a aplicação do dispositivo simulado através do IoT Hub, e depois recebe o seu reconhecimento de entrega.

> [!NOTE]
> O IoT Hub tem suporte SDK para muitas plataformas e idiomas de dispositivos (incluindo C, Java, Python e Javascript) através de SDKs de dispositivoS Azure IoT. Para obter instruções passo a passo sobre como ligar o seu dispositivo ao código deste tutorial, e geralmente ao Azure IoT Hub, consulte o [Azure IoT Developer Center](https://www.azure.com/develop/iot).

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta ativa do Azure. (Se não tiver uma conta, pode criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.)

* Um centro de IoT ativo em Azure.

* A amostra de código das amostras do [Azure.](https://github.com/Azure-Samples/azure-iot-samples-ios/archive/master.zip)

* A versão mais recente do [XCode](https://developer.apple.com/xcode/), com a última versão do SDK do iOS. Este início rápido foi testado com XCode 9.3 e o iOS 11.3.

* A versão mais recente do [CocoaPods](https://guides.cocoapods.org/using/getting-started.html).

* Certifique-se de que a porta 8883 está aberta na sua firewall. A amostra do dispositivo neste artigo utiliza o protocolo MQTT, que comunica sobre a porta 8883. Este porto pode ser bloqueado em alguns ambientes de rede corporativa e educacional. Para obter mais informações e formas de contornar esta questão, consulte [Connecting to IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="simulate-an-iot-device"></a>Simular um dispositivo IoT

Nesta secção, simula um dispositivo iOS que executa uma aplicação Swift para receber mensagens nuvem-dispositivo do hub IoT. 

Este é o dispositivo de amostra que cria no artigo Enviar por email o artigo [Telemetria de um dispositivo para um hub IoT](quickstart-send-telemetry-ios.md). Se já tem isso em funcionamento, pode saltar esta secção.

### <a name="install-cocoapods"></a>Instalar CocoaPods

O CocoaPods gere dependências para projetos de iOS que utilizam bibliotecas de terceiros.

Numa janela de terminal, navegue para a pasta Azure-IoT-Samples-iOS que transferiu nos pré-requisitos. Depois, navegue para o projeto de exemplo:

```sh
cd quickstart/sample-device
```

Confirme que XCode está fechado e execute o seguinte comando para instalar os CocoaPods que são declarados no ficheiro **podfile**:

```sh
pod install
```

Para além de instalar os pods necessários para o projeto, o comando de instalação também criou um ficheiro de área de trabalho do XCode, que já está configurado para utilizar os pods para dependências.

### <a name="run-the-sample-device-application"></a>Executar a aplicação do dispositivo de amostra

1. Recupere a cadeia de ligação para o seu dispositivo. Pode copiar esta cadeia a partir do [portal Azure](https://portal.azure.com) na lâmina de detalhes do dispositivo, ou recuperá-la com o seguinte comando CLI:

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id {YourDeviceID} --output table
    ```

1. Abra a área de trabalho de exemplo no XCode.

   ```sh
   open "MQTT Client Sample.xcworkspace"
   ```

2. Expandir o projeto **MQTT Client Sample** e, em seguida, pasta com o mesmo nome.  

3. Abra **ViewController.swift** para edição no XCode. 

4. Procure a **variável de ligação E** atualize o valor com a cadeia de ligação do dispositivo que copiou no primeiro passo.

5. Guarde as alterações. 

6. Execute o projeto no emulador do dispositivo com o botão **Build and run** (Compilar e executar) ou a combinação de teclas **comando + r**.

   ![Executar o projeto](media/iot-hub-ios-swift-c2d/run-sample.png)

## <a name="get-the-iot-hub-connection-string"></a>Obtenha a cadeia de conexão do hub IoT

Neste artigo cria-se um serviço de backend para enviar mensagens nuvem-a-dispositivo através do hub IoT que criou [Enviar por email o artigo Telemetria para um hub IoT](quickstart-send-telemetry-ios.md). Para enviar mensagens nuvem-para-dispositivo, o seu serviço necessita da permissão **de ligação de serviço.** Por padrão, cada IoT Hub é criado com uma política de acesso compartilhado chamada **serviço** que concede esta permissão.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="simulate-a-service-device"></a>Simular um dispositivo de serviço

Nesta secção, simula-se um segundo dispositivo iOS com uma aplicação Swift que envia mensagens nuvem-para-dispositivo através do hub IoT. Esta configuração é útil para cenários IoT onde existe um iPhone ou iPad funcionando como um controlador para outros dispositivos iOS ligados a um hub IoT.

### <a name="install-cocoapods"></a>Instalar CocoaPods

O CocoaPods gere dependências para projetos de iOS que utilizam bibliotecas de terceiros.

Navegue para a pasta Azure IoT iOS Samples que descarregou nos pré-requisitos. Em seguida, navegue para o projeto de serviço de amostra:

```sh
cd quickstart/sample-service
```

Confirme que XCode está fechado e execute o seguinte comando para instalar os CocoaPods que são declarados no ficheiro **podfile**:

```sh
pod install
```

Para além de instalar os pods necessários para o projeto, o comando de instalação também criou um ficheiro de área de trabalho do XCode, que já está configurado para utilizar os pods para dependências.

### <a name="run-the-sample-service-application"></a>Executar a aplicação de serviço de amostra

1. Abra a área de trabalho de exemplo no XCode.

   ```sh
   open AzureIoTServiceSample.xcworkspace
   ```

2. Expanda o projeto **AzureIoTServiceSample** e, em seguida, expanda a pasta com o mesmo nome.  

3. Abra **ViewController.swift** para edição no XCode. 

4. Procure a **variável de ligaçãoDesso** e atualize o valor com a cadeia de ligação de serviço que copiou anteriormente na [cadeia de ligação do hub IoT](#get-the-iot-hub-connection-string).

5. Guarde as alterações.

6. No Xcode, altere as definições do emulador para um dispositivo iOS diferente do utilizado para executar o dispositivo IoT. O XCode não pode executar vários emuladores do mesmo tipo.

   ![Mude o dispositivo emulador](media/iot-hub-ios-swift-c2d/change-device.png)

7. Executar o projeto no emulador do dispositivo com o botão **Construir e executar** ou o comando de combinação de tecla + **r**.

   ![Executar o projeto](media/iot-hub-ios-swift-c2d/run-app.png)

## <a name="send-a-cloud-to-device-message"></a>Envie uma mensagem nuvem-a-dispositivo

Está agora pronto a utilizar as duas aplicações para enviar e receber mensagens cloud-to-device.

1. Na aplicação **iOS App Sample** em execução no dispositivo IoT simulado, clique em **Iniciar**. A aplicação começa a enviar mensagens de dispositivo para nuvem, mas também começa a ouvir mensagens cloud-to-device.

   ![Ver aplicação de dispositivo IoT de amostra](media/iot-hub-ios-swift-c2d/view-d2c.png)

2. Na aplicação **IoTHub Service Client Sample** em execução no dispositivo de serviço simulado, insira o ID para o dispositivo IoT para o que pretende enviar uma mensagem. 

3. Escreva uma mensagem de texto simples e, em seguida, clique em **Enviar**.

    Várias ações acontecem assim que clica em enviar. A amostra de serviço envia a mensagem para o seu hub IoT, a que a aplicação tem acesso devido à cadeia de ligação de serviço que forneceu. O seu hub IoT verifica o ID do dispositivo, envia a mensagem para o dispositivo de destino e envia um recibo de confirmação para o dispositivo de origem. A aplicação que está a funcionar no seu dispositivo IoT simulado verifica as mensagens do IoT Hub e imprime o texto a partir do mais recente no ecrã.

    A sua saída deve parecer o seguinte exemplo:

   ![Ver mensagens nuvem-a-dispositivo](media/iot-hub-ios-swift-c2d/view-c2d.png)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a enviar e receber mensagens cloud-to-device.

Para ver exemplos de soluções completas de ponta a ponta que utilizam o Hub IoT, consulte a documentação [Azure IoT Solution Accelerators.](https://azure.microsoft.com/documentation/suites/iot-suite/)

Para saber mais sobre o desenvolvimento de soluções com o IoT Hub, consulte o guia de desenvolvimento do [IoT Hub.](iot-hub-devguide.md)
