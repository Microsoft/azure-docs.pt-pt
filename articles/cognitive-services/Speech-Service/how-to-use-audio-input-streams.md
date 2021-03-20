---
title: Conceitos de fluxo de entrada de áudio SDK de discurso
titleSuffix: Azure Cognitive Services
description: Uma visão geral das capacidades da API de entrada de áudio do Speech SDK.
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: fmegen
ms.custom: devx-track-csharp
ms.openlocfilehash: 87fa97dafe9de4a23f5eaadfd4083cd1ca517cde
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "95026595"
---
# <a name="about-the-speech-sdk-audio-input-stream-api"></a>Sobre o fluxo de entrada de áudio SDK do Discurso API

A API do **Fluxo** de Entrada áudio do Speech SDK fornece uma forma de transmitir áudio para os reconhecedores em vez de utilizar as APIs do microfone ou do ficheiro de entrada.

São necessários os seguintes passos quando se utilizam fluxos de entrada de áudio:

- Identifique o formato do fluxo de áudio. O formato deve ser suportado pelo Speech SDK e pelo serviço Speech. Atualmente, apenas é suportada a seguinte configuração:

  As amostras de áudio estão em formato PCM, um canal, 16 bits por amostra, 8000 ou 16000 amostras por segundo (16000 ou 32000 bytes por segundo), dois blocos alinhados (16 bits incluindo estofos para uma amostra).

  O código correspondente no SDK para criar o formato áudio é assim:

  ```csharp
  byte channels = 1;
  byte bitsPerSample = 16;
  int samplesPerSecond = 16000; // or 8000
  var audioFormat = AudioStreamFormat.GetWaveFormatPCM(samplesPerSecond, bitsPerSample, channels);
  ```

- Certifique-se de que o seu código fornece os dados áudio RAW de acordo com estas especificações. Também garanta que amostras de 16 bits chegam em formato pouco endiano. As amostras assinadas também são apoiadas. Se os dados da fonte de áudio não corresponderem aos formatos suportados, o áudio deve ser transcodificado para o formato necessário.

- Crie a sua própria classe de fluxo de entrada de áudio derivada de `PullAudioInputStreamCallback` . Implementar os `Read()` e `Close()` membros. A assinatura exata da função é dependente da linguagem, mas o código será semelhante a esta amostra de código:

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

- Crie uma configuração áudio baseada no seu formato de áudio e fluxo de entrada. Passe tanto na configuração regular da fala como na configuração de entrada de áudio quando criar o seu reconhecimento. Por exemplo:

  ```csharp
  var audioConfig = AudioConfig.FromStreamInput(new ContosoAudioStream(config), audioFormat);

  var speechConfig = SpeechConfig.FromSubscription(...);
  var recognizer = new SpeechRecognizer(speechConfig, audioConfig);

  // run stream through recognizer
  var result = await recognizer.RecognizeOnceAsync();

  var text = result.GetText();
  ```

## <a name="next-steps"></a>Passos seguintes

- [Criar uma conta do Azure gratuita](https://azure.microsoft.com/free/cognitive-services/)
- [Veja como reconhecer a fala em C #](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnet)