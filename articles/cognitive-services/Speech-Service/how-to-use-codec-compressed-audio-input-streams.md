---
title: Áudio comprimido de código stream com o Serviço Speech SDK - Speech
titleSuffix: Azure Cognitive Services
description: Aprenda a transmitir áudio comprimido para o serviço de Fala com o SDK de Fala. Disponível C++ C#para , e Java para Linux, Java em Android e Objective-C no iOS.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: amishu
ms.openlocfilehash: 2b530da06b02091ce66ff7c116f3e17ddcc22497
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78331115"
---
# <a name="using-codec-compressed-audio-input-with-the-speech-sdk"></a>Utilização de entrada de áudio comprimido codec com o SDK da fala

A API de entrada de **áudio comprimido** do Speech SDK fornece uma forma de transmitir áudio comprimido para o serviço de Fala usando pullStream ou PushStream.

> [!IMPORTANT]
> O streaming de áudio de entrada C++comprimido C#é atualmente suportado para , e Java em Linux (Ubuntu 16.04, Ubuntu 18.04, Debian 9, RHEL 8, CentOS 8). Também é suportado para [Java em Android](how-to-use-codec-compressed-audio-input-streams-android.md) e Objective-C na plataforma [iOS.](how-to-use-codec-compressed-audio-input-streams-ios.md)
> É necessária a versão 1.7.0 ou superior do Discurso (versão 1.10.0 ou superior para RHEL 8, CentOS 8).

Para acenar/PCM consulte a documentação da fala principal.  Fora do wav/PCM, são suportados os seguintes formatos de entrada comprimidos codec:

- MP3
- OPUS/OGG
- FLAC
- ALAW em recipiente de awav
- MULAW em recipiente de awav

## <a name="prerequisites"></a>Pré-requisitos

O manuseamento do áudio comprimido é implementado utilizando [gStreamer](https://gstreamer.freedesktop.org). Por razão de licenciamento, os binários Gstreamer não são compilados e ligados ao SDK da fala. Assim, o desenvolvedor de aplicações precisa de instalar o seguinte em 18.04, 16.04 e Debian 9 para usar áudio de entrada comprimido.

```sh
sudo apt install libgstreamer1.0-0 gstreamer1.0-plugins-base gstreamer1.0-plugins-good gstreamer1.0-plugins-bad gstreamer1.0-plugins-ugly
```

No RHEL/CentOS 8:

```sh
sudo yum install gstreamer1 gstreamer1-plugins-base gstreamer1-plugins-good gstreamer1-plugins-bad-free gstreamer1-plugins-ugly-free
```

> [!NOTE]
> No RHEL/CentOS 8, siga as instruções sobre como configurar o [OpenSSL para Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

## <a name="example-code-using-codec-compressed-audio-input"></a>Código de exemplo utilizando entrada de áudio comprimido codec

Para transmitir num formato áudio comprimido para o serviço De Fala, crie `PullAudioInputStream` ou `PushAudioInputStream`. Em seguida, crie uma `AudioConfig` a partir de uma instância da sua classe de fluxo, especificando o formato de compressão do fluxo.

Vamos supor que tem uma classe de fluxo de entrada chamada `myPushStream` e está usando OPUS/OGG. O seu código pode parecer assim:

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

var speechConfig = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");

// Create an audio config specifying the compressed audio format and the instance of your input stream class.
var audioFormat = AudioStreamFormat.GetCompressedFormat(AudioStreamContainerFormat.OGG_OPUS);
var audioConfig = AudioConfig.FromStreamInput(myPushStream, audioFormat);

var recognizer = new SpeechRecognizer(speechConfig, audioConfig);

var result = await recognizer.RecognizeOnceAsync();

var text = result.GetText();
```

## <a name="next-steps"></a>Passos seguintes

- [Obter a subscrição de avaliação de Voz](https://azure.microsoft.com/try/cognitive-services/)
* [Veja como reconhecer o discurso em Java](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java)
