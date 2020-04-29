---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 138a9ef9d483ca0d460bab7185d646669650f83e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81399857"
---
:::row:::
    :::column span="3":::
        O SDK de Speech suporta o Windows 10 e o Windows Server 2016, ou versões posteriores. Versões anteriores **não** são oficialmente suportadas. É possível utilizar partes do SDK de Discurso com versões anteriores do Windows, embora não seja aconselhável.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Windows" src="https://docs.microsoft.com/media/logos/logo_Windows.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

### <a name="system-requirements"></a>Requisitos de sistema

O SDK de Speech no Windows requer o <a href="https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads" target="_blank">Microsoft Visual C++ <span class="docon docon-navigate-external x-hidden-focus"></span> Redistribuable for Visual Studio 2019</a> no sistema.

- <a href="https://aka.ms/vs/16/release/vc_redist.x86.exe" target="_blank">Instalação para x86<span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://aka.ms/vs/16/release/vc_redist.x64.exe" target="_blank">Instalação para x64<span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://aka.ms/vs/16/release/vc_redist.arm64.exe" target="_blank">Instalação para ARMx64<span class="docon docon-navigate-external x-hidden-focus"></span></a>

### <a name="c"></a>C#

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

Para a entrada do microfone, as bibliotecas da Fundação Media devem ser instaladas. Estas bibliotecas fazem parte do Windows 10 e do Windows Server 2016. É possível usar o SDK do Discurso sem estas bibliotecas, desde que um microfone não seja usado como dispositivo de entrada de áudio.

Os ficheiros SDK de Discurso necessários podem ser implantados no mesmo diretório que a sua aplicação. Desta forma, a sua aplicação pode aceder diretamente às bibliotecas. Certifique-se de que seleciona a versão correta (x86/x64) que corresponde à sua aplicação.

| Nome                                            | Função                                             |
|-------------------------------------------------|------------------------------------------------------|
| `Microsoft.CognitiveServices.Speech.core.dll`   | Core SDK, necessário para implantação nativa e gerida |
| `Microsoft.CognitiveServices.Speech.csharp.dll` | Necessário para a implantação gerida                      |

> [!NOTE]
> A partir do lançamento 1.3.0, o ficheiro `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` (enviado em versões anteriores) já não é necessário. A funcionalidade está agora integrada no Núcleo SDK.

> [!IMPORTANT]
> Para o projeto C# da Windows Forms App (.NET Framework), certifique-se de que as bibliotecas estão incluídas nas definições de implementação do seu projeto. Pode verificar isto `Properties -> Publish Section`por baixo. Clique `Application Files` no botão e encontre as bibliotecas correspondentes da lista de deslocamentos para baixo. Certifique-se de que `Included`o valor está definido para . O Visual Studio incluirá o ficheiro quando o projeto for publicado/implementado.

### <a name="c"></a>C++

[!INCLUDE [Get C++ Speech SDK](get-speech-sdk-cpp.md)]

### <a name="python"></a>Python

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

### <a name="java"></a>Java

[!INCLUDE [Get Java Speech SDK](get-speech-sdk-java.md)]
