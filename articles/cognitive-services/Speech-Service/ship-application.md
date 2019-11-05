---
title: Desenvolver aplicativos com o SDK de fala-serviço de fala
titleSuffix: Azure Cognitive Services
description: Saiba como criar aplicativos usando o SDK de fala.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: jhakulin
ms.custom: seodec18
ms.openlocfilehash: 166ae00085f07ef24d746b60947a31e7680a0f00
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491007"
---
# <a name="ship-an-application"></a>Lançar uma aplicação

Observe a [licença do SDK de fala](https://aka.ms/csspeech/license201809), bem como os [avisos de software](https://csspeechstorage.blob.core.windows.net/drop/1.0.0/ThirdPartyNotices.html) de terceiros quando você distribui o SDK de fala dos serviços cognitivas do Azure. Além disso, examine a [política de privacidade da Microsoft](https://aka.ms/csspeech/privacy).

Dependendo da plataforma, existem dependências diferentes para executar seu aplicativo.

## <a name="windows"></a>Windows

O SDK de fala dos serviços cognitivas é testado no Windows 10 e no Windows Server 2016.

O SDK de fala dos serviços cognitivas requer o [ C++ Microsoft Visual redistribuível para Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) no sistema. Você pode baixar instaladores para a versão mais recente do `Microsoft Visual C++ Redistributable for Visual Studio 2019` aqui:

- [Win32](https://aka.ms/vs/16/release/vc_redist.x86.exe)
- [x86](https://aka.ms/vs/16/release/vc_redist.x64.exe)

Se seu aplicativo usar código gerenciado, o `.NET Framework 4.6.1` ou posterior será necessário no computador de destino.

Para entrada de microfone, as bibliotecas de Media Foundation devem ser instaladas. Essas bibliotecas fazem parte do Windows 10 e do Windows Server 2016. É possível usar o SDK de fala sem essas bibliotecas, desde que um microfone não seja usado como o dispositivo de entrada de áudio.

Os arquivos do SDK de fala necessários podem ser implantados no mesmo diretório que o seu aplicativo. Dessa forma, seu aplicativo pode acessar diretamente as bibliotecas. Verifique se você selecionou a versão correta (Win32/x64) que corresponde ao seu aplicativo.

| Nome | Função
|:-----|:----|
| `Microsoft.CognitiveServices.Speech.core.dll` | SDK principal, necessário para implantação nativa e gerenciada
| `Microsoft.CognitiveServices.Speech.csharp.dll` | Necessário para implantação gerenciada

>[!NOTE]
> A partir da versão 1.3.0, o arquivo `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` (fornecido em versões anteriores) não é mais necessário. A funcionalidade agora está integrada no SDK principal.

>[!NOTE]
> Para o projeto do .NET Framework (Windows Forms C# app), verifique se as bibliotecas estão incluídas nas configurações de implantação do projeto. Você pode verificar isso em `Properties -> Publish Section`. Clique no botão `Application Files` e localize as bibliotecas correspondentes na lista rolar para baixo. Verifique se o valor está definido como `Included`. O Visual Studio incluirá o arquivo quando o projeto for publicado/implantado.

## <a name="linux"></a>Linux

O SDK de fala atualmente dá suporte às distribuições Ubuntu 16, 4, Ubuntu 18, 4 e Debian 9.
Para um aplicativo nativo, você precisa enviar a biblioteca do SDK de fala `libMicrosoft.CognitiveServices.Speech.core.so`.
Verifique se você selecionou a versão (x86, x64) que corresponde ao seu aplicativo. Dependendo da versão do Linux, você também pode precisar incluir as seguintes dependências:

* As bibliotecas compartilhadas da biblioteca GNU C (incluindo a biblioteca de programação de threads POSIX, `libpthreads`)
* A biblioteca OpenSSL (`libssl.so.1.0.0` ou `libssl.so.1.0.2`)
* A biblioteca compartilhada para aplicativos ALSA (`libasound.so.2`)

No Ubuntu, as bibliotecas GNU C já devem estar instaladas por padrão. Os três últimos podem ser instalados usando estes comandos:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.0 libasound2
```

No Debian 9, instale estes pacotes:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.2 libasound2
```

## <a name="next-steps"></a>Passos seguintes

* [Obter a subscrição de avaliação de Voz](https://azure.microsoft.com/try/cognitive-services/)
* [Veja como reconhecer a fala noC#](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
