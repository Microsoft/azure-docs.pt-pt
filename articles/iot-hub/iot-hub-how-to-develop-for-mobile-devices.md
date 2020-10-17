---
title: Desenvolver para dispositivos móveis usando Azure IoT SDKs Microsoft Docs
description: Guia do desenvolvedor - Saiba como desenvolver-se para dispositivos móveis usando Azure IoT Hub SDKs.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/16/2018
ms.author: robinsh
ms.openlocfilehash: 63fade17d10189cbe8e775b2a859902f1c2bb004
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92151637"
---
# <a name="develop-for-mobile-devices-using-azure-iot-sdks"></a>Desenvolver para dispositivos móveis usando Azure IoT SDKs

As coisas na Internet das Coisas podem referir-se a uma vasta gama de dispositivos com capacidade variada: sensores, microcontroladores, dispositivos inteligentes, gateways industriais e até dispositivos móveis.  Um dispositivo móvel pode ser um dispositivo IoT, onde está a enviar telemetria de dispositivo para nuvem e gerido pela nuvem.  Também pode ser o dispositivo que executa uma aplicação de serviço back-end, que gere outros dispositivos IoT.  Em ambos os casos, [os SDKs Azure IoT Hub](./iot-hub-devguide-sdks.md) podem ser usados para desenvolver aplicações que funcionam para dispositivos móveis.  

## <a name="develop-for-native-ios-platform"></a>Desenvolver para plataforma nativa iOS

Os SDKs Azure IoT Hub fornecem suporte nativo da plataforma iOS através do Azure IoT Hub C SDK.  Pode pensar nisso como um IOS SDK que pode incorporar no seu projeto Swift ou Objetive C XCode.  Existem duas formas de utilizar o C SDK no iOS:

* Utilize diretamente as bibliotecas De Cacau no projeto XCode.  
* Descarregue o código fonte para C SDK e construa para plataforma iOS seguindo as [instruções de construção](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) do MacOS.  

Azure IoT Hub C SDK está escrito em C99 para portabilidade máxima para várias plataformas.  O processo de porção envolve a escrita de uma camada fina de adoção para os componentes específicos da plataforma, que podem ser encontrados aqui para [iOS.](https://github.com/Azure/azure-c-shared-utility/tree/master/pal/ios-osx)  As funcionalidades do C SDK podem ser alavancadas na plataforma iOS, incluindo os primitivos Azure IoT Hub suportados e funcionalidades específicas do SDK, como a política de rejulgar a fiabilidade da rede.  A interface para iOS SDK também é semelhante à interface para Azure IoT Hub C SDK.  

Estas documentações percorrem a forma de desenvolver uma aplicação de dispositivo ou aplicação de serviço num dispositivo iOS:

* [Guia de Início Rápido: Enviar telemetria a partir de um dispositivo para um hub IoT](quickstart-send-telemetry-ios.md)  
* [Envie mensagens da nuvem para o seu dispositivo com o hub IoT](iot-hub-ios-swift-c2d.md) 

### <a name="develop-with-azure-iot-hub-cocoapod-libraries"></a>Desenvolva-se com bibliotecas Azure IoT Hub CocoaPod

Azure IoT Hub SDKs lança um conjunto de bibliotecas de Cacau Objective-C para o desenvolvimento do iOS.  Para ver a mais recente lista de bibliotecas de Cacau, consulte [o CocoaPods para o Microsoft Azure IoT](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/samples/ios/CocoaPods.md).  Uma vez que as bibliotecas relevantes são incorporadas no seu projeto XCode, existem duas maneiras de escrever o código relacionado com o IoT Hub:

* Função Objetivo C: Se o seu projeto estiver escrito no Objective-C, pode ligar diretamente para as APIs do Azure IoT Hub C SDK.  Se o seu projeto estiver escrito em Swift, pode ligar `@objc func` antes de criar a sua função e passar a escrever todas as lógicas relacionadas com o Azure IoT Hub usando o código C ou Objective-C.  Um conjunto de amostras que demonstrem ambas podem ser encontradas no [repositório de amostras.](https://github.com/Azure-Samples/azure-iot-samples-ios)  

* Incorpore amostras C: Se tiver escrito uma aplicação de dispositivo C, pode fazê-la referência diretamente no seu projeto XCode:
    * Adicione o ficheiro sample.c ao seu projeto XCode a partir do XCode.  
    * Adicione o ficheiro do cabeçalho à sua dependência.  Um ficheiro de cabeçalho está incluído no [repositório](https://github.com/Azure-Samples/azure-iot-samples-ios) de amostras como exemplo. Para mais informações, visite a página de documentação da Apple para [o Objective-C](https://developer.apple.com/documentation/objectivec).

## <a name="develop-for-android-platform"></a>Desenvolver para plataforma Android
Azure IoT Hub Java SDK suporta plataforma Android.  Para a versão API específica testada, visite a [página de suporte da](iot-hub-device-sdk-platform-support.md) nossa plataforma para obter a mais recente atualização.

Estas documentações percorrem a forma de desenvolver uma aplicação de dispositivo ou aplicação de serviço num dispositivo Android utilizando Gradle e Android Studio:

* [Guia de Início Rápido: Enviar telemetria a partir de um dispositivo para um hub IoT](quickstart-send-telemetry-android.md)  
* [Quickstart: Controle um dispositivo](quickstart-control-device-android.md) 

## <a name="next-steps"></a>Passos seguintes

* [Referência API IoT Hub REST](/rest/api/iothub/)
* [Código fonte Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)