---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 4a4705647b90d29f47e37b88531f3432c6a2f448
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102434557"
---
Ao desenvolver-se para o macOS, existem três SDKs de discurso disponíveis.

- O Discurso Objective-C SDK está disponível nativamente como um pacote De Cacau
- O .NET Speech SDK pode ser usado com **Xamarin.Mac,** uma vez que implementa .NET Standard 2.0
- O Python Speech SDK está disponível como módulo PyPI

> [!TIP]
> Para obter mais informações sobre o Discurso Objective-C SDK com Swift, consulte <a href="https://developer.apple.com/documentation/swift/imported_c_and_objective-c_apis/importing_objective-c_into_swift" target="_blank">a importação do Objectivo-C em Swift </a>.

### <a name="system-requirements"></a>Requisitos de sistema

- Uma versão macOS 10.13 ou mais tarde

# <a name="xcode"></a>[Xcode](#tab/mac-xcode)

:::row:::
    :::column span="3":::
        O pacote macOS CocoaPod está disponível para download e utilização com o ambiente de desenvolvimento integrado <a href="https://apps.apple.com/us/app/xcode/id497799835" target="_blank">Xcode 9.4.1 (ou posterior) </a> (IDE). Primeiro, <a href="https://aka.ms/csspeech/macosbinary" target="_blank">descarregue o CocoaPod binário. </a> Extrair a cápsula no mesmo diretório para a sua utilização pretendida, criar um *Podfile* e enumerar o `pod` como a `target` .
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

target 'MyApp' do
  pod 'MicrosoftCognitiveServicesSpeech', '~> 1.15.0'
end
```

# <a name="xamarinmac"></a>[Xamarin.Mac](#tab/mac-xamarin)

:::row:::
    :::column span="3":::
        Xamarin.Mac expõe o macOS completo SDK para desenvolvedores .NET para construir aplicações mac nativas usando C#. Para mais informações, consulte <a href="https://docs.microsoft.com/xamarin/mac/" target="_blank">Xamarin.Mac. </a>
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Xamarin" src="https://docs.microsoft.com/media/logos/logo_xamarin.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

# <a name="python"></a>[Python](#tab/mac-python)

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

---

#### <a name="additional-resources"></a>Recursos adicionais

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/objectivec/macos" target="_blank">macOS Speech SDK quickstart Objective-C source code </a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/swift/macos" target="_blank">macOS Speech SDK quickstart Swift source code </a>