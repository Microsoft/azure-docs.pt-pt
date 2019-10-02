---
title: Áudio compactado do codec de fluxo com o SDK de fala-serviço de fala
titleSuffix: Azure Cognitive Services
description: Saiba como transmitir áudio compactado para os serviços de fala do Azure com o SDK de fala. Disponível para C++o C#, o e o Java para Linux, Java no Android e Objective-C no Ios.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: amishu
ms.openlocfilehash: 8f2896a6289ecaf4046d705da106636258cdadc5
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802249"
---
# <a name="using-codec-compressed-audio-input-with-the-speech-sdk"></a>Usando o codec de entrada de áudio compactado com o SDK de fala

A API de **fluxo de entrada de áudio compactada** do SDK de fala fornece uma maneira de transmitir áudio compactado para o serviço de fala usando PullStream ou PushStream.

> [!IMPORTANT]
> O áudio de entrada compactado de streaming C++tem C#suporte no momento para o, o e o Java no Linux (Ubuntu 16, 4, Ubuntu 18, 4, Debian 9). Também há suporte para [Java no Android](how-to-use-codec-compressed-audio-input-streams-android.md) e [no Objective-C na plataforma iOS](how-to-use-codec-compressed-audio-input-streams-ios.md) .
> O Speech SDK versão 1.7.0 ou superior é necessário.

Para wav/PCM, consulte a documentação de fala principal.  Fora de wav/PCM, há suporte para os seguintes formatos de entrada compactados do Codec:

- MP3
- OPUS/OGG
- FLAC
- ALAW no contêiner WAV
- MULAW no contêiner WAV

## <a name="prerequisites"></a>Pré-requisitos

O tratamento de áudio compactado é implementado usando o [GStreamer](https://gstreamer.freedesktop.org). Por motivo de licenciamento, os binários GStreamer não são compilados e vinculados com o SDK de fala. Portanto, o desenvolvedor de aplicativos precisa instalar o seguinte em 18, 4, 16, 4 e Debian 9 para usar áudio de entrada compactado.

```sh
sudo apt install libgstreamer1.0-0 gstreamer1.0-plugins-base gstreamer1.0-plugins-good gstreamer1.0-plugins-bad gstreamer1.0-plugins-ugly
```

## <a name="example-code-using-codec-compressed-audio-input"></a>Código de exemplo usando a entrada de áudio compactado por codec

Para transmitir em um formato de áudio compactado para os serviços de fala, crie `PullAudioInputStream` ou `PushAudioInputStream`. Em seguida, crie um `AudioConfig` de uma instância de sua classe de fluxo, especificando o formato de compactação do fluxo.

Vamos supor que você tenha uma classe de fluxo de entrada chamada `myPushStream` e esteja usando OPUS/OGG. Seu código pode ter a seguinte aparência:

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
- [Veja como a reconhecer a conversão de voz em c#](quickstart-csharp-dotnet-windows.md)
