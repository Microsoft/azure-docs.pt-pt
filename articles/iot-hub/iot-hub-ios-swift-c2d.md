---
title: Mensagens da nuvem para o dispositivo com o Hub IoT do Azure (iOS) | Microsoft Docs
description: Como enviar mensagens da nuvem para o dispositivo para um dispositivo de um hub IoT do Azure usando os SDKs do IoT do Azure para iOS.
author: kgremban
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/19/2018
ms.author: kgremban
ms.openlocfilehash: 8c6013e5bda2bec1c8a09fd9f2434cdd477eed01
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68618517"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-ios"></a>Enviar mensagens da nuvem para o dispositivo com o Hub IoT (iOS)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

O Hub IoT do Azure é um serviço totalmente gerenciado que ajuda a habilitar comunicações bidirecionais confiáveis e seguras entre milhões de dispositivos e um back-end de solução. O guia de início rápido [Enviar telemetria de um dispositivo para um hub IOT](quickstart-send-telemetry-ios.md) mostra como criar um hub IOT, provisionar uma identidade do dispositivo nele e codificar um aplicativo de dispositivo simulado que envia mensagens do dispositivo para a nuvem.

Este tutorial mostrar-lhe como:

* De seu back-end da solução, envie mensagens da nuvem para o dispositivo para um único dispositivo por meio do Hub IoT.

* Receber mensagens da nuvem para o dispositivo em um dispositivo.

* Do seu back-end da solução, solicite a confirmação de entrega (*comentários*) para mensagens enviadas a um dispositivo do Hub IOT.

Você pode encontrar mais informações sobre mensagens da nuvem para o dispositivo na [seção mensagens do guia do desenvolvedor do Hub IOT](iot-hub-devguide-messaging.md).

No final deste artigo, você executará dois projetos do iOS Swift:

* **exemplo-dispositivo**, o mesmo aplicativo criado em [Enviar telemetria de um dispositivo para um hub IOT](quickstart-send-telemetry-ios.md), que se conecta ao seu hub IOT e recebe mensagens da nuvem para o dispositivo.

* **Sample-Service**, que envia uma mensagem da nuvem para o dispositivo para o aplicativo de dispositivo simulado por meio do Hub IOT e recebe sua confirmação de entrega.

