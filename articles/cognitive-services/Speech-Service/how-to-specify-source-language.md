---
title: Como especificar a língua de origem para a fala para o texto
titleSuffix: Azure Cognitive Services
description: O SDK de discurso permite especificar a língua de origem ao converter a fala em texto. Este artigo descreve como usar os métodos FromConfig e SourceLanguageConfig para que o serviço de Fala conheça a língua de origem e forneça um alvo de modelo personalizado.
services: cognitive-services
author: susanhu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: qiohu
zone_pivot_groups: programming-languages-set-two
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 1b134fd3d09eeda340e7323638a36b68336242c2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91362032"
---
# <a name="specify-source-language-for-speech-to-text"></a>Especifique a linguagem de origem para a fala para o texto

Neste artigo, você aprenderá a especificar a língua de origem para uma entrada de áudio passada para o SDK do discurso para reconhecimento de voz. Adicionalmente, o código de exemplo é fornecido para especificar um modelo de fala personalizado para um reconhecimento melhorado.

::: zone pivot="programming-language-csharp"

## <a name="how-to-specify-source-language-in-c"></a>Como especificar a língua de origem em C #

No exemplo seguinte, a língua de origem é fornecida explicitamente como um parâmetro utilizando `SpeechRecognizer` a construção.

```csharp
var recognizer = new SpeechRecognizer(speechConfig, "de-DE", audioConfig);
```

No exemplo seguinte, a língua de origem é fornecida através da utilização `SourceLanguageConfig` . Então, o `sourceLanguageConfig` é passado como um parâmetro para `SpeechRecognizer` construir.

```csharp
var sourceLanguageConfig = SourceLanguageConfig.FromLanguage("de-DE");
var recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

No exemplo seguinte, o idioma de origem e o ponto final personalizado são fornecidos através da utilização `SourceLanguageConfig` . Então, o `sourceLanguageConfig` é passado como um parâmetro para `SpeechRecognizer` construir.

```csharp
var sourceLanguageConfig = SourceLanguageConfig.FromLanguage("de-DE", "The Endpoint ID for your custom model.");
var recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `SpeechRecognitionLanguage` e `EndpointId` os métodos definidos são depreciados da `SpeechConfig` classe em C#. O uso destes métodos é desencorajado, e não deve ser usado na construção de um `SpeechRecognizer` .

::: zone-end

::: zone pivot="programming-language-cpp"


## <a name="how-to-specify-source-language-in-c"></a>Como especificar a língua de origem em C++

No exemplo seguinte, a língua de origem é fornecida explicitamente como um parâmetro utilizando o `FromConfig` método.

```C++
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, "de-DE", audioConfig);
```

No exemplo seguinte, a língua de origem é fornecida através da utilização `SourceLanguageConfig` . Em seguida, o `sourceLanguageConfig` é passado como um parâmetro para quando se cria o `FromConfig` `recognizer` .

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

No exemplo seguinte, o idioma de origem e o ponto final personalizado são fornecidos através da utilização `SourceLanguageConfig` . O `sourceLanguageConfig` é passado como um parâmetro para quando se cria o `FromConfig` `recognizer` .

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE", "The Endpoint ID for your custom model.");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `SetSpeechRecognitionLanguage` e `SetEndpointId` são métodos precedidos da `SpeechConfig` classe em C++ e Java. O uso destes métodos é desencorajado, e não deve ser usado na construção de um `SpeechRecognizer` .

::: zone-end

::: zone pivot="programming-language-java"

## <a name="how-to-specify-source-language-in-java"></a>Como especificar a língua de origem em Java

No exemplo seguinte, a língua de origem é fornecida explicitamente ao criar um novo `SpeechRecognizer` .

```Java
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, "de-DE", audioConfig);
```

No exemplo seguinte, a língua de origem é fornecida através da utilização `SourceLanguageConfig` . Em seguida, o `sourceLanguageConfig` é passado como um parâmetro ao criar um novo `SpeechRecognizer` .

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

No exemplo seguinte, o idioma de origem e o ponto final personalizado são fornecidos através da utilização `SourceLanguageConfig` . Em seguida, o `sourceLanguageConfig` é passado como um parâmetro ao criar um novo `SpeechRecognizer` .

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE", "The Endpoint ID for your custom model.");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `setSpeechRecognitionLanguage` e `setEndpointId` são métodos precedidos da `SpeechConfig` classe em C++ e Java. O uso destes métodos é desencorajado, e não deve ser usado na construção de um `SpeechRecognizer` .

::: zone-end

::: zone pivot="programming-language-python"

