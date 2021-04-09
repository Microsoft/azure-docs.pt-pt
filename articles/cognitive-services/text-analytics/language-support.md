---
title: Suporte linguístico - Text Analytics API
titleSuffix: Azure Cognitive Services
description: Uma lista de línguas naturais apoiada pela API text Analytics. Este artigo explica quais as línguas suportadas para cada operação.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: aahi
ms.openlocfilehash: f6a109c10491ad2eabb12069157e9e6f394bc1f4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "101736615"
---
# <a name="text-analytics-api-v3-language-support"></a>Suporte linguístico API v3 de API de análise de texto 

#### <a name="sentiment-analysis"></a>[Análise de Sentimentos](#tab/sentiment-analysis)

| Linguagem              | Código do idioma | v3 suporte | Versão do modelo V3 inicial: |              Notas |
|:----------------------|:-------------:|:----------:|:--------------------------:|-------------------:|
| Chinese-Simplified    |   `zh-hans`   |     ✓      |         2019-10-01         | `zh` também aceitou |
| Chinese-Traditional   |   `zh-hant`   |    ✓      |         2019-10-01         |                    |
| Inglês               |     `en`      |     ✓      |         2019-10-01         |                    |
| Francês                |     `fr`      |     ✓      |         2019-10-01         |                    |
| Alemão                |     `de`      |     ✓      |         2019-10-01         |                    |
| Italiano               |     `it`      |     ✓      |         2019-10-01         |                    |
| Japonês              |     `ja`      |     ✓      |         2019-10-01         |                    |
| Coreano                |     `ko`      |    ✓      |         2019-10-01         |                    |
| Norueguês (Bokmål)   |     `no`      |     ✓      |         2020-07-01         |                    |
| Português (Brasil)   |    `pt-BR`    |     ✓      |         2020-04-01         |                    |
| Português (Portugal) |    `pt-PT`    |     ✓      |         2019-10-01         | `pt` também aceitou |
| Espanhol               |     `es`      |     ✓      |         2019-10-01         |                    |
| Turco               |     `tr`      |     ✓       |         2020-07-01        |                    |

### <a name="opinion-mining-v31-preview-only"></a>Mineração de opinião (apenas v3.1-pré-visualização)

| Linguagem              | Código do idioma | Começando com a versão do modelo V3: |              Notas |
|:----------------------|:-------------:|:------------------------------------:|-------------------:|
| Inglês               |     `en`      |              2020-04-01              |                    |


#### <a name="named-entity-recognition-ner"></a>[Reconhecimento de Entidades Nomeadas (NER)](#tab/named-entity-recognition)

> [!NOTE]
> * Apenas as entidades "Pessoa", "Localização" e "Organização" são devolvidas para línguas marcadas com *.

| Linguagem               | Código do idioma | v3 suporte | Começando com a versão do modelo V3: |       Notas        |
|:-----------------------|:-------------:|:----------:|:-------------------------------:|:------------------:|
| Árabe                 |     `ar`      |      ✓*    |               2019-10-01        |                    |
| Chinese-Simplified     |   `zh-hans`   |     ✓      |               2021-01-15        | `zh` também aceitou |
| Chinese-Traditional   |   `zh-hant`   |     ✓*      |               2019-10-01        |                    |
| Checo                 |     `cs`      |     ✓*      |               2019-10-01        |                    |
| Dinamarquês                |     `da`      |     ✓*      |               2019-10-01        |                    |
| Neerlandês                 |     `nl`      |     ✓*      |               2019-10-01        |                    |
| Inglês                |     `en`      |     ✓      |               2019-10-01        |                    |
| Finlandês               |     `fi`      |     ✓*      |               2019-10-01        |                    |
| Francês                 |     `fr`      |     ✓      |               2021-01-15        |                    |
| Alemão                 |     `de`      |     ✓      |               2021-01-15        |                    |
| Hebraico                |     `he`      |     ✓*      |               2019-10-01        |                    |
| Húngaro             |     `hu`      |     ✓*      |               2019-10-01        |                    |
| Italiano               |     `it`      |     ✓       |               2021-01-15        |                    |
| Japonês              |     `ja`      |     ✓       |               2021-01-15        |                    |
| Coreano                |     `ko`      |     ✓       |               2021-01-15        |                    |
| Norueguês (Bokmål)   |     `no`      |     ✓*      |               2019-10-01        | `nb` também aceitou |
| Polaco                |     `pl`      |     ✓*      |               2019-10-01        |                    |
| Português (Brasil)   |    `pt-BR`    |     ✓       |               2021-01-15        |                    |
| Português (Portugal) |    `pt-PT`    |     ✓       |               2021-01-15        | `pt` também aceitou |
| Russo              |     `ru`      |     ✓*       |               2019-10-01        |                    |
| Espanhol               |     `es`      |     ✓       |               2020-04-01        |                    |
| Sueco               |     `sv`      |     ✓*      |               2019-10-01        |                    |
| Turco               |     `tr`      |     ✓*      |               2019-10-01        |                    |

