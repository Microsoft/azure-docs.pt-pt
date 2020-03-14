---
title: Apoio linguístico - LUIS
titleSuffix: Azure Cognitive Services
description: LUIS tem uma variedade de funcionalidades no serviço. Nem todas as funcionalidades são em paridade de linguagem mesmo. Certifique-se de que os recursos de que interesse são suportados na cultura do idioma que estiver a filtrar. Uma aplicação do LUIS é específica da cultura e não pode ser alterada depois de definida.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: diberry
ms.openlocfilehash: 4b6d954d06f09bef5240bddc4860ddbc83513d69
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79220855"
---
# <a name="language-and-region-support-for-luis"></a>Suporte de idioma e região para LUIS

LUIS tem uma variedade de funcionalidades no serviço. Nem todas as funcionalidades são em paridade de linguagem mesmo. Certifique-se de que os recursos de que interesse são suportados na cultura do idioma que estiver a filtrar. Uma aplicação do LUIS é específica da cultura e não pode ser alterada depois de definida.

## <a name="multi-language-luis-apps"></a>Aplicações de vários idiomas LUIS

Se precisar de uma aplicação de cliente do LUIS de vários idioma, como um chatbot, tem algumas opções. Se o LUIS suportar todos os idiomas, vai desenvolver uma aplicação LUIS para cada idioma. Cada aplicação LUIS tem um ID de aplicação exclusivo e o registo do ponto final. Se precisar de fornecer compreensão linguística para uma linguagem que o LUIS não suporta, pode utilizar a [API](../Translator/translator-info-overview.md) do Microsoft Tradutor para traduzir a expressão numa linguagem suportada, submeter a expressão ao ponto final do LUIS e receber as pontuações resultantes.

## <a name="languages-supported"></a>Idiomas suportados

LUIS compreende expressões com nos seguintes idiomas:

