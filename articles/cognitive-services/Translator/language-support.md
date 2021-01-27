---
title: Suporte linguístico - Tradutor
titleSuffix: Azure Cognitive Services
description: O Tradutor de Serviços Cognitivos suporta as seguintes línguas para tradução de texto para texto usando tradução automática neural (NMT).
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 06/10/2020
ms.author: lajanuar
ms.openlocfilehash: 935a9e92de88c2519dc1a1042315d204e8f60099
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98919923"
---
# <a name="language-and-region-support-for-text-and-speech-translation"></a>Apoio linguístico e regional para tradução de texto e fala

Utilize o Tradutor para traduzir para e a partir de qualquer uma das mais de 70 línguas de tradução de texto. A Tradução Automática Neural (NMT) é o novo padrão para traduções automáticas alimentadas por IA de alta qualidade e está disponível como o padrão usando V3 de Tradutor quando um sistema neural está disponível.

Também pode utilizar o Tradutor em conjunto com o Custom Tradutor para construir sistemas de tradução neural que compreendam a terminologia utilizada no seu próprio negócio e indústria, e com o Microsoft Speech Service para adicionar tradução de voz à sua app.

[Saiba mais sobre como funciona a tradução automática](https://www.microsoft.com/translator/mt.aspx)

## <a name="text-translation"></a>Tradução de texto
A Tradução de Texto está disponível utilizando a operação Translate para ou a partir de qualquer uma das línguas disponíveis no Tradutor. A API também oferece deteção de linguagem utilizando a operação Detect, transliteração utilizando a operação Transliterada e dicionários bilingues utilizando as operações de Procura do Dicionário e Exemplos de Dicionário. As línguas disponíveis para cada uma destas operações estão listadas abaixo. 

### <a name="translate"></a>Tradução

O tradutor apoia as seguintes línguas para tradução de texto para texto. 

[Ver Traduzir documentação de referência de operação](reference/v3-0-translate.md)

| Linguagem | Código do idioma |
|:-|:-:|
| Afrikaans | `af` |
| Árabe | `ar` |
| Assamese | `as` |
| Bangla | `bn` |
| Bósnio (Latim) | `bs` |
| Búlgaro | `bg` |
| Cantonês (Tradicional) | `yue` |
| Catalão | `ca` |
| Chinês simplificado | `zh-Hans` |
| Chinês tradicional | `zh-Hant` |
| Croata | `hr` |
| Checo | `cs` |
| Rio Dari | `prs` |
| Dinamarquês | `da` |
| Neerlandês | `nl` |
| Inglês | `en` |
| Estónio | `et` |
| Fijian | `fj` |
| Filipino | `fil` |
| Finlandês | `fi` |
| Francês | `fr` |
| Francês (Canadá) | `fr-ca` |
| Alemão | `de` |
| Grego | `el` |
| Guzerate | `gu` |
| Crioulo haitiano | `ht` |
| Hebraico | `he` |
| Hindi | `hi` |
| Hmong Daw | `mww` |
| Húngaro | `hu` |
| Islandês | `is` |
| Indonésio | `id` |
| Inuktitut | `iu` |
| Irlandês | `ga` |
| Italiano | `it` |
| Japonês | `ja` |
| Canarês | `kn` |
| Cazaque | `kk` |
| Klingon | `tlh-Latn` |
| Klingon (plqaD) | `tlh-Piqd` |
| Coreano | `ko` |
| Curdo (Central) | `ku` |
| Curdo (Norte) | `kmr` |
| Letão | `lv` |
| Lituano | `lt` |
| Malgaxe | `mg` |
| Malaio | `ms` |
| Malaiala | `ml` |
| Maltês | `mt` |
| Maori | `mi` |
| Marata | `mr` |
| Norueguês | `nb` |
| Odia | `or` |
| Rio Pashto | `ps` |
| Persa | `fa` |
| Polaco | `pl` |
| Português (Brasil) | `pt` |
| Português (Portugal) | `pt-pt` |
| Punjabi | `pa` |
| Queretaro Otomi | `otq` |
| Romeno | `ro` |
| Russo | `ru` |
| Samoa | `sm` |
| Sérvio (Cirílico) | `sr-Cyrl` |
| Sérvio (Latim) | `sr-Latn` |
| Eslovaco | `sk` |
| Esloveno | `sl` |
| Espanhol | `es` |
| Suaíli | `sw` |
| Sueco | `sv` |
| Taitiano | `ty` |
| Tâmil | `ta` |
| Telugu | `te` |
| Tailandês | `th` |
| Tongan | `to` |
| Turco | `tr` |
| Ucraniano | `uk` |
| Urdu | `ur` |
| Vietnamita | `vi` |
| Galês | `cy` |
| Maia-de-iucatec | `yua` |

> [!NOTE]
> O código `pt` linguístico será desafins a `pt-br` português (Brasil).

### <a name="detect"></a>Detetar

O tradutor deteta as seguintes línguas para tradução e transliteração.

[Ver Detetar documentação de referência de operação](reference/v3-0-detect.md)

| Linguagem | Código do idioma |
|:-|:-:|
| Afrikaans | `af` |
| Árabe | `ar` |
| Búlgaro | `bg` |
| Catalão | `ca` |
| Chinês simplificado | `zh-Hans` |
| Chinês tradicional | `zh-Hant` |
| Croata | `hr` |
| Checo | `cs` |
| Dinamarquês | `da` |
| Neerlandês | `nl` |
| Inglês | `en` |
| Estónio | `et` |
| Finlandês | `fi` |
| Francês | `fr` |
| Alemão | `de` |
| Grego | `el` |
| Guzerate | `gu` |
| Crioulo haitiano | `ht` |
| Hebraico | `he` |
| Hindi | `hi` |
| Húngaro | `hu` |
| Islandês | `is` |
| Indonésio | `id` |
| Irlandês | `ga` |
| Italiano | `it` |
| Japonês | `ja` |
| Klingon | `tlh-Latn` |
| Coreano | `ko` |
| Curdo (Central) | `ku-Arab` |
| Letão | `lv` |
| Lituano | `lt` |
| Malaio | `ms` |
| Maltês | `mt` |
| Norueguês | `nb` |
| Rio Pashto | `ps` |
| Persa | `fa` |
| Polaco | `pl` |
| Português | `pt` |
| Romeno | `ro` |
| Russo | `ru` |
| Sérvio (Cirílico) | `sr-Cyrl` |
| Sérvio (Latim) | `sr-Latn` |
| Eslovaco | `sk` |
| Esloveno | `sl` |
| Espanhol | `es` |
| Suaíli | `sw` |
| Sueco | `sv` |
| Taitiano | `ty` |
| Tailandês | `th` |
| Turco | `tr` |
| Ucraniano | `uk` |
| Urdu | `ur` |
| Vietnamita | `vi` |
| Galês | `cy` |
| Maia-de-iucatec | `yua` |

### <a name="transliterate"></a>Transliterar

O método transliterado suporta as seguintes línguas. No "De/Para", "<->" indica que a língua pode ser transliterada de ou para qualquer um dos scripts listados. O "->" indica que a linguagem só pode ser transliterada de um script para o outro.

[Ver documentação de referência de operação transliterada](reference/v3-0-translate.md)


| Linguagem    | Código do idioma | Script | Para/De | Script|
|:----------- |:-------------:|:-------------:|:-------------:|:-------------:|
| Árabe | `ar` | Árabe `Arab` | <--> | Latim `Latn` |
| Assamese | `as` | Bengalês `Beng` | <--> | Latim `Latn` |
| Bangla  | `bn` | Bengalês `Beng` | <--> | Latim `Latn` |
|Bielorrusso| `be` | Cirílico `Cyrl`  | <--> | Latim `Latn` |
|Búlgaro| `bg` | Cirílico `Cyrl`  | <--> | Latim `Latn` |
| Chinês (Simplificado) | `zh-Hans` | Chinês simplificado `Hans`| <--> | Latim `Latn` |
| Chinês (Simplificado) | `zh-Hans` | Chinês simplificado `Hans`| <--> | Chinês tradicional `Hant`|
| Chinês (Tradicional) | `zh-Hant` | Chinês tradicional `Hant`| <--> | Latim `Latn` |
| Chinês (Tradicional) | `zh-Hant` | Chinês tradicional `Hant`| <--> | Chinês simplificado `Hans` |
|Grego| `el` | Grego `Grek`  | <--> | Latim `Latn` |
| Guzerate | `gu`  | Guzerate `Gujr` | <--> | Latim `Latn` |
| Hebraico | `he` | Hebraico `Hebr` | <--> | Latim `Latn` |
| Hindi | `hi` | Devanagari `Deva` | <--> | Latim `Latn` |
| Japonês | `ja` | Japonês `Jpan` | <--> | Latim `Latn` |
| Canarês | `kn` | Canarês `Knda` | <--> | Latim `Latn` |
|Cazaque| `kk` | Cirílico `Cyrl`  | <--> | Latim `Latn` |
|Coreano| `ko` | Coreano `Kore`  | <--> | Latim `Latn` |
|Quirguiz| `ky` | Cirílico `Cyrl`  | <--> | Latim `Latn` |
|Macedónio| `mk` | Cirílico `Cyrl`  | <--> | Latim `Latn` |
| Malaiala | `ml` | Malaiala `Mlym` | <--> | Latim `Latn` |
| Marata | `mr` | Devanagari `Deva` | <--> | Latim `Latn` |
|Mongol| `mn` | Cirílico `Cyrl`  | <--> | Latim `Latn` |
| Odia | `or` | Oriya `Orya` | <--> | Latim `Latn` |
|Persa| `fa` | Árabe `Arab`  | <--> | Latim `Latn` |
| Punjabi | `pa` | Rio Gurmukhi `Guru`  | <--> | Latim `Latn`  |
|Russo| `ru` | Cirílico `Cyrl`  | <--> | Latim `Latn` |
| Sérvio (Cirílico) | `sr-Cyrl` | Cirílico `Cyrl`  | --> | Latim `Latn` |
| Sérvio (Latim) | `sr-Latn` | Latim `Latn` | --> | Cirílico `Cyrl`|
|Sindhi| `sd` | Árabe `Arab`  | <--> | Latim `Latn` |
|Sinhala| `si` | Sinhala `Sinh`  | <--> | Latim `Latn` |
|Rio Tajik| `tg` | Cirílico `Cyrl`  | <--> | Latim `Latn` |
| Tâmil | `ta` | Tâmil `Taml` | <--> | Latim `Latn` |
|Tártaro| `tt` | Cirílico `Cyrl`  | <--> | Latim `Latn` |
| Telugu | `te` | Telugu `Telu` | <--> | Latim `Latn` |
| Tailandês | `th` | Tailandês `Thai` | --> | Latim `Latn` |
|Ucraniano| `uk` | Cirílico `Cyrl`  | <--> | Latim `Latn` |
|Urdu| `ur` | Árabe `Arab`  | <--> | Latim `Latn` |

### <a name="dictionary"></a>Dicionário

O dicionário suporta as seguintes línguas de ou para o inglês usando os métodos Lookup e Examples.

Ver documentação de referência para as operações [de Procura](reference/v3-0-dictionary-lookup.md) do Dicionário e [Exemplos de Dicionário.](reference/v3-0-dictionary-examples.md)

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
| Klingon      | `tlh`          |
| Coreano      | `ko`          |
| Letão      | `lv`          |
| Lituano      | `lt`          |
| Malaio      | `ms`          |
| Maltês      | `mt`          |
| Norueguês      | `nb`          |
| Persa      | `fa`          |
| Polaco      | `pl`          |
| Português (Brasil)     | `pt`          |
| Romeno      | `ro`          |
| Russo      | `ru`          |
| Sérvio (Latim)      | `sr-Latn`          |
| Eslovaco     | `sk`          |
| Esloveno      | `sl`          |
| Espanhol      | `es`          |
| Suaíli      | `sw`          |
| Sueco      | `sv`          |
| Tâmil      | `ta`          |
| Tailandês      | `th`          |
| Turco      | `tr`          |
| Ucraniano      | `uk`          |
| Urdu      | `ur`          |
| Vietnamita      | `vi`          |
| Galês      | `cy`          |

### <a name="access-the-translator-language-list-programmatically"></a>Aceda à lista de línguas tradutora programáticamente

Pode obter uma lista de línguas apoiadas para Tradutor utilizando o método idiomas. Pode ver a lista por recurso, código de idioma, bem como o nome da língua em inglês ou qualquer outro idioma suportado. Esta lista é atualizada automaticamente pelo serviço Microsoft Tradutor, uma vez que são disponibilizados novos idiomas.

[Ver documentação de referência de operação de línguas](reference/v3-0-languages.md)

## <a name="customization"></a>Personalização

Os seguintes idiomas estão disponíveis para personalização de ou para inglês usando [o Tradutor Personalizado.](https://aka.ms/CustomTranslator)

| Linguagem    | Código do idioma |
|:----------- |:-------------:|
|Afrikaans| `af`|
| Árabe       | `ar`          |
| Bangla      | `bn`          |
| Bósnio (Latim)      | `bs`          |
| Búlgaro      | `bg`          |
|Catalão|   `ca`    |
| Chinês simplificado      | `zh-Hans`          |
|Chinês tradicional|   `zh-Hant`   |
| Croata      | `hr`          |
| Checo      | `cs`          |
| Dinamarquês      | `da`          |
| Neerlandês      | `nl`          |
| Inglês    | `en`     |
| Estónio      | `et`          |
|Fijian|    `fj`    |
|Filipino|  `fil`   |
| Finlandês      | `fi`          |
| Francês      | `fr`          |
| Alemão      | `de`          |
| Grego      | `el`          |
| Guzerate| `gu`    |
| Hebraico      | `he`          |
| Hindi      | `hi`          |
| Húngaro      | `hu`          |
| Islandês | `is` |
| Indonésio|   `id`    |
| Irlandês | `ga`  |
| Italiano      | `it`          |
| Japonês      | `ja`          |
|Canarês|`kn`|
| Coreano      | `ko`          |
| Letão      | `lv`          |
| Lituano      | `lt`          |
| Malgaxe| `mg`    |
| Malaio|    `ms` |
|Maltês|   `mt`    |
| Maori| `mi`  |
| Marata| `mr`  |
| Norueguês      | `nb`          |
| Persa      | `fa`          |
| Polaco      | `pl`          |
| Português (Brasil) | `pt` |
| Punjabi|`pa`|
| Romeno      | `ro`          |
| Russo      | `ru`          |
| Samoa|   `sm`    |
| Sérvio (Latim)      | `sr-Latn`          |
| Eslovaco     | `sk`          |
| Esloveno      | `sl`          |
| Espanhol      | `es`          |
| Suaíli|  `sw`    |
| Sueco      | `sv`          |
|Taitiano|  `ty`    |
| Tailandês      | `th`          |
|Tongan|    `to`    |
| Turco      | `tr`          |
| Ucraniano      | `uk`          |
| Urdu| `ur`    |
| Vietnamita      | `vi`          |
| Galês | `cy` |

## <a name="speech-translation"></a>Tradução de Voz
A tradução da fala está disponível utilizando o serviço de Discurso de Tradutor com Serviços Cognitivos. Consulte [a documentação do Serviço de Discurso](../speech-service/index.yml) para saber mais sobre a utilização da tradução da fala e para ver todas as [opções linguísticas disponíveis.](../speech-service/language-support.md)

### <a name="speech-to-text"></a>Conversão de voz em texto
Converta a fala em texto para traduzir para a linguagem de texto à sua escolha. A palavra-a-texto é usada para a tradução de texto ou para tradução fala-a-fala quando usada em conjunto com a síntese da fala.

| Linguagem    |
|:----------- |
|Árabe|
|Cantonês (Tradicional)|
|Catalão|
|Chinês simplificado|
|Chinês tradicional|
|Dinamarquês|
|Neerlandês|
|Inglês|
|Finlandês|
|Francês|
|Francês (Canadá)|
|Alemão|
|Guzerate|
|Hindi|
|Italiano|
|Japonês|
|Coreano|
|Marata|
|Norueguês|
|Polaco|
|Português (Brasil)|
|Português (Portugal)|
|Russo|
|Espanhol|
|Sueco|
|Tâmil|
|Telugu|
|Tailandês|
|Turco|

### <a name="text-to-speech"></a>Conversão de texto em voz
Converter texto em discurso. O texto-a-discurso é usado para adicionar produção audível dos resultados de tradução, ou para tradução fala-a-fala quando usado com discurso-a-texto. 

| Linguagem |
|:-|
| Árabe |
| Búlgaro |
| Cantonês (Tradicional) |
| Catalão |
| Chinês simplificado |
| Chinês tradicional |
| Croata |
| Checo |
| Dinamarquês |
| Neerlandês |
| Inglês |
| Finlandês |
| Francês |
| Francês (Canadá) |
| Alemão |
| Grego |
| Hebraico |
| Hindi |
| Húngaro |
| Indonésio |
| Italiano |
| Japonês |
| Coreano |
| Malaio |
| Norueguês |
| Polaco |
| Português (Brasil) |
| Português (Portugal) |
| Romeno |
| Russo |
| Eslovaco |
| Esloveno |
| Espanhol |
| Sueco |
| Tâmil |
| Telugu |
| Tailandês |
| Turco |
| Vietnamita |

## <a name="view-the-language-list-on-the-microsoft-translator-website"></a>Ver a lista de idiomas no site do Microsoft Tradutor

Para uma rápida olhada nas línguas, o site microsoft Tradutor mostra todas as línguas suportadas pelo Tradutor para tradução de texto e serviço de fala para tradução de fala. Esta lista não inclui informações específicas do programador, tais como códigos linguísticos.

[Ver a lista de línguas](https://www.microsoft.com/translator/languages.aspx)