#### <a name="key-phrase-extraction"></a>[Extração de frase-chave](#tab/key-phrase-extraction)

| Linguagem              | Código do idioma |  v3 suporte | Disponível a partir da versão do modelo V3: |       Notas        |
|:----------------------|:-------------:|:----------:|:-----------------------------------------:|:------------------:|
| Dinamarquês                |     `da`      |     ✓     |                2019-10-01                 |                    |
| Neerlandês                 |     `nl`      |     ✓      |                2019-10-01                 |                    |
| Inglês               |     `en`      |     ✓      |                2019-10-01                 |                    |
| Finlandês               |     `fi`      |     ✓      |                2019-10-01                 |                    |
| Francês                |     `fr`      |     ✓      |                2019-10-01                 |                    |
| Alemão                |     `de`      |     ✓      |                2019-10-01                 |                    |
| Italiano               |     `it`      |     ✓      |                2019-10-01                 |                    |
| Japonês              |     `ja`      |     ✓      |                2019-10-01                 |                    |
| Coreano                |     `ko`      |     ✓      |                2019-10-01                 |                    |
| Norueguês (Bokmål)   |     `no`      |     ✓      |                2020-07-01                 | `nb` também aceitou |
| Polaco                |     `pl`      |    ✓      |                2019-10-01                 |                    |
| Português (Brasil)   |    `pt-BR`    |     ✓      |                2019-10-01                 |                    |
| Português (Portugal) |    `pt-PT`    |    ✓      |                2019-10-01                 | `pt` também aceitou |
| Russo               |     `ru`      |     ✓      |                2019-10-01                 |                    |
| Espanhol               |     `es`      |     ✓      |                2019-10-01                 |                    |
| Sueco               |     `sv`      |     ✓      |                2019-10-01                 |                    |

#### <a name="entity-linking"></a>[Ligação de entidades](#tab/entity-linking)

| Linguagem | Código do idioma |  v3 suporte | Disponível a partir da versão do modelo V3: | Notas |
|:---------|:-------------:|:----------:|:-----------------------------------------:|:-----:|
| Inglês  |     `en`      |     ✓      |                2019-10-01                 |       |
| Espanhol  |     `es`      |    ✓      |                2019-10-01                 |       |

#### <a name="personally-identifiable-information-pii"></a>[Informação Pessoalmente Identificável (PII)](#tab/pii)

| Linguagem               | Código do idioma | v3 suporte | Começando com a versão do modelo V3: |       Notas        |
|:-----------------------|:-------------:|:----------:|:-------------------------------:|:------------------:|
| Chinese-Simplified     |   `zh-hans`   |     ✓      |               2021-01-15        | `zh` também aceitou |
| Inglês                |     `en`      |     ✓      |               2020-07-01        |                    |
| Francês                 |     `fr`      |     ✓      |               2021-01-15        |                    |
| Alemão                 |     `de`      |     ✓      |               2021-01-15        |                    |
| Italiano               |     `it`      |     ✓       |               2021-01-15        |                    |
| Japonês              |     `ja`      |     ✓       |               2021-01-15        |                    |
| Coreano                |     `ko`      |     ✓       |               2021-01-15        |                    |
| Português (Brasil)   |    `pt-BR`    |     ✓       |               2021-01-15        |                    |
| Português (Portugal) |    `pt-PT`    |     ✓       |               2021-01-15        | `pt` também aceitou |
| Espanhol               |     `es`      |     ✓       |               2020-04-01        |                    |

#### <a name="language-detection"></a>[Deteção de Idioma](#tab/language-detection)

