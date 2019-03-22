---
title: Speech Synthesis Markup Language (SSML) - serviços de voz
titleSuffix: Azure Cognitive Services
description: Usando a linguagem de marcação de síntese de voz para controlar pronúncia e prosody em voz.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 57fc7e699d88dbe777750e3acdb7f96794b66fc0
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57837174"
---
# <a name="speech-synthesis-markup-language-ssml"></a>Speech Synthesis Markup Language (SSML)

O Speech Synthesis Markup Language (SSML) é uma linguagem de marcação baseada em XML que fornece uma forma de controlar a pronúncia e *prosody* de voz. Prosody refere-se ao ritmo e pitch de voz — suas músicas, se quiser. Pode especificar palavras foneticamente, fornecer indicações para interpretar números, insira a coloca em pausa, pitch de controle, volume e taxa de e muito mais. Para obter mais informações, consulte [Speech Synthesis Markup Language (SSML) versão 1.0](https://www.w3.org/TR/2009/REC-speech-synthesis-20090303/).

Para obter uma lista completa de vozes (neurais e padrão), as localidades e idiomas suportados, consulte [suporte de idiomas](language-support.md#text-to-speech).

As secções seguintes fornecem exemplos para voz comuns as tarefas de síntese.

## <a name="add-a-break"></a>Adicionar uma quebra de
```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, Jessa24kRUS)'>
    Welcome to Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.
</voice> </speak>
```

## <a name="change-speaking-rate"></a>Taxa de alteração de fala

Taxa de fala pode ser aplicado a vozes padrão no word ou no nível de frases. Ao passo que a taxa de fala só pode ser aplicada para vozes neurais ao nível da sentença.

```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, Guy24kRUS)'>
<prosody rate="+30.00%">
    Welcome to Microsoft Cognitive Services Text-to-Speech API.
</prosody></voice> </speak>
```

## <a name="pronunciation"></a>Pronúncia
```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, Jessa24kRUS)'>
    <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme>
</voice> </speak>
```

## <a name="change-volume"></a>Volume de alterações

Alterações de volume podem ser aplicadas a vozes padrão no word ou no nível de frases. Ao passo que só podem ser aplicadas alterações de volume para vozes neurais ao nível da sentença.

```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>
<prosody volume="+20.00%">
    Welcome to Microsoft Cognitive Services Text-to-Speech API.
</prosody></voice> </speak>
```

## <a name="change-pitch"></a>Alterar o argumento de venda

Pitch alterações podem ser aplicadas a vozes padrão no word ou no nível de frases. Ao passo que só podem ser aplicadas alterações de argumento de venda para vozes neurais ao nível da sentença.

```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
    <voice  name='Microsoft Server Speech Text to Speech Voice (en-US, Guy24kRUS)'>
    Welcome to <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody>
</voice> </speak>
```

## <a name="change-pitch-contour"></a>Contour de argumento de venda de alteração

> [!IMPORTANT]
> Alterações de contour Pitch não são suportadas com vozes neurais.

```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>
<prosody contour="(80%,+20%) (90%,+30%)" >
    Good morning.
</prosody></voice> </speak>
```

## <a name="use-multiple-voices"></a>Utilizar várias vozes
```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>
    Good morning!
</voice>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, Guy24kRUS)'>
    Good morning to you too Jessa!
</voice> </speak>
```

## <a name="next-steps"></a>Passos Seguintes

* [Suporte de idiomas: vozes, localidades e idiomas](language-support.md)
