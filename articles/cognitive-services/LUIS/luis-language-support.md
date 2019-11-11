---
title: Suporte a idiomas-LUIS
titleSuffix: Azure Cognitive Services
description: O LUIS tem uma variedade de recursos dentro do serviço. Nem todos os recursos estão na mesma paridade de linguagem. Verifique se os recursos dos quais você está interessado têm suporte na cultura de idioma para a qual você está se concentrando. Um aplicativo LUIS é específico da cultura e não pode ser alterado após ser definido.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: diberry
ms.openlocfilehash: 83fd06078500be7b5bd58e9ea92d957f9d77f892
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904213"
---
# <a name="language-and-region-support-for-luis"></a>Suporte a idiomas e regiões para LUIS

O LUIS tem uma variedade de recursos dentro do serviço. Nem todos os recursos estão na mesma paridade de linguagem. Verifique se os recursos dos quais você está interessado têm suporte na cultura de idioma para a qual você está se concentrando. Um aplicativo LUIS é específico da cultura e não pode ser alterado após ser definido.

## <a name="multi-language-luis-apps"></a>Aplicativos LUIS de vários idiomas

Se você precisar de um aplicativo cliente LUIS em vários idiomas, como um chatbot, terá algumas opções. Se o LUIS der suporte a todos os idiomas, você desenvolverá um aplicativo LUIS para cada idioma. Cada aplicativo LUIS tem uma ID de aplicativo exclusiva e um log de ponto de extremidade. Se você precisar fornecer uma compreensão de linguagem para um idioma LUIS não suporta, você pode usar a [API do Microsoft Translator](../Translator/translator-info-overview.md) para converter o expressão em um idioma com suporte, enviar o expressão para o ponto de extremidade do Luis e receber as pontuações resultantes.

## <a name="languages-supported"></a>Idiomas com suporte

LUIS compreende declarações nos seguintes idiomas:

