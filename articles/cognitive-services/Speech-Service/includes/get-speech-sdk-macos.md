---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 57de7f1e7c37fec66cda666d3f144e52849a026f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81400041"
---
Ao desenvolver-se para macOS, existem três SDKs de Discurso disponíveis.

- O Objective-C Speech SDK está disponível de forma nativa como um pacote CocoaPod
- O .NET Speech SDK poderia ser usado com **Xamarin.Mac** à medida que implementa .NET Standard 2.0
- O Python Speech SDK está disponível como módulo PyPI

> [!TIP]
> Para obter mais detalhes utilizando o Objective-C Speech SDK com Swift, consulte <a href="https://developer.apple.com/documentation/swift/imported_c_and_objective-c_apis/importing_objective-c_into_swift" target="_blank">a importação do Objectivo-C em <span class="docon docon-navigate-external x-hidden-focus"> </span>Swift </a>.

### <a name="system-requirements"></a>Requisitos de sistema

- Uma versão macOS 10.13 ou posterior

# <a name="xcode"></a>[Xcode](#tab/mac-xcode)

:::row:::
    :::column span="3":::
        O pacote macOS CocoaPod está disponível para download e utilização com o ambiente de desenvolvimento integrado <a href="https://apps.apple.com/us/app/xcode/id497799835" target="_blank">Xcode 9.4.1 (ou mais tarde) <span class="docon docon-navigate-external x-hidden-focus"></span> </a> (IDE). Primeiro, <a href="https://aka.ms/csspeech/macosbinary" target="_blank">descarregue o <span class="docon docon-navigate-external x-hidden-focus"> </span>CocoaPod binário. </a> Extrair a cápsula no mesmo diretório para a sua utilização `target`pretendida, criar um *Podfile* e enumerar o `pod` como a .
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
  pod 'MicrosoftCognitiveServicesSpeech', '~> 1.11.0'
end
```

# <a name="xamarinmac"></a>[Xamarin.Mac](#tab/mac-xamarin)

:::row:::
    :::column span="3":::
        Xamarin.Mac expõe o macOS SDK completo para os desenvolvedores .NET para construir aplicações mac nativas usando C#. Para mais informações, consulte <a href="https://docs.microsoft.com/xamarin/mac/" target="_blank">Xamarin.Mac <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.
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

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/objectivec/macos" target="_blank">macOS Speech SDK quickstart Código fonte Objectivo-C<span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/swift/macos" target="_blank">macOS Speech SDK quickstart Swift código fonte<span class="docon docon-navigate-external x-hidden-focus"></span></a>