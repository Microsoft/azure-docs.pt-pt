---
title: Como usar a detecção automática de idioma para fala no serviço de fala de texto
titleSuffix: Azure Cognitive Services
description: O SDK de fala dá suporte à detecção automática de idioma para fala em texto. Ao usar esse recurso, o áudio fornecido é comparado com uma lista de idiomas fornecida e a correspondência mais provável é determinada. O valor retornado pode então ser usado para selecionar o modelo de idioma usado para a fala de texto.
services: cognitive-services
author: susanhu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/26/2019
ms.author: qiohu
ms.openlocfilehash: e156704d3ad54c3437f921fae536a497e1d94868
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73506969"
---
# <a name="automatic-language-detection-for-speech-to-text"></a>Detecção automática de idioma para fala em texto

A detecção automática de idioma é usada para determinar a correspondência mais provável para o áudio passado para o SDK de fala em comparação com uma lista de idiomas fornecidos. O valor retornado pela detecção automática de idioma é usado para selecionar o modelo de linguagem de fala para texto, fornecendo uma transcrição mais precisa. Para ver quais idiomas estão disponíveis, consulte [suporte a idiomas](language-support.md).

Neste artigo, você aprenderá a usar `AutoDetectSourceLanguageConfig` para construir um objeto de `SpeechRecognizer` e recuperar o idioma detectado.

> [!IMPORTANT]
> Esse recurso só está disponível para o SDK de fala C++ para o e o SDK de fala para Java.

## <a name="automatic-language-detection-with-the-speech-sdk"></a>Detecção automática de idioma com o SDK de fala

A detecção automática de idioma atualmente tem um limite no lado dos serviços de dois idiomas por detecção. Tenha essa limitação em mente ao construção de seu objeto de `AudoDetectSourceLanguageConfig`. Nos exemplos a seguir, você criará um `AutoDetectSourceLanguageConfig`e, em seguida, o usará para construir um `SpeechRecognizer`.

>[!TIP]
> Você também pode especificar um modelo personalizado a ser usado ao executar a fala em texto. Para obter mais informações, consulte [usar um modelo personalizado para detecção automática de idioma](#use-a-custom-model-for-automatic-language-detection).

Os trechos de código a seguir ilustram como usar a detecção automática de idioma em seus aplicativos:

```C++
auto autoDetectSourceLanguageConfig = AutoDetectSourceLanguageConfig::FromLanguages({ "en-US", "de-DE" });
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, autoDetectSourceLanguageConfig, audioConfig);
speechRecognitionResult = recognizer->RecognizeOnceAsync().get();
auto autoDetectSourceLanguageResult = AutoDetectSourceLanguageResult::FromResult(speechRecognitionResult);
auto detectedLanguage = autoDetectSourceLanguageResult->Language;
```

```Java
AutoDetectSourceLanguageConfig autoDetectSourceLanguageConfig = AutoDetectSourceLanguageConfig.fromLanguages(Arrays.asList("en-US", "de-DE"));
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, autoDetectSourceLanguageConfig, audioConfig);
Future<SpeechRecognitionResult> future = recognizer.recognizeOnceAsync();
SpeechRecognitionResult result = future.get(30, TimeUnit.SECONDS);
AutoDetectSourceLanguageResult autoDetectSourceLanguageResult = AutoDetectSourceLanguageResult.fromResult(result);
String detectedLanguage = autoDetectSourceLanguageResult.getLanguage();

recognizer.close();
speechConfig.close();
autoDetectSourceLanguageConfig.close();
audioConfig.close();
result.close();
```

## <a name="use-a-custom-model-for-automatic-language-detection"></a>Usar um modelo personalizado para detecção automática de idioma

Além da detecção de idioma usando modelos de serviço de fala, você pode especificar um modelo personalizado para reconhecimento avançado. Se um modelo personalizado não for fornecido, o serviço usará o modelo de idioma padrão.

Os trechos de código a seguir ilustram como especificar um modelo personalizado em sua chamada para o serviço de fala. Se o idioma detectado for `en-US`, o modelo padrão será usado. Se o idioma detectado for `fr-FR`, o ponto de extremidade para o modelo personalizado será usado:

```C++
std::vector<std::shared_ptr<SourceLanguageConfig>> sourceLanguageConfigs;
sourceLanguageConfigs.push_back(SourceLanguageConfig::FromLanguage("en-US"));
sourceLanguageConfigs.push_back(SourceLanguageConfig::FromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR"));
auto autoDetectSourceLanguageConfig = AutoDetectSourceLanguageConfig::FromSourceLanguageConfigs(sourceLanguageConfigs);
```

```Java
List sourceLanguageConfigs = new ArrayList<SourceLanguageConfig>();
sourceLanguageConfigs.add(SourceLanguageConfig.fromLanguage("en-US"));
sourceLanguageConfigs.add(SourceLanguageConfig.fromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR"));
AutoDetectSourceLanguageConfig autoDetectSourceLanguageConfig = AutoDetectSourceLanguageConfig.fromSourceLanguageConfigs(sourceLanguageConfigs);
```

## <a name="next-steps"></a>Passos seguintes

* [Documentação de referência do SDK de fala](speech-sdk.md)
