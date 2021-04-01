---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: fc4cc5063f72ff3f0db62cde79f7908add86166e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102434587"
---
:::row:::
    :::column span="3":::
        O Speech SDK suporta o Windows 10 e o Windows Server 2016 ou versões posteriores. Versões anteriores **não** são oficialmente suportadas. É possível utilizar partes do SDK do Discurso com versões anteriores do Windows, embora não seja aconselhável.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Windows" src="https://docs.microsoft.com/media/logos/logo_Windows.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

### <a name="system-requirements"></a>Requisitos de sistema

O Speech SDK no Windows requer o <a href="https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads" target="_blank">Microsoft Visual C++ Redistribuable for Visual Studio 2019 </a> no sistema.

- <a href="https://aka.ms/vs/16/release/vc_redist.x86.exe" target="_blank">Instalação para x86 </a>
- <a href="https://aka.ms/vs/16/release/vc_redist.x64.exe" target="_blank">Instalação para x64 </a>
- <a href="https://aka.ms/vs/16/release/vc_redist.arm64.exe" target="_blank">Instalação para ARMx64 </a>

### <a name="c"></a>C#

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

Para a entrada do microfone, as bibliotecas da Media Foundation devem ser instaladas. Estas bibliotecas fazem parte do Windows 10 e do Windows Server 2016. É possível utilizar o Speech SDK sem estas bibliotecas, desde que um microfone não seja utilizado como o dispositivo de entrada de áudio.

Os ficheiros SDK de discurso necessários podem ser implantados no mesmo diretório que a sua aplicação. Desta forma, a sua aplicação pode aceder diretamente às bibliotecas. Certifique-se de que seleciona a versão correta (x86/x64) que corresponde à sua aplicação.

| Name                                            | Função                                             |
|-------------------------------------------------|------------------------------------------------------|
| `Microsoft.CognitiveServices.Speech.core.dll`   | Core SDK, necessário para implantação nativa e gerida |
| `Microsoft.CognitiveServices.Speech.csharp.dll` | Necessário para implantação gerida                      |

> [!NOTE]
> A partir do lançamento 1.3.0 o ficheiro `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` (enviado em versões anteriores) já não é necessário. A funcionalidade está agora integrada no núcleo SDK.

> [!IMPORTANT]
> Para o projeto Windows Forms App (.NET Framework) C#, certifique-se de que as bibliotecas estão incluídas nas definições de implantação do seu projeto. Pode verificar isto em `Properties -> Publish Section` baixo. Clique no `Application Files` botão e encontre bibliotecas correspondentes da lista de desacorridos. Certifique-se de que o valor está definido para `Included` . O Visual Studio incluirá o ficheiro quando o projeto for publicado/implementado.

### <a name="c"></a>C++

[!INCLUDE [Get C++ Speech SDK](get-speech-sdk-cpp.md)]

### <a name="python"></a>Python

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

### <a name="java"></a>Java

[!INCLUDE [Get Java Speech SDK](get-speech-sdk-java.md)]
