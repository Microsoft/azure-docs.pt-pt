---
title: Como especificar o idioma de origem de fala para texto
titleSuffix: Azure Cognitive Services
description: O SDK de fala permite que você especifique o idioma de origem ao converter a fala em texto. Este artigo descreve como usar os métodos FromConfig e SourceLanguageConfig para permitir que o serviço de fala saiba o idioma de origem e forneça um destino de modelo personalizado.
services: cognitive-services
author: susanhu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/07/2020
ms.author: qiohu
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: e4f4dd3c1e23855a8a1a69dac72c232779206f1d
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76121714"
---
# <a name="specify-source-language-for-speech-to-text"></a>Especificar o idioma de origem de fala para texto

Neste artigo, você aprenderá a especificar o idioma de origem para uma entrada de áudio passada para o SDK de fala para reconhecimento de fala. Além disso, o código de exemplo é fornecido para especificar um modelo de fala personalizado para um reconhecimento aprimorado.

::: zone pivot="programming-language-csharp"

## <a name="how-to-specify-source-language-in-c"></a>Como especificar o idioma de origem noC#

Neste exemplo, o idioma de origem é fornecido explicitamente como um parâmetro usando `SpeechRecognizer` construção.

```csharp
var recognizer = new SpeechRecognizer(speechConfig, "de-DE", audioConfig);
```

Neste exemplo, o idioma de origem é fornecido usando `SourceLanguageConfig`. Em seguida, a `sourceLanguageConfig` é passada como um parâmetro para `SpeechRecognizer` construção.

```csharp
var sourceLanguageConfig = SourceLanguageConfig.FromLanguage("de-DE");
var recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

Neste exemplo, o idioma de origem e o ponto de extremidade personalizado são fornecidos usando `SourceLanguageConfig`. Em seguida, a `sourceLanguageConfig` é passada como um parâmetro para `SpeechRecognizer` construção.

```csharp
var sourceLanguageConfig = SourceLanguageConfig.FromLanguage("de-DE", "The Endpoint ID for your custom model.");
var recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> os métodos `SpeechRecognitionLanguage` e `EndpointId` definidos são preteridos da classe `SpeechConfig` C#no. O uso desses métodos é desencorajado e não deve ser usado ao construir um `SpeechRecognizer`.

::: zone-end

::: zone pivot="programming-language-cpp"


## <a name="how-to-specify-source-language-in-c"></a>Como especificar o idioma de origem noC++

Neste exemplo, o idioma de origem é fornecido explicitamente como um parâmetro usando o método `FromConfig`.

```C++
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, "de-DE", audioConfig);
```

Neste exemplo, o idioma de origem é fornecido usando `SourceLanguageConfig`. Em seguida, a `sourceLanguageConfig` é passada como um parâmetro para `FromConfig` ao criar o `recognizer`.

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

Neste exemplo, o idioma de origem e o ponto de extremidade personalizado são fornecidos usando `SourceLanguageConfig`. O `sourceLanguageConfig` é passado como um parâmetro para `FromConfig` ao criar o `recognizer`.

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE", "The Endpoint ID for your custom model.");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `SetSpeechRecognitionLanguage` e `SetEndpointId` são métodos preteridos da classe `SpeechConfig` em C++ e Java. O uso desses métodos é desencorajado e não deve ser usado ao construir um `SpeechRecognizer`.

::: zone-end

::: zone pivot="programming-language-java"

## <a name="how-to-specify-source-language-in-java"></a>Como especificar o idioma de origem em Java

Neste exemplo, o idioma de origem é fornecido explicitamente ao criar um novo `SpeechRecognizer`.

```Java
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, "de-DE", audioConfig);
```

Neste exemplo, o idioma de origem é fornecido usando `SourceLanguageConfig`. Em seguida, o `sourceLanguageConfig` é passado como um parâmetro ao criar um novo `SpeechRecognizer`.

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

Neste exemplo, o idioma de origem e o ponto de extremidade personalizado são fornecidos usando `SourceLanguageConfig`. Em seguida, o `sourceLanguageConfig` é passado como um parâmetro ao criar um novo `SpeechRecognizer`.

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE", "The Endpoint ID for your custom model.");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `setSpeechRecognitionLanguage` e `setEndpointId` são métodos preteridos da classe `SpeechConfig` em C++ e Java. O uso desses métodos é desencorajado e não deve ser usado ao construir um `SpeechRecognizer`.

::: zone-end

::: zone pivot="programming-language-python"

## <a name="how-to-specify-source-language-in-python"></a>Como especificar o idioma de origem no Python

A primeira etapa é criar um `speech_config`:

```Python
speech_key, service_region = "YourSubscriptionKey", "YourServiceRegion"
speech_config = speechsdk.SpeechConfig(subscription=speech_key, region=service_region)
```

Em seguida, especifique o idioma de origem do seu áudio com `speech_recognition_language`:

```Python
speech_config.speech_recognition_language="de-DE"
```

Se você estiver usando um modelo personalizado para reconhecimento, poderá especificar o ponto de extremidade com `endpoint_id`:

```Python
speech_config.endpoint_id = "The Endpoint ID for your custom model."
```

::: zone-end

::: zone pivot="programming-language-more"

## <a name="how-to-specify-source-language-in-javascript"></a>Como especificar o idioma de origem em JavaScript

A primeira etapa é criar um `SpeechConfig`:

```Javascript
var speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionkey", "YourRegion");
```

Em seguida, especifique o idioma de origem do seu áudio com `speechRecognitionLanguage`:

```Javascript
speechConfig.speechRecognitionLanguage = "de-DE";
```

Se você estiver usando um modelo personalizado para reconhecimento, poderá especificar o ponto de extremidade com `endpointId`:

```Javascript
speechConfig.endpointId = "The Endpoint ID for your custom model.";
```

## <a name="how-to-specify-source-language-in-objective-c"></a>Como especificar o idioma de origem no Objective-C

A primeira etapa é criar um `speechConfig`:

```Objective-C
SPXSpeechConfiguration *speechConfig = [[SPXSpeechConfiguration alloc] initWithSubscription:@"YourSubscriptionkey" region:@"YourRegion"];
```

Em seguida, especifique o idioma de origem do seu áudio com `speechRecognitionLanguage`:

```Objective-C
speechConfig.speechRecognitionLanguage = @"de-DE";
```

Se você estiver usando um modelo personalizado para reconhecimento, poderá especificar o ponto de extremidade com `endpointId`:

```Objective-C
speechConfig.endpointId = @"The Endpoint ID for your custom model.";
```

::: zone-end

## <a name="see-also"></a>Ver também

* Para obter uma lista de idiomas com suporte e localidades de fala para texto, consulte [suporte a idiomas](language-support.md).

## <a name="next-steps"></a>Passos seguintes

* [Documentação de referência do SDK de fala](speech-sdk.md)
