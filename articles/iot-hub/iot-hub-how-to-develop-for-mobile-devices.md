---
title: Desenvolver dispositivos móveis utilizando DSKs Azure IoT [ Microsoft Docs
description: Guia de desenvolvimento - Saiba como se desenvolver para dispositivos móveis utilizando SDKs Azure IoT Hub.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/16/2018
ms.author: robinsh
ms.openlocfilehash: 945b02003a443c04e692fdc06ca5714de362d074
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "68883087"
---
# <a name="develop-for-mobile-devices-using-azure-iot-sdks"></a>Desenvolver para dispositivos móveis usando SDKs Azure IoT

As coisas na Internet das Coisas podem referir-se a uma vasta gama de dispositivos com capacidadevariada: sensores, microcontroladores, dispositivos inteligentes, gateways industriais e até dispositivos móveis.  Um dispositivo móvel pode ser um dispositivo IoT, onde está a enviar telemetria de dispositivo para nuvem e gerido pela nuvem.  Também pode ser o dispositivo que executa uma aplicação de serviço back-end, que gere outros dispositivos IoT.  Em ambos os casos, os [SDKs Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks) podem ser usados para desenvolver aplicações que funcionem para dispositivos móveis.  

## <a name="develop-for-native-ios-platform"></a>Desenvolver para plataforma nativa iOS

Os SDKs Azure IoT Hub fornecem suporte nativo da plataforma iOS através do Azure IoT Hub C SDK.  Pode pensar nisso como um SDK iOS que pode incorporar no seu projeto Swift ou Objetive C XCode.  Existem duas formas de utilizar o C SDK no iOS:

* Utilize as bibliotecas Do CocoaPod no projeto XCode diretamente.  
* Descarregue o código fonte para C SDK e construa para a plataforma iOS seguindo a instrução de [construção](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) para O MacOS.  

O Azure IoT Hub C SDK está escrito em C99 para a máxima portabilidade a várias plataformas.  O processo de porting envolve a escrita de uma fina camada de adoção para os componentes específicos da plataforma, que pode ser encontrado aqui para [iOS](https://github.com/Azure/azure-c-shared-utility/tree/master/pal/ios-osx).  As funcionalidades no C SDK podem ser alavancadas na plataforma iOS, incluindo os primitivos Azure IoT Hub suportados e funcionalidades específicas do SDK, tais como a política de retry para a fiabilidade da rede.  A interface para iOS SDK também é semelhante à interface para Azure IoT Hub C SDK.  

Estas documentações passam por como desenvolver uma aplicação de dispositivo ou aplicação de serviço num dispositivo iOS:

* [Guia de Início Rápido: Enviar telemetria a partir de um dispositivo para um hub IoT](quickstart-send-telemetry-ios.md)  
* [Envie mensagens da nuvem para o seu dispositivo com hub IoT](iot-hub-ios-swift-c2d.md) 

### <a name="develop-with-azure-iot-hub-cocoapod-libraries"></a>Desenvolver com bibliotecas de CocoaPod Azure IoT Hub

O Azure IoT Hub SDKs lança um conjunto de bibliotecas objetivas de CocoaPod para desenvolvimento do iOS.  Para ver a mais recente lista de bibliotecas do CocoaPod, consulte [CocoaPods para o Microsoft Azure IoT](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/samples/ios/CocoaPods.md).  Uma vez incorporadas as bibliotecas relevantes no seu projeto XCode, existem duas formas de escrever o código relacionado com o IoT Hub:

* Função Objetivo C: Se o seu projeto estiver escrito no Objectivo-C, pode ligar diretamente para APIs do Azure IoT Hub C SDK.  Se o seu projeto estiver escrito `@objc func` em Swift, pode ligar antes de criar a sua função, e continuar a escrever todas as lógicas relacionadas com o Hub Azure IoT usando o código C ou Objective-C.  Um conjunto de amostras que demonstrem ambos podem ser encontrados no [repositório](https://github.com/Azure-Samples/azure-iot-samples-ios)da amostra .  

* Incorporar amostras C: Se tiver escrito uma aplicação de dispositivo C, pode referenciar diretamente no seu projeto XCode:
    * Adicione o ficheiro sample.c ao seu projeto XCode a partir do XCode.  
    * Adicione o ficheiro do cabeçalho à sua dependência.  Um ficheiro de cabeçalho está incluído no [repositório](https://github.com/Azure-Samples/azure-iot-samples-ios) da amostra como exemplo. Para mais informações, visite a página de documentação da Apple para [o Objectivo-C](https://developer.apple.com/documentation/objectivec).

## <a name="develop-for-android-platform"></a>Desenvolver para plataforma Android
Azure IoT Hub Java SDK suporta plataforma Android.  Para a versão API específica testada, visite a nossa página de suporte da [plataforma](iot-hub-device-sdk-platform-support.md) para obter a mais recente atualização.

Estas documentações passam por como desenvolver uma aplicação de dispositivo ou aplicação de serviço num dispositivo Android usando Gradle e Android Studio:

* [Guia de Início Rápido: Enviar telemetria a partir de um dispositivo para um hub IoT](quickstart-send-telemetry-android.md)  
* [Quickstart: Controle um dispositivo](quickstart-control-device-android.md) 

## <a name="next-steps"></a>Passos seguintes

* [Referência IoT Hub REST API](https://docs.microsoft.com/rest/api/iothub/)
* [Código fonte Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)
