---
title: Consultar ponto de extremidade de contêiner de conversão de texto em fala
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: 8460ddca5cff2b3da540b5fa8cf66e0687892789
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/28/2019
ms.locfileid: "73491073"
---
O contêiner fornece [APIs de ponto de extremidade baseadas em REST](../rest-text-to-speech.md). Há muitos [projetos de código-fonte de exemplo](https://azure.microsoft.com/resources/samples/cognitive-speech-tts/) para a plataforma, estrutura e variações de linguagem disponíveis.

Com o contêiner de *conversão de texto em fala padrão* , você deve confiar na localidade e na voz da marca de imagem que baixou. Por exemplo, se você baixou a marca de `latest`, a localidade padrão será `en-US` e a voz `JessaRUS`. O argumento `{VOICE_NAME}` seria então [`en-US-JessaRUS`](../language-support.md#standard-voices). Consulte o exemplo SSML abaixo:

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="en-US-JessaRUS">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

No entanto, para *texto personalizado para fala* , você precisará obter a **voz/modelo** do [portal de voz personalizado](https://aka.ms/custom-voice-portal). O nome do modelo personalizado é sinônimo do nome da voz. Navegue até a página de **treinamento** e copie a **voz/modelo** para usar como o argumento `{VOICE_NAME}`.
<br><br>
:::image type="content" source="../media/custom-voice/custom-voice-model-voice-name.png" alt-text="Modelo de voz personalizado-nome da voz":::

Consulte o exemplo SSML abaixo:

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="custom-voice-model">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

Vamos construir uma solicitação HTTP POST, fornecendo alguns cabeçalhos e uma carga de dados. Substitua o espaço reservado `{VOICE_NAME}` pelo seu próprio valor.

```curl
curl -s -v -X POST http://localhost:5000/speech/synthesize/cognitiveservices/v1 \
 -H 'Accept: audio/*' \
 -H 'Content-Type: application/ssml+xml' \
 -H 'X-Microsoft-OutputFormat: riff-16khz-16bit-mono-pcm' \
 -d '<speak version="1.0" xml:lang="en-US"><voice name="{VOICE_NAME}">This is a test, only a test.</voice></speak>'
```

Este comando:

* Constrói uma solicitação HTTP POST para o ponto de extremidade `speech/synthesize/cognitiveservices/v1`.
* Especifica um cabeçalho de `Accept` de `audio/*`
* Especifica um cabeçalho `Content-Type` de `application/ssml+xml`, para obter mais informações, consulte [corpo da solicitação](../rest-text-to-speech.md#request-body).
* Especifica um cabeçalho `X-Microsoft-OutputFormat` de `riff-16khz-16bit-mono-pcm`, para obter mais opções, consulte [saída de áudio](../rest-text-to-speech.md#audio-outputs).
* Envia a solicitação de [linguagem de marcação de síntese de fala (SSML)](../speech-synthesis-markup.md) dada a `{VOICE_NAME}` ao ponto de extremidade.