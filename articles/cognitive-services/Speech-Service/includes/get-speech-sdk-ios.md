---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: dapine
ms.openlocfilehash: 1f3864b28ba6f5bdbffe6601d059a21328d56a5e
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656701"
---
:::row:::
    :::column span="3":::
        Ao desenvolver-se para iOS, existem dois SDKs de Discurso disponíveis. O Objective-C Speech SDK está disponível de forma nativa como um pacote iOS CocoaPod. Em alternativa, o .NET Speech SDK pode ser usado com Xamarin.iOS à medida que implementa .NET Standard 2.0.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="iOS" src="https://docs.microsoft.com/media/logos/logo_ios.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> Para obter mais detalhes utilizando o Objective-C Speech SDK com Swift, consulte <a href="https://developer.apple.com/documentation/swift/imported_c_and_objective-c_apis/importing_objective-c_into_swift" target="_blank">a importação do Objectivo-C em <span class="docon docon-navigate-external x-hidden-focus"> </span>Swift </a>.

### <a name="system-requirements"></a>Requisitos de sistema

- Uma versão macOS 10.3 ou posterior
- Alvo iOS 9.3 ou mais tarde

# <a name="xcode"></a>[Xcode](#tab/ios-xcode)

:::row:::
    :::column span="3":::
        O pacote iOS CocoaPod está disponível para download e utilização com o ambiente de desenvolvimento integrado <a href="https://apps.apple.com/us/app/xcode/id497799835" target="_blank">Xcode 9.4.1 (ou mais tarde) <span class="docon docon-navigate-external x-hidden-focus"></span> </a> (IDE). Primeiro, <a href="https://aka.ms/csspeech/iosbinary" target="_blank">descarregue o <span class="docon docon-navigate-external x-hidden-focus"> </span>CocoaPod binário. </a> Extrair a cápsula no mesmo diretório para a sua utilização `target`pretendida, criar um *Podfile* e enumerar o `pod` como a .
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
        Xamarin.iOS expõe o iOS SDK completo para desenvolvedores .NET. Construa aplicações totalmente nativas de iOS usando C# ou F# no Estúdio Visual. Para mais informações, consulte <a href="https://docs.microsoft.com/xamarin/ios/" target="_blank">Xamarin.iOS <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Xamarin" src="https://docs.microsoft.com/media/logos/logo_xamarin.svg" width="60px">
            &nbsp;❤️ &nbsp;        <img alt="iOS" src="https://docs.microsoft.com/media/logos/logo_ios.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

---

#### <a name="additional-resources"></a>Recursos adicionais

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/objectivec/ios" target="_blank">iOS Speech SDK quickstart Código fonte Objectivo-C<span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/swift/ios" target="_blank">iOS Speech SDK quickstart Swift código fonte<span class="docon docon-navigate-external x-hidden-focus"></span></a>