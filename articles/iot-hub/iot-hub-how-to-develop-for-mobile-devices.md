---
title: Desenvolver para dispositivos móveis usando SDKs do Azure IoT | Microsoft Docs
description: Guia do desenvolvedor-saiba mais sobre como desenvolver dispositivos móveis usando SDKs do Hub IoT do Azure.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/16/2018
ms.author: robinsh
ms.openlocfilehash: 945b02003a443c04e692fdc06ca5714de362d074
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68883087"
---
# <a name="develop-for-mobile-devices-using-azure-iot-sdks"></a>Desenvolver para dispositivos móveis usando SDKs do Azure IoT

As coisas na Internet das Coisas podem se referir a uma ampla gama de dispositivos com capacidade variável: sensores, microcontroladores, dispositivos inteligentes, gateways industriais e até mesmo dispositivos móveis.  Um dispositivo móvel pode ser um dispositivo IoT, no qual ele está enviando telemetria do dispositivo para a nuvem e gerenciado pela nuvem.  Ele também pode ser o dispositivo que executa um aplicativo de serviço de back-end, que gerencia outros dispositivos IoT.  Em ambos os casos, os [SDKs do Hub IOT do Azure](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks) podem ser usados para desenvolver aplicativos que funcionam para dispositivos móveis.  

## <a name="develop-for-native-ios-platform"></a>Desenvolver para a plataforma iOS nativa

Os SDKs do Hub IoT do Azure fornecem suporte nativo à plataforma iOS por meio do SDK C do Hub IoT do Azure.  Você pode considerar isso como um SDK do iOS que pode ser incorporado em seu projeto do XCode para o seu Swift ou objetivo do C.  Há duas maneiras de usar o SDK do C no iOS:

* Use as bibliotecas CocoaPod no projeto do XCode diretamente.  
* Baixe o código-fonte do SDK do C e compile para a plataforma iOS seguindo a [instrução Build](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) para MacOS.  

O SDK C do Hub IoT do Azure é escrito em C99 para portabilidade máxima para várias plataformas.  O processo de portabilidade envolve a gravação de uma camada de adoção fina para os componentes específicos da plataforma, que podem ser encontrados aqui para o [Ios](https://github.com/Azure/azure-c-shared-utility/tree/master/pal/ios-osx).  Os recursos no SDK do C podem ser aproveitados na plataforma iOS, incluindo os primitivos do Hub IoT do Azure com suporte e recursos específicos do SDK, como a política de repetição para a confiabilidade da rede.  A interface para o SDK do iOS também é semelhante à interface do SDK C do Hub IoT do Azure.  

Essas documentações percorrem como desenvolver um aplicativo de dispositivo ou de serviço em um dispositivo iOS:

* [Quickstart: Enviar telemetria de um dispositivo para um hub IoT](quickstart-send-telemetry-ios.md)  
* [Enviar mensagens da nuvem para seu dispositivo com o Hub IoT](iot-hub-ios-swift-c2d.md) 

### <a name="develop-with-azure-iot-hub-cocoapod-libraries"></a>Desenvolver com bibliotecas CocoaPod do Hub IoT do Azure

Os SDKs do Hub IoT do Azure lançam um conjunto de bibliotecas de CocoaPod Objective-C para o desenvolvimento do iOS.  Para ver a lista mais recente de bibliotecas CocoaPod, consulte [CocoaPods para Microsoft Azure IOT](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/samples/ios/CocoaPods.md).  Depois que as bibliotecas relevantes são incorporadas ao seu projeto do XCode, há duas maneiras de escrever código relacionado ao Hub IoT:

* Função Objective C: Se o projeto for escrito em Objective-C, você poderá chamar APIs do SDK do Hub IoT do Azure diretamente.  Se seu projeto for escrito em Swift, você poderá chamar `@objc func` antes de criar sua função e continuar escrevendo todas as lógicas relacionadas ao Hub IOT do Azure usando o código C ou Objective-C.  Um conjunto de amostras que demonstra ambos pode ser encontrado no [repositório de exemplo](https://github.com/Azure-Samples/azure-iot-samples-ios).  

* Incorpore exemplos de C: Se você escreveu um aplicativo de dispositivo C, você pode referenciá-lo diretamente em seu projeto XCode:
    * Adicione o arquivo Sample. c ao projeto XCode do XCode.  
    * Adicione o arquivo de cabeçalho à sua dependência.  Um arquivo de cabeçalho é incluído no [repositório de exemplo](https://github.com/Azure-Samples/azure-iot-samples-ios) como um exemplo. Para obter mais informações, consulte a página de documentação da Apple para [Objective-C](https://developer.apple.com/documentation/objectivec).

## <a name="develop-for-android-platform"></a>Desenvolver para a plataforma Android
O SDK Java do Hub IoT do Azure dá suporte à plataforma Android.  Para a versão de API específica testada, visite nossa [página de suporte da plataforma](iot-hub-device-sdk-platform-support.md) para obter a atualização mais recente.

Essas documentações explicam como desenvolver um aplicativo de dispositivo ou de serviço em um dispositivo Android usando gradle e Android Studio:

* [Quickstart: Enviar telemetria de um dispositivo para um hub IoT](quickstart-send-telemetry-android.md)  
* [Quickstart: Controlar um dispositivo](quickstart-control-device-android.md) 

## <a name="next-steps"></a>Passos Seguintes

* [Referência da API REST do Hub IoT](https://docs.microsoft.com/rest/api/iothub/)
* [Código-fonte do SDK do Azure IoT C](https://github.com/Azure/azure-iot-sdk-c)
