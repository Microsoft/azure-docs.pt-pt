---
title: Como rastrear o uso da memória SDK do Discurso - Serviço de fala
titleSuffix: Azure Cognitive Services
description: O Serviço de Discurso SDK suporta numerosas linguagens de programação para conversão de texto-a-texto e texto-a-voz, juntamente com a tradução da fala. Este artigo discute a gestão da memória incorporada no SDK.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: rhurey
ms.custom: devx-track-csharp
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: c7e74ce8f4d0b7889d2e5bcd117eaa43e2b6991b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88934145"
---
# <a name="how-to-track-speech-sdk-memory-usage"></a>Como rastrear o uso da memória SDK do Discurso

O SDK do Discurso baseia-se numa base de código nativa que é projetada em múltiplas linguagens de programação através de uma série de camadas de interoperabilidade. Cada projeção específica da linguagem tem características idiomaticamente corretas para gerir o ciclo de vida do objeto. Além disso, o SDK do discurso inclui ferramentas de gestão da memória para rastrear o uso de recursos com registo de objetos e limites de objetos. 

## <a name="how-to-read-object-logs"></a>Como ler registos de objetos

Se [a sessão de SDK de fala estiver ativada,](how-to-use-logging.md)são emitidas tags de rastreio para permitir a observação histórica dos objetos. Estas tags incluem: 

* `TrackHandle` ou `StopTracking` 
* O tipo de objeto
* O número atual de objetos que são rastreados o tipo do objeto, e o número atual sendo rastreado.

Aqui está um registo de amostras: 

```terminal
(284): 8604ms SPX_DBG_TRACE_VERBOSE:  handle_table.h:90 TrackHandle type=Microsoft::CognitiveServices::Speech::Impl::ISpxRecognitionResult handle=0x0x7f688401e1a0, ptr=0x0x7f688401e1a0, total=19
```

## <a name="set-a-warning-threshold"></a>Definir um limiar de aviso

Tem a opção de criar um limiar de aviso, e se esse limiar for ultrapassado (assumindo que a exploração madeireira está ativada), é registada uma mensagem de aviso. A mensagem de aviso contém um depósito de todos os objetos existentes juntamente com a sua contagem. Esta informação pode ser usada para entender melhor as questões. 

Para ativar um limiar de aviso, deve ser especificado num `SpeechConfig` objeto. Este objeto é verificado quando um novo reconhecimento é criado. Nos seguintes exemplos, vamos supor que criou um exemplo `SpeechConfig` `config` chamado:

::: zone pivot="programming-language-csharp"

```csharp
config.SetProperty("SPEECH-ObjectCountWarnThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
config->SetProperty("SPEECH-ObjectCountWarnThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-java"

```java
config.setProperty("SPEECH-ObjectCountWarnThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_config.set_property_by_name("SPEECH-ObjectCountWarnThreshold", "10000")?
```

::: zone-end

::: zone pivot="programming-language-more"

```ObjectiveC
[config setPropertyTo:@"10000" byName:"SPEECH-ObjectCountWarnThreshold"];
```

::: zone-end

> [!TIP]
> O valor padrão desta propriedade é de 10.000.

## <a name="set-an-error-threshold"></a>Definir um limiar de erro 

Utilizando o SDK de discurso, pode definir o número máximo de objetos permitidos num dado momento. Se esta definição estiver ativada, quando o número máximo for atingido, as tentativas de criar novos objetos reconhecíveis falharão. Os objetos existentes continuarão a funcionar.

Aqui está um erro de amostra:

```terminal
Runtime error: The maximum object count of 500 has been exceeded.
The threshold can be adjusted by setting the SPEECH-ObjectCountErrorThreshold property on the SpeechConfig object.
See http://https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-object-tracking-speech-sdk for more detailed information.
Handle table dump by ojbect type:
class Microsoft::CognitiveServices::Speech::Impl::ISpxRecognitionResult 0
class Microsoft::CognitiveServices::Speech::Impl::ISpxRecognizer 0
class Microsoft::CognitiveServices::Speech::Impl::ISpxAudioConfig 0
class Microsoft::CognitiveServices::Speech::Impl::ISpxSpeechConfig 0
```

Para ativar um limiar de erro, este deve ser especificado num `SpeechConfig` objeto. Este objeto é verificado quando um novo reconhecimento é criado. Nos seguintes exemplos, vamos supor que criou um exemplo `SpeechConfig` `config` chamado:

::: zone pivot="programming-language-csharp"

```csharp
config.SetProperty("SPEECH-ObjectCountErrorThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
config->SetProperty("SPEECH-ObjectCountErrorThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-java"

```java
config.setProperty("SPEECH-ObjectCountErrorThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_config.set_property_by_name("SPEECH-ObjectCountErrorThreshold", "10000")?
```

::: zone-end

::: zone pivot="programming-language-more"

```objc
[config setPropertyTo:@"10000" byName:"SPEECH-ObjectCountErrorThreshold"];
```

::: zone-end

> [!TIP]
> O valor padrão desta propriedade é o valor máximo específico da plataforma para um tipo de `size_t` dados. Um reconhecimento típico consumirá entre 7 e 10 objetos internos.

## <a name="next-steps"></a>Passos seguintes

* [Saiba mais sobre o Discurso SDK](speech-sdk.md)