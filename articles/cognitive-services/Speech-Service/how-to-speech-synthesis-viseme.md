---
title: Como obter eventos de pose facial para sincronização labial
titleSuffix: Azure Cognitive Services
description: O Discurso SDK apoia o evento viseme na síntese da fala, que são usadas para representar as poses-chave no discurso observado, como a posição dos lábios, mandíbula e língua ao produzir um determinado fonme.
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/03/2021
ms.author: yulili
ms.custom: references_regions
zone_pivot_groups: programming-languages-speech-services-nomore-variant
ms.openlocfilehash: e97c48d4e42627d0fc2caaa4f66e81b9a0cafa86
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105643903"
---
# <a name="get-facial-pose-events"></a>Obtenha eventos de pose facial

> [!NOTE]
> Viseme só funciona para `en-US-AriaNeural` a voz por enquanto.

Um viseme é a descrição visual de um fonês em linguagem falada.
Define a posição do rosto e da boca ao dizer uma palavra.
Cada viseme retrata as poses faciais chave para um conjunto específico de fones.
Não há correspondência entre visemes e fones.
Muitas vezes, vários fonmes correspondem a um único viseme, uma vez que vários fones de telefone ficam igual no rosto quando produzidos, tais como `s` e `z` .
Consulte a [tabela de mapeamento entre visemes e fones de telefone](#map-phonemes-to-visemes).

Usando visemes, você pode criar assistente de transmissão de notícias mais natural e inteligente, mais personagens de jogos e desenhos animados interativos, e vídeos de ensino de linguagem mais intuitiva. As pessoas com deficiência auditiva também podem captar sons visualmente e conteúdo sonoro "lip-read" que mostra visemes num rosto animado.

## <a name="get-viseme-events-with-the-speech-sdk"></a>Obtenha eventos visemes com o Discurso SDK

Para fazer eventos visemes, convertemos o texto de entrada num conjunto de sequências de fonei e respetivas sequências visemas. Estimamos a hora de início de cada viseme no áudio da fala. Os eventos viseme contêm uma sequência de IDs viseme, cada um com uma compensação no áudio onde esse viseme aparece. Estes eventos podem impulsionar animações bocais que simulam uma pessoa que fala o texto de entrada.

| Parâmetro | Descrição |
|-----------|-------------|
| Viseme ID | Número inteiro que especifica um viseme. Em inglês (Estados Unidos), oferecemos 22 visemes diferentes para retratar as formas da boca para um conjunto específico de fones. Consulte a [tabela de mapeamento entre o Viseme ID e os telefones](#map-phonemes-to-visemes).  |
| Compensação de áudio | A hora de início de cada viseme, em carrapatos (100 nanosegundos). |

Para obter eventos visemes, subscreva o `VisemeReceived` evento em Speech SDK.
Os seguintes snippets mostram como subscrever o evento viseme.

::: zone pivot="programming-language-csharp"

```csharp
using (var synthesizer = new SpeechSynthesizer(speechConfig, audioConfig))
{
    // Subscribes to viseme received event
    synthesizer.VisemeReceived += (s, e) =>
    {
        Console.WriteLine($"Viseme event received. Audio offset: " +
            $"{e.AudioOffset / 10000}ms, viseme id: {e.VisemeId}.");
    };

    var result = await synthesizer.SpeakSsmlAsync(ssml));
}

```

::: zone-end

::: zone pivot="programming-language-cpp"

```cpp
auto synthesizer = SpeechSynthesizer::FromConfig(speechConfig, audioConfig);

// Subscribes to viseme received event
synthesizer->VisemeReceived += [](const SpeechSynthesisVisemeEventArgs& e)
{
    cout << "viseme event received. "
        // The unit of e.AudioOffset is tick (1 tick = 100 nanoseconds), divide by 10,000 to convert to milliseconds.
        << "Audio offset: " << e.AudioOffset / 10000 << "ms, "
        << "viseme id: " << e.VisemeId << "." << endl;
};

auto result = synthesizer->SpeakSsmlAsync(ssml).get();
```

::: zone-end

::: zone pivot="programming-language-java"

```java
SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);

// Subscribes to viseme received event
synthesizer.VisemeReceived.addEventListener((o, e) -> {
    // The unit of e.AudioOffset is tick (1 tick = 100 nanoseconds), divide by 10,000 to convert to milliseconds.
    System.out.print("Viseme event received. Audio offset: " + e.getAudioOffset() / 10000 + "ms, ");
    System.out.println("viseme id: " + e.getVisemeId() + ".");
});

SpeechSynthesisResult result = synthesizer.SpeakSsmlAsync(ssml).get();
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_synthesizer = speechsdk.SpeechSynthesizer(speech_config=speech_config, audio_config=audio_config)

# Subscribes to viseme received event
speech_synthesizer.viseme_received.connect(lambda evt: print(
    "Viseme event received: audio offset: {}ms, viseme id: {}.".format(evt.audio_offset / 10000, evt.viseme_id)))

result = speech_synthesizer.speak_ssml_async(ssml).get()
```

::: zone-end

::: zone pivot="programming-language-javascript"

```Javascript
var synthesizer = new SpeechSDK.SpeechSynthesizer(speechConfig, audioConfig);

// Subscribes to viseme received event
synthesizer.visemeReceived = function (s, e) {
    window.console.log("(Viseme), Audio offset: " + e.audioOffset / 10000 + "ms. Viseme ID: " + e.visemeId);
}

synthesizer.speakSsmlAsync(ssml);
```

::: zone-end

::: zone pivot="programming-language-objectivec"

```Objective-C
SPXSpeechSynthesizer *synthesizer =
    [[SPXSpeechSynthesizer alloc] initWithSpeechConfiguration:speechConfig
                                           audioConfiguration:audioConfig];

// Subscribes to viseme received event
[synthesizer addVisemeReceivedEventHandler: ^ (SPXSpeechSynthesizer *synthesizer, SPXSpeechSynthesisVisemeEventArgs *eventArgs) {
    NSLog(@"Viseme event received. Audio offset: %fms, viseme id: %lu.", eventArgs.audioOffset/10000., eventArgs.visemeId);
}];

[synthesizer speakSsml:ssml];
```

::: zone-end

## <a name="map-phonemes-to-visemes"></a>Fonm de fonme para visemes

Os visemes variam de acordo com a linguagem. Cada idioma tem um conjunto de visemes que correspondem aos seus fonmes específicos. A tabela seguinte mostra a correspondência entre fonmes do Alfabeto Fonético Internacional (IPA) e iDs viseme para inglês (Estados Unidos).

| IPA | Exemplo | Viseme ID |
|-----|---------|-----------|
| i   | **ea** t   | 6 |
| ɪ   | **i** f | 6 |
| eɪ  | **um** te | 4 |
| ɛ | **e** muito | 4 |
|æ  |   **um** ctive        |1|
|ɑ  |   **o** bstinado     |2|
|ɔ  |   c **au** se         |3|
|ʊ  |   b **oo** k          |4|
|oʊ |   **o** ld           |8|
|u  |   **U** ber          |7|
|ʌ  |   **u** ncle         |1|
|aɪ |   **i** ce           |11|
|aʊ |   **ou** t           |9|
|ɔɪ |   **oi** l           |10|
|ju |   **Yu** ma          |[6, 7]|
|ə  |   **um** ir           |1|
|ɪɹ |   **orelha** s          |[6, 13]|
|ɛɹ |   avião **de ar**      |[4, 13]|
|ʊɹ |   c **o seu** e          |[4, 13]|
|aɪ(φ)ɹ |   **Terra de ire**   |[11, 13]|
|aʊ(φ)ɹ |   **hora** s     |[9, 13]|
|ɔɹ |   **ou** ange        |[3, 13]|
|ɑɹ |   **ar** tist        |[2, 13]|
|ɝ  |   **orelha** th         |[5, 13]|
|ɚ  |   todos **er** gy       |[1, 13]|
|w  |   **w** ith, s **ue** de   |7|
|j  |   **y** ard,f **e** w     |6|
|p  |   **p** ut           |21|
|b  |   **b** ig           |21|
|t  |   **t** alca          |19|
|d  |   **d** ig           |19|
|K  |   **c** ut           |20|
|g  |   **g** o            |20|
|m  |   **m** em, s **m** cinzas    |21|
|n  |   **n** o, s **n** ow      |19|
|ŋ  |   li **n** k          |20|
|f  |   **f** ork          |18|
|v  |   **v** alue         |18|
|θ  |   **th** em          |17|
|ð  |   **th** en          |17|
|t  |   **é** que           |15|
|z  |   **z** ap           |15|
|ʃ  |   **sh** e           |16|
|ʒ  |   **J** acques       |16|
|h  |   **h** elp          |12|
|tʃ |   **ch** em          |16|
|dʒ |   **j** oy           |16|
|l  |   **Id,** g **l** anúncio     |14|
|ɹ  |   **r** ed, b **r** ing    |13|


## <a name="next-steps"></a>Passos seguintes

* [Documentação de referência do SDK de fala](speech-sdk.md)
