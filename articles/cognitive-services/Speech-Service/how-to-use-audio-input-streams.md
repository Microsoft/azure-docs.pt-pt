---
title: Conceitos de fluxo de fluxo de entrada de áudio SDK de fala
titleSuffix: Azure Cognitive Services
description: Uma visão geral das capacidades da corrente de entrada áudio do Speech SDK API.
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: fmegen
ms.openlocfilehash: 3039276a49e7bb41660d114e78ca047a3f77f279
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "74109947"
---
# <a name="about-the-speech-sdk-audio-input-stream-api"></a>Sobre o fluxo de entrada áudio SDK speech SDK API

A API audio **input stream** do Speech SDK fornece uma forma de transmitir áudio para os reputadores em vez de usar o microfone ou o ficheiro de entrada APIs.

São necessários os seguintes passos quando utilizar fluxos de entrada áudio:

- Identifique o formato do fluxo de áudio. O formato deve ser apoiado pelo SDK da Fala e pelo serviço de Discurso. Atualmente, apenas é suportada a seguinte configuração:

  Amostras de áudio em formato PCM, um canal, 16000 amostras por segundo, 32000 bytes por segundo, dois blocos alinhados (16 bits incluindo estofo para uma amostra), 16 bits por amostra.

  O código correspondente no SDK para criar o formato áudio é assim:

  ```csharp
  byte channels = 1;
  byte bitsPerSample = 16;
  int samplesPerSecond = 16000;
  var audioFormat = AudioStreamFormat.GetWaveFormatPCM(samplesPerSecond, bitsPerSample, channels);
  ```

- Certifique-se de que o seu código pode fornecer os dados áudio RAW de acordo com estas especificações. Se os seus dados de fonte de áudio não corresponderem aos formatos suportados, o áudio deve ser transcodificado para o formato necessário.

- Crie a sua própria classe `PullAudioInputStreamCallback`de fluxo de entrada de áudio derivada de . Implementar `Read()` os `Close()` e membros. A assinatura exata da função é dependente da linguagem, mas o código será semelhante a esta amostra de código:

  ```csharp
   public class ContosoAudioStream : PullAudioInputStreamCallback {
      ContosoConfig config;

      public ContosoAudioStream(const ContosoConfig& config) {
          this.config = config;
      }

      public int Read(byte[] buffer, uint size) {
          // returns audio data to the caller.
          // e.g. return read(config.YYY, buffer, size);
      }

      public void Close() {
          // close and cleanup resources.
      }
   };
  ```

- Crie uma configuração de áudio baseada no seu formato de áudio e fluxo de entrada. Passe tanto na configuração regular da fala como na configuração de entrada de áudio quando criar o seu reconhecimento. Por exemplo:

  ```csharp
  var audioConfig = AudioConfig.FromStreamInput(new ContosoAudioStream(config), audioFormat);

  var speechConfig = SpeechConfig.FromSubscription(...);
  var recognizer = new SpeechRecognizer(speechConfig, audioConfig);

  // run stream through recognizer
  var result = await recognizer.RecognizeOnceAsync();

  var text = result.GetText();
  ```

## <a name="next-steps"></a>Passos seguintes

- [Obter a subscrição de avaliação de Voz](https://azure.microsoft.com/try/cognitive-services/)
- [Veja como reconhecer o discurso em C #](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
