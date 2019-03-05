---
title: Suporte de idiomas
titleSuffix: Azure Cognitive Services
description: LUIS tem uma variedade de funcionalidades no serviço. Nem todas as funcionalidades são em paridade de linguagem mesmo. Certifique-se de que os recursos de que interesse são suportados na cultura do idioma que estiver a filtrar. Uma aplicação do LUIS é específica da cultura e não pode ser alterada depois de definida.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 03/04/2019
ms.author: diberry
ms.openlocfilehash: 98df1d9612d18e4ab5044bd92822b2df76286b12
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57340869"
---
# <a name="language-and-region-support-for-luis"></a>Suporte de idioma e região para LUIS

LUIS tem uma variedade de funcionalidades no serviço. Nem todas as funcionalidades são em paridade de linguagem mesmo. Certifique-se de que os recursos de que interesse são suportados na cultura do idioma que estiver a filtrar. Uma aplicação do LUIS é específica da cultura e não pode ser alterada depois de definida.

## <a name="multi-language-luis-apps"></a>Aplicações de vários idiomas LUIS

Se precisar de uma aplicação de cliente do LUIS de vários idioma, como um chatbot, tem algumas opções. Se o LUIS suportar todos os idiomas, vai desenvolver uma aplicação LUIS para cada idioma. Cada aplicação LUIS tem um ID de aplicação exclusivo e o registo do ponto final. Se tiver de fornecer a linguagem de compreensão de um idioma não suporta o LUIS, pode utilizar [API do Microsoft Translator](../Translator/translator-info-overview.md) para traduzir a expressão num idioma suportado, submeta a expressão para o ponto de extremidade do LUIS e receber o pontuações resultantes.

## <a name="languages-supported"></a>Idiomas suportados

LUIS compreende expressões com nos seguintes idiomas:

| Idioma |Região  |  Domínio pré-criado | Entidade pré-criados | Recomendações de lista de frase | **[Análise de texto](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages)<br>(Sentimentos e<br>Palavras-chave)|
|--|--|:--:|:--:|:--:|:--:|
| Inglês americano |`en-US` | ✔ | ✔  |✔|✔|
| *[Chinês](#chinese-support-notes) |`zh-CN` | ✔ | ✔ |✔|-|
| Neerlandês |`nl-NL` |-|  -   |-|✔|
| Francês (França) |`fr-FR` |-| ✔ |✔ |✔|
| Francês (Canadá) |`fr-CA` |-|   -   |-|✔|
| Alemão |`de-DE` |-| ✔ |✔ |✔|
| Italiano |`it-IT` |-| ✔ |✔|✔|
| *[Japonês](#japanese-support-notes) |`ja-JP` |-| ✔ |✔|Apenas expressões-chave|
| Coreano |`ko-KR` |-|   -   |-|Apenas expressões-chave|
| Português (Brasil) |`pt-BR` |-| ✔ |✔ |nem todas as culturas secundárias|
| Espanhol (Espanha) |`es-ES` |-| ✔ |✔|✔|
| Espanhol (México)|`es-MX` |-|  -   |✔|✔|
| Turco | `tr-TR` |-|-|-|Apenas o sentimento|


Suporte de idiomas varia para [entidades pré-concebidas](luis-reference-prebuilt-entities.md) e [domínios pré-concebidos](luis-reference-prebuilt-domains.md).

### <a name="chinese-support-notes"></a>* Notas de suporte chinês

 - Na `zh-cn` cultura, LUIS espera que o caractere do chinês simplificado, em vez do conjunto de caracteres tradicional.
 - Os nomes de objetivos, entidades, funcionalidades e as expressões regulares podem ser em chinês ou numeral carateres.
 - Consulte a [referência de domínios pré-concebidos](luis-reference-prebuilt-domains.md) para obter informações em que são suportados domínios pré-concebidos a `zh-cn` cultura.
<!--- When writing regular expressions in Chinese, do not insert whitespace between Chinese characters.-->

### <a name="japanese-support-notes"></a>* Notas de suporte japonês

 - Como o LUIS não fornece análise sintática e não saberá a diferença entre Keigo e japonês informal, tem de incorporar os diferentes níveis de formalidade como exemplos de treinamento para as suas aplicações.
     - でございます não é o mesmo que です.
     - です não é o mesmo que だ.

### <a name="text-analytics-support-notes"></a>* * Notas de suporte de análise de texto
Análise de texto inclui keyPhrase pré-criados de análise de sentimentos e entidades. Português apenas é suportado para subcultures: `pt-PT` e `pt-BR`. Todas as outras culturas são suportadas ao nível da cultura principal. Saiba mais sobre a análise de texto [idiomas suportados](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages).

### <a name="speech-api-supported-languages"></a>Idiomas suportado de API de voz
Consulte Voz [idiomas suportados](https://docs.microsoft.com/azure/cognitive-services/Speech/api-reference-rest/supportedlanguages##interactive-and-dictation-mode) para idiomas de modo de ditado de voz.

### <a name="bing-spell-check-supported-languages"></a>Verificação ortográfica do Bing suportado idiomas
Ver a verificação ortográfica do Bing [idiomas suportados](https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/bing-spell-check-supported-languages) para obter uma lista de idiomas suportados e o estado.

## <a name="rare-or-foreign-words-in-an-application"></a>Palavras raro ou externas num aplicativo
Na `en-us` cultura, LUIS aprende a distinguir palavras mais em inglês, incluindo gíria. Na `zh-cn` cultura, LUIS aprende a distinguir a maioria dos caracteres do chinês. Se utilizar uma palavra raro no `en-us` ou caráter no `zh-cn`, e verá que o LUIS parece não é possível distinguir essa palavra ou caráter, que pode adicionar essa palavra ou de caracteres para um [recurso lista frase](luis-how-to-add-features.md). Por exemplo, palavras fora a cultura da aplicação – ou seja, palavras estrangeiras – devem ser adicionadas a um recurso de lista de frase. Esta lista de frase deve ser marcada não-intercambiáveis, para indicar que o conjunto de palavras raro constitui uma classe que deve aprender a reconhecer o LUIS, mas não são sinónimos ou intercambiáveis entre si.

### <a name="hybrid-languages"></a>Idiomas híbrida
Idiomas híbrida combinam palavras de dois culturas, como inglês e chinês. Essas linguagens não são suportadas no LUIS, como uma aplicação se baseia numa cultura única.

## <a name="tokenization"></a>Atomização
Para fazer o machine learning, o LUIS divide uma expressão em [tokens](luis-glossary.md#token) com base na cultura.

|Idioma|  cada espaço ou caráter especial | nível de caractere|palavras compostas|[entidade com token devolvida](luis-concept-data-extraction.md#tokenized-entity-returned)
|--|:--:|:--:|:--:|:--:|
|Chinês||✔||✔|
|Neerlandês|||✔|✔|
|Inglês (en-us)|✔ ||||
|Francês (fr-FR)|✔||||
|Francês (fr-AC)|✔||||
|Alemão|||✔|✔|
|Italiano|✔||||
|Japonês||||✔|
|Coreano||✔||✔|
|Português (Brasil)|✔||||
|Espanhol (es-ES)|✔||||
|Espanhol (es-MX)|✔||||