| Linguagem |Região  |  Domínio pré-criado | Entidade pré-criados | Recomendações de lista de frases | análise [de texto](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages) **<br>(Sentimentos e<br>Palavras-chave)|
|--|--|:--:|:--:|:--:|:--:|
| Inglês americano |`en-US` | ✔ | ✔  |✔|✔|
| Árabe (pré-visualização - árabe moderno padrão) |`ar-AR`|-|-|-|-|
| *[chinês](#chinese-support-notes) |`zh-CN` | ✔ | ✔ |✔|-|
| Neerlandês |`nl-NL` |✔|  -   |-|✔|
| Francês (França) |`fr-FR` |✔| ✔ |✔ |✔|
| Francês (Canadá) |`fr-CA` |-|   -   |-|✔|
| Alemão |`de-DE` |✔| ✔ |✔ |✔|
| Hindi | `hi-IN`|-|-|-|-|
| Italiano |`it-IT` |✔| ✔ |✔|✔|
| *[japonês](#japanese-support-notes) |`ja-JP` |✔| ✔ |✔|Apenas expressões-chave|
| Coreano |`ko-KR` |✔|   -   |-|Apenas expressões-chave|
| Português (Brasil) |`pt-BR` |✔| ✔ |✔ |nem todas as culturas secundárias|
| Espanhol (Espanha) |`es-ES` |✔| ✔ |✔|✔|
| Espanhol (México)|`es-MX` |-|  -   |✔|✔|
| Turco | `tr-TR` |✔|-|-|Apenas sentimento|

O apoio linguístico varia para [entidades pré-construídas](luis-reference-prebuilt-entities.md) e [domínios pré-construídos.](luis-reference-prebuilt-domains.md)

[!INCLUDE [Chinese language support notes](includes/chinese-language-support-notes.md)]

### <a name="japanese-support-notes"></a>\* Notas de suporte japonês

 - Como o LUIS não fornece análise sintática e não saberá a diferença entre Keigo e japonês informal, tem de incorporar os diferentes níveis de formalidade como exemplos de treinamento para as suas aplicações.
     - でございます não é o mesmo que です.
     - です não é o mesmo que だ.

[!INCLUDE [Text Analytics support notes](includes/text-analytics-support-notes.md)]

### <a name="speech-api-supported-languages"></a>Idiomas suportado de API de voz
Consulte [as línguas apoiadas](../speech-service/speech-to-text.md) pela fala para as línguas do modo de ditado da fala.

### <a name="bing-spell-check-supported-languages"></a>Verificação ortográfica do Bing suportado idiomas
Consulte bing spell check [idiomas suportados](https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/bing-spell-check-supported-languages) para obter uma lista de idiomas e estatuto suportados.

## <a name="rare-or-foreign-words-in-an-application"></a>Palavras raro ou externas num aplicativo
Na cultura `en-us`, luis aprende a distinguir a maioria das palavras inglesas, incluindo a gíria. Na cultura `zh-cn`, o LUIS aprende a distinguir a maioria dos caracteres chineses. Se usar uma palavra rara em `en-us` ou personagem em `zh-cn`, e vir que luis parece incapaz de distinguir essa palavra ou personagem, pode adicionar essa palavra ou personagem a uma [característica de lista de frases](luis-how-to-add-features.md). Por exemplo, palavras fora a cultura da aplicação – ou seja, palavras estrangeiras – devem ser adicionadas a um recurso de lista de frase.

<!--This phrase list should be marked non-interchangeable, to indicate that the set of rare words forms a class that LUIS should learn to recognize, but they are not synonyms or interchangeable with each other.-->

### <a name="hybrid-languages"></a>Idiomas híbrida
Idiomas híbrida combinam palavras de dois culturas, como inglês e chinês. Essas linguagens não são suportadas no LUIS, como uma aplicação se baseia numa cultura única.

## <a name="tokenization"></a>Atomização
Para realizar machine learning, LUIS quebra uma expressão em [tokens baseados](luis-glossary.md#token) na cultura.

|Linguagem|  cada espaço ou caráter especial | nível de caractere|palavras compostas|[entidade tokenizada devolvida](luis-concept-data-extraction.md#tokenized-entity-returned)
|--|:--:|:--:|:--:|:--:|
|Árabe|||||
|Chinês||✔||✔|
|Neerlandês|||✔|✔|
|Inglês (en-us)|✔ ||||
|Francês (fr-FR)|✔||||
|Francês (fr-AC)|✔||||
|Alemão|||✔|✔|
| Hindi |✔|-|-|-|-|
|Italiano|✔||||
|Japonês||||✔|
|Coreano||✔||✔|
|Português (Brasil)|✔||||
|Espanhol (es-ES)|✔||||
|Espanhol (es-MX)|✔||||

### <a name="custom-tokenizer-versions"></a>Versões personalizadas de tokenizer

As seguintes culturas têm versões personalizadas de tokenizer:

|Cultura|Versão|Objetivo|
|--|--|--|
|Alemão<br>`de-de`|1.0.0|Tokeniza palavras dividindo-as usando um tokenizer baseado em machine learning que tenta quebrar palavras compostas nos seus componentes únicos.<br>Se um utilizador entrar `Ich fahre einen krankenwagen` como uma expressão, é virado para `Ich fahre einen kranken wagen`. Permitindo a marcação de `kranken` e `wagen` independentemente como diferentes entidades.|
|Alemão<br>`de-de`|1.0.2|Tokeniza palavras dividindo-as em espaços.<br> se um utilizador entrar `Ich fahre einen krankenwagen` como uma expressão, continua a ser um único símbolo. Assim, `krankenwagen` é marcado como uma entidade única. |

### <a name="migrating-between-tokenizer-versions"></a>Migrar entre versões de tokenizer
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

A tokenização acontece ao nível da aplicação. Não há suporte para a tokenização ao nível da versão.

[Importe o ficheiro como uma nova app](luis-how-to-start-new-app.md), em vez de uma versão. Esta ação significa que a nova aplicação tem um ID de aplicação diferente, mas utiliza a versão tokenizer especificada no ficheiro.