## <a name="how-to-specify-source-language-in-python"></a>Como especificar a língua de origem em Python

No exemplo seguinte, a língua de origem é fornecida explicitamente como um parâmetro utilizando `SpeechRecognizer` a construção.

```Python
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, language="de-DE", audio_config=audio_config)
```

No exemplo seguinte, a língua de origem é fornecida através da utilização `SourceLanguageConfig` . Então, o `SourceLanguageConfig` é passado como um parâmetro para `SpeechRecognizer` construir.

```Python
source_language_config = speechsdk.languageconfig.SourceLanguageConfig("de-DE")
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, source_language_config=source_language_config, audio_config=audio_config)
```

No exemplo seguinte, o idioma de origem e o ponto final personalizado são fornecidos através da utilização `SourceLanguageConfig` . Então, o `SourceLanguageConfig` é passado como um parâmetro para `SpeechRecognizer` construir.

```Python
source_language_config = speechsdk.languageconfig.SourceLanguageConfig("de-DE", "The Endpoint ID for your custom model.")
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, source_language_config=source_language_config, audio_config=audio_config)
```

>[!Note]
> `speech_recognition_language` e `endpoint_id` as propriedades são depreciadas da classe em `SpeechConfig` Python. O uso destas propriedades é desencorajado, e eles não devem ser usados ao construir um `SpeechRecognizer` .

::: zone-end

::: zone pivot="programming-language-more"

## <a name="how-to-specify-source-language-in-javascript"></a>Como especificar a língua de origem em Javascript

O primeiro passo é criar `SpeechConfig` um:

```Javascript
var speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionkey", "YourRegion");
```

Em seguida, especifique a linguagem de origem do seu áudio `speechRecognitionLanguage` com:

```Javascript
speechConfig.speechRecognitionLanguage = "de-DE";
```

Se estiver a utilizar um modelo personalizado para reconhecimento, pode especificar o ponto final `endpointId` com:

```Javascript
speechConfig.endpointId = "The Endpoint ID for your custom model.";
```

## <a name="how-to-specify-source-language-in-objective-c"></a>Como especificar a língua de origem no Objectivo-C

No exemplo seguinte, a língua de origem é fornecida explicitamente como um parâmetro utilizando `SPXSpeechRecognizer` a construção.

```Objective-C
SPXSpeechRecognizer* speechRecognizer = \
    [[SPXSpeechRecognizer alloc] initWithSpeechConfiguration:speechConfig language:@"de-DE" audioConfiguration:audioConfig];
```

No exemplo seguinte, a língua de origem é fornecida através da utilização `SPXSourceLanguageConfiguration` . Então, o `SPXSourceLanguageConfiguration` é passado como um parâmetro para `SPXSpeechRecognizer` construir.

```Objective-C
SPXSourceLanguageConfiguration* sourceLanguageConfig = [[SPXSourceLanguageConfiguration alloc]init:@"de-DE"];
SPXSpeechRecognizer* speechRecognizer = [[SPXSpeechRecognizer alloc] initWithSpeechConfiguration:speechConfig
                                                                     sourceLanguageConfiguration:sourceLanguageConfig
                                                                              audioConfiguration:audioConfig];
```

No exemplo seguinte, o idioma de origem e o ponto final personalizado são fornecidos através da utilização `SPXSourceLanguageConfiguration` . Então, o `SPXSourceLanguageConfiguration` é passado como um parâmetro para `SPXSpeechRecognizer` construir.

```Objective-C
SPXSourceLanguageConfiguration* sourceLanguageConfig = \
        [[SPXSourceLanguageConfiguration alloc]initWithLanguage:@"de-DE"
                                                     endpointId:@"The Endpoint ID for your custom model."];
SPXSpeechRecognizer* speechRecognizer = [[SPXSpeechRecognizer alloc] initWithSpeechConfiguration:speechConfig
                                                                     sourceLanguageConfiguration:sourceLanguageConfig
                                                                              audioConfiguration:audioConfig];
```

>[!Note]
> `speechRecognitionLanguage` e `endpointId` as propriedades são depreciadas da classe em `SPXSpeechConfiguration` Objective-C. O uso destas propriedades é desencorajado, e eles não devem ser usados ao construir um `SPXSpeechRecognizer` .

::: zone-end

## <a name="see-also"></a>Ver também

* Para obter uma lista de línguas e locais apoiados para falar a texto, consulte [o suporte linguístico](language-support.md).

## <a name="next-steps"></a>Passos seguintes

* [Documentação de referência do SDK de fala](speech-sdk.md)