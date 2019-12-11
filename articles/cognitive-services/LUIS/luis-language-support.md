---
title: Suporte a idiomas-LUIS
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
ms.openlocfilehash: f6b95f76af4c83459ac81ff1703d8588f649326c
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74970547"
---
# <a name="language-and-region-support-for-luis"></a>Suporte de idioma e região para LUIS

LUIS tem uma variedade de funcionalidades no serviço. Nem todas as funcionalidades são em paridade de linguagem mesmo. Certifique-se de que os recursos de que interesse são suportados na cultura do idioma que estiver a filtrar. Uma aplicação do LUIS é específica da cultura e não pode ser alterada depois de definida.

## <a name="multi-language-luis-apps"></a>Aplicações de vários idiomas LUIS

Se precisar de uma aplicação de cliente do LUIS de vários idioma, como um chatbot, tem algumas opções. Se o LUIS suportar todos os idiomas, vai desenvolver uma aplicação LUIS para cada idioma. Cada aplicação LUIS tem um ID de aplicação exclusivo e o registo do ponto final. Se tiver de fornecer a linguagem de compreensão de um idioma não suporta o LUIS, pode utilizar [API do Microsoft Translator](../Translator/translator-info-overview.md) para traduzir a expressão num idioma suportado, submeta a expressão para o ponto de extremidade do LUIS e receber o pontuações resultantes.

## <a name="languages-supported"></a>Idiomas suportados

LUIS compreende expressões com nos seguintes idiomas:

| Linguagem |Região  |  Domínio pré-criado | Entidade pré-criados | Recomendações da lista de frases | **[Análise de texto](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages)<br>(Sentimentos e<br>Palavras-chave)|
|--|--|:--:|:--:|:--:|:--:|
| Inglês americano |`en-US` | ✔ | ✔  |✔|✔|
| Árabe (visualização-árabe moderno padrão) |`ar-AR`|-|-|-|-|
| *[Chinês](#chinese-support-notes) |`zh-CN` | ✔ | ✔ |✔|-|
| Neerlandês |`nl-NL` |✔|  -   |-|✔|
| Francês (França) |`fr-FR` |✔| ✔ |✔ |✔|
| Francês (Canadá) |`fr-CA` |-|   -   |-|✔|
| Alemão |`de-DE` |✔| ✔ |✔ |✔|
| Hindi | `hi-IN`|-|-|-|-|
| Italiano |`it-IT` |✔| ✔ |✔|✔|
| *[Japonês](#japanese-support-notes) |`ja-JP` |✔| ✔ |✔|Apenas expressões-chave|
| Coreano |`ko-KR` |✔|   -   |-|Apenas expressões-chave|
| Português (Brasil) |`pt-BR` |✔| ✔ |✔ |nem todas as culturas secundárias|
| Espanhol (Espanha) |`es-ES` |✔| ✔ |✔|✔|
| Espanhol (México)|`es-MX` |-|  -   |✔|✔|
| Turco | `tr-TR` |✔|-|-|Somente sentimentos|

Suporte de idiomas varia para [entidades pré-concebidas](luis-reference-prebuilt-entities.md) e [domínios pré-concebidos](luis-reference-prebuilt-domains.md).

[!INCLUDE [Chinese language support notes](includes/chinese-language-support-notes.md)]

### <a name="japanese-support-notes"></a>\* Notas de suporte japonês

 - Como o LUIS não fornece análise sintática e não saberá a diferença entre Keigo e japonês informal, tem de incorporar os diferentes níveis de formalidade como exemplos de treinamento para as suas aplicações.
     - でございます não é o mesmo que です.
     - です não é o mesmo que だ.

[!INCLUDE [Text Analytics support notes](includes/text-analytics-support-notes.md)]

### <a name="speech-api-supported-languages"></a>Idiomas suportado de API de voz
Consulte Voz [idiomas suportados](https://docs.microsoft.com/azure/cognitive-services/Speech/api-reference-rest/supportedlanguages##interactive-and-dictation-mode) para idiomas de modo de ditado de voz.

### <a name="bing-spell-check-supported-languages"></a>Verificação ortográfica do Bing suportado idiomas
Ver a verificação ortográfica do Bing [idiomas suportados](https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/bing-spell-check-supported-languages) para obter uma lista de idiomas suportados e o estado.

## <a name="rare-or-foreign-words-in-an-application"></a>Palavras raro ou externas num aplicativo
Na `en-us` cultura, LUIS aprende a distinguir palavras mais em inglês, incluindo gíria. Na `zh-cn` cultura, LUIS aprende a distinguir a maioria dos caracteres do chinês. Se utilizar uma palavra raro no `en-us` ou caráter no `zh-cn`, e verá que o LUIS parece não é possível distinguir essa palavra ou caráter, que pode adicionar essa palavra ou de caracteres para um [recurso lista frase](luis-how-to-add-features.md). Por exemplo, palavras fora a cultura da aplicação – ou seja, palavras estrangeiras – devem ser adicionadas a um recurso de lista de frase.

<!--This phrase list should be marked non-interchangeable, to indicate that the set of rare words forms a class that LUIS should learn to recognize, but they are not synonyms or interchangeable with each other.-->

### <a name="hybrid-languages"></a>Idiomas híbrida
Idiomas híbrida combinam palavras de dois culturas, como inglês e chinês. Essas linguagens não são suportadas no LUIS, como uma aplicação se baseia numa cultura única.

## <a name="tokenization"></a>Atomização
Para fazer o machine learning, o LUIS divide uma expressão em [tokens](luis-glossary.md#token) com base na cultura.

|Linguagem|  cada espaço ou caráter especial | nível de caractere|palavras compostas|[entidade com token devolvida](luis-concept-data-extraction.md#tokenized-entity-returned)
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

### <a name="custom-tokenizer-versions"></a>Versões personalizadas do criador

As seguintes culturas têm versões de criador personalizadas:

|Cultura|Versão|Finalidade|
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