| Idioma |Região  |  Domínio predefinido | Entidade predefinida | Recomendações da lista de frases | **[análise de texto](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages)<br>(Sentimentos e<br>Palavras-chave|
|--|--|:--:|:--:|:--:|:--:|
| Inglês americano |`en-US` | ✔ | ✔  |✔|✔|
| *[chinês](#chinese-support-notes) |`zh-CN` | ✔ | ✔ |✔|-|
| Holandês |`nl-NL` |✔|  -   |-|✔|
| Francês (França) |`fr-FR` |✔| ✔ |✔ |✔|
| Francês (Canadá) |`fr-CA` |-|   -   |-|✔|
| Alemão |`de-DE` |✔| ✔ |✔ |✔|
| Hindi | `hi-IN`|-|-|-|-|
| Italiano |`it-IT` |✔| ✔ |✔|✔|
| *[japonês](#japanese-support-notes) |`ja-JP` |✔| ✔ |✔|Somente frase-chave|
| Coreano |`ko-KR` |✔|   -   |-|Somente frase-chave|
| Português (Brasil) |`pt-BR` |✔| ✔ |✔ |Nem todas as subculturas|
| Espanhol (Espanha) |`es-ES` |✔| ✔ |✔|✔|
| Espanhol (México)|`es-MX` |-|  -   |✔|✔|
| Turco | `tr-TR` |✔|-|-|Somente sentimentos|

O suporte ao idioma varia para [entidades predefinidas](luis-reference-prebuilt-entities.md) e [domínios predefinidos](luis-reference-prebuilt-domains.md).

[!INCLUDE [Chinese language support notes](includes/chinese-language-support-notes.md)]

### <a name="japanese-support-notes"></a>\* Notas de suporte em Japonês

 - Como o LUIS não fornece análise sintática e não entenderá a diferença entre Keigo e informais em Japonês, você precisa incorporar os diferentes níveis de formalização como exemplos de treinamento para seus aplicativos.
     - でございます não é o mesmo que です.
     - です não é o mesmo que だ.

[!INCLUDE [Text Analytics support notes](includes/text-analytics-support-notes.md)]

### <a name="speech-api-supported-languages"></a>Speech API idiomas com suporte
Consulte [idiomas com suporte](https://docs.microsoft.com/azure/cognitive-services/Speech/api-reference-rest/supportedlanguages##interactive-and-dictation-mode) de fala para idiomas do modo de ditado de fala.

### <a name="bing-spell-check-supported-languages"></a>Verificação Ortográfica do Bing idiomas com suporte
Consulte Verificação Ortográfica do Bing [idiomas com suporte](https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/bing-spell-check-supported-languages) para obter uma lista de idiomas e status com suporte.

## <a name="rare-or-foreign-words-in-an-application"></a>Palavras raras ou estrangeiras em um aplicativo
Na cultura de `en-us`, o LUIS aprende a distinguir a maioria das palavras em inglês, incluindo gírias. Na cultura `zh-cn`, o LUIS aprende a distinguir a maioria dos caracteres chineses. Se você usar uma palavra rara em `en-us` ou caractere no `zh-cn`, e perceber que LUIS parece não conseguir distinguir essa palavra ou caractere, você pode adicionar essa palavra ou caractere a um [recurso de lista de frases](luis-how-to-add-features.md). Por exemplo, palavras fora da cultura do aplicativo, ou seja, palavras estrangeiras, devem ser adicionadas a um recurso de lista de frases. 

<!--This phrase list should be marked non-interchangeable, to indicate that the set of rare words forms a class that LUIS should learn to recognize, but they are not synonyms or interchangeable with each other.-->

### <a name="hybrid-languages"></a>Idiomas híbridos
Os idiomas híbridos combinam palavras de duas culturas, como Inglês e chinês. Não há suporte para esses idiomas no LUIS porque um aplicativo é baseado em uma única cultura.

## <a name="tokenization"></a>Tokens
Para executar o Machine Learning, o LUIS quebra um expressão em [tokens](luis-glossary.md#token) com base na cultura.

|Idioma|  cada espaço ou caractere especial | nível de caractere|palavras compostas|[entidade indexada retornada](luis-concept-data-extraction.md#tokenized-entity-returned)
|--|:--:|:--:|:--:|:--:|
|Chinês||✔||✔|
|Holandês|||✔|✔|
|Inglês (en-us)|✔ ||||
|Francês (fr-FR)|✔||||
|Francês (fr-CA)|✔||||
|Alemão|||✔|✔|
| Hindi |✔|-|-|-|-|
|Italiano|✔||||
|Japonês||||✔|
|Coreano||✔||✔|
|Português (Brasil)|✔||||
|Espanhol (es-ES)|✔||||
|Espanhol (es-MX)|✔||||

### <a name="custom-tokenizer-versions"></a>Versões personalizadas do criador

As seguintes culturas têm versões de criador personalizadas:

|Cultura|Versão|Objetivo|
|--|--|--|
|Alemão<br>`de-de`|1.0.0|Cria tokens palavras dividindo-as usando um criador baseado em Machine Learning que tenta dividir palavras compostas em seus componentes únicos.<br>Se um usuário inserir `Ich fahre einen krankenwagen` como um expressão, ele será ativado para `Ich fahre einen kranken wagen`. Permitir a marcação de `kranken` e `wagen` de forma independente como entidades diferentes.|
|Alemão<br>`de-de`|1.0.2|Cria tokens palavras dividindo-as em espaços.<br> se um usuário inserir `Ich fahre einen krankenwagen` como um expressão, ele permanecerá como um único token. Portanto `krankenwagen` é marcado como uma única entidade. |

### <a name="migrating-between-tokenizer-versions"></a>Migrando entre versões do criador
<!--
Your first choice is to change the tokenizer version in the app file, then import the version. This action changes how the utterances are tokenized but allows you to keep the same app ID. 

Tokenizer JSON for 1.0.0. Notice the property value for  `tokenizerVersion`. 

```JSON
{
    "luis_schema_version": "3.2.0",
    "versionId": "0.1",
    "name": "german_app_1.0.0",
    "desc": "",
    "culture": "de-de",
    "tokenizerVersion": "1.0.0",
    "intents": [
        {
            "name": "i1"
        },
        {
            "name": "None"
        }
    ],
    "entities": [
        {
            "name": "Fahrzeug",
            "roles": []
        }
    ],
    "composites": [],
    "closedLists": [],
    "patternAnyEntities": [],
    "regex_entities": [],
    "prebuiltEntities": [],
    "model_features": [],
    "regex_features": [],
    "patterns": [],
    "utterances": [
        {
            "text": "ich fahre einen krankenwagen",
            "intent": "i1",
            "entities": [
                {
                    "entity": "Fahrzeug",
                    "startPos": 23,
                    "endPos": 27
                }
            ]
        }
    ],
    "settings": []
}
```

Tokenizer JSON for version 1.0.1. Notice the property value for  `tokenizerVersion`. 

```JSON
{
    "luis_schema_version": "3.2.0",
    "versionId": "0.1",
    "name": "german_app_1.0.1",
    "desc": "",
    "culture": "de-de",
    "tokenizerVersion": "1.0.1",
    "intents": [
        {
            "name": "i1"
        },
        {
            "name": "None"
        }
    ],
    "entities": [
        {
            "name": "Fahrzeug",
            "roles": []
        }
    ],
    "composites": [],
    "closedLists": [],
    "patternAnyEntities": [],
    "regex_entities": [],
    "prebuiltEntities": [],
    "model_features": [],
    "regex_features": [],
    "patterns": [],
    "utterances": [
        {
            "text": "ich fahre einen krankenwagen",
            "intent": "i1",
            "entities": [
                {
                    "entity": "Fahrzeug",
                    "startPos": 16,
                    "endPos": 27
                }
            ]
        }
    ],
    "settings": []
}
```
-->

A geração de tokens ocorre no nível do aplicativo. Não há suporte para geração de tokens no nível de versão. 

[Importe o arquivo como um novo aplicativo](luis-how-to-start-new-app.md), em vez de uma versão. Essa ação significa que o novo aplicativo tem uma ID de aplicativo diferente, mas usa a versão criador especificada no arquivo. 
