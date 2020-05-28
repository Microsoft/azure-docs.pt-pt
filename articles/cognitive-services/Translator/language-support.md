---
title: Apoio linguístico - Tradutor
titleSuffix: Azure Cognitive Services
description: O Tradutor de Serviços Cognitivos suporta as seguintes línguas para tradução de texto a texto utilizando tradução da máquina neural (NMT).
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 05/26/2020
ms.author: swmachan
ms.openlocfilehash: 359079ad73fcf162fb742afe74c1c1de5896c35c
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83996079"
---
# <a name="language-and-region-support-for-translator"></a>Apoio à língua e à região para tradutor

O Tradutor suporta as seguintes línguas para a tradução de texto para texto. A Tradução da Máquina Neural (NMT) é o novo padrão para traduções automáticas de alta qualidade alimentadas por IA e está disponível como padrão utilizando V3 de Tradutor quando um sistema neural está disponível.

[Saiba mais sobre como funciona a tradução automática](https://www.microsoft.com/translator/mt.aspx)

## <a name="translation"></a>Tradução

**Tradutor V2**

> [!NOTE]
> A V2 foi depreciada a 30 de abril de 2018. Por favor, emigre as suas aplicações para v3 de forma a tirar partido das novas funcionalidades disponíveis exclusivamente em V3.

* Apenas estatístico: Não existe nenhum sistema neural disponível para esta língua.
* Neural disponível: Um sistema neural está disponível. Utilize o parâmetro `category=generalnn` para aceder ao sistema neural.
* Padrão neural: Neural é o sistema de tradução padrão. Utilize o parâmetro `category=smt` para aceder ao sistema estatístico para utilização com o Microsoft Tradutor Hub.
* Apenas neural: Apenas a tradução neural está disponível.

**Tradutor V3** O Tradutor V3 é neural por defeito e os sistemas estatísticos só estão disponíveis quando não existe nenhum sistema neural.

> [!NOTE]
> Atualmente, um subconjunto das línguas neurais está disponível no Tradutor Personalizado e estamos gradualmente adicionando outros. [Ver idiomas atualmente disponíveis em Tradutor Personalizado.](#customization)

|Linguagem|    Código do idioma|    V3 API|
|:-----|:-----:|:-----|
|Afrikaans|    `af`|    Neural|
|Árabe|    `ar`    |    Neural|
|Rio Bangla|    `bn`    |    Neural|
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
|Canarês|`kn`| Neural
|Rio Kiswahili|    `sw`    |    Estatística|
|Rio Klingon|    `tlh`    |    Estatística|
|Klingon (plqaD)|    `tlh-Qaak`    |    Estatística|
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
|Samoano|    `sm`    |    Estatística|
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
|Maia yucateca|    `yua`    |    Estatística|

> [!NOTE]
> O código `pt` linguístico será indefinido para `pt-br` , Português (Brasil).

## <a name="transliteration"></a>Transliteração

O método transliterado suporta as seguintes línguas. No "To/From", "<-->" indica que a língua pode ser transliterada de ou para qualquer um dos scripts listados. O "-->" indica que a língua só pode ser transliterada de um guião para outro.

| Linguagem    | Código do idioma | Script | Para/De | Script|
|:----------- |:-------------:|:-------------:|:-------------:|:-------------:|
| Árabe | `ar` | Árabe`Arab` | <--> | Latim`Latn` |
|Rio Bangla  | `bn` | Bengalês`Beng` | <--> | Latim`Latn` |
| Chinês (Simplificado) | `zh-Hans` | Chinês simplificado`Hans`| <--> | Latim`Latn` |
| Chinês (Simplificado) | `zh-Hans` | Chinês simplificado`Hans`| <--> | Chinês tradicional`Hant`|
| Chinês (Tradicional) | `zh-Hant` | Chinês tradicional`Hant`| <--> | Latim`Latn` |
| Chinês (Tradicional) | `zh-Hant` | Chinês tradicional`Hant`| <--> | Chinês simplificado`Hans` |
| Guzerate | `gu`  | Guzerate`Gujr` | --> | Latim`Latn` |
| Hebraico | `he` | Hebraico`Hebr` | <--> | Latim`Latn` |
| Hindi | `hi` | Rio Devanagari`Deva` | <--> | Latim`Latn` |
| Japonês | `ja` | Japonês`Jpan` | <--> | Latim`Latn` |
| Canarês | `kn` | Canarês`Knda` | --> | Latim`Latn` |
| Malaiala | `ml` | Malaiala`Mlym` | --> | Latim`Latn` |
| Marata | `mr` | Rio Devanagari`Deva` | --> | Latim`Latn` |
| Oriya | `or` | Oriya`Orya` | <--> | Latim`Latn` |
| Punjabi | `pa` | Rio Gurmukhi`Guru`  | <--> | Latim`Latn`  |
| Sérvio (Cirílico) | `sr-Cyrl` | Cirílico`Cyrl`  | --> | Latim`Latn` |
| Sérvio (Latim) | `sr-Latn` | Latim`Latn` | --> | Cirílico`Cyrl`|
| Tâmil | `ta` | Tâmil`Taml` | --> | Latim`Latn` |
| Telugu | `te` | Telugu`Telu` | --> | Latim`Latn` |
| Tailandês | `th` | Tailandês`Thai` | --> | Latim`Latn` |

## <a name="dictionary"></a>Dicionário

O dicionário suporta as seguintes línguas de ou para inglês utilizando os métodos De supérão e Exemplos.

| Linguagem    | Código do idioma |
|:----------- |:-------------:|
| Afrikaans      | `af`          |
| Árabe       | `ar`          |
| Rio Bangla      | `bn`          |
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
| Rio Klingon      | `tlh`          |
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

Tradutor deteta todas as línguas disponíveis para tradução e transliteração.


## <a name="access-the-translator-language-list-programmatically"></a>Aceda à lista de línguas tradutora programáticamente

Pode obter uma lista de idiomas suportados para tradutor v3.0 utilizando o método Idiomas. Pode ver a lista por recurso, código de idioma, bem como o nome de língua em inglês ou qualquer outro idioma suportado. Esta lista é automaticamente atualizada pelo serviço Microsoft Tradutor à medida que novos idiomas são disponibilizados.

[Ver documentação de referência da operação Idiomas](reference/v3-0-languages.md)

## <a name="customization"></a>Personalização

Os seguintes idiomas estão disponíveis para personalização de ou para inglês usando [tradutor personalizado](https://aka.ms/CustomTranslator).

| Linguagem    | Código do idioma |
|:----------- |:-------------:|
| Árabe       | `ar`          |
| Rio Bangla      | `bn`          |
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
| Samoano|    `sm`    |
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

Para uma rápida olhada nos idiomas, o site do Microsoft Tradutor mostra todos os idiomas suportados pelos APIs de Tradução e Fala. Esta lista não inclui informações específicas do desenvolvedor, tais como códigos de idiomas.

[Ver a lista de línguas](https://www.microsoft.com/translator/languages.aspx)
