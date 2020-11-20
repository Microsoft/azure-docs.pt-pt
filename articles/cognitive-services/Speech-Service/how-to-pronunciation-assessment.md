---
title: Como usar a fala SDK para avaliação de pronúncia
titleSuffix: Azure Cognitive Services
description: O SDK do Discurso apoia a avaliação da pronúncia, que avalia a qualidade da pronúncia da entrada da fala, com indicadores de precisão, fluência, completude, etc.
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/29/2020
ms.author: yulili
ms.custom: references_regions
zone_pivot_groups: programming-languages-set-nineteen
ms.openlocfilehash: 1cc313daf0e76ddd14865959410b07d9bdc189d7
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94984926"
---
# <a name="pronunciation-assessment"></a>Avaliação da pronúncia

A avaliação da pronúncia avalia a pronúncia da fala e dá aos oradores feedback sobre a precisão e fluência do áudio falado.
Com a avaliação da pronúncia, os alunos de línguas podem praticar, obter feedback instantâneo e melhorar a sua pronúncia para que possam falar e apresentar com confiança.
Os educadores podem usar a capacidade de avaliar a pronúncia de vários falantes em tempo real.

Neste artigo, você vai aprender a configurar `PronunciationAssessmentConfig` e recuperar o uso do discurso `PronunciationAssessmentResult` SDK.

> [!NOTE]
> A funcionalidade de avaliação da pronúncia só está disponível nas regiões `westus` `eastasia` `centralindia` e, e só suporta a `en-US` linguagem.

## <a name="pronunciation-assessment-with-the-speech-sdk"></a>Avaliação de pronúncia com o SDK do discurso

Nas amostras abaixo, você vai criar `PronunciationAssessmentConfig` um, em seguida, aplicá-lo a `SpeechRecognizer` .

Os seguintes snippets ilustram como utilizar a deteção automática de idiomas nas suas aplicações:

::: zone pivot="programming-language-csharp"

```csharp
var pronunciationAssessmentConfig = new PronunciationAssessmentConfig(
    "reference text", GradingSystem.HundredMark, Granularity.Phoneme);

using (var recognizer = new SpeechRecognizer(
    speechConfig,
    audioConfig))
{
    // apply the pronunciation assessment configuration to the speech recognizer
    pronunciationAssessmentConfig.ApplyTo(recognizer);
    var speechRecognitionResult = await recognizer.RecognizeOnceAsync();
    var pronunciationAssessmentResult =
        PronunciationAssessmentResult.FromResult(speechRecognitionResult);
    var pronunciationScore = pronunciationAssessmentResult.PronunciationScore;
}
```

::: zone-end

::: zone pivot="programming-language-cpp"

```cpp
auto pronunciationAssessmentConfig =
    PronunciationAssessmentConfig::Create("reference text",
        PronunciationAssessmentGradingSystem::HundredMark,
        PronunciationAssessmentGranularity::Phoneme);

auto recognizer = SpeechRecognizer::FromConfig(
    speechConfig,
    audioConfig);

// apply the pronunciation assessment configuration to the speech recognizer
pronunciationAssessmentConfig->ApplyTo(recognizer);
speechRecognitionResult = recognizer->RecognizeOnceAsync().get();
auto pronunciationAssessmentResult =
    PronunciationAssessmentResult::FromResult(speechRecognitionResult);
auto pronunciationScore = pronunciationAssessmentResult->PronunciationScore;
```

::: zone-end

::: zone pivot="programming-language-java"

```java
PronunciationAssessmentConfig pronunciationAssessmentConfig =
    new PronunciationAssessmentConfig("reference text", 
        PronunciationAssessmentGradingSystem.HundredMark,
        PronunciationAssessmentGranularity.Phoneme);

SpeechRecognizer recognizer = new SpeechRecognizer(
    speechConfig,
    audioConfig);

// apply the pronunciation assessment configuration to the speech recognizer
pronunciationAssessmentConfig.applyTo(recognizer);
Future<SpeechRecognitionResult> future = recognizer.recognizeOnceAsync();
SpeechRecognitionResult result = future.get(30, TimeUnit.SECONDS);
PronunciationAssessmentResult pronunciationAssessmentResult =
    PronunciationAssessmentResult.fromResult(result);
Double pronunciationScore = pronunciationAssessmentResult.getPronunciationScore();

recognizer.close();
speechConfig.close();
audioConfig.close();
pronunciationAssessmentConfig.close();
result.close();
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
pronunciation_assessment_config = \
        speechsdk.PronunciationAssessmentConfig(reference_text='reference text',
                grading_system=msspeech.PronunciationAssessmentGradingSystem.HundredMark,
                granularity=msspeech.PronunciationAssessmentGranularity.Phoneme)
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, \
        audio_config=audio_config)

# apply the pronunciation assessment configuration to the speech recognizer
pronunciation_assessment_config.apply_to(speech_recognizer)
result = speech_recognizer.recognize_once()
pronunciation_assessment_result = speechsdk.PronunciationAssessmentResult(result)
pronunciation_score = pronunciation_assessment_result.pronunciation_score
```

