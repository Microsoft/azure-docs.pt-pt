---
title: Apoio linguístico - LUIS
titleSuffix: Azure Cognitive Services
description: O LUIS tem uma variedade de funcionalidades dentro do serviço. Nem todas as características estão na mesma paridade linguística. Certifique-se de que as funcionalidades que lhe interessam são suportadas na cultura linguística que está a ser alvo. Uma aplicação LUIS é específica da cultura e não pode ser alterada uma vez definida.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: diberry
ms.openlocfilehash: 69ff3aa3f649b054192f9dd001af83dc8dbc2317
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83745697"
---
# <a name="language-and-region-support-for-luis"></a>Apoio à língua e à região para o LUIS

O LUIS tem uma variedade de funcionalidades dentro do serviço. Nem todas as características estão na mesma paridade linguística. Certifique-se de que as funcionalidades que lhe interessam são suportadas na cultura linguística que está a ser alvo. Uma aplicação LUIS é específica da cultura e não pode ser alterada uma vez definida.

## <a name="multi-language-luis-apps"></a>Aplicativos LUIS multilingidiomas

Se precisa de uma aplicação de cliente LUIS multilingues, como um chatbot, tem algumas opções. Se o LUIS apoiar todas as línguas, desenvolve uma aplicação LUIS para cada idioma. Cada aplicação LUIS tem um ID de aplicação único e registo de ponto final. Se precisar de fornecer compreensão linguística para uma língua que o LUIS não suporta, pode utilizar o [serviço Tradutor](../Translator/translator-info-overview.md) para traduzir a expressão numa linguagem apoiada, submeter a expressão ao ponto final do LUIS e receber as pontuações resultantes.

## <a name="languages-supported"></a>Idiomas suportados

LUIS entende as expressões nas seguintes línguas:

| Linguagem |Região  |  Domínio pré-construído | Entidade pré-construída | Recomendações de lista de frases | **[Análise de texto](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages)<br>(Sentimento e<br>Palavras-chave)|
|--|--|:--:|:--:|:--:|:--:|
| Inglês (Estados Unidos) |`en-US` | ✔ | ✔  |✔|✔|
| Árabe (pré-visualização - árabe moderno padrão) |`ar-AR`|-|-|-|-|
| *[Chinês](#chinese-support-notes) |`zh-CN` | ✔ | ✔ |✔|-|
| Neerlandês |`nl-NL` |✔|-|-|✔|
| Francês (França) |`fr-FR` |✔| ✔ |✔ |✔|
| Francês (Canadá) |`fr-CA` |-|-|-|✔|
| Alemão |`de-DE` |✔| ✔ |✔ |✔|
| Guzerate | `gu-IN`|-|-|-|-|
| Hindi | `hi-IN`|-|✔|-|-|
| Italiano |`it-IT` |✔| ✔ |✔|✔|
| *[Japonês](#japanese-support-notes) |`ja-JP` |✔| ✔ |✔|Só frase-chave|
| Coreano |`ko-KR` |✔|-|-|Só frase-chave|
| Marata | `mr-IN`|-|-|-|-|
| Português (Brasil) |`pt-BR` |✔| ✔ |✔ |nem todas as subculturas|
| Espanhol (Espanha) |`es-ES` |✔| ✔ |✔|✔|
| Espanhol (México)|`es-MX` |-|-|✔|✔|
| Tâmil | `ta-IN`|-|-|-|-|
| Telugu | `te-IN`|-|-|-|-|
| Turco | `tr-TR` |✔|✔|-|Apenas sentimento|




O apoio linguístico varia para [entidades pré-construídas](luis-reference-prebuilt-entities.md) e [domínios pré-construídos.](luis-reference-prebuilt-domains.md)

[!INCLUDE [Chinese language support notes](includes/chinese-language-support-notes.md)]

### <a name="japanese-support-notes"></a>*Notas de apoio japonesas

 - Como o LUIS não fornece análises sintáticas e não compreenderá a diferença entre Keigo e japonês informal, é necessário incorporar os diferentes níveis de formalidade como exemplos de formação para as suas aplicações.
     - Não é o mesmo que o que aconteceu.
     - Não é o mesmo que.

[!INCLUDE [Text Analytics support notes](includes/text-analytics-support-notes.md)]

### <a name="speech-api-supported-languages"></a>Línguas apoiadas pela API da fala
Consulte [as línguas apoiadas](../speech-service/speech-to-text.md) pela fala para as línguas do modo de ditado da fala.

### <a name="bing-spell-check-supported-languages"></a>Bing Spell Check idiomas suportados
Consulte bing spell check [idiomas suportados](https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/bing-spell-check-supported-languages) para obter uma lista de idiomas e estatuto suportados.

## <a name="rare-or-foreign-words-in-an-application"></a>Palavras raras ou estrangeiras numa aplicação
Na `en-us` cultura, LUIS aprende a distinguir a maioria das palavras inglesas, incluindo a gíria. Na `zh-cn` cultura, LUIS aprende a distinguir a maioria dos caracteres chineses. Se usar uma palavra rara `en-us` ou personagem em , e vir que LUIS parece incapaz de distinguir essa palavra ou `zh-cn` personagem, pode adicionar essa palavra ou personagem a uma [característica de lista de frases](luis-how-to-add-features.md). Por exemplo, palavras fora da cultura da aplicação -- isto é, palavras estrangeiras - devem ser adicionadas a uma característica da lista de frases.

<!--This phrase list should be marked non-interchangeable, to indicate that the set of rare words forms a class that LUIS should learn to recognize, but they are not synonyms or interchangeable with each other.-->

### <a name="hybrid-languages"></a>Línguas híbridas
As línguas híbridas combinam palavras de duas culturas como o inglês e o chinês. Estas línguas não são suportadas no LUIS porque uma aplicação é baseada numa única cultura.

## <a name="tokenization"></a>Tokenização
Para realizar machine learning, LUIS quebra uma expressão em [tokens baseados](luis-glossary.md#token) na cultura.

|Linguagem|  cada espaço ou caráter especial | nível de caráter|palavras compostas
|--|:--:|:--:|:--:|
|Árabe|✔|||
|Chinês||✔||
|Neerlandês|✔||✔|
|Inglês (en-us)|✔ |||
|Francês (fr-FR)|✔|||
|Francês (fr-CA)|✔|||
|Alemão|✔||✔|
|Guzerate|✔|||
|Hindi|✔|||
|Italiano|✔|||
|Japonês|||✔
|Coreano||✔||
|Marata|✔|||
|Português (Brasil)|✔|||
|Espanhol (es-ES)|✔|||
|Espanhol (es-MX)|✔|||
|Tâmil|✔|||
|Telugu|✔|||
|Turco|✔|||


### <a name="custom-tokenizer-versions"></a>Versões personalizadas de tokenizer

As seguintes culturas têm versões personalizadas de tokenizer:

|Cultura|Versão|Objetivo|
|--|--|--|
|Alemão<br>`de-de`|1.0.0|Tokeniza palavras dividindo-as usando um tokenizer baseado em machine learning que tenta quebrar palavras compostas nos seus componentes únicos.<br>Se um utilizador entrar `Ich fahre einen krankenwagen` como uma expressão, é virado para `Ich fahre einen kranken wagen` . Permitindo a marcação `kranken` e de forma independente como `wagen` diferentes entidades.|
|Alemão<br>`de-de`|1.0.2|Tokeniza palavras dividindo-as em espaços.<br> Se um utilizador entrar `Ich fahre einen krankenwagen` como uma expressão, continua a ser um único símbolo. Assim, `krankenwagen` é marcado como uma entidade única. |
|Neerlandês<br>`de-de`|1.0.0|Tokeniza palavras dividindo-as usando um tokenizer baseado em machine learning que tenta quebrar palavras compostas nos seus componentes únicos.<br>Se um utilizador entrar `Ik ga naar de kleuterschool` como uma expressão, é virado para `Ik ga naar de kleuter school` . Permitindo a marcação `kleuter` e de forma independente como `school` diferentes entidades.|
|Neerlandês<br>`de-de`|1.0.1|Tokeniza palavras dividindo-as em espaços.<br> Se um utilizador entrar `Ik ga naar de kleuterschool` como uma expressão, continua a ser um único símbolo. Assim, `kleuterschool` é marcado como uma entidade única. |


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
