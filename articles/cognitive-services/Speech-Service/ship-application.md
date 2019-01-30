---
title: Programar aplicações com o SDK - serviços de voz de voz
titleSuffix: Azure Cognitive Services
description: Saiba como criar aplicações com o SDK de voz.
services: cognitive-services
author: wolfma61
manager: cgronlun
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/18/2018
ms.author: wolfma
ms.custom: seodec18
ms.openlocfilehash: b37ba55e0d9e1a93994f90630f92075deb4af7e5
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55206447"
---
# <a name="ship-an-application"></a>Enviar uma aplicação

Observe a [licença do SDK de voz](https://aka.ms/csspeech/license201809), bem como o [avisos de software de terceiros](https://csspeechstorage.blob.core.windows.net/drop/1.0.0/ThirdPartyNotices.html) quando distribui o SDK de voz dos serviços cognitivos do Azure. Além disso, reveja os [declaração de privacidade do Microsoft](https://aka.ms/csspeech/privacy).

Consoante a plataforma, existem dependências diferentes para executar seu aplicativo.

## <a name="windows"></a>Windows

O SDK de voz dos serviços cognitivos é testado no Windows 10 e no Windows Server 2016.

O SDK de voz dos serviços cognitivos requer o [Microsoft Visual C++ Redistributable para Visual Studio 2017](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) no sistema. Pode baixar os programas de instalação para a versão mais recente do `Microsoft Visual C++ Redistributable for Visual Studio 2017` aqui:

- [Win32](https://aka.ms/vs/15/release/vc_redist.x86.exe)
- [x64](https://aka.ms/vs/15/release/vc_redist.x64.exe)

Se o aplicativo usar código gerenciado, o `.NET Framework 4.6.1` ou posterior é necessário no computador de destino.

Para a entrada do microfone, as bibliotecas do Media Foundation tem de ser instaladas. Essas bibliotecas fazem parte do Windows 10 e Windows Server 2016. É possível utilizar o SDK de voz dessas bibliotecas, desde que um microfone não é utilizado como o dispositivo de entrada de áudio.

Os ficheiros necessários do SDK de voz podem ser implementados no mesmo diretório que seu aplicativo. Desta forma, que seu aplicativo pode acessar diretamente as bibliotecas. Certificar-se de que seleciona a versão correta (Win32/x64) que corresponde à sua aplicação.

| Nome | Função
|:-----|:----|
| `Microsoft.CognitiveServices.Speech.core.dll` | Core SDK, necessária para a implementação nativa e gerenciada
| `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` | necessário para a implementação gerida
| `Microsoft.CognitiveServices.Speech.csharp.dll` | necessário para a implementação gerida

## <a name="linux"></a>Linux

Para uma aplicação nativa, terá de enviar a biblioteca do SDK de voz, `libMicrosoft.CognitiveServices.Speech.core.so`.
Certificar-se de que seleciona a versão (x86, x64) que corresponde à sua aplicação. Dependendo da versão do Linux, também poderá ter de incluir as seguintes dependências:

* As bibliotecas compartilhadas da biblioteca do GNU C (incluindo a biblioteca de programação de Threads POSIX, `libpthreads`)
* A biblioteca OpenSSL (`libssl.so.1.0.0`)
* A biblioteca de cURL (`libcurl.so.4`)
* A biblioteca partilhada para aplicativos de ALSA (`libasound.so.2`)

No Ubuntu 16.04 ou 18.04, por exemplo, as bibliotecas de C do GNU devem já ser instaladas por predefinição. Os três últimos podem ser instalados utilizando estes comandos:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.0 libcurl3 libasound2 wget
```

## <a name="next-steps"></a>Passos Seguintes

* [Obter a subscrição de avaliação de Voz](https://azure.microsoft.com/try/cognitive-services/)
* [Veja como a reconhecer a conversão de voz em c#](quickstart-csharp-dotnet-windows.md)
