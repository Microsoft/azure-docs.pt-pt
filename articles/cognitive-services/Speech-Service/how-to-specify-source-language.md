---
title: Como especificar a linguagem fonte para a fala ao texto
titleSuffix: Azure Cognitive Services
description: O SDK do Discurso permite especificar a linguagem de origem ao converter a fala em texto. Este artigo descreve como usar os métodos FromConfig e SourceLanguageConfig para informar o serviço de Fala da língua de origem e fornecer um alvo de modelo personalizado.
services: cognitive-services
author: susanhu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/07/2020
ms.author: qiohu
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: f0723534d9d2187593cb73f058ffea62473b80a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235982"
---
# <a name="specify-source-language-for-speech-to-text"></a>Especificar a linguagem fonte para a fala ao texto

Neste artigo, você aprenderá a especificar a linguagem fonte para uma entrada áudio passada para o SDK da fala para reconhecimento da fala. Além disso, o código de exemplo é fornecido para especificar um modelo de fala personalizado para um melhor reconhecimento.

::: zone pivot="programming-language-csharp"

## <a name="how-to-specify-source-language-in-c"></a>Como especificar a linguagem fonte em C #

Neste exemplo, a linguagem fonte é fornecida explicitamente como parâmetro utilizando `SpeechRecognizer` a construção.

```csharp
var recognizer = new SpeechRecognizer(speechConfig, "de-DE", audioConfig);
```

Neste exemplo, a linguagem fonte `SourceLanguageConfig`é fornecida utilizando . Então, `sourceLanguageConfig` o é passado `SpeechRecognizer` como um parâmetro para construir.

```csharp
var sourceLanguageConfig = SourceLanguageConfig.FromLanguage("de-DE");
var recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

Neste exemplo, a linguagem de origem e `SourceLanguageConfig`o ponto final personalizado são fornecidos utilizando . Então, `sourceLanguageConfig` o é passado `SpeechRecognizer` como um parâmetro para construir.

```csharp
var sourceLanguageConfig = SourceLanguageConfig.FromLanguage("de-DE", "The Endpoint ID for your custom model.");
var recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `SpeechRecognitionLanguage`e `EndpointId` os métodos definidos `SpeechConfig` são depreciados da classe em C#. O uso destes métodos é desencorajado, e não `SpeechRecognizer`deve ser usado na construção de um .

::: zone-end

::: zone pivot="programming-language-cpp"


## <a name="how-to-specify-source-language-in-c"></a>Como especificar a linguagem fonte em C++

Neste exemplo, a linguagem fonte é fornecida explicitamente como parâmetro utilizando o `FromConfig` método.

```C++
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, "de-DE", audioConfig);
```

Neste exemplo, a linguagem fonte `SourceLanguageConfig`é fornecida utilizando . Em seguida, o `sourceLanguageConfig` é passado `FromConfig` como `recognizer`um parâmetro para quando criar o .

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

Neste exemplo, a linguagem de origem e `SourceLanguageConfig`o ponto final personalizado são fornecidos utilizando . O `sourceLanguageConfig` é passado como `FromConfig` um parâmetro `recognizer`para quando se cria o .

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE", "The Endpoint ID for your custom model.");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `SetSpeechRecognitionLanguage`e `SetEndpointId` são métodos depreciados `SpeechConfig` da classe em C++ e Java. O uso destes métodos é desencorajado, e não `SpeechRecognizer`deve ser usado na construção de um .

::: zone-end

::: zone pivot="programming-language-java"

## <a name="how-to-specify-source-language-in-java"></a>Como especificar a língua de origem em Java

Neste exemplo, a linguagem fonte é fornecida `SpeechRecognizer`explicitamente ao criar um novo .

```Java
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, "de-DE", audioConfig);
```

Neste exemplo, a linguagem fonte `SourceLanguageConfig`é fornecida utilizando . Em seguida, o `sourceLanguageConfig` é passado como `SpeechRecognizer`um parâmetro ao criar um novo .

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

Neste exemplo, a linguagem de origem e `SourceLanguageConfig`o ponto final personalizado são fornecidos utilizando . Em seguida, o `sourceLanguageConfig` é passado como `SpeechRecognizer`um parâmetro ao criar um novo .

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE", "The Endpoint ID for your custom model.");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `setSpeechRecognitionLanguage`e `setEndpointId` são métodos depreciados `SpeechConfig` da classe em C++ e Java. O uso destes métodos é desencorajado, e não `SpeechRecognizer`deve ser usado na construção de um .

::: zone-end

::: zone pivot="programming-language-python"

## <a name="how-to-specify-source-language-in-python"></a>Como especificar a linguagem fonte em Python

Neste exemplo, a linguagem fonte é fornecida explicitamente como parâmetro utilizando `SpeechRecognizer` a construção.

```Python
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, language="de-DE", audio_config=audio_config)
```

Neste exemplo, a linguagem fonte `SourceLanguageConfig`é fornecida utilizando . Então, `SourceLanguageConfig` o é passado `SpeechRecognizer` como um parâmetro para construir.

```Python
source_language_config = speechsdk.languageconfig.SourceLanguageConfig("de-DE")
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, source_language_config=source_language_config, audio_config=audio_config)
```

Neste exemplo, a linguagem de origem e `SourceLanguageConfig`o ponto final personalizado são fornecidos utilizando . Então, `SourceLanguageConfig` o é passado `SpeechRecognizer` como um parâmetro para construir.

```Python
source_language_config = speechsdk.languageconfig.SourceLanguageConfig("de-DE", "The Endpoint ID for your custom model.")
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, source_language_config=source_language_config, audio_config=audio_config)
```

>[!Note]
> `speech_recognition_language`e `endpoint_id` as propriedades são depreciadas da `SpeechConfig` classe em Python. O uso destas propriedades é desencorajado, e não `SpeechRecognizer`deve ser usado na construção de um .

::: zone-end

::: zone pivot="programming-language-more"

## <a name="how-to-specify-source-language-in-javascript"></a>Como especificar a linguagem fonte no Javascript

O primeiro passo é `SpeechConfig`criar um:

```Javascript
var speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionkey", "YourRegion");
```

Em seguida, especifique `speechRecognitionLanguage`a linguagem fonte do seu áudio com:

```Javascript
speechConfig.speechRecognitionLanguage = "de-DE";
```

Se estiver a utilizar um modelo personalizado para reconhecimento, `endpointId`pode especificar o ponto final com:

```Javascript
speechConfig.endpointId = "The Endpoint ID for your custom model.";
```

## <a name="how-to-specify-source-language-in-objective-c"></a>Como especificar a língua de origem no Objectivo-C

O primeiro passo é `speechConfig`criar um:

```Objective-C
SPXSpeechConfiguration *speechConfig = [[SPXSpeechConfiguration alloc] initWithSubscription:@"YourSubscriptionkey" region:@"YourRegion"];
```

Em seguida, especifique `speechRecognitionLanguage`a linguagem fonte do seu áudio com:

```Objective-C
speechConfig.speechRecognitionLanguage = @"de-DE";
```

Se estiver a utilizar um modelo personalizado para reconhecimento, `endpointId`pode especificar o ponto final com:

```Objective-C
speechConfig.endpointId = @"The Endpoint ID for your custom model.";
```

::: zone-end

## <a name="see-also"></a>Consulte também

* Para obter uma lista de línguas e locais apoiados para a fala ao texto, consulte o [suporte da linguagem.](language-support.md)

## <a name="next-steps"></a>Passos seguintes

* [Documentação de referência do SDK do discurso](speech-sdk.md)
