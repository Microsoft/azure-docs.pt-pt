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
ms.date: 12/17/2020
ms.author: aahi
ms.openlocfilehash: a0cd968c90a27d6f1ae79d08fb2bd027169233db
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98133791"
---
# <a name="text-analytics-api-v3-language-support"></a>Suporte linguístico API v3 de API de análise de texto 

#### <a name="sentiment-analysis"></a>[Análise de Sentimentos](#tab/sentiment-analysis)

| Linguagem              | Código do idioma | v2 apoio | v3 suporte | Versão do modelo V3 inicial: |              Notas |
|:----------------------|:-------------:|:----------:|:----------:|:--------------------------:|-------------------:|
| Chinese-Simplified    |   `zh-hans`   |     ✓      |     ✓      |         2019-10-01         | `zh` também aceitou |
| Chinese-Traditional   |   `zh-hant`   |            |     ✓      |         2019-10-01         |                    |
| Dinamarquês               |     `da`      |     ✓      |            |                            |                    |
| Neerlandês                 |     `nl`      |     ✓      |            |                            |                    |
| Inglês               |     `en`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Finlandês               |     `fi`      |     ✓      |            |                            |                    |
| Francês                |     `fr`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Alemão                |     `de`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Grego                 |     `el`      |     ✓      |            |                            |                    |
| Hindi                 |     `hi`      |            |      ✓     |          2020-04-01        |                    |
| Italiano               |     `it`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Japonês              |     `ja`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Coreano                |     `ko`      |            |     ✓      |         2019-10-01         |                    |
| Norueguês (Bokmål)   |     `no`      |     ✓      |     ✓      |         2020-07-01         |                    |
| Polaco                |     `pl`      |     ✓      |            |                            |                    |
| Português (Brasil)   |    `pt-BR`    |            |     ✓      |         2020-04-01         |                    |
| Português (Portugal) |    `pt-PT`    |     ✓      |     ✓      |         2019-10-01         | `pt` também aceitou |
| Russo               |     `ru`      |     ✓      |            |                            |                    |
| Espanhol               |     `es`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Sueco               |     `sv`      |     ✓      |            |                            |                    |
| Turco               |     `tr`      |     ✓      |     ✓       |         2020-07-01        |                    |

### <a name="opinion-mining-v31-preview-only"></a>Mineração de opinião (apenas v3.1-pré-visualização)

| Linguagem              | Código do idioma | Começando com a versão do modelo V3: |              Notas |
|:----------------------|:-------------:|:------------------------------------:|-------------------:|
| Inglês               |     `en`      |              2020-04-01              |                    |


#### <a name="named-entity-recognition-ner"></a>[Reconhecimento de Entidades Nomeadas (NER)](#tab/named-entity-recognition)

> [!NOTE]
> * O NER v3 suporta atualmente apenas línguas inglesa e espanhola. Se ligar para o NER v3 com um idioma diferente, a API devolverá os resultados v2.1, desde que o idioma seja suportado na versão 2.1.
> * v2.1 apenas devolve o conjunto completo de entidades disponíveis para as línguas inglesa, chinesa-simplificada, francesa, alemã e espanhola.  As entidades "Pessoa", "Localização" e "Organização" são devolvidas para as outras línguas apoiadas.

| Linguagem               | Código do idioma | v2.1 apoio | v3 suporte | Começando com a versão do modelo V3: |       Notas        |
|:-----------------------|:-------------:|:----------:|:----------:|:-------------------------------:|:------------------:|
| Árabe                |     `ar`      |     ✓      |            |                                 |                    |
| Chinese-Simplified     |   `zh-hans`   |     ✓      |            |                                 | `zh` também aceitou |
| Chinese-Traditional   |   `zh-hant`   |     ✓      |            |                                 |                    |
| Checo                 |     `cs`      |     ✓      |            |                                 |                    |
| Dinamarquês                |     `da`      |     ✓      |            |                                 |                    |
| Neerlandês                 |     `nl`      |     ✓      |            |                                 |                    |
| Inglês                |     `en`      |     ✓      |     ✓      |           2019-10-01            |                    |
| Finlandês               |     `fi`      |     ✓      |            |                                 |                    |
| Francês                 |     `fr`      |     ✓      |            |                                 |                    |
| Alemão                 |     `de`      |     ✓      |            |                                 |                    |
| Hebraico                |     `he`      |     ✓      |            |                                 |                    |
| Húngaro             |     `hu`      |     ✓      |            |                                 |                    |
| Italiano               |     `it`      |     ✓      |            |                                 |                    |
| Japonês              |     `ja`      |     ✓      |            |                                 |                    |
| Coreano                |     `ko`      |     ✓      |            |                                 |                    |
| Norueguês (Bokmål)   |     `no`      |     ✓      |            |                                 | `nb` também aceitou |
| Polaco                |     `pl`      |     ✓      |            |                                 |                    |
| Português (Brasil)   |    `pt-BR`    |     ✓      |            |                                 |                    |
| Português (Portugal) |    `pt-PT`    |     ✓      |            |                                 | `pt` também aceitou |
| Russo              |     `ru`      |     ✓      |            |                                 |                    |
| Espanhol               |     `es`      |     ✓      |     ✓       |              2020-04-01                   |                    |
| Sueco               |     `sv`      |     ✓      |            |                                 |                    |
| Turco               |     `tr`      |     ✓      |            |                                 |                    |

