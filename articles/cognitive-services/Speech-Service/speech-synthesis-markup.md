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
ms.openlocfilehash: 9871e0106ee6caf11c5a1e24459fbd2044f5f3d7
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65021444"
---
# <a name="speech-synthesis-markup-language-ssml"></a>Speech Synthesis Markup Language (SSML)

O Speech Synthesis Markup Language (SSML) é uma linguagem de marcação baseada em XML que fornece uma forma de controlar a pronúncia e *prosody* de voz. Prosody refere-se ao ritmo e pitch de voz — suas músicas, se quiser. Pode especificar palavras foneticamente, fornecer indicações para interpretar números, insira a coloca em pausa, pitch de controle, volume e taxa de e muito mais. Para obter mais informações, consulte [Speech Synthesis Markup Language (SSML) versão 1.0](https://www.w3.org/TR/2009/REC-speech-synthesis-20090303/). 

Para obter uma lista completa de vozes (neurais e padrão), as localidades e idiomas suportados, consulte [suporte de idiomas](language-support.md#text-to-speech).

As secções seguintes fornecem exemplos para voz comuns as tarefas de síntese.

## <a name="adjust-speaking-style-for-neural-voices"></a>Ajustar o estilo de fala para vozes neurais

Pode usar SSML para ajustar o estilo de fala quando utilizar um das vozes neurais.

Por predefinição, o serviço de texto para discurso sintetiza texto num estilo neutro. As vozes neurais estendem SSML com um `<mstts:express-as>` estilos do elemento que converte texto em fala sintetizada falar o diferentes. Atualmente, as marcas de estilo só são suportadas com estes vozes:

* `en-US-JessaNeural` 
* `zh-CN-XiaoxiaoNeural`.

Alterações de estilo de fala pode ser aplicada ao nível da sentença. Os estilos variam por meio da voz. Se um tipo de estilo não é suportado, o serviço irá devolver a fala sintetizada como o estilo de neutro predefinido.

| Voz | Estilo | Descrição | 
|-----------|-----------------|----------|
| `en-US-JessaNeural` | type=`cheerful` | Expresse emoção positivo e Feliz |
| | type=`empathy` | Expressa uma noção do vençam e compreender |
| `zh-CN-XiaoxiaoNeural` | type=`newscast` | Expresse um tom formal, semelhante às difusões de notícias |
| | type=`sentiment ` | Transmite uma mensagem touching ou uma história |

```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
<voice name='en-US-JessaNeural'>
<mstts:express-as type="cheerful"> 
    That'd be just amazing! 
</mstts:express-as></voice></speak>
```

## <a name="add-a-break"></a>Adicionar uma quebra de
```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='en-US-Jessa24kRUS'>
    Welcome to Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.
</voice> </speak>
```

## <a name="change-speaking-rate"></a>Taxa de alteração de fala

Taxa de fala pode ser aplicado a vozes padrão no word ou no nível de frases. Ao passo que a taxa de fala só pode ser aplicada para vozes neurais ao nível da sentença.

```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='en-US-Guy24kRUS'>
<prosody rate="+30.00%">
    Welcome to Microsoft Cognitive Services Text-to-Speech API.
</prosody></voice> </speak>
```

## <a name="pronunciation"></a>Pronúncia
```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='en-US-Jessa24kRUS'>
    <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme>
</voice> </speak>
```

## <a name="change-volume"></a>Volume de alterações

Alterações de volume podem ser aplicadas a vozes padrão no word ou no nível de frases. Ao passo que só podem ser aplicadas alterações de volume para vozes neurais ao nível da sentença.

```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='en-US-Jessa24kRUS'>
<prosody volume="+20.00%">
    Welcome to Microsoft Cognitive Services Text-to-Speech API.
</prosody></voice> </speak>
```

## <a name="change-pitch"></a>Alterar o argumento de venda

Pitch alterações podem ser aplicadas a vozes padrão no word ou no nível de frases. Ao passo que só podem ser aplicadas alterações de argumento de venda para vozes neurais ao nível da sentença.

```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
    <voice  name='en-US-Guy24kRUS'>
    Welcome to <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody>
</voice> </speak>
```

## <a name="change-pitch-contour"></a>Contour de argumento de venda de alteração

> [!IMPORTANT]
> Alterações de contour Pitch não são suportadas com vozes neurais.

```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='en-US-Jessa24kRUS'>
<prosody contour="(80%,+20%) (90%,+30%)" >
    Good morning.
</prosody></voice> </speak>
```

## <a name="use-multiple-voices"></a>Utilizar várias vozes
```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='en-US-Jessa24kRUS'>
    Good morning!
</voice>
<voice  name='en-US-Guy24kRUS'>
    Good morning to you too Jessa!
</voice> </speak>
```

## <a name="next-steps"></a>Passos Seguintes

* [Suporte de idiomas: vozes, localidades e idiomas](language-support.md)
