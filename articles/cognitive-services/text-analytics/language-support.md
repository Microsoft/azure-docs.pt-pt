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
ms.date: 06/03/2020
ms.author: aahi
ms.openlocfilehash: 4fd893691f68c8b51fa3ef234d7828b1bd70cea5
ms.sourcegitcommit: 813f7126ed140a0dff7658553a80b266249d302f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/06/2020
ms.locfileid: "84465597"
---
# <a name="text-analytics-api-v3-language-support"></a>Suporte linguístico API v3 de API de análise de texto 

> [!IMPORTANT]
> A versão 3.x da Text Analytics API está atualmente indisponível nas seguintes regiões: Índia Central, Uae North, China Norte 2, China Leste.


#### <a name="sentiment-analysis"></a>[Análise de Sentimentos](#tab/sentiment-analysis)

| Idioma              | Código do idioma | v2 apoio | v3 suporte | Versão do modelo V3 inicial: |              Notas |
|:----------------------|:-------------:|:----------:|:----------:|:--------------------------:|-------------------:|
| Chinês-Simplificado    |   `zh-hans`   |     ✓      |     ✓      |         2019-10-01         | `zh`também aceitou |
| Chinês-Tradicional   |   `zh-hant`   |            |     ✓      |         2019-10-01         |                    |
| Dinamarquês               |     `da`      |     ✓      |            |                            |                    |
| Neerlandês                 |     `nl`      |     ✓      |            |                            |                    |
| Inglês               |     `en`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Finlandês               |     `fi`      |     ✓      |            |                            |                    |
| Francês                |     `fr`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Alemão                |     `de`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Grego                 |     `el`      |     ✓      |            |                            |                    |
| Italiano               |     `it`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Japonês              |     `ja`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Coreano                |     `ko`      |            |     ✓      |         2019-10-01         |                    |
| Norueguês (Bokmål)   |     `no`      |     ✓      |            |                            |                    |
| Polaco                |     `pl`      |     ✓      |            |                            |                    |
| Português (Portugal) |    `pt-PT`    |     ✓      |     ✓      |         2019-10-01         | `pt`também aceitou |
| Russo               |     `ru`      |     ✓      |            |                            |                    |
| Espanhol               |     `es`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Sueco               |     `sv`      |     ✓      |            |                            |                    |
| Turco               |     `tr`      |     ✓      |            |                            |                    |

### <a name="opinion-mining-v31-preview-only"></a>Mineração de opinião (apenas v3.1-pré-visualização)

| Idioma              | Código do idioma | Começando com a versão do modelo V3: |              Notas |
|:----------------------|:-------------:|:------------------------------------:|-------------------:|
| Inglês               |     `en`      |              2020-04-01              |                    |


#### <a name="named-entity-recognition-ner"></a>[Reconhecimento de Entidades Nomeadas (NER)](#tab/named-entity-recognition)

> [!NOTE]
> * Atualmente, o NER v3 apenas suporta a língua inglesa. Se ligar para o NER v3 com um idioma diferente, a API devolverá os resultados v2.1, desde que o idioma seja suportado na versão 2.1.
> * v2.1 apenas devolve o conjunto completo de entidades disponíveis para as línguas inglesa, chinesa-simplificada, francesa, alemã e espanhola.  As entidades "Pessoa", "Localização" e "Organização" são devolvidas para as outras línguas apoiadas.

| Idioma               | Código do idioma | v2.1 apoio | v3 suporte | Começando com a versão do modelo V3: |       Notas        |
|:-----------------------|:-------------:|:----------:|:----------:|:-------------------------------:|:------------------:|
| Árabe                |     `ar`      |     ✓      |            |                                 |                    |
| Checo                 |     `cs`      |     ✓      |            |                                 |                    |
| Chinês-Simplificado     |   `zh-hans`   |     ✓      |            |                                 | `zh`também aceitou |
| Chinês-Tradicional   |   `zh-hant`   |     ✓      |            |                                 |                    |
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
| Norueguês (Bokmål)   |     `no`      |     ✓      |            |                                 | `nb`também aceitou |
| Polaco                |     `pl`      |     ✓      |            |                                 |                    |
| Português (Portugal) |    `pt-PT`    |     ✓      |            |                                 | `pt`também aceitou |
| Português (Brasil)   |    `pt-BR`    |     ✓      |            |                                 |                    |
| Russo              |     `ru`      |     ✓      |            |                                 |                    |
| Espanhol               |     `es`      |     ✓      |            |                                 |                    |
| Sueco               |     `sv`      |     ✓      |            |                                 |                    |
| Turco               |     `tr`      |     ✓      |            |                                 |                    |

#### <a name="key-phrase-extraction"></a>[Extração de frase-chave](#tab/key-phrase-extraction)

| Idioma              | Código do idioma | v2 apoio | v3 suporte | Disponível a partir da versão do modelo V3: |       Notas        |
|:----------------------|:-------------:|:----------:|:----------:|:-----------------------------------------:|:------------------:|
| Neerlandês                 |     `nl`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Inglês               |     `en`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Finlandês               |     `fi`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Francês                |     `fr`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Alemão                |     `de`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Italiano               |     `it`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Japonês              |     `ja`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Coreano                |     `ko`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Norueguês (Bokmål)   |     `no`      |     ✓      |     ✓      |                2019-10-01                 | `nb`também aceitou |
| Polaco                |     `pl`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Português (Portugal) |    `pt-PT`    |     ✓      |     ✓      |                2019-10-01                 | `pt`também aceitou |
| Português (Brasil)   |    `pt-BR`    |     ✓      |     ✓      |                2019-10-01                 |                    |
| Russo               |     `ru`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Espanhol               |     `es`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Sueco               |     `sv`      |     ✓      |     ✓      |                2019-10-01                 |                    |

#### <a name="entity-linking"></a>[Ligação de entidades](#tab/entity-linking)

| Idioma | Código do idioma | v2 apoio | v3 suporte | Disponível a partir da versão do modelo V3: | Notas |
|:---------|:-------------:|:----------:|:----------:|:-----------------------------------------:|:-----:|
| Inglês  |     `en`      |     ✓      |     ✓      |                2019-10-01                 |       |
| Espanhol  |     `es`      |     ✓      |     ✓      |                2019-10-01                 |       |

#### <a name="language-detection"></a>[Deteção de Idioma](#tab/language-detection)

A API text Analytics pode detetar uma vasta gama de línguas, variantes, dialetos e algumas línguas regionais/culturais.  A Deteção de Idiomas devolve o "script" de uma língua. Por exemplo, para a frase "tenho um cão" ele vai voltar `en` em vez de `en-US` . O único caso especial é o chinês, onde a capacidade de deteção de idiomas regressará `zh_CHS` ou se pode determinar o script dado o texto `zh_CHT` fornecido. Em situações em que um guião específico não possa ser identificado para um documento chinês, ele regressará simplesmente `zh` .

Não publicamos a lista exata de línguas para esta funcionalidade, mas pode detetar uma grande variedade de línguas, variantes, dialetos e algumas línguas regionais/culturais. 

Se tiver conteúdo expresso num idioma menos utilizado, pode tentar a Deteção de Idiomas para ver se devolve um código. A resposta para línguas que não podem ser detetadas é `unknown` .

---

## <a name="see-also"></a>Consulte também

* [O que é a API de Análise de Texto?](overview.md)   