#### <a name="key-phrase-extraction"></a>[Extração de frase-chave](#tab/key-phrase-extraction)

| Linguagem              | Código do idioma | v2 apoio | v3 suporte | Disponível a partir da versão do modelo V3: |       Notas        |
|:----------------------|:-------------:|:----------:|:----------:|:-----------------------------------------:|:------------------:|
| Dinamarquês                |     `da`      |     ✓      |      ✓     |                2019-10-01                 |                    |
| Neerlandês                 |     `nl`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Inglês               |     `en`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Finlandês               |     `fi`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Francês                |     `fr`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Alemão                |     `de`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Italiano               |     `it`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Japonês              |     `ja`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Coreano                |     `ko`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Norueguês (Bokmål)   |     `no`      |     ✓      |     ✓      |                2019-10-01                 | `nb` também aceitou |
| Polaco                |     `pl`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Português (Brasil)   |    `pt-BR`    |     ✓      |     ✓      |                2019-10-01                 |                    |
| Português (Portugal) |    `pt-PT`    |     ✓      |     ✓      |                2019-10-01                 | `pt` também aceitou |
| Russo               |     `ru`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Espanhol               |     `es`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Sueco               |     `sv`      |     ✓      |     ✓      |                2019-10-01                 |                    |

#### <a name="entity-linking"></a>[Ligação de entidades](#tab/entity-linking)

| Linguagem | Código do idioma | v2 apoio | v3 suporte | Disponível a partir da versão do modelo V3: | Notas |
|:---------|:-------------:|:----------:|:----------:|:-----------------------------------------:|:-----:|
| Inglês  |     `en`      |     ✓      |     ✓      |                2019-10-01                 |       |
| Espanhol  |     `es`      |     ✓      |     ✓      |                2019-10-01                 |       |

#### <a name="language-detection"></a>[Deteção de Idioma](#tab/language-detection)

