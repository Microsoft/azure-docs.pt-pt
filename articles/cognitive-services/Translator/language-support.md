---
title: Suporte ao idioma-API de Tradução de Texto
titleSuffix: Azure Cognitive Services
description: O API de Tradução de Texto dá suporte aos seguintes idiomas para conversão de texto em texto usando a conversão de máquina neural (NMT).
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 5e96ced1bd87a0ed8be19bf4e07f7b0d9b319aad
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73837334"
---
# <a name="language-and-region-support-for-the-translator-text-api"></a>Suporte a idiomas e regiões para o API de Tradução de Texto

O API de Tradução de Texto dá suporte aos seguintes idiomas para tradução de texto para texto. A conversão de máquina neural (NMT) é o novo padrão para traduções de máquina com ia de alta qualidade e está disponível como o padrão usando V3 do API de Tradução de Texto quando um sistema neural está disponível.

[Saiba mais sobre como a tradução automática funciona](https://www.microsoft.com/translator/mt.aspx)

## <a name="translation"></a>Tradução

**API do tradutor v2**

> [!NOTE]
> O v2 foi preterido em 30 de abril de 2018. Migre seus aplicativos para v3 a fim de aproveitar as novas funcionalidades disponíveis exclusivamente na v3.

* Somente estatística: nenhum sistema neural está disponível para este idioma.
* Neural disponível: um sistema neural está disponível. Use o parâmetro `category=generalnn` para acessar o sistema neural.
* Padrão neural: neural é o sistema de tradução padrão. Use o parâmetro `category=smt` para acessar o sistema estatístico para uso com o Hub do Microsoft Translator.
* Somente neural: somente a tradução neural está disponível.

**API do tradutor v3** A API do tradutor v3 é neural por padrão e os sistemas estatísticos estão disponíveis somente quando não existe nenhum sistema neural.

> [!NOTE]
> Atualmente, um subconjunto dos idiomas neurais está disponível no Tradutor personalizado e estamos adicionando gradualmente outros. [Exiba os idiomas disponíveis no momento no Tradutor personalizado](#customization).

|Idioma|  Código do idioma|  API V2| API V3|
|:-----|:-----:|:-----|:-----|
|Africâner| `af`    |Somente estatística|  Neural|
|Árabe|    `ar`    |Neural disponível|  Neural|
|Bengali|    `bn`    |Neural disponível|  Neural|
|Bósnio (latino)|   `bs`    |Neural disponível|  Neural|
|Búlgaro| `bg`    |Neural disponível|  Neural|
|Cantonês (tradicional)|   `yue`   |Somente estatística|  Multiplex|
|Catalão|   `ca`    |Somente estatística|  Multiplex|
|Chinês simplificado|    `zh-Hans`   |Padrão neural |Neural|
|Chinês tradicional|   `zh-Hant`   |Padrão neural |Neural|
|Croata|  `hr`    |Neural disponível|  Neural|
|Tcheco| `cs`    |Neural disponível|  Neural|
|Dinamarquês|    `da`    |Neural disponível   |Neural|
|Holandês| `nl`    |Neural disponível|  Neural|
|Português|   `en`    |Neural disponível|  Neural|
|Estoniano|  `et`    |Neural disponível|  Neural|
|Fijiano|    `fj`    |Somente estatística|  Multiplex|
|Filipinas|  `fil`   |Somente estatística|  Multiplex|
|Finlandês|   `fi`    |Neural disponível|  Neural|
|Francês|    `fr`    |Neural disponível|  Neural|
|Alemão|    `de`    |Neural disponível|  Neural|
|Grego| `el`    |Neural disponível|  Neural|
|Haitiano|    `ht`    |Somente estatística   |Multiplex|
|Hebraico |`he`   |Neural disponível   |Neural|
|Hindi| `hi`    |Padrão neural|    Neural|
|Hmong Daw| `mww`   |Somente estatística|  Multiplex|
|Húngaro| `hu`    |Neural disponível|  Neural|
|Islandês| `is`    |Somente neural|   Neural|
|Indonésio|    `id`    |Somente estatística|  Multiplex|
|Italiano|   `it`    |Neural disponível|  Neural|
|Japonês|  `ja`    |Neural disponível|  Neural|
|Suaíli| `sw`    |Somente estatística|  Multiplex|
|Klingon|   `tlh`   |Somente estatística|  Multiplex|
|Klingon (plqaD)|   `tlh-Qaak`  |Somente estatística|  Multiplex|
|Coreano |`ko`   |Neural disponível|  Neural|
|Letão|   `lv`    |Neural disponível|  Neural|
|Lituano|    `lt`    |Neural disponível|  Neural|
|Malagache|  `mg`    |Somente estatística|  Multiplex|
|Malaio| `ms`    |Somente estatística   |Multiplex|
|Maltês|   `mt`    |Somente estatística|  Multiplex|
|Norueguês| `nb`    |Neural disponível|  Neural|
|Persa|   `fa`    |Neural disponível|  Neural|
|Polaco|    `pl`    |Neural disponível|  Neural|
|Português|    `pt`    |Neural disponível|  Neural|
|Queretaro Otomi|   `otq`   |Somente estatística|  Multiplex|
|Romeno|  `ro`    |Neural disponível|  Neural|
|Russo|   `ru`    |Neural disponível|  Neural|
|Samoan|    `sm`    |Somente estatística|  Multiplex|
|Sérvio (Cirílico)|    `sr-Cyrl`   |Somente estatística|  Multiplex|
|Sérvio (latino)|   `sr-Latn`   |Somente estatística   |Multiplex|
|Eslovaco|    `sk`    |Neural disponível|  Neural|
|Esloveno| `sl`    |Neural disponível|  Neural|
|Espanhol|   `es`    |Neural disponível|  Neural|
|Sueco|   `sv`    |Neural disponível   |Neural|
|Tahitian|  `ty`    |Somente estatística|  Multiplex|
|Tâmil| `ta`    |Somente estatística|  Multiplex|
|Telugu|    `te`    |Somente neural|   Neural|
|Tailandês|  `th`    |Neural disponível|  Neural|
|Tonganês|    `to`    |Somente estatística|  Multiplex|
|Turco|   `tr`    |Neural disponível   |Neural|
|Ucraniano| `uk`    |Neural disponível|  Neural|
|Urdu|  `ur`    |Somente estatística|  Multiplex|
|Vietnamita|    `vi`    |Neural disponível|  Neural|
|Galês| `cy`    |Neural disponível|  Neural|
|Yucatec Maya|  `yua`   |Somente estatística|  Multiplex|

## <a name="transliteration"></a>Transliteração

O método transliterate dá suporte aos seguintes idiomas. No "to/from", "<-->" indica que a linguagem pode ser transliterada de ou para qualquer um dos scripts listados. O "-->" indica que o idioma só pode ser transliterado de um script para o outro.

| Idioma    | Código do idioma | Script | De/para | Script|
|:----------- |:-------------:|:-------------:|:-------------:|:-------------:|
| Árabe | `ar` | `Arab` árabe | <--> | `Latn` latino |
|Bengali  | `bn` | `Beng` Bengali | <--> | `Latn` latino |
| Chinês (Simplificado) | `zh-Hans` | Chinês simplificado `Hans`| <--> | `Latn` latino |
| Chinês (Simplificado) | `zh-Hans` | Chinês simplificado `Hans`| <--> | Chinês tradicional `Hant`|
| Chinês (Tradicional) | `zh-Hant` | Chinês tradicional `Hant`| <--> | `Latn` latino |
| Chinês (Tradicional) | `zh-Hant` | Chinês tradicional `Hant`| <--> | Chinês simplificado `Hans` |
| Guzerate | `gu`  | `Gujr` Guzerate | --> | `Latn` latino |
| Hebraico | `he` | Hebraico `Hebr` | <--> | `Latn` latino |
| Hindi | `hi` | `Deva` de Devanágari | <--> | `Latn` latino |
| Japonês | `ja` | `Jpan` japonesas | <--> | `Latn` latino |
| Kannada | `kn` | `Knda` de Kannada | --> | `Latn` latino |
| Malaiala | `ml` | `Mlym` malaiala | --> | `Latn` latino |
| Marata | `mr` | `Deva` de Devanágari | --> | `Latn` latino |
| Oriá | `or` | `Orya` odia | <--> | `Latn` latino |
| Punjabi | `pa` | `Guru` Gurmukhi  | <--> | `Latn` latino  |
| Sérvio (Cirílico) | `sr-Cyrl` | `Cyrl` cirílica  | --> | `Latn` latino |
| Sérvio (latino) | `sr-Latn` | `Latn` latino | --> | `Cyrl` cirílica|
| Tâmil | `ta` | `Taml` Tamil | --> | `Latn` latino |
| Telugu | `te` | `Telu` de Telugu | --> | `Latn` latino |
| Tailandês | `th` | `Thai` tailandês | <--> | `Latn` latino |

## <a name="dictionary"></a>Dicionário

O dicionário dá suporte aos seguintes idiomas de ou para o inglês usando os métodos Lookup e examples.

| Idioma    | Código do idioma |
|:----------- |:-------------:|
| Africâner      | `af`          |
| Árabe       | `ar`          |
| Bengali      | `bn`          |
| Bósnio (latino)      | `bs`          |
| Búlgaro      | `bg`          |
| Catalão      | `ca`          |
| Chinês simplificado      | `zh-Hans`          |
| Croata      | `hr`          |
| Tcheco      | `cs`          |
| Dinamarquês      | `da`          |
| Holandês      | `nl`          |
| Estoniano      | `et`          |
| Finlandês      | `fi`          |
| Francês      | `fr`          |
| Alemão      | `de`          |
| Grego      | `el`          |
| Haitiano      | `ht`          |
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
| Sérvio (latino)      | `sr-Latn`          |
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

API de Tradução de Texto detecta todos os idiomas disponíveis para tradução e transliteração.


## <a name="access-the-translator-text-api-language-list-programmatically"></a>Acessar a lista de idiomas do API de Tradução de Texto programaticamente

Você pode recuperar uma lista de idiomas com suporte para o API de Tradução de Texto v 3.0 usando o método Languages. Você pode exibir a lista por recurso, código de idioma, bem como o nome do idioma em inglês ou qualquer outro idioma com suporte. Essa lista é atualizada automaticamente pelo serviço do Microsoft Translator conforme novos idiomas são disponibilizados.

[Exibir a documentação de referência da operação de idiomas](reference/v3-0-languages.md)

## <a name="customization"></a>Personalização

Os idiomas a seguir estão disponíveis para personalização de ou para o inglês usando o [Tradutor personalizado](https://aka.ms/CustomTranslator).

| Idioma    | Código do idioma |
|:----------- |:-------------:|
| Árabe       | `ar`          |
| Bengali      | `bn`          |
| Bósnio (latino)      | `bs`          |
| Búlgaro      | `bg`          |
| Chinês simplificado      | `zh-Hans`          |
|Chinês tradicional|   `zh-Hant`   |
| Croata      | `hr`          |
| Tcheco      | `cs`          |
| Dinamarquês      | `da`          |
| Holandês      | `nl`          |
| Português    | `en`     |
| Estoniano      | `et`          |
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
|Suaíli| `sw`    |
| Coreano      | `ko`          |
| Letão      | `lv`          |
| Lituano      | `lt`          |
|Malagache|  `mg`    |
| Norueguês      | `nb`          |
| Persa      | `fa`          |
| Polaco      | `pl`          |
| Português      | `pt`          |
| Romeno      | `ro`          |
| Russo      | `ru`          |
|Samoan|    `sm`    |
| Sérvio (latino)      | `sr-Latn`          |
| Eslovaco     | `sk`          |
| Esloveno      | `sl`          |
| Espanhol      | `es`          |
| Sueco      | `sv`          |
| Tailandês      | `th`          |
| Turco      | `tr`          |
| Ucraniano      | `uk`          |
| Vietnamita      | `vi`          |
| Galês | `cy` |

## <a name="access-the-list-on-the-microsoft-translator-website"></a>Acessar a lista no site do Microsoft Translator

Para uma visão rápida dos idiomas, o site do Microsoft Translator mostra todos os idiomas com suporte nas APIs de Tradução de Texto e fala. Essa lista não inclui informações específicas do desenvolvedor, como códigos de idioma.

[Consulte a lista de idiomas](https://www.microsoft.com/translator/languages.aspx)
