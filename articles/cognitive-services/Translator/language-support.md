---
title: Suporte linguístico - API de Texto tradutor
titleSuffix: Azure Cognitive Services
description: A API de texto tradutor suporta os seguintes idiomas para a tradução de texto a texto utilizando tradução da máquina neural (NMT).
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/10/2020
ms.author: swmachan
ms.openlocfilehash: ec5c1295f883bb37ad5f016bc3346c134863e6bd
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77118812"
---
# <a name="language-and-region-support-for-the-translator-text-api"></a>Apoio à língua e à região para a API de Texto tradutor

A API de Texto tradutor suporta as seguintes línguas para a tradução de texto para texto. A Tradução da Máquina Neural (NMT) é o novo padrão para traduções automáticas de alta qualidade alimentadas por IA e está disponível como padrão utilizando v3 da API de texto tradutor quando um sistema neural está disponível.

[Saiba mais sobre como funciona a tradução automática](https://www.microsoft.com/translator/mt.aspx)

## <a name="translation"></a>Tradução

**V2 Tradutor API**

> [!NOTE]
> A V2 foi depreciada a 30 de abril de 2018. Por favor, emigre as suas aplicações para v3 de forma a tirar partido das novas funcionalidades disponíveis exclusivamente em V3.

* Apenas estatístico: Não existe nenhum sistema neural disponível para esta língua.
* Neural disponível: Um sistema neural está disponível. Utilize o parâmetro `category=generalnn` para aceder ao sistema neural.
* Padrão neural: Neural é o sistema de tradução padrão. Utilize o parâmetro `category=smt` para aceder ao sistema estatístico para utilização com o Microsoft Tradutor Hub.
* Apenas neural: Apenas a tradução neural está disponível.

**V3 Tradutor API** A API tradutora V3 é neural por padrão e os sistemas estatísticos só estão disponíveis quando não existe nenhum sistema neural.

> [!NOTE]
> Atualmente, um subconjunto das línguas neurais está disponível no Tradutor Personalizado e estamos gradualmente adicionando outros. [Ver idiomas atualmente disponíveis em Tradutor Personalizado.](#customization)

|Idioma|  Código do idioma|  V3 API|
|:-----|:-----:|:-----|
|Afrikaans| `af`|   Neural|
|Árabe|    `ar`    |   Neural|
|Bangla|    `bn`    |   Neural|
|Bósnio (Latim)|   `bs`    |   Neural|
|Búlgaro| `bg`    |   Neural|
|Cantonês (tradicional)|   `yue`|  Estatística|
|Catalão|   `ca`    |   Estatística|
|Chinês simplificado|    `zh-Hans`|Neural|
|Chinês tradicional|   `zh-Hant`       |Neural|
|Croata|  `hr`    |Neural|
|Checo| `cs`    |   Neural|
|Dinamarquês|    `da`        |Neural|
|Neerlandês| `nl`|   Neural|
|Português|   `en`    |   Neural|
|Estónio|  `et`    |   Neural|
|Fijian|    `fj`    |   Estatística|
|Filipino|  `fil`   |   Estatística|
|Finlandês|   `fi`    |   Neural|
|Francês|    `fr`    |   Neural|
|Alemão|    `de`    |   Neural|
|Grego| `el`    |   Neural|
|Haitian Creole|    `ht`        |Estatística|
|Hebraico |`he`   |Neural
|Hindi| `hi`    |   Neural|
|Hmong Daw| `mww`   |   Estatística|
|Húngaro| `hu`    |   Neural|
|Islandês| `is`    |   Neural|
|Indonésio|    `id`    |   Estatística|
|Irlandês | `ga`| Neural
|Italiano|   `it`    |   Neural|
|Japonês|  `ja`    |   Neural|
|Canarim|`kn`| Neural
|Suaíli| `sw`    |   Estatística|
|Klingon|   `tlh`   |   Estatística|
|Klingon (plqaD)|   `tlh-Qaak`  |   Estatística|
|Coreano |`ko`   |   Neural|
|Letão|   `lv`    |   Neural|
|Lituano|    `lt`    |   Neural|
|Ariary|  `mg`    |   Estatística|
|Malaio| `ms`        |Estatística|
|Malayalam| `ml` | Neural
|Maltês|   `mt`    |   Estatística|
|Maori| `mi`  | Neural|
|Norueguês| `nb`    |   Neural|
|Persa|   `fa`    |   Neural|
|Polaco|    `pl`    |   Neural|
|Português (Brasil)|   `pt-br` |   Neural|
|Português (Portugal)| `pt-pt` | Neural
|Punjabi|`pa`|Neural
|Queretaro Otomi|   `otq`   |   Estatística|
|Romeno|  `ro`    |   Neural|
|Russo|   `ru`    |   Neural|
|Samoan|    `sm`    |   Estatística|
|Sérvio (Cirílico)|    `sr-Cyrl`|  Estatística|
|Sérvio (Latim)|   `sr-Latn`       |Estatística|
|Eslovaco|    `sk`    |   Neural|
|Esloveno| `sl`    |   Neural|
|Espanhol|   `es`    |   Neural|
|Sueco|   `sv`    |Neural|
|Tahitian|  `ty`    |Estatística|
|Tamil| `ta`    |   Neural|
|Télego|    `te`    |   Neural|
|Tailandês|  `th`    |   Neural|
|Tongan|    `to`    |   Estatística|
|Turco|   `tr`        |Neural|
|Ucraniano| `uk`    |   Neural|
|Urdu|  `ur`    |   Estatística|
|Vietnamita|    `vi`    |   Neural|
|Galês| `cy`    |   Neural|
|Yucatec Maya|  `yua`   |   Estatística|

> [!NOTE]
> O código linguístico `pt` será incumprido rédo a `pt-br`, Português (Brasil).

## <a name="transliteration"></a>Transliteração

O método transliterado suporta as seguintes línguas. No "To/From", "<->" indica que a língua pode ser transliterada de ou para qualquer um dos scripts listados. O "-&gt"," indica que a língua só pode ser transliterada de um guião para outro.

| Idioma    | Código do idioma | Script | Para/de | Script|
|:----------- |:-------------:|:-------------:|:-------------:|:-------------:|
| Árabe | `ar` | `Arab` árabe | <--> | `Latn` latino |
|Bangla  | `bn` | `Beng` de Bengali | <--> | `Latn` latino |
| Chinês (Simplificado) | `zh-Hans` | `Hans` simplificado chinês| <--> | `Latn` latino |
| Chinês (Simplificado) | `zh-Hans` | `Hans` simplificado chinês| <--> | `Hant` tradicional chinês|
| Chinês (Tradicional) | `zh-Hant` | `Hant` tradicional chinês| <--> | `Latn` latino |
| Chinês (Tradicional) | `zh-Hant` | `Hant` tradicional chinês| <--> | `Hans` simplificado chinês |
| Guzarate | `gu`  | `Gujr` Gujarati | --> | `Latn` latino |
| Hebraico | `he` | `Hebr` hebraico | <--> | `Latn` latino |
| Hindi | `hi` | Devanagari `Deva` | <--> | `Latn` latino |
| Japonês | `ja` | `Jpan` japonesa | <--> | `Latn` latino |
| Canarim | `kn` | `Knda` de Kannada | --> | `Latn` latino |
| Malayalam | `ml` | `Mlym` malaia | --> | `Latn` latino |
| Marata | `mr` | Devanagari `Deva` | --> | `Latn` latino |
| Oriya | `or` | Oriya `Orya` | <--> | `Latn` latino |
| Punjabi | `pa` | `Guru` de Gurmukhi  | <--> | `Latn` latino  |
| Sérvio (Cirílico) | `sr-Cyrl` | `Cyrl` cirílico  | --> | `Latn` latino |
| Sérvio (Latim) | `sr-Latn` | `Latn` latino | --> | `Cyrl` cirílico|
| Tamil | `ta` | `Taml` Tamil | --> | `Latn` latino |
| Télego | `te` | `Telu` de Telugu | --> | `Latn` latino |
| Tailandês | `th` | `Thai` tailandeses | <--> | `Latn` latino |

## <a name="dictionary"></a>Dicionário

O dicionário suporta as seguintes línguas de ou para inglês utilizando os métodos De supérão e Exemplos.

| Idioma    | Código do idioma |
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
| Haitian Creole      | `ht`          |
| Hebraico      | `he`          |
| Hindi      | `hi`          |
| Hmong Daw      | `mww`          |
| Húngaro      | `hu`          |
| Islandês    | `is`  |
| Indonésio      | `id`          |
| Italiano      | `it`          |
| Japonês      | `ja`          |
| Suaíli      | `sw`          |
| Klingon      | `tlh`          |
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
| Tamil      | `ta`          |
| Tailandês      | `th`          |
| Turco      | `tr`          |
| Ucraniano      | `uk`          |
| Urdu      | `ur`          |
| Vietnamita      | `vi`          |
| Galês      | `cy`          |

## <a name="detect"></a>Detetar

A API do Texto tradutor deteta todas as línguas disponíveis para tradução e transliteração.


## <a name="access-the-translator-text-api-language-list-programmatically"></a>Aceda à lista de línguas DaPI de Texto tradutor programática

Pode obter uma lista de idiomas suportados para o Texto tradutor API v3.0 utilizando o método Idiomas. Pode ver a lista por recurso, código de idioma, bem como o nome de língua em inglês ou qualquer outro idioma suportado. Esta lista é automaticamente atualizada pelo serviço Microsoft Tradutor à medida que novos idiomas são disponibilizados.

[Ver documentação de referência da operação Idiomas](reference/v3-0-languages.md)

## <a name="customization"></a>Personalização

Os seguintes idiomas estão disponíveis para personalização de ou para inglês usando [tradutor personalizado](https://aka.ms/CustomTranslator).

| Idioma    | Código do idioma |
|:----------- |:-------------:|
| Árabe       | `ar`          |
| Bangla      | `bn`          |
| Bósnio (Latim)      | `bs`          |
| Búlgaro      | `bg`          |
| Chinês simplificado      | `zh-Hans`          |
|Chinês tradicional|   `zh-Hant`   |
| Croata      | `hr`          |
| Checo      | `cs`          |
| Dinamarquês      | `da`          |
| Neerlandês      | `nl`          |
| Português    | `en`     |
| Estónio      | `et`          |
| Finlandês      | `fi`          |
| Francês      | `fr`          |
| Alemão      | `de`          |
| Grego      | `el`          |
| Hebraico      | `he`          |
| Hindi      | `hi`          |
| Húngaro      | `hu`          |
| Islandês | `is` |
| Indonésio|   `id`    |
| Irlandês | `ga`  |
| Italiano      | `it`          |
| Japonês      | `ja`          |
| Suaíli|    `sw`    |
| Coreano      | `ko`          |
| Letão      | `lv`          |
| Lituano      | `lt`          |
| Ariary| `mg`    |
| Maori| `mi`  |
| Norueguês      | `nb`          |
| Persa      | `fa`          |
| Polaco      | `pl`          |
| Português (Brasil) | `pt-br` |
| Romeno      | `ro`          |
| Russo      | `ru`          |
| Samoan|   `sm`    |
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

Para uma rápida olhada nos idiomas, o site do Microsoft Tradutor mostra todos os idiomas suportados pelas APIs de Texto e Fala do Tradutor. Esta lista não inclui informações específicas do desenvolvedor, tais como códigos de idiomas.

[Ver a lista de línguas](https://www.microsoft.com/translator/languages.aspx)