A API text Analytics pode detetar uma vasta gama de línguas, variantes, dialetos e algumas línguas regionais/culturais, e devolver línguas detetadas com o seu nome e código. Os parâmetros do código linguístico de deteção de linguagem de deteção de linguagem de análise de texto estão em conformidade com a [norma BCP-47,](https://tools.ietf.org/html/bcp47) com a maioria a estar em conformidade com os identificadores [ISO-639-1.](https://www.iso.org/iso-639-language-codes.html) 

Se tiver conteúdo expresso num idioma menos utilizado, pode tentar a Deteção de Idiomas para ver se devolve um código. A resposta para línguas que não podem ser detetadas é `unknown` .

| Linguagem | Código do Idioma | v3 suporte | Disponível a partir da versão do modelo V3: |
|:-|:-:|:-:|:-:|
| Africânes | `af` | ✓ |  |
| Albanês | `sq` | ✓ |  |
| Árabe | `ar` | ✓ |  |
| Arménio | `hy` | ✓ |  |
| Basco | `eu` | ✓ |  |
| Bielorrusso | `be` | ✓ |  |
| Bengali | `bn` | ✓ |  |
| Bósnio | `bs` | ✓ | 2020-09-01 |
| Búlgaro | `bg` | ✓ |  |
| Birmanês | `my` | ✓ |  |
| Catalão, Valenciano | `ca` | ✓ |  |
| Central Khmer | `km` | ✓ |  |
| Chinês | `zh` | ✓ |  |
| Chinês Simplificado | `zh_chs` | ✓ |  |
| Tradicional chinês | `zh_cht` | ✓ |  |
| Croata | `hr` | ✓ |  |
| Checo | `cs` | ✓ |  |
| Dinamarquês | `da` | ✓ |  |
| Rio Dari | `prs` | ✓ | 2020-09-01 |
| Divehi, Dhivehi, Maldivian | `dv` | ✓ |  |
| Holandês, Flamengo | `nl` | ✓ |  |
| Inglês | `en` | ✓ |  |
| Esperanto | `eo` | ✓ |  |
| Estónio | `et` | ✓ |  |
| Fijian | `fj` | ✓ | 2020-09-01 |
| Finlandês | `fi` | ✓ |  |
| Francês | `fr` | ✓ |  |
| Galego | `gl` | ✓ |  |
| Geórgia | `ka` | ✓ |  |
| Alemão | `de` | ✓ |  |
| Grego | `el` | ✓ |  |
| Rio Gujarati | `gu` | ✓ |  |
| Haiti, crioulo haitiano | `ht` | ✓ |  |
| Hebraico | `he` | ✓ |  |
| Hindi | `hi` | ✓ |  |
| Hmong Daw | `mww` | ✓ | 2020-09-01 |
| Húngaro | `hu` | ✓ |  |
| Islandês | `is` | ✓ |  |
| Indonésio | `id` | ✓ |  |
| Inuktitut | `iu` | ✓ |  |
| Irlandês | `ga` | ✓ |  |
| Italiano | `it` | ✓ |  |
| Japonês | `ja` | ✓ |  |
| Rio Kannada | `kn` | ✓ |  |
| Cazaque | `kk` | ✓ | 2020-09-01 |
| Coreano | `ko` | ✓ |  |
| Curdo | `ku` | ✓ |  |
| Rio Lao | `lo` | ✓ |  |
| Latim | `la` | ✓ |  |
| Letão | `lv` | ✓ |  |
| Lituano | `lt` | ✓ |  |
| Macedónio | `mk` | ✓ |  |
| Malgaxe | `mg` | ✓ | 2020-09-01 |
| Malaio | `ms` | ✓ |  |
| Malayalam | `ml` | ✓ |  |
| Maltês | `mt` | ✓ |  |
| Rio Maori | `mi` | ✓ | 2020-09-01 |
| Rio Marathi | `mr` | ✓ | 2020-09-01 |
| Norueguês | `no` | ✓ |  |
| Norueguês (Nynorsk) | `nn` | ✓ |  |
| Oriya | `or` | ✓ |  |
| Pashto | `ps` | ✓ |  |
| Persa | `fa` | ✓ |  |
| Polaco | `pl` | ✓ |  |
| Português | `pt` | ✓ |  |
| Punjabi | `pa` | ✓ |  |
| Queretaro Otomi | `otq` | ✓ | 2020-09-01 |
| Romeno, Moldávio, Moldávia | `ro` | ✓ |  |
| Russo | `ru` | ✓ |  |
| Samoa | `sm` | ✓ | 2020-09-01 |
| Sérvio | `sr` | ✓ |  |
| Sinhala | `si` | ✓ |  |
| Eslovaco | `sk` | ✓ |  |
| Esloveno | `sl` | ✓ |  |
| Somália | `so` | ✓ |  |
| Espanhol, Castelhiano | `es` | ✓ |  |
| Suaíli | `sw` | ✓ |  |
| Sueco | `sv` | ✓ |  |
| Tagalog | `tl` | ✓ |  |
| Taitiano | `ty` | ✓ | 2020-09-01 |
| Tâmil | `ta` | ✓ |  |
| Telugu | `te` | ✓ |  |
| Tailandês | `th` | ✓ |  |
| Tongan | `to` | ✓ | 2020-09-01 |
| Turco | `tr` | ✓ |  |
| Ucraniano | `uk` | ✓ |  |
| Rio Urdu | `ur` | ✓ |  |
| Uzbeque | `uz` | ✓ |  |
| Vietnamita | `vi` | ✓ |  |
| Galês | `cy` | ✓ |  |
| Iídiche | `yi` | ✓ |  |
| Maia-de-iucatec | `yua` | ✓ |  |

---

## <a name="see-also"></a>Veja também

* [O que é a API de Análise de Texto?](overview.md)   
