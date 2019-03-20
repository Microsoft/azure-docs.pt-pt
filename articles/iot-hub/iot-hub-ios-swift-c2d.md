---
title: Mensagens de cloud para o dispositivo com o IoT Hub do Azure (iOS) | Documentos da Microsoft
description: Como enviar mensagens da cloud para dispositivo a um dispositivo de um hub de IoT do Azure com o Azure IoT SDKs para iOS.
author: kgremban
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/19/2018
ms.author: kgremban
ms.openlocfilehash: e7e8d12af92a566753d8f3d7baf5019bae44de2c
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58163522"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-ios"></a>Enviar mensagens da cloud para o dispositivo com o IoT Hub (iOS)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

O IoT Hub do Azure é um serviço totalmente gerido que ajuda a habilitar comunicações bidirecionais fiáveis e seguras entre milhões de dispositivos e uma solução de back-end. O [enviar telemetria a partir de um dispositivo para um hub IoT](quickstart-send-telemetry-ios.md) artigo mostra como criar um hub IoT, aprovisionar uma identidade de dispositivo no mesmo e código de uma aplicação de dispositivo simulado que envia mensagens do dispositivo para a cloud.

Este artigo mostra-lhe como para:

* Da sua solução back-end, envie mensagens de cloud-para-dispositivo para um único dispositivo através do IoT Hub.

* Receba mensagens da cloud para o dispositivo num dispositivo.

* Da sua solução back-end, pedir confirmação de entrega (*comentários*) para mensagens enviadas para um dispositivo do IoT Hub.

Pode encontrar mais informações sobre mensagens de cloud para o dispositivo na [secção do Guia do programador do IoT Hub de mensagens](iot-hub-devguide-messaging.md).

No final deste artigo, executa Swift dois projetos de iOS:

* **dispositivo de exemplo**, a mesma aplicação que criou no [enviar telemetria a partir de um dispositivo para um hub IoT](quickstart-send-telemetry-ios.md), que liga ao seu hub IoT e recebe mensagens de cloud-para-dispositivo.

* **serviço de exemplo**, que envia uma mensagem de cloud-para-dispositivo para a aplicação de dispositivo simulado através do IoT Hub e, em seguida, recebe a confirmação de entrega.

