---
title: Suporte linguístico - Tradutor
titleSuffix: Azure Cognitive Services
description: O Tradutor de Serviços Cognitivos suporta as seguintes línguas para tradução de texto para texto usando tradução automática neural (NMT).
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 05/26/2020
ms.author: swmachan
ms.openlocfilehash: 6339b86166aff008bf17b5096d42629daf6e3401
ms.sourcegitcommit: b55d1d1e336c1bcd1c1a71695b2fd0ca62f9d625
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2020
ms.locfileid: "84434282"
---
# <a name="language-and-region-support-for-translator"></a>Apoio linguístico e regional ao Tradutor

O Tradutor apoia as seguintes línguas para tradução de texto para texto. A Tradução Automática Neural (NMT) é o novo padrão para traduções automáticas alimentadas por IA de alta qualidade e está disponível como o padrão usando V3 de Tradutor quando um sistema neural está disponível.

[Saiba mais sobre como funciona a tradução automática](https://www.microsoft.com/translator/mt.aspx)

## <a name="translation"></a>Tradução

**Tradutor V2**

> [!NOTE]
> A V2 foi depreciada a 30 de abril de 2018. Por favor, emordene as suas aplicações para v3 de forma a tirar partido das novas funcionalidades disponíveis exclusivamente na V3.

* Apenas estatístico: Não existe nenhum sistema neural disponível para esta língua.
* Neural disponível: Um sistema neural está disponível. Use o parâmetro `category=generalnn` para aceder ao sistema neural.
* Padrão neural: Neural é o sistema de tradução predefinido. Utilize o parâmetro `category=smt` para aceder ao sistema estatístico para utilização com o Microsoft Tradutor Hub.
* Apenas neural: Só existe tradução neural.

**Tradutor V3** O Tradutor V3 é neural por padrão e os sistemas estatísticos só estão disponíveis quando não existe sistema neural.

> [!NOTE]
> Atualmente, um subconjunto das línguas neurais está disponível no Custom Tradutor e estamos gradualmente adicionando outros. [Ver idiomas atualmente disponíveis no Custom Tradutor.](#customization)

|Linguagem|    Código do idioma|    V3 API|
|:-----|:-----:|:-----|
|Afrikaans|    `af`|    Neural|
|Árabe|    `ar`    |    Neural|
|Bangla|    `bn`    |    Neural|
|Bósnio (Latim)|    `bs`    |    Neural|
|Búlgaro|    `bg`    |    Neural|
|Cantonês (Tradicional)|    `yue`|    Estatística|
|Catalão|    `ca`    |    Estatística|
|Chinês simplificado|    `zh-Hans`|Neural|
|Chinês tradicional|    `zh-Hant`        |Neural|
|Croata|    `hr`    |Neural|
|Checo|    `cs`    |    Neural|
|Dinamarquês|    `da`        |Neural|
|Neerlandês|    `nl`|    Neural|
|Inglês|    `en`    |    Neural|
|Estónio|    `et`    |    Neural|
|Fijian|    `fj`    |    Estatística|
|Filipino|    `fil`    |    Estatística|
|Finlandês|    `fi`    |    Neural|
|Francês|    `fr`    |    Neural|
|Alemão|    `de`    |    Neural|
|Grego|    `el`    |    Neural|
|Guzerate|    `gu`    |    Neural|
|Crioulo haitiano|    `ht`        |Estatística|
|Hebraico    |`he`    |Neural
|Hindi|    `hi`    |    Neural|
|Hmong Daw|    `mww`    |    Estatística|
|Húngaro|    `hu`    |    Neural|
|Islandês|    `is`    |    Neural|
|Indonésio|    `id`    |    Estatística|
|Irlandês | `ga`| Neural
|Italiano|    `it`    |    Neural|
|Japonês|    `ja`    |    Neural|
|Canarês|`kn`| Neural|
|Cazaque |`kk`| Neural|
|Rio Kiswahili|    `sw`    |    Estatística|
|Klingon|    `tlh-Latn`    |    Estatística|
|Klingon (plqaD)|    `tlh-Piqd`    |    Estatística|
|Coreano    |`ko`    |    Neural|
|Letão|    `lv`    |    Neural|
|Lituano|    `lt`    |    Neural|
|Malgaxe|    `mg`    |    Estatística|
|Malaio|    `ms`        |Estatística|
|Malaiala| `ml` | Neural
|Maltês|    `mt`    |    Estatística|
|Maori| `mi`  | Neural|
|Marata| `mr`  | Neural|
|Norueguês|    `nb`    |    Neural|
|Persa|    `fa`    |    Neural|
|Polaco|    `pl`    |    Neural|
|Português (Brasil)|    `pt-br`    |    Neural|
|Português (Portugal)| `pt-pt` | Neural
|Punjabi|`pa`|Neural
|Queretaro Otomi|    `otq`    |    Estatística|
|Romeno|    `ro`    |    Neural|
|Russo|    `ru`    |    Neural|
|Samoa|    `sm`    |    Estatística|
|Sérvio (Cirílico)|    `sr-Cyrl`|    Estatística|
|Sérvio (Latim)|    `sr-Latn`        |Estatística|
|Eslovaco|    `sk`    |    Neural|
|Esloveno|    `sl`    |    Neural|
|Espanhol|    `es`    |    Neural|
|Sueco|    `sv`    |Neural|
|Taitiano|    `ty`    |Estatística|
|Tâmil|    `ta`    |    Neural|
|Telugu|    `te`    |    Neural|
|Tailandês|    `th`    |    Neural|
|Tongan|    `to`    |    Estatística|
|Turco|    `tr`        |Neural|
|Ucraniano|    `uk`    |    Neural|
|Urdu|    `ur`    |    Estatística|
|Vietnamita|    `vi`    |    Neural|
|Galês|    `cy`    |    Neural|
|Maia-de-iucatec|    `yua`    |    Estatística|

> [!NOTE]
> O código `pt` linguístico será desafins a `pt-br` português (Brasil).

## <a name="transliteration"></a>Transliteração

O método transliterado suporta as seguintes línguas. No "De/Para", "<->" indica que a língua pode ser transliterada de ou para qualquer um dos scripts listados. O "->" indica que a linguagem só pode ser transliterada de um script para o outro.

| Linguagem    | Código do idioma | Script | Para/De | Script|
|:----------- |:-------------:|:-------------:|:-------------:|:-------------:|
| Árabe | `ar` | Árabe`Arab` | <--> | Latim`Latn` |
|Bangla  | `bn` | Bengalês`Beng` | <--> | Latim`Latn` |
| Chinês (Simplificado) | `zh-Hans` | Chinês simplificado`Hans`| <--> | Latim`Latn` |
| Chinês (Simplificado) | `zh-Hans` | Chinês simplificado`Hans`| <--> | Chinês tradicional`Hant`|
| Chinês (Tradicional) | `zh-Hant` | Chinês tradicional`Hant`| <--> | Latim`Latn` |
| Chinês (Tradicional) | `zh-Hant` | Chinês tradicional`Hant`| <--> | Chinês simplificado`Hans` |
| Guzerate | `gu`  | Guzerate`Gujr` | <--> | Latim`Latn` |
| Hebraico | `he` | Hebraico`Hebr` | <--> | Latim`Latn` |
| Hindi | `hi` | Devanagari`Deva` | <--> | Latim`Latn` |
| Japonês | `ja` | Japonês`Jpan` | <--> | Latim`Latn` |
| Canarês | `kn` | Canarês`Knda` | <--> | Latim`Latn` |
| Malaiala | `ml` | Malaiala`Mlym` | <--> | Latim`Latn` |
| Marata | `mr` | Devanagari`Deva` | <--> | Latim`Latn` |
| Oriya | `or` | Oriya`Orya` | <--> | Latim`Latn` |
| Punjabi | `pa` | Rio Gurmukhi`Guru`  | <--> | Latim`Latn`  |
| Sérvio (Cirílico) | `sr-Cyrl` | Cirílico`Cyrl`  | --> | Latim`Latn` |
| Sérvio (Latim) | `sr-Latn` | Latim`Latn` | --> | Cirílico`Cyrl`|
| Tâmil | `ta` | Tâmil`Taml` | <--> | Latim`Latn` |
| Telugu | `te` | Telugu`Telu` | <--> | Latim`Latn` |
| Tailandês | `th` | Tailandês`Thai` | <--> | Latim`Latn` |

## <a name="dictionary"></a>Dicionário

O dicionário suporta as seguintes línguas de ou para o inglês usando os métodos Lookup e Examples.

| Linguagem    | Código do idioma |
|:----------- |:-------------:|
| Afrikaans      | `af`          |
| Árabe       | `ar`          |
| Bangla      | `bn`          |
| Bósnio (Latim)      | `bs`          |
| Búlgaro      | `bg`          |
| Catalão      | `ca`          |
| Chinês simplificado      | `zh-Hans`          |
| Croata      | `hr`          |
| Checo      | `cs`          |
| Dinamarquês      | `da`          |
| Neerlandês      | `nl`          |
| Estónio      | `et`          |
| Finlandês      | `fi`          |
| Francês      | `fr`          |
| Alemão      | `de`          |
| Grego      | `el`          |
| Crioulo haitiano      | `ht`          |
| Hebraico      | `he`          |
| Hindi      | `hi`          |
| Hmong Daw      | `mww`          |
| Húngaro      | `hu`          |
| Islandês    | `is`  |
| Indonésio      | `id`          |
| Italiano      | `it`          |
| Japonês      | `ja`          |
| Rio Kiswahili      | `sw`          |
| Klingon      | `tlh-Latn`          |
| Coreano      | `ko`          |
| Letão      | `lv`          |
| Lituano      | `lt`          |
| Malaio      | `ms`          |
| Maltês      | `mt`          |
| Norueguês      | `nb`          |
| Persa      | `fa`          |
| Polaco      | `pl`          |
| Português (Brasil)     | `pt-br`          |
| Romeno      | `ro`          |
| Russo      | `ru`          |
| Sérvio (Latim)      | `sr-Latn`          |
| Eslovaco     | `sk`          |
| Esloveno      | `sl`          |
| Espanhol      | `es`          |
| Sueco      | `sv`          |
| Tâmil      | `ta`          |
| Tailandês      | `th`          |
| Turco      | `tr`          |
| Ucraniano      | `uk`          |
| Urdu      | `ur`          |
| Vietnamita      | `vi`          |
| Galês      | `cy`          |

## <a name="detect"></a>Detetar

O tradutor deteta todas as línguas disponíveis para tradução e transliteração.


## <a name="access-the-translator-language-list-programmatically"></a>Aceda à lista de línguas tradutora programáticamente

Pode obter uma lista de línguas apoiadas para tradutor v3.0 utilizando o método idiomas. Pode ver a lista por recurso, código de idioma, bem como o nome da língua em inglês ou qualquer outro idioma suportado. Esta lista é atualizada automaticamente pelo serviço Microsoft Tradutor, uma vez que são disponibilizados novos idiomas.

[Ver documentação de referência de operação de línguas](reference/v3-0-languages.md)

## <a name="customization"></a>Personalização

Os seguintes idiomas estão disponíveis para personalização de ou para inglês usando [o Tradutor Personalizado.](https://aka.ms/CustomTranslator)

| Linguagem    | Código do idioma |
|:----------- |:-------------:|
| Árabe       | `ar`          |
| Bangla      | `bn`          |
| Bósnio (Latim)      | `bs`          |
| Búlgaro      | `bg`          |
| Chinês simplificado      | `zh-Hans`          |
|Chinês tradicional|    `zh-Hant`    |
| Croata      | `hr`          |
| Checo      | `cs`          |
| Dinamarquês      | `da`          |
| Neerlandês      | `nl`          |
| Inglês    | `en`     |
| Estónio      | `et`          |
| Finlandês      | `fi`          |
| Francês      | `fr`          |
| Alemão      | `de`          |
| Grego      | `el`          |
| Hebraico      | `he`          |
| Hindi      | `hi`          |
| Húngaro      | `hu`          |
| Islandês | `is` |
| Indonésio|    `id`    |
| Irlandês | `ga`    |
| Italiano      | `it`          |
| Japonês      | `ja`          |
| Rio Kiswahili|    `sw`    |
| Coreano      | `ko`          |
| Letão      | `lv`          |
| Lituano      | `lt`          |
| Malgaxe|    `mg`    |
| Maori| `mi`  |
| Norueguês      | `nb`          |
| Persa      | `fa`          |
| Polaco      | `pl`          |
| Português (Brasil) | `pt-br` |
| Romeno      | `ro`          |
| Russo      | `ru`          |
| Samoa|    `sm`    |
| Sérvio (Latim)      | `sr-Latn`          |
| Eslovaco     | `sk`          |
| Esloveno      | `sl`          |
| Espanhol      | `es`          |
| Sueco      | `sv`          |
| Tailandês      | `th`          |
| Turco      | `tr`          |
| Ucraniano      | `uk`          |
| Vietnamita      | `vi`          |
| Galês | `cy` |

## <a name="access-the-list-on-the-microsoft-translator-website"></a>Aceda à lista no site do Microsoft Tradutor

Para uma rápida olhada nos idiomas, o site do Microsoft Tradutor mostra todos os idiomas suportados pelas APIs tradutor e de fala. Esta lista não inclui informações específicas do programador, tais como códigos linguísticos.

[Ver a lista de línguas](https://www.microsoft.com/translator/languages.aspx)
