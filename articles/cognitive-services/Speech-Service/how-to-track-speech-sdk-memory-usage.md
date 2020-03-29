---
title: Como rastrear o uso da memória Speech SDK - Serviço de fala
titleSuffix: Azure Cognitive Services
description: O SDK do Serviço de Fala apoia numerosas linguagens de programação para a conversão fala-a-texto e texto-a-fala, juntamente com a tradução da fala. Este artigo discute a ferramenta de gestão da memória incorporada no SDK.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: rhurey
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: da5103317a2215aca68cec14ba8a0951258c9b89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "75456434"
---
# <a name="how-to-track-speech-sdk-memory-usage"></a>Como rastrear o uso da memória SDK do Discurso

O Speech SDK baseia-se numa base de código nativa que é projetada em várias linguagens de programação através de uma série de camadas de interoperabilidade. Cada projeção específica da linguagem tem características idiomáticas corretas para gerir o ciclo de vida do objeto. Além disso, o SDK do Discurso inclui ferramentas de gestão de memória para rastrear o uso de recursos com o registo de objetos e os limites de objetos. 

## <a name="how-to-read-object-logs"></a>Como ler registos de objetos

Se o [registo do Speech SDK estiver ativado,](how-to-use-logging.md)as tags de rastreio são emitidas para permitir a observação de objetos históricos. Estas etiquetas incluem: 

* `TrackHandle` ou `StopTracking` 
* O tipo de objeto
* O número atual de objetos que são rastreados o tipo do objeto, e o número atual a ser rastreado.

Aqui está um registo de amostras: 

```terminal
(284): 8604ms SPX_DBG_TRACE_VERBOSE:  handle_table.h:90 TrackHandle type=Microsoft::CognitiveServices::Speech::Impl::ISpxRecognitionResult handle=0x0x7f688401e1a0, ptr=0x0x7f688401e1a0, total=19
```

## <a name="set-a-warning-threshold"></a>Estabeleça um limiar de aviso

Tem a opção de criar um limiar de alerta e, se esse limiar for ultrapassado (assumindo que o registo está ativado), é registada uma mensagem de aviso. A mensagem de aviso contém uma lixeira de todos os objetos existentes juntamente com a sua contagem. Estas informações podem ser usadas para entender melhor as questões. 

Para ativar um limiar de advertência, `SpeechConfig` deve ser especificado num objeto. Este objeto é verificado quando um novo reconhecimento é criado. Nos seguintes exemplos, vamos supor que criou `SpeechConfig` `config`uma instância chamada:

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
speech_config.set_property_by_name(“SPEECH-ObjectCountWarnThreshold", "10000")?
```

::: zone-end

::: zone pivot="programming-language-more"

```ObjectiveC
[config setPropertyTo:@"10000" byName:"SPEECH-ObjectCountWarnThreshold"];
```

::: zone-end

> [!TIP]
> O valor padrão para esta propriedade é de 10.000.

## <a name="set-an-error-threshold"></a>Definir um limiar de erro 

Utilizando o SDK do Discurso, pode definir o número máximo de objetos permitidos num dado momento. Se esta definição estiver ativada, quando o número máximo for atingido, as tentativas de criar novos objetos reconhecedores falharão. Os objetos existentes continuarão a funcionar.

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

Para ativar um limiar de erro, `SpeechConfig` deve ser especificado num objeto. Este objeto é verificado quando um novo reconhecimento é criado. Nos seguintes exemplos, vamos supor que criou `SpeechConfig` `config`uma instância chamada:

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
speech_config.set_property_by_name(“SPEECH-ObjectCountErrorThreshold", "10000")?
```

::: zone-end

::: zone pivot="programming-language-more"

```objc
[config setPropertyTo:@"10000" byName:"SPEECH-ObjectCountErrorThreshold"];
```

::: zone-end

> [!TIP]
> O valor padrão para esta propriedade é o `size_t` valor máximo específico da plataforma para um tipo de dados. Um reconhecimento típico consumirá entre 7 e 10 objetos internos.

## <a name="next-steps"></a>Passos seguintes

* [Obtenha a subscrição do teste do serviço Speech](get-started.md)
* [Aprenda a reconhecer a fala usando um microfone](quickstarts/speech-to-text-from-microphone.md)