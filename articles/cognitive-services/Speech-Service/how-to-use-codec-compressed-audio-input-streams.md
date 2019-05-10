---
title: Áudio de codec comprimido Stream com o SDK de voz - serviços de voz
titleSuffix: Azure Cognitive Services
description: Saiba como transmitir áudio compactado para serviços de voz do Azure com o SDK de voz. Disponível para C++, C#e o Java para Linux.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: amishu
ms.openlocfilehash: 41a55eca321cbe1bfa23a889b8e3ce7c701ce769
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/09/2019
ms.locfileid: "65468052"
---
# <a name="using-codec-compressed-audio-input-with-the-speech-sdk"></a>Usar o codec comprimidos a entrada de áudio com o SDK de voz

O SDK de voz **Stream de entrada de áudio compactados** API fornece uma maneira para transmitir áudio compactado para o serviço de voz usando PullStream ou PushStream.

> [!IMPORTANT]
> Transmissão em fluxo de áudio compactado só é suportado para C++, C#e o Java no Linux (Ubuntu 16.04, Ubuntu 18.04, Debian 9).

Para wav/PCM consulte a documentação de voz principais.  Fora do wav/PCM, são suportados os seguintes formatos de entrada de codec comprimido:

- MP3
- OPUS/OGG

## <a name="prerequisites-to-using-codec-compressed-audio-input"></a>Pré-requisitos para utilizar o codec compactado de entrada de áudio

Instale estas dependências adicionais para utilizar a entrada de áudio compactada com o SDK de voz para Linux:

```sh
sudo apt install libgstreamer1.0-0 gstreamer1.0-plugins-base gstreamer1.0-plugins-good gstreamer1.0-plugins-bad gstreamer1.0-plugins-ugly
```

## <a name="example-code-using-codec-compressed-audio-input"></a>Código de exemplo usando o codec compactado de entrada de áudio

Para transmitir em fluxo num formato compactado de áudio para os serviços de voz, crie `PullAudioInputStream` ou `PushAudioInputStream`. Em seguida, crie um `AudioConfig` de uma instância da sua classe de fluxos, especificando o formato de compressão da transmissão em fluxo.

Vamos supor que tem uma classe de fluxo de entrada chamada `myPushStream` e estiver a utilizar OPUS/OGG. Seu código pode ter este aspeto:

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

## <a name="next-steps"></a>Passos Seguintes

- [Obter a subscrição de avaliação de Voz](https://azure.microsoft.com/try/cognitive-services/)
- [Veja como a reconhecer a conversão de voz em c#](quickstart-csharp-dotnet-windows.md)
