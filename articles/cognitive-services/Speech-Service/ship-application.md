---
title: Desenvolver aplicativos com o Speech SDK - Serviço de Discurso
titleSuffix: Azure Cognitive Services
description: Aprenda a implementar uma aplicação que usa o SDK do Discurso em plataformas suportadas.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: dapine
ms.custom: seodec18
ms.openlocfilehash: 4f75adba27c8173f918fa1afbd44f307d50eb995
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76902020"
---
# <a name="ship-an-application"></a>Lançar uma aplicação

Observe a [licença do SDK de voz](https://aka.ms/csspeech/license201809), bem como o [avisos de software de terceiros](https://csspeechstorage.blob.core.windows.net/drop/1.0.0/ThirdPartyNotices.html) quando distribui o SDK de voz dos serviços cognitivos do Azure. Além disso, reveja os [declaração de privacidade do Microsoft](https://aka.ms/csspeech/privacy).

Consoante a plataforma, existem dependências diferentes para executar seu aplicativo.

## <a name="windows"></a>Windows

O SDK de voz dos serviços cognitivos é testado no Windows 10 e no Windows Server 2016.

O SDK de Discurso de Serviços Cognitivos requer o [Microsoft Visual C++ Redistribuable for Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) no sistema. Pode baixar os programas de instalação para a versão mais recente do `Microsoft Visual C++ Redistributable for Visual Studio 2019` aqui:

- [Win32](https://aka.ms/vs/16/release/vc_redist.x86.exe)
- [x64](https://aka.ms/vs/16/release/vc_redist.x64.exe)

Se o aplicativo usar código gerenciado, o `.NET Framework 4.6.1` ou posterior é necessário no computador de destino.

Para a entrada do microfone, as bibliotecas do Media Foundation tem de ser instaladas. Essas bibliotecas fazem parte do Windows 10 e Windows Server 2016. É possível utilizar o SDK de voz dessas bibliotecas, desde que um microfone não é utilizado como o dispositivo de entrada de áudio.

Os ficheiros necessários do SDK de voz podem ser implementados no mesmo diretório que seu aplicativo. Desta forma, que seu aplicativo pode acessar diretamente as bibliotecas. Certificar-se de que seleciona a versão correta (Win32/x64) que corresponde à sua aplicação.

| Nome | Função |
| :--- | :------- |
| `Microsoft.CognitiveServices.Speech.core.dll`   | Core SDK, necessária para a implementação nativa e gerenciada |
| `Microsoft.CognitiveServices.Speech.csharp.dll` | necessário para a implementação gerida                      |

> [!NOTE]
> A partir do lançamento 1.3.0, o ficheiro `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` (enviado em versões anteriores) já não é necessário. A funcionalidade está agora integrada no Núcleo SDK.

> [!NOTE]
> Para o projeto Windows Forms C# App (.NET Framework), certifique-se de que as bibliotecas estão incluídas nas definições de implementação do seu projeto. Pode verificar isto em `Properties -> Publish Section`. Clique no botão `Application Files` e encontre as bibliotecas correspondentes na lista de deslocamentos para baixo. Certifique-se de que o valor está definido para `Included`. O Visual Studio incluirá o ficheiro quando o projeto for publicado/implementado.

## <a name="linux"></a>Linux

O SDK de Discurso apoia atualmente as distribuições Ubuntu 16.04, Ubuntu 18.04 e Debian 9.
Para uma aplicação nativa, terá de enviar a biblioteca do SDK de voz, `libMicrosoft.CognitiveServices.Speech.core.so`.
Certificar-se de que seleciona a versão (x86, x64) que corresponde à sua aplicação. Dependendo da versão do Linux, também poderá ter de incluir as seguintes dependências:

- As bibliotecas compartilhadas da biblioteca do GNU C (incluindo a biblioteca de programação de Threads POSIX, `libpthreads`)
- Biblioteca OpenSSL (`libssl.so.1.0.0` ou `libssl.so.1.0.2`)
- A biblioteca partilhada para aplicativos de ALSA (`libasound.so.2`)

Em Ubuntu, as bibliotecas GNU C já devem ser instaladas por padrão. Os três últimos podem ser instalados utilizando estes comandos:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.0 libasound2
```

No Debian 9 instale estes pacotes:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.2 libasound2
```

## <a name="next-steps"></a>Passos seguintes

- [Obter a subscrição de avaliação de Voz](https://azure.microsoft.com/try/cognitive-services/)
- [Veja como a reconhecer a conversão de voz em c#](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
