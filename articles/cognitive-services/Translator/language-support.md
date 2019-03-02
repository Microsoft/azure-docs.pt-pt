---
title: Suporte de idiomas - API de texto do tradutor
titleSuffix: Azure Cognitive Services
description: Uma lista de idiomas suportados pela API de texto do Translator.
services: cognitive-services
author: Jann-Skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/21/2019
ms.author: v-jansko
ms.openlocfilehash: aed3d08f96265736889deccf51d427a6631e1ea9
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57241582"
---
# <a name="language-and-region-support-for-the-translator-text-api"></a>Suporte de idioma e região para a API de texto do tradutor

A API de texto do Translator suporta os seguintes idiomas para a tradução de texto para texto. Tradução automática neuronal (NMT) é o novo padrão para conversões de máquina de com tecnologia de ia de alta qualidade e está disponível como sendo o padrão usando o V3 da API de texto do Translator quando um sistema neural disponível.

[Saiba mais sobre como funciona a tradução automática](https://www.microsoft.com/translator/mt.aspx)

**V2 Translator API**

> [!NOTE]
> V2 foi preterido no dia 30 de Abril de 2018 e será descontinuada a 30 de Abril de 2019.

* Estatística: Nenhum sistema neural está disponível para este idioma.
* Neurais disponíveis: Um sistema neural está disponível. Utilize o parâmetro `category=generalnn` para acessar o sistema neural.
* Predefinição neural: Neural é o sistema de tradução do padrão. Utilize o parâmetro `category=smt` para aceder ao sistema de estatístico para utilização com o Microsoft Translator Hub.
* Neural apenas: Apenas tradução neural está disponível.

**API do Microsoft Translator v3** a API do Microsoft Translator V3 é neural por predefinição e sistemas estatísticos só estão disponíveis quando nenhum sistema neural existe. Tradutor personalizado só pode ser utilizado com linguagens neurais. [Ver os idiomas atualmente disponíveis no personalizado Translator](#customization).

|Idioma|  Código do idioma|  V2 API| V3 API|
|:-----|:-----:|:-----|:-----|
|Afrikaans| `af`    |Apenas estatística|  Neural|
|Árabe|    `ar`    |Disponível Neural|  Neural|
|Bangla|    `bn`    |Disponível Neural|  Neural|
|Bósnio (Latim)|   `bs`    |Disponível Neural|  Neural|
|Búlgaro| `bg`    |Disponível Neural|  Neural|
|Cantonês (tradicional)|   `yue`   |Apenas estatística|  Estatísticas|
|Catalão|   `ca`    |Apenas estatística|  Estatísticas|
|Chinês Simplificado|    `zh-Hans`   |Predefinição neural |Neural|
|Chinês Tradicional|   `zh-Hant`   |Predefinição neural |Neural|
|Croata|  `hr`    |Disponível Neural|  Neural|
|Checo| `cs`    |Disponível Neural|  Neural|
|Dinamarquês|    `da`    |Disponível Neural   |Neural|
|Neerlandês| `nl`    |Disponível Neural|  Neural|
|Português|   `en`    |Disponível Neural|  Neural|
|Estónio|  `et`    |Disponível Neural|  Neural|
|Fijian|    `fj`    |Apenas estatística|  Estatísticas|
|Filipino|  `fil`   |Apenas estatística|  Estatísticas|
|Finlandês|   `fi`    |Disponível Neural|  Neural|
|Francês|    `fr`    |Disponível Neural|  Neural|
|Alemão|    `de`    |Disponível Neural|  Neural|
|Grego| `el`    |Disponível Neural|  Neural|
|Haitian Creole|    `ht`    |Apenas estatística   |Estatísticas|
|Hebraico |`he`   |Disponível Neural   |Neural|
|Hindi| `hi`    |Predefinição neural|    Neural|
|Hmong Daw| `mww`   |Apenas estatística|  Estatísticas|
|Húngaro| `hu`    |Disponível Neural|  Neural|
|Islandês| `is`    |Apenas Neural|   Neural|
|Indonésio|    `id`    |Apenas estatística|  Estatísticas|
|Italiano|   `it`    |Disponível Neural|  Neural|
|Japonês|  `ja`    |Disponível Neural|  Neural|
|Suaíli| `sw`    |Apenas estatística|  Estatísticas|
|Klingon|   `tlh`   |Apenas estatística|  Estatísticas|
|Klingon (plqaD)|   `tlh-Qaak`  |Apenas estatística|  Estatísticas|
|Coreano |`ko`   |Disponível Neural|  Neural|
|Letão|   `lv`    |Disponível Neural|  Neural|
|Lituano|    `lt`    |Disponível Neural|  Neural|
|Ariary|  `mg`    |Apenas estatística|  Estatísticas|
|Malaio| `ms`    |Apenas estatística   |Estatísticas|
|Maltês|   `mt`    |Apenas estatística|  Estatísticas|
|Norueguês| `nb`    |Disponível Neural|  Neural|
|Persa|   `fa`    |Apenas estatística|  Estatísticas|
|Polaco|    `pl`    |Disponível Neural|  Neural|
|Português|    `pt`    |Disponível Neural|  Neural|
|Queretaro Otomi|   `otq`   |Apenas estatística|  Estatísticas|
|Romeno|  `ro`    |Disponível Neural|  Neural|
|Russo|   `ru`    |Disponível Neural|  Neural|
|Samoan|    `sm`    |Apenas estatística|  Estatísticas|
|Sérvio (Cirílico)|    `sr-Cyrl`   |Apenas estatística|  Estatísticas|
|Sérvio (Latim)|   `sr-Latn`   |Apenas estatística   |Estatísticas|
|Eslovaco|    `sk`    |Disponível Neural|  Neural|
|Esloveno| `sl`    |Disponível Neural|  Neural|
|Espanhol|   `es`    |Disponível Neural|  Neural|
|Sueco|   `sv`    |Disponível Neural   |Neural|
|Tahitian|  `ty`    |Apenas estatística|  Estatísticas|
|Tamil| `ta`    |Apenas estatística|  Estatísticas|
|Télego|    `te`    |Apenas Neural|   Neural|
|Tailandês|  `th`    |Disponível Neural|  Neural|
|Tongan|    `to`    |Apenas estatística|  Estatísticas|
|Turco|   `tr`    |Disponível Neural   |Neural|
|Ucraniano| `uk`    |Disponível Neural|  Neural|
|Urdu|  `ur`    |Apenas estatística|  Estatísticas|
|Vietnamita|    `vi`    |Disponível Neural|  Neural|
|Galês| `cy`    |Disponível Neural|  Neural|
|Yucatec Maya|  `yua`   |Apenas estatística|  Estatísticas|

## <a name="transliteration"></a>Transliteração

O método Transliterate suporta os seguintes idiomas. Na "para/de", "<> –" indica que o idioma pode ser Transliterado de ou para qualquer um dos scripts listados. O "-->" indica que a linguagem pode apenas transliterada a partir de um script para outro.

| Idioma    | Código do idioma | Script | Em | Script|
|:----------- |:-------------:|:-------------:|:-------------:|:-------------:|
| Árabe | `ar` | Árabe `Arab` | <--> | Latim `Latn` |
|Bangla  | `bn` | Bengali `Beng` | <--> | Latim `Latn` |
| Chinês (Simplificado) | `zh-Hans` | Chinês simplificado `Hans`| <--> | Latim `Latn` |
| Chinês (Simplificado) | `zh-Hans` | Chinês simplificado `Hans`| <--> | Chinês tradicional `Hant`|
| Chinês (Tradicional) | `zh-Hant` | Chinês tradicional `Hant`| <--> | Latim `Latn` |
| Chinês (Tradicional) | `zh-Hant` | Chinês tradicional `Hant`| <--> | Chinês simplificado `Hans` |
| Guzarate | `gu`  | Gujarati `Gujr` | --> | Latim `Latn` |
| Hebraico | `he` | Hebraico `Hebr` | <--> | Latim `Latn` |
| Hindi | `hi` | Devanagari `Deva` | <--> | Latim `Latn` |
| Japonês | `ja` | Japonês `Jpan` | <--> | Latim `Latn` |
| Canarim | `kn` | Kannada `Knda` | --> | Latim `Latn` |
| Malayalam | `ml` | Malaiala `Mlym` | --> | Latim `Latn` |
| Marata | `mr` | Devanagari `Deva` | --> | Latim `Latn` |
| Odia | `or` | Oriya `Orya` | <--> | Latim `Latn` |
| Punjabi | `pa` | Gurmukhi `Guru`  | <--> | Latim `Latn`  |
| Sérvio (Cirílico) | `sr-Cyrl` | Cirílico `Cyrl`  | --> | Latim `Latn` |
| Sérvio (Latim) | `sr-Latn` | Latim `Latn` | --> | Cirílico `Cyrl`|
| Tamil | `ta` | Tamil `Taml` | --> | Latim `Latn` |
| Télego | `te` | Télugo `Telu` | --> | Latim `Latn` |
| Tailandês | `th` | Tailandês `Thai` | <--> | Latim `Latn` |

## <a name="dictionary"></a>Dicionário

O dicionário suporta os seguintes idiomas de ou para inglês usando os métodos de pesquisa e exemplos.

| Idioma    | Código do idioma |
|:----------- |:-------------:|
| Afrikaans      | `af`          |
| Árabe       | `ar`          |
| Bangla      | `bn`          |
| Bósnio (Latim)      | `bs`          |
| Búlgaro      | `bg`          |
| Catalão      | `ca`          |
| Chinês Simplificado      | `zh-Hans`          |
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
| Português      | `pt`          |
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

## <a name="detect"></a>Detect

Os seguintes idiomas são compatíveis com o método de deteção. Detetar Maio identificar idiomas que não é possível converter o Microsoft Translator.

| Idioma    |
|:----------- |
| Afrikaans |
| Albanês |
| Árabe |
| Basco (Basco) |
| Bielorrusso |
| Búlgaro |
| Catalão |
| Chinês |
| Chinês (Simplificado) |
| Chinês (Tradicional) |
| Croata |
| Checo |
| Dinamarquês |
| Neerlandês |
| Português |
| Esperanto |
| Estónio |
| Finlandês |
| Francês |
| Galego |
| Alemão |
| Grego |
| Haitian Creole |
| Hebraico |
| Hindi |
| Húngaro |
| Islandês |
| Indonésio |
| Irlandês |
| Italiano |
| Japonês |
| Coreano |
| Curdo (Árabe) |
| Curdo (Latim) |
| Latim |
| Letão |
| Lituano |
| Macedónio |
| Malaio |
| Maltês |
| Norueguês |
| Norueguês (Nynorsk) |
| Pastó |
| Persa |
| Polaco |
| Português |
| Romeno |
| Russo |
| Sérvio (Cirílico) |
| Sérvio (Latim) |
| Eslovaco |
| Esloveno |
| Somali |
| Espanhol |
| kiswahili |
| Sueco |
| Tagalog |
| Télego |
| Tailandês |
| Turco |
| Ucraniano |
| Urdu |
| Usbeque (Cirílico) |
| Usbeque (Latim) |
| Vietnamita |
| Galês |
| Ídiche |

## <a name="access-the-translator-text-api-language-list-programmatically"></a>Aceder à lista de idiomas de API de texto do Translator por meio de programação

Pode obter uma lista de idiomas suportados para o v3.0 de API de texto do Translator usando o método de idiomas. Pode ver a lista por funcionalidade, o código de idioma, bem como o nome do idioma em inglês ou qualquer outro idioma suportado. Esta lista é atualizada automaticamente pelo serviço do Microsoft Translator como novos idiomas estão disponíveis.

[Ver documentação de referência de operação de idiomas](reference/v3-0-languages.md)

## <a name="customization"></a>Personalização

Os seguintes idiomas estão disponíveis para a personalização de ou para utilizar em inglês [Translator personalizado](http://aka.ms/CustomTranslator).

| Idioma    | Código do idioma |
|:----------- |:-------------:|
| Árabe       | `ar`          |
| Bangla      | `bn`          |
| Bósnio (Latim)      | `bs`          |
| Búlgaro      | `bg`          |
| Chinês Simplificado      | `zh-Hans`          |
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
| Italiano      | `it`          |
| Japonês      | `ja`          |
| Coreano      | `ko`          |
| Letão      | `lv`          |
| Lituano      | `lt`          |
| Norueguês      | `nb`          |
| Polaco      | `pl`          |
| Português      | `pt`          |
| Romeno      | `ro`          |
| Russo      | `ru`          |
| Sérvio (Latim)      | `sr-Latn`          |
| Eslovaco     | `sk`          |
| Esloveno      | `sl`          |
| Espanhol      | `es`          |
| Sueco      | `sv`          |
| Tailandês      | `th`          |
| Turco      | `tr`          |
| Ucraniano      | `uk`          |
| Vietnamita      | `vi`          |

## <a name="access-the-list-on-the-microsoft-translator-website"></a>Aceder à lista no site do Microsoft Translator

Para examinar os idiomas, o site do Microsoft Translator mostra todos os idiomas suportados pelo texto de tradutor e APIs de voz. Esta lista não inclui informações específicas de desenvolvedor como códigos de idioma.

[Consulte a lista de idiomas](https://www.microsoft.com/translator/languages.aspx)
