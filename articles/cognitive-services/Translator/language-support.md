---
title: Suporte ao idioma-API de Tradução de Texto
titleSuffix: Azure Cognitive Services
description: Uma lista de idiomas naturais com suporte pelo API de Tradução de Texto.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 6ab500fe6a7f288bcfbdc9168ada8bdad5bdb6ea
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/03/2019
ms.locfileid: "68772957"
---
# <a name="language-and-region-support-for-the-translator-text-api"></a>Suporte a idiomas e regiões para o API de Tradução de Texto

O API de Tradução de Texto dá suporte aos seguintes idiomas para tradução de texto para texto. A conversão de máquina neural (NMT) é o novo padrão para traduções de máquina com ia de alta qualidade e está disponível como o padrão usando V3 do API de Tradução de Texto quando um sistema neural está disponível.

[Saiba mais sobre como a tradução automática funciona](https://www.microsoft.com/translator/mt.aspx)

## <a name="translation"></a>Tradução

**API do tradutor v2**

> [!NOTE]
> O v2 foi preterido em 30 de abril de 2018. Migre seus aplicativos para v3 a fim de aproveitar as novas funcionalidades disponíveis exclusivamente na v3.

* Somente estatística: Nenhum sistema neural está disponível para este idioma.
* Neural disponível: Um sistema neural está disponível. Use o parâmetro `category=generalnn` para acessar o sistema neural.
* Padrão neural: Neural é o sistema de tradução padrão. Use o parâmetro `category=smt` para acessar o sistema estatístico para uso com o Hub do Microsoft Translator.
* Somente neural: Somente a tradução neural está disponível.

**API do tradutor v3** A API do tradutor v3 é neural por padrão e os sistemas estatísticos estão disponíveis somente quando não existe nenhum sistema neural.

> [!NOTE]
> Atualmente, um subconjunto dos idiomas neurais está disponível no Tradutor personalizado e estamos adicionando gradualmente outros. [Exiba os idiomas disponíveis no momento no Tradutor personalizado](#customization).

|Idioma|  Código do idioma|  API V2| API V3|
|:-----|:-----:|:-----|:-----|
|Afrikaans| `af`    |Somente estatística|  Neural|
|Árabe|    `ar`    |Neural disponível|  Neural|
|Bangla|    `bn`    |Neural disponível|  Neural|
|Bósnio (Latim)|   `bs`    |Neural disponível|  Neural|
|Búlgaro| `bg`    |Neural disponível|  Neural|
|Cantonês (tradicional)|   `yue`   |Somente estatística|  Multiplex|
|Catalão|   `ca`    |Somente estatística|  Multiplex|
|Chinês simplificado|    `zh-Hans`   |Padrão neural |Neural|
|Chinês tradicional|   `zh-Hant`   |Padrão neural |Neural|
|Croata|  `hr`    |Neural disponível|  Neural|
|Checo| `cs`    |Neural disponível|  Neural|
|Dinamarquês|    `da`    |Neural disponível   |Neural|
|Neerlandês| `nl`    |Neural disponível|  Neural|
|Português|   `en`    |Neural disponível|  Neural|
|Estónio|  `et`    |Neural disponível|  Neural|
|Fijian|    `fj`    |Somente estatística|  Multiplex|
|Filipino|  `fil`   |Somente estatística|  Multiplex|
|Finlandês|   `fi`    |Neural disponível|  Neural|
|Francês|    `fr`    |Neural disponível|  Neural|
|Alemão|    `de`    |Neural disponível|  Neural|
|Grego| `el`    |Neural disponível|  Neural|
|Haitian Creole|    `ht`    |Somente estatística   |Multiplex|
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
|Ariary|  `mg`    |Somente estatística|  Multiplex|
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
|Sérvio (Latim)|   `sr-Latn`   |Somente estatística   |Multiplex|
|Eslovaco|    `sk`    |Neural disponível|  Neural|
|Esloveno| `sl`    |Neural disponível|  Neural|
|Espanhol|   `es`    |Neural disponível|  Neural|
|Sueco|   `sv`    |Neural disponível   |Neural|
|Tahitian|  `ty`    |Somente estatística|  Multiplex|
|Tamil| `ta`    |Somente estatística|  Multiplex|
|Télego|    `te`    |Somente neural|   Neural|
|Tailandês|  `th`    |Neural disponível|  Neural|
|Tongan|    `to`    |Somente estatística|  Multiplex|
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
| Árabe | `ar` | Árabe`Arab` | <--> | Latim`Latn` |
|Bangla  | `bn` | Bengalês`Beng` | <--> | Latim`Latn` |
| Chinês (Simplificado) | `zh-Hans` | Chinês simplificado`Hans`| <--> | Latim`Latn` |
| Chinês (Simplificado) | `zh-Hans` | Chinês simplificado`Hans`| <--> | Chinês tradicional`Hant`|
| Chinês (Tradicional) | `zh-Hant` | Chinês tradicional`Hant`| <--> | Latim`Latn` |
| Chinês (Tradicional) | `zh-Hant` | Chinês tradicional`Hant`| <--> | Chinês simplificado`Hans` |
| Guzarate | `gu`  | Guzerate`Gujr` | --> | Latim`Latn` |
| Hebraico | `he` | Hebraico`Hebr` | <--> | Latim`Latn` |
| Hindi | `hi` | Devanagari `Deva` | <--> | Latim`Latn` |
| Japonês | `ja` | Japonês`Jpan` | <--> | Latim`Latn` |
| Canarim | `kn` | Kannada`Knda` | --> | Latim`Latn` |
| Malayalam | `ml` | Malaiala`Mlym` | --> | Latim`Latn` |
| Marata | `mr` | Devanagari `Deva` | --> | Latim`Latn` |
| Odia | `or` | Oriá`Orya` | <--> | Latim`Latn` |
| Punjabi | `pa` | Gurmukhi `Guru`  | <--> | Latim`Latn`  |
| Sérvio (Cirílico) | `sr-Cyrl` | Cirílico`Cyrl`  | --> | Latim`Latn` |
| Sérvio (Latim) | `sr-Latn` | Latim`Latn` | --> | Cirílico`Cyrl`|
| Tamil | `ta` | Tâmil`Taml` | --> | Latim`Latn` |
| Télego | `te` | Telugu`Telu` | --> | Latim`Latn` |
| Tailandês | `th` | Tailandês`Thai` | <--> | Latim`Latn` |

## <a name="dictionary"></a>Dicionário

O dicionário dá suporte aos seguintes idiomas de ou para o inglês usando os métodos Lookup e examples.

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

API de Tradução de Texto detecta todos os idiomas disponíveis para tradução e transliteração.


## <a name="access-the-translator-text-api-language-list-programmatically"></a>Acessar a lista de idiomas do API de Tradução de Texto programaticamente

Você pode recuperar uma lista de idiomas com suporte para o API de Tradução de Texto v 3.0 usando o método Languages. Você pode exibir a lista por recurso, código de idioma, bem como o nome do idioma em inglês ou qualquer outro idioma com suporte. Essa lista é atualizada automaticamente pelo serviço do Microsoft Translator conforme novos idiomas são disponibilizados.

[Exibir a documentação de referência da operação de idiomas](reference/v3-0-languages.md)

## <a name="customization"></a>Personalização

Os idiomas a seguir estão disponíveis para personalização de ou para o inglês usando o [Tradutor personalizado](https://aka.ms/CustomTranslator).

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
| Italiano      | `it`          |
| Japonês      | `ja`          |
|Suaíli| `sw`    |
| Coreano      | `ko`          |
| Letão      | `lv`          |
| Lituano      | `lt`          |
|Ariary|  `mg`    |
| Norueguês      | `nb`          |
| Polaco      | `pl`          |
| Português      | `pt`          |
| Romeno      | `ro`          |
| Russo      | `ru`          |
|Samoan|    `sm`    |
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

## <a name="access-the-list-on-the-microsoft-translator-website"></a>Acessar a lista no site do Microsoft Translator

Para uma visão rápida dos idiomas, o site do Microsoft Translator mostra todos os idiomas com suporte nas APIs de Tradução de Texto e fala. Essa lista não inclui informações específicas do desenvolvedor, como códigos de idioma.

[Consulte a lista de idiomas](https://www.microsoft.com/translator/languages.aspx)
