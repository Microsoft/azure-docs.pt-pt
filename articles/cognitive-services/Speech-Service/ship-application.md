---
title: Programar aplicações com o SDK - serviços de voz de voz
titleSuffix: Azure Cognitive Services
description: Saiba como criar aplicações com o SDK de voz.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: jhakulin
ms.custom: seodec18
ms.openlocfilehash: 8fc27002af4ebef0825b23c806cfedbe7adf9642
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2019
ms.locfileid: "68404823"
---
# <a name="ship-an-application"></a>Enviar uma aplicação

Observe a [licença do SDK de voz](https://aka.ms/csspeech/license201809), bem como o [avisos de software de terceiros](https://csspeechstorage.blob.core.windows.net/drop/1.0.0/ThirdPartyNotices.html) quando distribui o SDK de voz dos serviços cognitivos do Azure. Além disso, reveja os [declaração de privacidade do Microsoft](https://aka.ms/csspeech/privacy).

Consoante a plataforma, existem dependências diferentes para executar seu aplicativo.

## <a name="windows"></a>Windows

O SDK de voz dos serviços cognitivos é testado no Windows 10 e no Windows Server 2016.

O SDK de fala dos serviços cognitivas requer o [Microsoft Visual C++ redistribuível para Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) no sistema. Pode baixar os programas de instalação para a versão mais recente do `Microsoft Visual C++ Redistributable for Visual Studio 2019` aqui:

- [Win32](https://aka.ms/vs/16/release/vc_redist.x86.exe)
- [x64](https://aka.ms/vs/16/release/vc_redist.x64.exe)

Se o aplicativo usar código gerenciado, o `.NET Framework 4.6.1` ou posterior é necessário no computador de destino.

Para a entrada do microfone, as bibliotecas do Media Foundation tem de ser instaladas. Essas bibliotecas fazem parte do Windows 10 e Windows Server 2016. É possível utilizar o SDK de voz dessas bibliotecas, desde que um microfone não é utilizado como o dispositivo de entrada de áudio.

Os ficheiros necessários do SDK de voz podem ser implementados no mesmo diretório que seu aplicativo. Desta forma, que seu aplicativo pode acessar diretamente as bibliotecas. Certificar-se de que seleciona a versão correta (Win32/x64) que corresponde à sua aplicação.

| Nome | Função
|:-----|:----|
| `Microsoft.CognitiveServices.Speech.core.dll` | Core SDK, necessária para a implementação nativa e gerenciada
| `Microsoft.CognitiveServices.Speech.csharp.dll` | necessário para a implementação gerida

>[!NOTE]
> A partir da versão 1.3.0, o `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` arquivo (fornecido em versões anteriores) não é mais necessário. A funcionalidade agora está integrada no SDK principal.

>[!NOTE]
> Para o projeto do .NET Framework (Windows Forms C# app), verifique se as bibliotecas estão incluídas nas configurações de implantação do projeto. Você pode verificar isso em `Properties -> Publish Section`. Clique no `Application Files` botão e localize as bibliotecas correspondentes na lista rolar para baixo. Verifique se o valor está definido como `Included`. O Visual Studio incluirá o arquivo quando o projeto for publicado/implantado.

## <a name="linux"></a>Linux

O SDK de fala atualmente dá suporte às distribuições Ubuntu 16, 4, Ubuntu 18, 4 e Debian 9.
Para uma aplicação nativa, terá de enviar a biblioteca do SDK de voz, `libMicrosoft.CognitiveServices.Speech.core.so`.
Certificar-se de que seleciona a versão (x86, x64) que corresponde à sua aplicação. Dependendo da versão do Linux, também poderá ter de incluir as seguintes dependências:

* As bibliotecas compartilhadas da biblioteca do GNU C (incluindo a biblioteca de programação de Threads POSIX, `libpthreads`)
* A biblioteca OpenSSL (`libssl.so.1.0.0` ou `libssl.so.1.0.2`)
* A biblioteca partilhada para aplicativos de ALSA (`libasound.so.2`)

No Ubuntu, as bibliotecas GNU C já devem estar instaladas por padrão. Os três últimos podem ser instalados utilizando estes comandos:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.0 libasound2
```

No Debian 9, instale estes pacotes:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.2 libasound2
```

## <a name="next-steps"></a>Passos Seguintes

* [Obter a subscrição de avaliação de Voz](https://azure.microsoft.com/try/cognitive-services/)
* [Veja como a reconhecer a conversão de voz em c#](quickstart-csharp-dotnet-windows.md)
