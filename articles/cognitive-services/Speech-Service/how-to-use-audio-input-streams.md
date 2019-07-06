---
title: Conceitos de fluxo de entrada de áudio do SDK de voz
titleSuffix: Azure Cognitive Services
description: Uma visão geral dos recursos do fluxo de entrada do SDK de voz áudio API.
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: fmegen
ms.openlocfilehash: 06b69da7f7435ce8a1e32150b7abe161ebdf527c
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606506"
---
# <a name="about-the-speech-sdk-audio-input-stream-api"></a>API do stream sobre a entrada de áudio do SDK de voz

O SDK de voz **Stream de entrada de áudio** API fornece uma maneira para transmitir fluxos de áudio para os reconhecedores tenham em vez de utilizar o microfone ou o ficheiro de entrada de APIs.

Os seguintes passos são necessários quando utilizar o áudio de entrada fluxos:

- Identifica o formato do fluxo de áudio. O formato tem de suportar o SDK de voz e o serviço de voz. Atualmente, é suportada apenas a seguinte configuração:

  Exemplos de áudio no formato PCM, um canal, 16000 exemplos por segundo, 32000 bytes por segundo, dois blocos alinham (incluindo o preenchimento de um exemplo de 16 bits), de 16 bits por amostra.

  O código correspondente no SDK para criar o formato de áudio tem esta aparência:

  ```
  byte channels = 1;
  byte bitsPerSample = 16;
  int samplesPerSecond = 16000;
  var audioFormat = AudioStreamFormat.GetWaveFormatPCM(samplesPerSecond, bitsPerSample, channels);
  ```

- Certifique-se de que seu código pode fornecer os dados de áudio não processados, de acordo com essas especificações. Se os dados de origem de áudio não corresponderem os formatos suportados, o áudio tem de ser transcodificados no formato necessário.

- Criar sua própria classe de fluxo de entrada de áudio derivada de `PullAudioInputStreamCallback`. Implementar o `Read()` e `Close()` membros. A assinatura de função exata é dependente de idioma, mas o código será semelhante ao seguinte exemplo de código:

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

- Crie uma configuração de áudio com base na sua áudio stream de entrada e de formato. Transmita sua configuração de voz regular e a configuração de entrada de áudio quando cria o reconhecedor. Por exemplo:

  ```
  var audioConfig = AudioConfig.FromStreamInput(new ContosoAudioStream(config), audioFormat);

  var speechConfig = SpeechConfig.FromSubscription(...);
  var recognizer = new SpeechRecognizer(speechConfig, audioConfig);

  // run stream through recognizer
  var result = await recognizer.RecognizeOnceAsync();

  var text = result.GetText();
  ```

## <a name="next-steps"></a>Passos Seguintes

* [Obter a subscrição de avaliação de Voz](https://azure.microsoft.com/try/cognitive-services/)
* [Veja como a reconhecer a conversão de voz em c#](quickstart-csharp-dotnet-windows.md)