> [!NOTE]
> O Hub IoT tem suporte do SDK para várias plataformas de dispositivo e linguagens (incluindo C, Java, Python e JavaScript) por meio de SDKs do dispositivo IoT do Azure. Para obter instruções detalhadas sobre como conectar seu dispositivo ao código deste tutorial e, em geral, ao Hub IoT do Azure, consulte o centro de [desenvolvedores do Azure IOT](https://www.azure.com/develop/iot).

Para concluir este tutorial, precisa do seguinte:

* Uma conta ativa do Azure. (Se você não tiver uma conta, poderá criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.)

* Um hub IoT ativo no Azure.

* O exemplo de código dos [exemplos do Azure](https://github.com/Azure-Samples/azure-iot-samples-ios/archive/master.zip).

* A versão mais recente do [XCode](https://developer.apple.com/xcode/), com a última versão do SDK do iOS. Este início rápido foi testado com XCode 9.3 e o iOS 11.3.

* A versão mais recente do [CocoaPods](https://guides.cocoapods.org/using/getting-started.html).

## <a name="simulate-an-iot-device"></a>Simular um dispositivo IoT

Nesta seção, você simula um dispositivo iOS executando um aplicativo Swift para receber mensagens da nuvem para o dispositivo do Hub IoT. 

Este é o dispositivo de exemplo que você cria no artigo [Enviar telemetria de um dispositivo para um hub IOT](quickstart-send-telemetry-ios.md). Se você já tiver essa execução, poderá ignorar esta seção.

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

### <a name="run-the-sample-device-application"></a>Executar o aplicativo de dispositivo de exemplo

1. Recupere a cadeia de conexão para seu dispositivo. Você pode copiar essa cadeia de caracteres do [portal do Azure](https://portal.azure.com) na folha detalhes do dispositivo ou recuperá-la com o seguinte comando da CLI:

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id {YourDeviceID} --output table
    ```

1. Abra a área de trabalho de exemplo no XCode.

   ```sh
   open "MQTT Client Sample.xcworkspace"
   ```

2. Expanda o projeto de **exemplo do cliente MQTT** e, em seguida, a pasta com o mesmo nome.  

3. Abra **ViewController.swift** para edição no XCode. 

4. Pesquise a variável **ConnectionString** e atualize o valor com a cadeia de conexão do dispositivo que você copiou na primeira etapa.

5. Guarde as alterações. 

6. Execute o projeto no emulador do dispositivo com o botão **Build and run** (Compilar e executar) ou a combinação de teclas **comando + r**.

   ![Executar o projeto](media/iot-hub-ios-swift-c2d/run-sample.png)

## <a name="get-the-iot-hub-connection-string"></a>Obter a cadeia de conexão do Hub IoT

Neste artigo, você cria um serviço de back-end para enviar mensagens da nuvem para o dispositivo por meio do Hub IoT criado em [Enviar telemetria de um dispositivo para um hub IOT](quickstart-send-telemetry-ios.md). Para enviar mensagens da nuvem para o dispositivo, seu serviço precisa da permissão de **conexão de serviço** . Por padrão, todo Hub IoT é criado com uma política de acesso compartilhado chamada **serviço** que concede essa permissão.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="simulate-a-service-device"></a>Simular um dispositivo de serviço

Nesta seção, você simula um segundo dispositivo iOS com um aplicativo Swift que envia mensagens da nuvem para o dispositivo por meio do Hub IoT. Essa configuração é útil para cenários de IoT em que há um iPhone ou iPad funcionando como um controlador para outros dispositivos iOS conectados a um hub IoT.

### <a name="install-cocoapods"></a>Instalar CocoaPods

O CocoaPods gere dependências para projetos de iOS que utilizam bibliotecas de terceiros.

Navegue até a pasta de exemplos do iOS do Azure IoT que você baixou nos pré-requisitos. Em seguida, navegue até o projeto de serviço de exemplo:

```sh
cd quickstart/sample-service
```

Confirme que XCode está fechado e execute o seguinte comando para instalar os CocoaPods que são declarados no ficheiro **podfile**:

```sh
pod install
```

Para além de instalar os pods necessários para o projeto, o comando de instalação também criou um ficheiro de área de trabalho do XCode, que já está configurado para utilizar os pods para dependências.

### <a name="run-the-sample-service-application"></a>Executar o aplicativo de serviço de exemplo

1. Abra a área de trabalho de exemplo no XCode.

   ```sh
   open AzureIoTServiceSample.xcworkspace
   ```

2. Expanda o projeto **AzureIoTServiceSample** e expanda a pasta com o mesmo nome.  

3. Abra **ViewController.swift** para edição no XCode. 

4. Pesquise a variável **ConnectionString** e atualize o valor com a cadeia de conexão de serviço que você copiou anteriormente em [obter a cadeia de conexão do Hub IOT](#get-the-iot-hub-connection-string).

5. Guarde as alterações.

6. No Xcode, altere as configurações do emulador para um dispositivo iOS diferente do usado para executar o dispositivo IoT. O XCode não pode executar vários emuladores do mesmo tipo.

   ![Alterar o dispositivo do emulador](media/iot-hub-ios-swift-c2d/change-device.png)

7. Execute o projeto no Device Emulator com o botão de **compilação e execução** ou o comando de combinação de teclas **+ r**.

   ![Executar o projeto](media/iot-hub-ios-swift-c2d/run-app.png)

## <a name="send-a-cloud-to-device-message"></a>Enviar uma mensagem da nuvem para o dispositivo

Agora você está pronto para usar os dois aplicativos para enviar e receber mensagens da nuvem para o dispositivo.

1. No aplicativo de **exemplo aplicativo IOS** em execução no dispositivo IOT simulado, clique em **Iniciar**. O aplicativo começa a enviar mensagens do dispositivo para a nuvem, mas também começa a escutar mensagens da nuvem para o dispositivo.

   ![Exibir aplicativo de dispositivo IoT de exemplo](media/iot-hub-ios-swift-c2d/view-d2c.png)

2. No aplicativo de **exemplo de cliente de serviço do IoTHub** em execução no dispositivo de serviço simulado, insira a ID do dispositivo IOT para o qual você deseja enviar uma mensagem. 

3. Escreva uma mensagem de texto sem formatação e clique em **Enviar**.

    Várias ações acontecem assim que você clica em enviar. O exemplo de serviço envia a mensagem para o Hub IoT, à qual o aplicativo tem acesso devido à cadeia de conexão de serviço que você forneceu. O Hub IoT verifica a ID do dispositivo, envia a mensagem para o dispositivo de destino e envia um recebimento de confirmação para o dispositivo de origem. O aplicativo em execução no dispositivo IoT simulado verifica mensagens do Hub IoT e imprime o texto do mais recente na tela.

    A saída deve ser semelhante ao exemplo a seguir:

   ![Exibir mensagens da nuvem para o dispositivo](media/iot-hub-ios-swift-c2d/view-c2d.png)

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, você aprendeu a enviar e receber mensagens da nuvem para o dispositivo.

Para ver exemplos de soluções completas de ponta a ponta que usam o Hub IoT, consulte a documentação dos aceleradores de [soluções do Azure IOT](https://azure.microsoft.com/documentation/suites/iot-suite/) .

Para saber mais sobre como desenvolver soluções com o Hub IoT, confira o [Guia do desenvolvedor do Hub IOT](iot-hub-devguide.md).