A API text Analytics pode detetar uma vasta gama de línguas, variantes, dialetos e algumas línguas regionais/culturais, e devolver línguas detetadas com o seu nome e código. Os parâmetros do código linguístico de deteção de linguagem de deteção de linguagem de análise de texto estão em conformidade com a [norma BCP-47,](https://tools.ietf.org/html/bcp47) com a maioria a estar em conformidade com os identificadores [ISO-639-1.](https://www.iso.org/iso-639-language-codes.html) 

Se tiver conteúdo expresso num idioma menos utilizado, pode tentar a Deteção de Idiomas para ver se devolve um código. A resposta para línguas que não podem ser detetadas é `unknown` .

| Linguagem | Código do Idioma | v3 suporte | Disponível a partir da versão do modelo V3: |
|:-|:-:|:-:|:-:|
|Afrikaans|`af`|✓|    |
|Albanês|`sq`|✓|    |
|Amárico|`am`|✓|2021-01-05|
|Árabe|`ar`|✓|    |
|Arménio|`hy`|✓|    |
|Assamese|`as`|✓|2021-01-05|
|Azerbaijão|`az`|✓|2021-01-05|
|Basco|`eu`|✓|    |
|Bielorrusso|`be`|✓|    |
|Bengalês|`bn`|✓|    |
|Bósnio|`bs`|✓|2020-09-01|
|Búlgaro|`bg`|✓|    |
|Birmanês|`my`|✓|    |
|Catalão|`ca`|✓|    |
|Central Khmer|`km`|✓|    |
|Chinês|`zh`|✓|    |
|Chinês simplificado|`zh_chs`|✓|    |
|Chinês tradicional|`zh_cht`|✓|    |
|Córsico|`co`|✓|2021-01-05|
|Croata|`hr`|✓|    |
|Checo|`cs`|✓|    |
|Dinamarquês|`da`|✓|    |
|Rio Dari|`prs`|✓|2020-09-01|
|Divehi|`dv`|✓|    |
|Neerlandês|`nl`|✓|    |
|Inglês|`en`|✓|    |
|Esperanto|`eo`|✓|    |
|Estónio|`et`|✓|    |
|Fijian|`fj`|✓|2020-09-01|
|Finlandês|`fi`|✓|    |
|Francês|`fr`|✓|    |
|Galego|`gl`|✓|    |
|Georgiano|`ka`|✓|    |
|Alemão|`de`|✓|    |
|Grego|`el`|✓|    |
|Guzerate|`gu`|✓|    |
|Haitiano|`ht`|✓|    |
|Rio Hausa|`ha`|✓|2021-01-05|
|Hebraico|`he`|✓|    |
|Hindi|`hi`|✓|    |
|Hmong Daw|`mww`|✓|2020-09-01|
|Húngaro|`hu`|✓|    |
|Islandês|`is`|✓|    |
|Igbo|`ig`|✓|2021-01-05|
|Indonésio|`id`|✓|    |
|Inuktitut|`iu`|✓|    |
|Irlandês|`ga`|✓|    |
|Italiano|`it`|✓|    |
|Japonês|`ja`|✓|    |
|Javanese|`jv`|✓|2021-01-05|
|Canarês|`kn`|✓|    |
|Cazaque|`kk`|✓|2020-09-01|
|Kinyarwanda|`rw`|✓|2021-01-05|
|Rio Kirghiz|`ky`|✓|2021-01-05|
|Coreano|`ko`|✓|    |
|Curdo|`ku`|✓|    |
|Rio Lao|`lo`|✓|    |
|Latim|`la`|✓|    |
|Letão|`lv`|✓|    |
|Lituano|`lt`|✓|    |
|Luxemburguês|`lb`|✓|2021-01-05|
|Macedónio|`mk`|✓|    |
|Malgaxe|`mg`|✓|2020-09-01|
|Malaio|`ms`|✓|    |
|Malaiala|`ml`|✓|    |
|Maltês|`mt`|✓|    |
|Maori|`mi`|✓|2020-09-01|
|Marata|`mr`|✓|2020-09-01|
|Mongol|`mn`|✓|2021-01-05|
|Nepali|`ne`|✓|2021-01-05|
|Norueguês|`no`|✓|    |
|Norueguês (Nynorsk)|`nn`|✓|    |
|Oriya|`or`|✓|    |
|Rio Pasht|`ps`|✓|    |
|Persa|`fa`|✓|    |
|Polaco|`pl`|✓|    |
|Português|`pt`|✓|    |
|Punjabi|`pa`|✓|    |
|Queretaro Otomi|`otq`|✓|2020-09-01|
|Romeno|`ro`|✓|    |
|Russo|`ru`|✓|    |
|Samoa|`sm`|✓|2020-09-01|
|Sérvio|`sr`|✓|    |
|Rio Shona|`sn`|✓|2021-01-05|
|Sindhi|`sd`|✓|2021-01-05|
|Sinhala|`si`|✓|    |
|Eslovaco|`sk`|✓|    |
|Esloveno|`sl`|✓|    |
|Somália|`so`|✓|    |
|Espanhol|`es`|✓|    |
|Sundanese|`su`|✓|2021-01-05|
|Suaíli|`sw`|✓|    |
|Sueco|`sv`|✓|    |
|Tagalog|`tl`|✓|    |
|Taitiano|`ty`|✓|2020-09-01|
|Rio Tajik|`tg`|✓|2021-01-05|
|Tâmil|`ta`|✓|    |
|Tártaro|`tt`|✓|2021-01-05|
|Telugu|`te`|✓|    |
|Tailandês|`th`|✓|    |
|Tibetano|`bo`|✓|2021-01-05|
|Rio Tigrinya|`ti`|✓|2021-01-05|
|Tongan|`to`|✓|2020-09-01|
|Turco|`tr`|✓|2021-01-05|
|Turcos|`tk`|✓|2021-01-05|
|Xhosa|`xh`|✓|2021-01-05|
|Rio Yoruba|`yo`|✓|2021-01-05|
|Zulu|`zu`|✓|2021-01-05|

---

## <a name="see-also"></a>Ver também

* [O que é a API de Análise de Texto?](overview.md)   
