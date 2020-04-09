---
title: Ponto final do recipiente texto-a-fala
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 6fe07da1e658efa8150c8cafb95bc5719c780aff
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879034"
---
O recipiente fornece [APIs de ponto final baseado em REPOUSO](../rest-text-to-speech.md). Existem muitos projetos de [código-fonte](https://azure.microsoft.com/resources/samples/cognitive-speech-tts/) de amostra para as variações de plataforma, enquadramento e linguagem disponíveis.

Com o recipiente *Standard Text-to-speech,* deve contar com o local e a voz da etiqueta de imagem que descarregou. Por exemplo, se descarregou a etiqueta `latest` `en-US` o `JessaRUS` local padrão é e a voz. O `{VOICE_NAME}` argumento seria, [`en-US-JessaRUS`](../language-support.md#standard-voices)então. Veja o exemplo SSML abaixo:

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="en-US-JessaRUS">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

No entanto, para *texto-a-voz personalizado,* terá de obter o **Voice/model** a partir do portal de [voz personalizado](https://aka.ms/custom-voice-portal). O nome do modelo personalizado é sinónimo do nome de voz. Navegue para a página **de Formação** e `{VOICE_NAME}` copie a **Voz/modelo** para usar como argumento.
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

Vamos construir um pedido HTTP POST, fornecendo alguns cabeçalhos e uma carga útil de dados. Substitua `{VOICE_NAME}` o espaço reservado pelo seu próprio valor.

```curl
curl -s -v -X POST http://localhost:5000/speech/synthesize/cognitiveservices/v1 \
 -H 'Accept: audio/*' \
 -H 'Content-Type: application/ssml+xml' \
 -H 'X-Microsoft-OutputFormat: riff-16khz-16bit-mono-pcm' \
 -d '<speak version="1.0" xml:lang="en-US"><voice name="{VOICE_NAME}">This is a test, only a test.</voice></speak>'
```

Este comando:

* Constrói um pedido HTTP `speech/synthesize/cognitiveservices/v1` POST para o ponto final.
* Especifica um `Accept` cabeçalho de`audio/*`
* Especifica um `Content-Type` cabeçalho de, `application/ssml+xml`para mais informações, consulte o órgão de [pedido](../rest-text-to-speech.md#request-body).
* Especifica um `X-Microsoft-OutputFormat` cabeçalho de, `riff-16khz-16bit-mono-pcm`para mais opções, ver saída de [áudio](../rest-text-to-speech.md#audio-outputs).
* Envia o pedido de markup de síntese `{VOICE_NAME}` da [fala (SSML)](../speech-synthesis-markup.md) dado o ponto final.