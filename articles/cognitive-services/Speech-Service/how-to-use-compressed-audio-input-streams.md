---
title: Áudio de Stream compactado com o SDK de voz - serviços de voz
titleSuffix: Azure Cognitive Services
description: Saiba como transmitir áudio compactado para serviços de voz do Azure com o SDK de voz. Disponível para C++, C#e o Java para Linux.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: amishu
ms.openlocfilehash: 2066dc3e20ab9fc92b23fd071728ea6a920d3324
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59012425"
---
# <a name="stream-compressed-audio-with-the-speech-sdk"></a>Áudio de Stream compactado com o SDK de voz

O SDK de voz **Stream de entrada de áudio compactados** API fornece uma maneira para transmitir áudio compactado para o serviço de voz usando PullStream ou PushStream.

> [!IMPORTANT]
> Transmissão em fluxo de áudio compactado só é suportado para C++, C#e o Java no Linux (Ubuntu 16.04 ou Ubuntu 18.04).
> O suporte está limitado a arquivos MP3 e OPUS/OGG.

## <a name="prerequisites"></a>Pré-requisitos

Tem de instalar estas dependências para utilizar a entrada de áudio compactada com o SDK de voz para Linux:

```sh
sudo apt install libgstreamer1.0-0 gstreamer1.0-plugins-base gstreamer1.0-plugins-good gstreamer1.0-plugins-bad gstreamer1.0-plugins-ugly
```

## <a name="streaming-compressed-audio"></a>Transmissão em fluxo de áudio compactado

Para transmitir em fluxo num formato compactado de áudio para os serviços de voz, crie `PullAudioInputStream` ou `PushAudioInputStream`. Em seguida, crie um `AudioConfig` de uma instância da sua classe de fluxos, especificando o formato de compressão da transmissão em fluxo.

Vamos supor que tem uma classe de fluxo de entrada chamada `myPushStream` e estiver a utilizar OPUS/OGG. Este é o que o código pode ser semelhante: 

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

* [Obter a subscrição de avaliação de Voz](https://azure.microsoft.com/try/cognitive-services/)
* [Veja como a reconhecer a conversão de voz em c#](quickstart-csharp-dotnet-windows.md)
