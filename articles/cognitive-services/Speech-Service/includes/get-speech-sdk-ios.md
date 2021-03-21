---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 0a9ca21cc14bb87797c962a89cf87ac184e73735
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102434568"
---
:::row:::
    :::column span="3":::
        Ao desenvolver para iOS, existem dois SDKs de discurso disponíveis. O Discurso Objective-C SDK está disponível nativamente como um pacote iOS CocoaPod. Em alternativa, o SDK de discurso .NET poderia ser utilizado com Xamarin.iOS, uma vez que implementa .NET Standard 2.0.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="iOS" src="https://docs.microsoft.com/media/logos/logo_ios.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> Para obter mais informações sobre o Discurso Objective-C SDK com Swift, consulte <a href="https://developer.apple.com/documentation/swift/imported_c_and_objective-c_apis/importing_objective-c_into_swift" target="_blank">a importação do Objectivo-C em Swift </a>.

### <a name="system-requirements"></a>Requisitos de sistema

- Uma versão macOS 10.3 ou mais tarde
- Alvo iOS 9.3 ou posterior

# <a name="xcode"></a>[Xcode](#tab/ios-xcode)

:::row:::
    :::column span="3":::
        O pacote iOS CocoaPod está disponível para download e utilização com o ambiente de desenvolvimento integrado <a href="https://apps.apple.com/us/app/xcode/id497799835" target="_blank">Xcode 9.4.1 (ou posterior) </a> (IDE). Primeiro, <a href="https://aka.ms/csspeech/iosbinary" target="_blank">descarregue o CocoaPod binário. </a> Extrair a cápsula no mesmo diretório para a sua utilização pretendida, criar um *Podfile* e enumerar o `pod` como a `target` .
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Xcode" src="https://docs.microsoft.com/media/logos/logo_xcode.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

```
platform :ios, '9.3'
use_frameworks!

target 'AppName' do
  pod 'MicrosoftCognitiveServicesSpeech', '~> 1.10.0'
end
```

# <a name="xamarinios"></a>[Xamarin.iOS](#tab/ios-xamarin)

:::row:::
    :::column span="3":::
        Xamarin.iOS expõe o SDK completo do iOS para desenvolvedores .NET. Construa aplicações totalmente nativas do iOS utilizando C# ou F# no Visual Studio. Para mais informações, consulte <a href="https://docs.microsoft.com/xamarin/ios/" target="_blank">Xamarin.iOS </a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Xamarin" src="https://docs.microsoft.com/media/logos/logo_xamarin.svg" width="60px">
            &nbsp;&nbsp;❤️        <img alt="iOS" src="https://docs.microsoft.com/media/logos/logo_ios.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

---

#### <a name="additional-resources"></a>Recursos adicionais

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/objectivec/ios" target="_blank">iOS Speech SDK quickstart Objective-C source code </a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/swift/ios" target="_blank">iOS Speech SDK quickstart Swift source code </a>