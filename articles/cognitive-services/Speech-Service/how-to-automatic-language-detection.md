---
title: Como usar a deteção automática de linguagem para a fala ao texto
titleSuffix: Azure Cognitive Services
description: O SDK da Fala suporta a deteção automática de linguagem para a fala a texto. Ao utilizar esta funcionalidade, o áudio fornecido é comparado com uma lista fornecida de idiomas, sendo determinada a correspondência mais provável. O valor devolvido pode então ser usado para selecionar o modelo de idioma utilizado para a fala a texto.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: trbye
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: fefbe793fa4a6b90ba9bf8d468d42dcbd315759c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81402205"
---
# <a name="automatic-language-detection-for-speech-to-text"></a>Deteção automática de linguagem para fala a texto

A deteção automática de idiomas é usada para determinar a correspondência mais provável para o áudio passado para o SDK da fala quando comparada com uma lista de idiomas fornecidos. O valor devolvido pela deteção automática de idiomas é então utilizado para selecionar o modelo de linguagem para a fala ao texto, proporcionando-lhe uma transcrição mais precisa. Para ver quais línguas estão disponíveis, consulte [o suporte da Linguagem.](language-support.md)

Neste artigo, você aprenderá a `AutoDetectSourceLanguageConfig` usar `SpeechRecognizer` para construir um objeto e recuperar a linguagem detetada.

> [!IMPORTANT]
> Esta funcionalidade só está disponível para o Speech SDK para C#, C++, Java e Python.

## <a name="automatic-language-detection-with-the-speech-sdk"></a>Deteção automática de linguagem com o SDK da Fala

A deteção automática de linguagens tem atualmente um limite de serviços de dois idiomas por deteção. Tenha em mente esta `AudoDetectSourceLanguageConfig` limitação ao construir o seu objeto. Nas amostras abaixo, criará `AutoDetectSourceLanguageConfig`um , em `SpeechRecognizer`seguida, usá-lo para construir um .

> [!TIP]
> Também pode especificar um modelo personalizado para usar ao executar o discurso por texto. Para mais informações, consulte [Utilize um modelo personalizado para deteção automática de idiomas.](#use-a-custom-model-for-automatic-language-detection)

Os seguintes snippets ilustram como usar a deteção automática de idiomas nas suas apps:

::: zone pivot="programming-language-csharp"

```csharp
var autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig.FromLanguages(
        new string[] { "en-US", "de-DE" });

using (var recognizer = new SpeechRecognizer(
    speechConfig,
    autoDetectSourceLanguageConfig,
    audioConfig))
{
    var speechRecognitionResult = await recognizer.RecognizeOnceAsync();
    var autoDetectSourceLanguageResult =
        AutoDetectSourceLanguageResult.FromResult(speechRecognitionResult);
    var detectedLanguage = autoDetectSourceLanguageResult.Language;
}
```

::: zone-end

::: zone pivot="programming-language-cpp"

```cpp
auto autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig::FromLanguages({ "en-US", "de-DE" });

auto recognizer = SpeechRecognizer::FromConfig(
    speechConfig,
    autoDetectSourceLanguageConfig,
    audioConfig);

speechRecognitionResult = recognizer->RecognizeOnceAsync().get();
auto autoDetectSourceLanguageResult =
    AutoDetectSourceLanguageResult::FromResult(speechRecognitionResult);
auto detectedLanguage = autoDetectSourceLanguageResult->Language;
```

::: zone-end

::: zone pivot="programming-language-java"

```java
AutoDetectSourceLanguageConfig autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig.fromLanguages(Arrays.asList("en-US", "de-DE"));

SpeechRecognizer recognizer = new SpeechRecognizer(
    speechConfig,
    autoDetectSourceLanguageConfig,
    audioConfig);

Future<SpeechRecognitionResult> future = recognizer.recognizeOnceAsync();
SpeechRecognitionResult result = future.get(30, TimeUnit.SECONDS);
AutoDetectSourceLanguageResult autoDetectSourceLanguageResult =
    AutoDetectSourceLanguageResult.fromResult(result);
String detectedLanguage = autoDetectSourceLanguageResult.getLanguage();

recognizer.close();
speechConfig.close();
autoDetectSourceLanguageConfig.close();
audioConfig.close();
result.close();
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
auto_detect_source_language_config = \
        speechsdk.languageconfig.AutoDetectSourceLanguageConfig(languages=["en-US", "de-DE"])
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, 
        auto_detect_source_language_config=auto_detect_source_language_config, 
        audio_config=audio_config)
result = speech_recognizer.recognize_once()
auto_detect_source_language_result = speechsdk.AutoDetectSourceLanguageResult(result)
detected_language = auto_detect_source_language_result.language
```

::: zone-end

## <a name="use-a-custom-model-for-automatic-language-detection"></a>Use um modelo personalizado para deteção automática de idiomas

Além da deteção de idiomas utilizando modelos de serviço da Fala, pode especificar um modelo personalizado para um reconhecimento melhorado. Se um modelo personalizado não for fornecido, o serviço utilizará o modelo de idioma predefinido.

Os cortes abaixo ilustram como especificar um modelo personalizado na sua chamada para o serviço de Fala. Se a linguagem `en-US`detetada for, então o modelo predefinido é utilizado. Se a linguagem `fr-FR`detetada for, então o ponto final para o modelo personalizado é utilizado:

::: zone pivot="programming-language-csharp"

```csharp
var sourceLanguageConfigs = new SourceLanguageConfig[]
{
    SourceLanguageConfig.FromLanguage("en-US"),
    SourceLanguageConfig.FromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR")
};
var autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig.FromSourceLanguageConfigs(
        sourceLanguageConfigs);
```

::: zone-end

::: zone pivot="programming-language-cpp"

```cpp
std::vector<std::shared_ptr<SourceLanguageConfig>> sourceLanguageConfigs;
sourceLanguageConfigs.push_back(
    SourceLanguageConfig::FromLanguage("en-US"));
sourceLanguageConfigs.push_back(
    SourceLanguageConfig::FromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR"));

auto autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig::FromSourceLanguageConfigs(
        sourceLanguageConfigs);
```

::: zone-end

::: zone pivot="programming-language-java"

```java
List sourceLanguageConfigs = new ArrayList<SourceLanguageConfig>();
sourceLanguageConfigs.add(
    SourceLanguageConfig.fromLanguage("en-US"));
sourceLanguageConfigs.add(
    SourceLanguageConfig.fromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR"));

AutoDetectSourceLanguageConfig autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig.fromSourceLanguageConfigs(
        sourceLanguageConfigs);
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
 en_language_config = speechsdk.languageconfig.SourceLanguageConfig("en-US")
 fr_language_config = speechsdk.languageconfig.SourceLanguageConfig("fr-FR", "The Endpoint Id for custom model of fr-FR")
 auto_detect_source_language_config = speechsdk.languageconfig.AutoDetectSourceLanguageConfig(
        sourceLanguageConfigs=[en_language_config, fr_language_config])
```

::: zone-end

## <a name="next-steps"></a>Passos seguintes

- [Documentação de referência do SDK do discurso](speech-sdk.md)
