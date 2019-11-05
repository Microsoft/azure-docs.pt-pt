---
title: Conceitos de fluxo de entrada de áudio do SDK de fala
titleSuffix: Azure Cognitive Services
description: Uma visão geral dos recursos da API de fluxo de entrada de áudio do SDK de fala.
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: fmegen
ms.openlocfilehash: e00feed416eb3e06b703a2ef4fe040f0c815716e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73464295"
---
# <a name="about-the-speech-sdk-audio-input-stream-api"></a>Sobre a API de fluxo de entrada de áudio do SDK de fala

A API de **fluxo de entrada de áudio** do SDK de fala fornece uma maneira de transmitir fluxos de áudio para os reconhecedores em vez de usar o microfone ou as APIs de arquivo de entrada.

As etapas a seguir são necessárias ao usar fluxos de entrada de áudio:

- Identifique o formato do fluxo de áudio. O formato deve ser suportado pelo SDK de fala e pelo serviço de fala. Atualmente, há suporte apenas para a seguinte configuração:

  Amostras de áudio no formato PCM, um canal, 16000 amostras por segundo, 32000 bytes por segundo, dois blocos de bloco (16 bits, incluindo preenchimento de um exemplo), 16 bits por amostra.

  O código correspondente no SDK para criar o formato de áudio é semelhante ao seguinte:

  ```
  byte channels = 1;
  byte bitsPerSample = 16;
  int samplesPerSecond = 16000;
  var audioFormat = AudioStreamFormat.GetWaveFormatPCM(samplesPerSecond, bitsPerSample, channels);
  ```

- Verifique se seu código pode fornecer os dados BRUTOs de áudio de acordo com essas especificações. Se os dados da fonte de áudio não corresponderem aos formatos com suporte, o áudio deverá ser transcodificado no formato necessário.

- Crie sua própria classe de fluxo de entrada de áudio derivada de `PullAudioInputStreamCallback`. Implemente os membros `Read()` e `Close()`. A assinatura de função exata depende do idioma, mas o código será semelhante a este exemplo de código:

  ```
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

- Crie uma configuração de áudio com base no seu formato de áudio e no fluxo de entrada. Passe a configuração de fala regular e a configuração de entrada de áudio ao criar o reconhecedor. Por exemplo:

  ```
  var audioConfig = AudioConfig.FromStreamInput(new ContosoAudioStream(config), audioFormat);

  var speechConfig = SpeechConfig.FromSubscription(...);
  var recognizer = new SpeechRecognizer(speechConfig, audioConfig);

  // run stream through recognizer
  var result = await recognizer.RecognizeOnceAsync();

  var text = result.GetText();
  ```

## <a name="next-steps"></a>Passos seguintes

* [Obter a subscrição de avaliação de Voz](https://azure.microsoft.com/try/cognitive-services/)
* [Veja como reconhecer a fala noC#](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