::: zone-end

::: zone pivot="programming-language-objectivec"

```Objective-C
SPXPronunciationAssessmentConfiguration* pronunciationAssessmentConfig =
    [[SPXPronunciationAssessmentConfiguration alloc]init:@"reference text"
                                           gradingSystem:SPXPronunciationAssessmentGradingSystem_HundredMark
                                             granularity:SPXPronunciationAssessmentGranularity_Phoneme];

SPXSpeechRecognizer* speechRecognizer = \
        [[SPXSpeechRecognizer alloc] initWithSpeechConfiguration:speechConfig
                                              audioConfiguration:audioConfig];

// apply the pronunciation assessment configuration to the speech recognizer
[pronunciationAssessmentConfig applyToRecognizer:speechRecognizer];

SPXSpeechRecognitionResult *result = [speechRecognizer recognizeOnce];
SPXPronunciationAssessmentResult* pronunciationAssessmentResult = [[SPXPronunciationAssessmentResult alloc] init:result];
double pronunciationScore = pronunciationAssessmentResult.pronunciationScore;
```

::: zone-end

### <a name="pronunciation-assessment-configuration-parameters"></a>Parâmetros de configuração da avaliação da pronúncia

Esta tabela lista os parâmetros de configuração para avaliação da pronúncia.

| Parâmetro | Descrição | Necessário? |
|-----------|-------------|---------------------|
| Texto de Referência | O texto contra o que a pronúncia será avaliado. | Necessário |
| Sistema de Classificação | O sistema de pontos para a calibração da pontuação. O `FivePoint` sistema dá uma pontuação de 0-5 pontos flutuantes, e dá uma `HundredMark` pontuação de 0-100 pontos flutuantes. Predefinição: `FivePoint`. | Opcional |
| Granularidade | A granularidade de avaliação. Os valores aceites são `Phoneme` , que mostram a pontuação no nível completo do texto, palavra e fon de texto, que mostra a `Word` pontuação no texto completo e no nível de palavra, `FullText` que mostra a pontuação apenas no nível de texto completo. Predefinição: `Phoneme`. | Opcional |
| EnableMiscue | Permite o cálculo do erro. Com isto ativado, as palavras pronunciadas serão comparadas com o texto de referência, e serão marcadas com omissão/inserção com base na comparação. Os valores aceites são `False` `True` e. Predefinição: `False`. | Opcional |
| CenárioId | Um GUID indicando um sistema de pontos personalizado. | Opcional |

### <a name="pronunciation-assessment-result-parameters"></a>Parâmetros de resultados de avaliação da pronúncia

Esta tabela enumera os parâmetros de resultado da avaliação da pronúncia.

| Parâmetro | Descrição |
|-----------|-------------|
| `AccuracyScore` | Precisão de pronúncia do discurso. A precisão indica como os fones correspondem à pronúncia de um altifalante nativo. A pontuação de precisão do nível de texto completo e palavra é agregada a partir da pontuação de precisão do nível do telefone. |
| `FluencyScore` | Fluência do discurso dado. A fluência indica o quão perto o discurso corresponde ao uso de quebras silenciosas de um orador nativo entre as palavras. |
| `CompletenessScore` | Completude da fala, determinada calculando a relação de palavras pronunciadas com a entrada de texto de referência. |
| `PronunciationScore` | Pontuação geral indicando a qualidade da pronúncia do discurso dado. Isto é agregado de `AccuracyScore` , `FluencyScore` e com `CompletenessScore` peso. |
| `ErrorType` | Este valor indica se uma palavra é omitida, inserida ou mal pronunciada, em comparação com `ReferenceText` . Os valores possíveis são `None` (ou seja, nenhum erro nesta palavra), `Omission` `Insertion` e `Mispronunciation` . |

## <a name="next-steps"></a>Próximos passos

<!-- TODO: update the sample links after release -->

<!-- ::: zone pivot="programming-language-csharp"
* See the [sample code](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_recognition_samples.cs#L741) on GitHub for automatic language detection
::: zone-end

::: zone pivot="programming-language-cpp"
* See the [sample code](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/cpp/windows/console/samples/speech_recognition_samples.cpp#L507) on GitHub for automatic language detection
::: zone-end

::: zone pivot="programming-language-java"
* See the [sample code](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/SpeechRecognitionSamples.java#L521) on GitHub for automatic language detection
::: zone-end

::: zone pivot="programming-language-python"
* See the [sample code](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_synthesis_sample.py#L434) on GitHub for automatic language detection
::: zone-end

::: zone pivot="programming-language-objectivec"
* See the [sample code](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/objective-c/ios/speech-samples/speech-samples/ViewController.m#L494) on GitHub for automatic language detection
::: zone-end -->

* [Documentação de referência do SDK de fala](speech-sdk.md)
