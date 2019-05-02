---
title: Idiomas suportados - API de voz do tradutor
titlesuffix: Azure Cognitive Services
description: Ver os idiomas suportados pela API de voz do Translator.
services: cognitive-services
author: Jann-Skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-speech
ms.topic: conceptual
ms.date: 3/5/2018
ms.author: v-jansko
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 513fd93f148a8a133901a5dbb60722fbc4a7d9e7
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2019
ms.locfileid: "64866256"
---
# <a name="languages-supported-by-the-translator-speech-api"></a>Idiomas suportados pela API de voz do Translator

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-translator-speech-deprecation-note.md)]

Os seguintes idiomas são suportados para tradução de voz. Se ambos os idiomas são suportados para tradução de voz, conversão de voz para a conversão de voz ou de voz em texto estão disponíveis. Se o idioma de destino não é suportado para a tradução de voz, apenas a voz para tradução de texto está disponível.

| Idiomas de voz    |
|:----------- |
| Árabe (Standard moderno)      |
| Português (Brasil)     |
| Chinês (Mandarim)      |
| Português      |
| Francês      |
| Alemão      |
| Italiano      |
| Japonês      |
| Russo      |
| Espanhol      |

A API de voz do Translator suporta os seguintes idiomas como um idioma de destino para a conversão de voz em tradução de texto.

| Idioma de texto    | Código do idioma |
|:----------- |:-------------:|
| Afrikaans      | `af`          |
| Árabe       | `ar`          |
| Bangla      | `bn`          |
| Bósnio (Latim)      | `bs`          |
| Búlgaro      | `bg`          |
| Cantonês (tradicional)      | `yue`          |
| Catalão      | `ca`          |
| Chinês simplificado      | `zh-Hans`          |
| Chinês tradicional      | `zh-Hant`          |
| Croata      | `hr`          |
| Checo      | `cs`          |
| Dinamarquês      | `da`          |
| Neerlandês      | `nl`          |
| Português      | `en`          |
| Estónio      | `et`          |
| Fijian      | `fj`          |
| Filipino      | `fil`          |
| Finlandês      | `fi`          |
| Francês      | `fr`          |
| Alemão      | `de`          |
| Grego      | `el`          |
| Haitian Creole      | `ht`          |
| Hebraico      | `he`          |
| Hindi      | `hi`          |
| Hmong Daw      | `mww`          |
| Húngaro      | `hu`          |
|Islandês|`is`          |
| Indonésio      | `id`          |
| Italiano      | `it`          |
| Japonês      | `ja`          |
| Suaíli      | `sw`          |
| Klingon      | `tlh`          |
| Klingon (plqaD)      | `tlh-Qaak`          |
| Coreano      | `ko`          |
| Letão      | `lv`          |
| Lituano      | `lt`          |
| Ariary      | `mg`          |
| Malaio      | `ms`          |
| Maltês      | `mt`          |
| Norueguês      | `nb`          |
| Persa      | `fa`          |
| Polaco      | `pl`          |
| Português      | `pt`          |
| Queretaro Otomi      | `otq`          |
| Romeno      | `ro`          |
| Russo      | `ru`          |
| Samoan      | `sm`          |
| Sérvio (Cirílico)      | `sr-Cyrl`          |
| Sérvio (Latim)      | `sr-Latn`          |
| Eslovaco     | `sk`          |
| Esloveno      | `sl`          |
| Espanhol      | `es`          |
| Sueco      | `sv`          |
| Tahitian      | `ty`          |
| Tamil      | `ta`          |
| Tailandês      | `th`          |
| Tongan      | `to`          |
| Turco      | `tr`          |
| Ucraniano      | `uk`          |
| Urdu      | `ur`          |
| Vietnamita      | `vi`          |
| Galês      | `cy`          |
| Yucatec Maya      | `yua`          |

## <a name="access-the-list-programmatically"></a>Aceder à lista por meio de programação

Pode acessar a lista de idiomas suportados programaticamente usando o recurso de idiomas. A lista fornece o código de idioma, bem como o nome do idioma em inglês, ou qualquer outro idioma suportado. Esta lista é atualizada automaticamente pelo serviço de voz do Translator à medida que novos idiomas forem disponíveis.

O recurso de idiomas devolve a lista de idiomas suportados para conversão de voz, o texto e o texto em voz. O recurso de idiomas não exija autenticação.

[Visite a referência de API para experimentar o método de idiomas](languages-reference.md)

## <a name="access-the-list-on-the-microsoft-translator-website"></a>Aceder à lista no site do Microsoft Translator

Para examinar os idiomas, o site do Microsoft Translator mostra todos os idiomas suportados pelo texto de tradutor e APIs de voz. Esta lista não inclui informações específicas de desenvolvedor como códigos de idioma.

[Consulte a lista de idiomas](https://www.microsoft.com/translator/languages.aspx)
