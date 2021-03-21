---
title: Ponto final do recipiente de texto para a fala
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/31/2020
ms.author: aahi
ms.openlocfilehash: 9a29244745b154aa81997813fcf4e1457f599270
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103622188"
---
O recipiente fornece [APIs de ponta com base em REST](../rest-text-to-speech.md). Existem muitos [projetos de código fonte de amostra](https://github.com/Azure-Samples/Cognitive-Speech-TTS) para plataforma, enquadramento e variações linguísticas disponíveis.

Com os recipientes Standard ou Neural Text-to-speech, deve contar com o local e a voz da etiqueta de imagem que descarregou. Por exemplo, se descarregou a `latest` etiqueta, o local padrão é `en-US` e a `AriaNeural` voz. O `{VOICE_NAME}` argumento seria [`en-US-AriaNeural`](../language-support.md#neural-voices) então. Veja o exemplo SSML abaixo:

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="en-US-AriaNeural">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

No entanto, para *o texto-a-voz personalizado,* terá de obter o **Voice/model** a partir do [portal de voz personalizado](https://aka.ms/custom-voice-portal). O nome do modelo personalizado é sinónimo do nome de voz. Navegue para a página **Treino** e copie a **Voz/modelo** para usar como `{VOICE_NAME}` argumento.
<br><br>
:::image type="content" source="../media/custom-voice/custom-voice-model-voice-name.png" alt-text="Modelo de voz personalizado - nome de voz":::

Veja o exemplo SSML abaixo:

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="custom-voice-model">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

Vamos construir um pedido HTTP POST, fornecendo alguns cabeçalhos e uma carga de dados. Substitua o `{VOICE_NAME}` espaço reservado pelo seu próprio valor.

```curl
curl -s -v -X POST http://localhost:5000/speech/synthesize/cognitiveservices/v1 \
 -H 'Accept: audio/*' \
 -H 'Content-Type: application/ssml+xml' \
 -H 'X-Microsoft-OutputFormat: riff-24khz-16bit-mono-pcm' \
 -d '<speak version="1.0" xml:lang="en-US"><voice name="{VOICE_NAME}">This is a test, only a test.</voice></speak>' > output.wav
```


Este comando:

* Constrói um pedido HTTP POST para o `speech/synthesize/cognitiveservices/v1` ponto final.
* Especifica um `Accept` cabeçalho de `audio/*`
* Especifica um `Content-Type` cabeçalho de `application/ssml+xml` , para mais informações, ver [corpo de pedido](../rest-text-to-speech.md#request-body).
* Especifica um `X-Microsoft-OutputFormat` cabeçalho de `riff-16khz-16bit-mono-pcm` , para mais opções ver saída de [áudio](../rest-text-to-speech.md#audio-outputs).
* Envia o pedido de síntese de [síntese de discurso (SSML)](../speech-synthesis-markup.md) dado o `{VOICE_NAME}` ponto final.