> [!NOTE]
> IoT Hub tem suporte SDK para muitas plataformas de dispositivos e linguagens (incluindo C, Java e Javascript) através de SDKs de dispositivo do IoT do Azure. Para obter instruções passo a passo sobre como ligar o seu dispositivo ao código do tutorial e, em geral, hub IoT do Azure, consulte a [Centro de programadores de IoT do Azure](https://www.azure.com/develop/iot).

Para concluir este tutorial, precisa do seguinte:

- Uma conta ativa do Azure. (Se não tiver uma conta, pode criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.)

- Um hub IoT Active Directory no Azure. 

- O exemplo de código a partir [exemplos do Azure](https://github.com/Azure-Samples/azure-iot-samples-ios/archive/master.zip) .

- A versão mais recente do [XCode](https://developer.apple.com/xcode/), com a última versão do SDK do iOS. Este início rápido foi testado com XCode 9.3 e o iOS 11.3.

- A versão mais recente do [CocoaPods](https://guides.cocoapods.org/using/getting-started.html).

## <a name="simulate-an-iot-device"></a>Simular um dispositivo de IoT

Nesta secção, simular um dispositivo iOS a executar uma aplicação Swift para receber mensagens da cloud para dispositivo do IoT hub. 

Este é o dispositivo de exemplo que criar no artigo [enviar telemetria a partir de um dispositivo para um hub IoT](quickstart-send-telemetry-ios.md). Se já tiver que executar, pode ignorar esta secção.

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

### <a name="run-the-sample-device-application"></a>Executar a aplicação de dispositivo de exemplo 

1. Obter a cadeia de ligação para o seu dispositivo. Pode copiar essa cadeia de caracteres a partir da [portal do Azure](https://portal.azure.com) no painel de detalhes do dispositivo, ou recuperá-la com o seguinte comando da CLI: 

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id {YourDeviceID} --output table
    ```

1. Abra a área de trabalho de exemplo no XCode.

   ```sh
   open "MQTT Client Sample.xcworkspace"
   ```

2. Expanda a **MQTT Client Sample** projeto e, em seguida, pasta, o mesmo nome.  

3. Abra **ViewController.swift** para edição no XCode. 

4. Procure o **connectionString** variável e atualize o valor com a ligação do dispositivo de cadeia que copiou no primeiro passo.

5. Guarde as alterações. 

6. Execute o projeto no emulador do dispositivo com o botão **Build and run** (Compilar e executar) ou a combinação de teclas **comando + r**. 

   ![Executar o projeto](media/iot-hub-ios-swift-c2d/run-sample.png)

## <a name="simulate-a-service-device"></a>Simular um dispositivo de serviço

Nesta secção, simular um segundo dispositivo iOS com uma aplicação Swift que envia mensagens de cloud para o dispositivo através do hub IoT. Esta configuração é útil para cenários de IoT onde existe um iPhone ou iPad a funcionar como um controlador para outros dispositivos iOS liguem a um hub IoT. 

### <a name="install-cocoapods"></a>Instalar CocoaPods

O CocoaPods gere dependências para projetos de iOS que utilizam bibliotecas de terceiros.

Navegue para a pasta de exemplos do Azure IoT iOS que transferiu nos pré-requisitos. Em seguida, navegue para o projeto de serviço de exemplo:

```sh
cd quickstart/sample-service
```

Confirme que XCode está fechado e execute o seguinte comando para instalar os CocoaPods que são declarados no ficheiro **podfile**:

```sh
pod install
```

Para além de instalar os pods necessários para o projeto, o comando de instalação também criou um ficheiro de área de trabalho do XCode, que já está configurado para utilizar os pods para dependências.

### <a name="run-the-sample-service-application"></a>Execute o exemplo de aplicação de serviço

1. Obter a cadeia de ligação de serviço para o seu hub IoT. Pode copiar essa cadeia de caracteres do [portal do Azure](https://portal.azure.com) partir a **iothubowner** política no **políticas de acesso partilhado** painel, ou recuperá-la com o seguinte comando da CLI:  

    ```azurecli-interactive
    az iot hub show-connection-string --name {YourIoTHubName} --output table
    ```

2. Abra a área de trabalho de exemplo no XCode.

   ```sh
   open AzureIoTServiceSample.xcworkspace
   ```

3. Expanda a **AzureIoTServiceSample** do projeto e, em seguida, expanda a pasta o mesmo nome.  

4. Abra **ViewController.swift** para edição no XCode. 

5. Procure o **connectionString** variável e atualize o valor com a cadeia de ligação de serviço que copiou anteriormente.

6. Guarde as alterações. 

7. No Xcode, altere as definições do emulador para um dispositivo iOS diferentes que utilizou para executar o dispositivo de IoT. XCode não é possível executar vários emuladores do mesmo tipo. 

   ![Alterar o emulador de dispositivo](media/iot-hub-ios-swift-c2d/change-device.png)

8. Executar o projeto no emulador do dispositivo com o **compilar e executar** botão ou a combinação de teclas **comando + r**. 

   ![Executar o projeto](media/iot-hub-ios-swift-c2d/run-app.png)

## <a name="send-a-cloud-to-device-message"></a>Enviar uma mensagem de cloud-para-dispositivo

Está agora pronto para utilizar as duas aplicações para enviar e receber mensagens da cloud para o dispositivo.

1. Na **exemplo de aplicação do iOS** aplicação em execução no dispositivo de IoT simulado, clique em **iniciar**. O aplicativo começa a enviar mensagens dispositivo-para-cloud, mas também começa a ouvir as mensagens da cloud para o dispositivo. 

   ![Ver a aplicação de dispositivo de IoT de exemplo](media/iot-hub-ios-swift-c2d/view-d2c.png)

2. Na **exemplo de cliente do serviço de IoTHub** aplicação em execução no dispositivo simulado do serviço, introduza o ID para o dispositivo de IoT que pretende enviar uma mensagem. 

3. Gravar uma mensagem de texto sem formatação, em seguida, clique em **enviar**. 

    Várias ações acontecem assim que clicar em enviar. O exemplo de serviço envia a mensagem ao seu IoT hub, que a aplicação tem acesso a devido a ligação de serviço de cadeias de caracteres que fornecido. O hub IoT verifica o ID de dispositivo, envia a mensagem para o dispositivo de destino e envia um recibo de confirmação para o dispositivo de origem. A aplicação em execução no seu dispositivo de IoT simulado verifica a existência de mensagens do IoT Hub e imprime o texto da mais recente na tela.

    A saída deve ser semelhante ao seguinte exemplo:

   ![Ver mensagens de cloud-para-dispositivo](media/iot-hub-ios-swift-c2d/view-c2d.png)


## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, aprendeu a enviar e receber mensagens da cloud para o dispositivo. 

Para ver exemplos de soluções ponto-a-ponto completas que utilizam o IoT Hub, consulte a [Aceleradores de solução de IoT do Azure](https://azure.microsoft.com/documentation/suites/iot-suite/) documentação.

Para saber mais sobre o desenvolvimento de soluções com o IoT Hub, veja a [Guia do programador do IoT Hub](iot-hub-devguide.md).
