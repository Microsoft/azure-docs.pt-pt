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
ms.openlocfilehash: 9507428e63b337b3d8419a833d03d081d494c522
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78330809"
---
# <a name="ship-an-application"></a>Lançar uma aplicação

Observe a [licença Speech SDK](https://aka.ms/csspeech/license201809), bem como os [avisos de software de terceiros](https://csspeechstorage.blob.core.windows.net/drop/1.0.0/ThirdPartyNotices.html) quando distribuir o SDK de Discurso dos Serviços Cognitivos Azure. Além disso, reveja a [Declaração de Privacidade](https://aka.ms/csspeech/privacy)da Microsoft .

Consoante a plataforma, existem dependências diferentes para executar seu aplicativo.

## <a name="windows"></a>Windows

O SDK de voz dos serviços cognitivos é testado no Windows 10 e no Windows Server 2016.

O SDK de Discurso de Serviços Cognitivos requer o [Microsoft Visual C++ Redistribuable for Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) no sistema. Pode baixar os instaladores para a versão mais recente do `Microsoft Visual C++ Redistributable for Visual Studio 2019` aqui:

- [Win32](https://aka.ms/vs/16/release/vc_redist.x86.exe)
- [x64](https://aka.ms/vs/16/release/vc_redist.x64.exe)

Se a sua aplicação utilizar o código gerido, o `.NET Framework 4.6.1` ou posteriormente é necessário na máquina-alvo.

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

O SDK de Discurso suporta atualmente as distribuições Ubuntu 16.04, Ubuntu 18.04, Debian 9, RHEL 8, CentOS 8.
Para uma aplicação nativa, você precisa enviar a biblioteca Speech SDK, `libMicrosoft.CognitiveServices.Speech.core.so`.
Certificar-se de que seleciona a versão (x86, x64) que corresponde à sua aplicação. Dependendo da versão do Linux, também poderá ter de incluir as seguintes dependências:

- As bibliotecas partilhadas da biblioteca GNU C (incluindo a biblioteca de programação POSIX Threads, `libpthreads`)
- Biblioteca OpenSSL (`libssl.so.1.0.0` ou `libssl.so.1.0.2`)
- Biblioteca partilhada para aplicações ALSA (`libasound.so.2`)

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

No RHEL/CentOS 8:

```sh
sudo yum update
sudo yum install alsa-lib openssl
```

> [!NOTE]
> No RHEL/CentOS 8, siga as instruções sobre como configurar o [OpenSSL para Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

## <a name="next-steps"></a>Passos seguintes

- [Obter a subscrição de avaliação de Voz](https://azure.microsoft.com/try/cognitive-services/)
- [Veja como reconhecer o discurso emC#](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
