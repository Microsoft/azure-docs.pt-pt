---
title: Suporte linguístico - Serviço de fala
titleSuffix: Azure Cognitive Services
description: O serviço Discurso apoia numerosas línguas para a conversão de texto-a-texto e de texto-a-voz, juntamente com a tradução da fala. Este artigo fornece uma lista completa de suporte linguístico por recurso de serviço.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: trbye
ms.custom: seodec18
ms.openlocfilehash: 437dc18dc16e879e95ff4ec7c1a9ab7ec3f17bef
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/01/2020
ms.locfileid: "84266003"
---
# <a name="language-and-voice-support-for-the-speech-service"></a>Suporte linguístico e de voz para o serviço de fala

O suporte linguístico varia de acordo com a funcionalidade do serviço speech. As tabelas que se seguem resumem o apoio linguístico às ofertas de serviços [de](#speech-to-text) [tradução](#speech-translation) de voz e [de voz.](#text-to-speech)

## <a name="speech-to-text"></a>Conversão de voz em texto

Tanto o Microsoft Speech SDK como o REST API suportam os seguintes idiomas (locais). 

Para melhorar a precisão, a personalização é oferecida para um subconjunto das línguas através do upload **de Transcrições áudio + etiqueta humana** ou texto **relacionado: frases**. Para saber mais sobre personalização, consulte [Começar com Discurso Personalizado.](how-to-custom-speech.md)

Para obter mais informações sobre como melhorar a pronúncia, consulte [Melhorar um modelo para Discurso Personalizado.](how-to-custom-speech-improve-accuracy.md#add-new-words-with-pronunciation)

<!--
To get the AM and ML bits:
https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20models%3A/GetSupportedLocalesForModels

To get pronunciation bits:
https://cris.ai -> Click on Adaptation Data -> scroll down to section "Pronunciation Datasets" -> Click on Import -> Locale: the list of locales there correspond to the supported locales
-->

| Região  | Linguagem                          | Suportado | Personalizações                                    |
|---------|-----------------------------------|-----------|---------------------------------------------------|
| `ar-AE` | Árabe (EAU)                      | Sim       | Não                                                |
| `ar-BH` | Árabe (Bahrein), padrão moderno | Yes       | Modelo de linguagem                                    |
| `ar-EG` | Árabe (Egito)                    | Yes       | Modelo de linguagem                                    |
| `ar-IL` | Árabe (Israel)                   | Sim       | Não                                                |
| `ar-KW` | Árabe (Kuwait)                   | Sim       | Não                                                |
| `ar-PS` | Árabe (Palestina)                | Sim       | Não                                                |
| `ar-QA` | Árabe (Qatar)                    | Sim       | Não                                                |
| `ar-SA` | Árabe (Arábia Saudita)             | Sim       | Não                                                |
| `ar-SY` | Árabe (Síria)                    | Yes       | Modelo de linguagem                                    |
| `ca-ES` | Catalão                           | Yes       | Modelo de linguagem                                    |
| `da-DK` | Dinamarquês (Dinamarca)                  | Yes       | Modelo de linguagem                                    |
| `de-DE` | Alemão (Alemanha)                  | Yes       | Modelo acústico<br>Modelo de linguagem<br>Pronúncia |
| `en-AU` | Inglês (Austrália)               | Yes       | Modelo acústico<br>Modelo de linguagem                  |
| `en-CA` | Inglês (Canadá)                  | Yes       | Modelo acústico<br>Modelo de linguagem                  |
| `en-GB` | Inglês (Reino Unido)          | Yes       | Modelo acústico<br>Modelo de linguagem<br>Pronúncia |
| `en-IN` | Inglês (Índia)                   | Yes       | Modelo acústico<br>Modelo de linguagem                  |
| `en-NZ` | Inglês (Nova Zelândia)             | Yes       | Modelo acústico<br>Modelo de linguagem                  |
| `en-US` | Inglês (Estados Unidos)           | Yes       | Modelo acústico<br>Modelo de linguagem<br>Pronúncia |
| `es-ES` | Espanhol (Espanha)                   | Yes       | Modelo acústico<br>Modelo de linguagem                  |
| `es-MX` | Espanhol (México)                  | Yes       | Modelo acústico<br>Modelo de linguagem                  |
| `fi-FI` | Finlandês (Finlândia)                 | Yes       | Modelo de linguagem                                    |
| `fr-CA` | Francês (Canadá)                   | Yes       | Modelo acústico<br>Modelo de linguagem                  |
| `fr-FR` | Francês (França)                   | Yes       | Modelo acústico<br>Modelo de linguagem<br>Pronúncia |
| `gu-IN` | Gujarati (indiano)                 | Yes       | Modelo de linguagem                                    |
| `hi-IN` | Hindi (Índia)                     | Yes       | Modelo acústico<br>Modelo de linguagem                  |
| `it-IT` | Italiano (Itália)                   | Yes       | Modelo acústico<br>Modelo de linguagem<br>Pronúncia |
| `ja-JP` | Japonês (Japão)                  | Yes       | Modelo de linguagem                                    |
| `ko-KR` | Coreano (Coreia)                    | Yes       | Modelo de linguagem                                    |
| `mr-IN` | Marathi (Índia)                   | Yes       | Modelo de linguagem                                    |
| `nb-NO` | Norueguês (Bokmål) (Noruega)       | Yes       | Modelo de linguagem                                    |
| `nl-NL` | Neerlandês (Países Baixos)               | Yes       | Modelo de linguagem                                    |
| `pl-PL` | Polaco (Polónia)                   | Yes       | Modelo de linguagem                                    |
| `pt-BR` | Português (Brasil)               | Yes       | Modelo acústico<br>Modelo de linguagem<br>Pronúncia |
| `pt-PT` | Português (Portugal)             | Yes       | Modelo de linguagem                                    |
| `ru-RU` | Russo (Rússia)                  | Yes       | Modelo acústico<br>Modelo de linguagem                  |
| `sv-SE` | Sueco (Suécia)                  | Yes       | Modelo de linguagem                                    |
| `ta-IN` | Tamil (Índia)                     | Yes       | Modelo de linguagem                                    |
| `te-IN` | Telugu (Índia)                    | Sim       | Não                                                |
| `th-TH` | Tailandês (Tailândia)                   | Sim       | Não                                                |
| `tr-TR` | Turco (Turquia)                  | Yes       | Modelo de linguagem                                    |
| `zh-CN` | Chinês (mandarim, simplificado)    | Yes       | Modelo acústico<br>Modelo de linguagem                  |
| `zh-HK` | Chinês (cantonês, tradicional)  | Yes       | Modelo de linguagem                                    |
| `zh-TW` | Chinês (Mandarim taiwanês)      | Yes       | Modelo de linguagem                                    |

## <a name="text-to-speech"></a>Conversão de texto em voz

Tanto os Microsoft Speech SDK como os REST APIs suportam estas vozes, cada uma das quais suporta uma linguagem e dialeto específicos, identificados pelo local. Também pode obter uma lista completa de línguas e vozes apoiadas para cada região/ponto final específico através das [vozes/lista API](rest-text-to-speech.md#get-a-list-of-voices). 

> [!IMPORTANT]
> Os preços variam para vozes normais, personalizadas e neurais. Visite a página [de preços](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) para obter informações adicionais.

### <a name="neural-voices"></a>Vozes neurais

O texto-a-discurso neural é um novo tipo de síntese da fala alimentada por redes neuronais profundas. Quando se usa uma voz neural, o discurso sintetizado é quase indistinguível das gravações humanas.

As vozes neurais podem ser usadas para tornar as interações com chatbots e assistentes de voz mais naturais e envolventes, converter textos digitais como e-books em audiolivros e melhorar os sistemas de navegação no carro. Com a prosódia natural humana e a clara articulação das palavras, as vozes neurais reduzem significativamente a fadiga auditiva quando os utilizadores interagem com os sistemas de IA.

Para obter mais informações sobre a disponibilidade regional, consulte [as regiões.](regions.md#standard-and-neural-voices)

|Região  | Linguagem            | Género | Nome de voz | Suporte de estilo |
|--|--|--|--|--|
| `de-DE` | Alemão (Alemanha)                | `Female` | "de-DE-KatjaNeural"      | Geral |
| `en-AU` | Inglês (Austrália)             | `Female` | "en-AU-NatashaNeural"    | Geral |
| `en-CA` | Inglês (Canadá)                | `Female` | "en-CA-ClaraNeural"      | Geral |
| `en-GB` | Inglês (Reino Unido)                    | `Female` | "en-GB-LibbyNeural"      | Geral |
|         |                                 | `Female` | "en-GB-MiaNeural"        | Geral |
| `en-US` | Inglês (E.U.A.)                    | `Female` | "en-US-AriaNeural"       | Geral, vários estilos de voz disponíveis |
|         |                                 | `Male`   | "en-US-GuyNeural"        | Geral |
| `es-ES` | Espanhol (Espanha)                 | `Female` | "es-ES-ElviraNeural"     | Geral |
| `es-MX` | Espanhol (México)                | `Female` | "es-MX-DaliaNeural"      | Geral |
| `fr-CA` | Francês (Canadá)                 | `Female` | "fr-CA-SylvieNeural"     | Geral |
| `fr-FR` | Francês (França)                 | `Female` | "fr-FR-DeniseNeural"     | Geral |
| `it-IT` | Italiano (Itália)                 | `Female` | "IT-ELSANeural"       | Geral |
| `ja-JP` | Japonês                        | `Female` | "ja-JP-NanamiNeural"     | Geral |
| `ko-KR` | Coreano                          | `Female` | "ko-KR-SunHiNeural"      | Geral |
| `nb-NO` | Norueguês                       | `Female` | "nb-NO-IselinNeural"     | Geral |
| `pt-BR` | Português (Brasil)             | `Female` | "pt-BR-FranciscaNeural"  | Geral |
| `tr-TR` | Turco                         | `Female` | "tr-TR-EmelNeural"       | Geral |
| `zh-CN` | Chinês (mandarim, simplificado)  | `Female` | "zh-CN-XiaoxiaoNeural"   | Geral, vários estilos de voz disponíveis |
|         |                                 | `Female` | "zh-CN-XiaoyouNeural"    | Voz infantil, otimizada para narração de história |
|         |                                 | `Male`   | "zh-CN-YunyangNeural"    | Otimizado para leitura de notícias, vários estilos de voz disponíveis |
|         |                                 | `Male`   | "zh-CN-YunyeNeural"      | Otimizado para narração de história |

> [!IMPORTANT]
> A `en-US-JessaNeural` voz mudou `en-US-AriaNeural` para. Se já usaste "Jessa" antes, converte-te em "Aria".

Para aprender como pode configurar e ajustar vozes neurais, consulte a [linguagem de marcação da síntese da fala](speech-synthesis-markup.md#adjust-speaking-styles).

> [!TIP]
> Pode continuar a utilizar o mapeamento de nome de serviço completo como "Microsoft Server Speech Text to Speech Voice (en-US, AriaNeural)" nos seus pedidos de síntese de fala.

### <a name="standard-voices"></a>Vozes padrão

Mais de 75 vozes padrão estão disponíveis em mais de 45 línguas e locais, que permitem converter texto em discurso sintetizado. Para obter mais informações sobre a disponibilidade regional, consulte [as regiões.](regions.md#standard-and-neural-voices)

| Região | Linguagem | Género | Nome de voz |
|--|--|--|--|
| <sup>1</sup>`ar-EG` | Árabe (Egito) | `Female` | "ar-EG-Hoda" |
| `ar-SA` | Árabe (Arábia Saudita) | `Male` | "ar-SA-Naayf" |
| `bg-BG` | Búlgaro | `Male` |  "bg-BG-Ivan" |
| `ca-ES` | Catalão | `Female` |  "ca-ES-HerenaRUS" |
| `cs-CZ` | Checo | `Male` | "cs-CZ-Jakub" |
| `da-DK` | Dinamarquês | `Female` |  "da-DK-HelleRUS" |
| `de-AT` | Alemão (Áustria) | `Male` | "de-AT-Michael" |
| `de-CH` | Alemão (Suíça) | `Male` |  "de-CH-Karsten" |
| `de-DE` | Alemão (Alemanha) | `Female` |  "de-DE-Hedda" |
|  |  | `Female` | "de-DE-HeddaRUS" |
|  |  | `Male` |  "de-DE-Stefan-Apollo" |
| `el-GR` | Grego | `Male` | "el-GR-Stefanos" |
| `en-AU` | Inglês (Austrália) | `Female` |  "en-AU-Catherine" |
|  |  | `Female` |  "en-AU-HayleyRUS" |
| `en-CA` | Inglês (Canadá) | `Female` |  "en-CA-Linda" |
|  |  | `Female` |  "en-CA-HeatherRUS" |
| `en-GB` | Inglês (Reino Unido) | `Female` |  "en-GB-Susan-Apollo" |
|  |  | `Female` |  "en-GB-HazelRUS" |
|  |  | `Male` |  "en-GB-George-Apollo" |
| `en-IE` | Inglês (Irlanda) | `Male` | "en-IE-Sean" |
| `en-IN` | Inglês (Índia) | `Female` | "en-IN-Heera-Apollo" |
|  |  | `Female` |  "en-IN-PriyaRUS" |
|  |  | `Male` |  "en-IN-Ravi-Apollo" |
| `en-US` | Inglês (E.U.A.) | `Female` |  "en-US-ZiraRUS" |
|  |  | `Female` | "en-US-AriaRUS" |
|  |  | `Male` | "en-US-BenjaminRUS" |
|  |  | `Male` |  "en-US-Guy24kRUS" |
| `es-ES` | Espanhol (Espanha) | `Female` |  "es-ES-Laura-Apollo" |
|  |  | `Female` | "es-ES-HelenaRUS" |
|  |  | `Male` | "es-ES-Pablo-Apollo" |
| `es-MX` | Espanhol (México) | `Female` |  "es-MX-HildaRUS" |
|  |  | `Male` | "es-MX-Raul-Apollo" |
| `fi-FI` | Finlandês | `Female` | "fi-FI-HeidiRUS" |
| `fr-CA` | Francês (Canadá) | `Female` | "fr-CA-Caroline" |
|  |  | `Female` | "fr-CA-HarmonieRUS" |
| `fr-CH` | Francês (Suíça) | `Male` | "fr-CH-Guillaume" |
| `fr-FR` | Francês (França) | `Female` |  "fr-FR-Julie-Apollo" |
|  |  | `Female` |"fr-FR-HortenseRUS" |
|  |  | `Male` |  "fr-FR-Paul-Apollo" |
| `he-IL` | Hebraico (Israel) | `Male` |  "he-IL-Asaf" |
| `hi-IN` | Hindi (Índia) | `Female` | "hi-IN-Kalpana-Apollo" |
|  |  | `Female` |  "hi-IN-Kalpana" |
|  |  | `Male` |  "hi-IN-Hemant" |
| `hr-HR` | Croata | `Male` | "hr-HR-Matej" |
| `hu-HU` | Húngaro | `Male` |  "hu-HU-Szabolcs" |
| `id-ID` | Indonésio | `Male` | "ID-ID-Andika" |
| `it-IT` | Italiano | `Male` |  "IT-Cosimo-Apollo" |
|  |  | `Female` |  "IT-IT-LuciaRUS" |
| `ja-JP` | Japonês | `Female` |  "ja-JP-Ayumi-Apollo" |
|  |  | `Male` | "ja-JP-Ichiro-Apollo" |
|  |  | `Female` |  "ja-JP-HarukaRUS" |
| `ko-KR` | Coreano | `Female` | "ko-KR-HeamiRUS" |
| `ms-MY` | Malaio | `Male` |  "Ms-MY-Rizwan" |
| `nb-NO` | Norueguês | `Female` |  "nb-NO-HuldaRUS" |
| `nl-NL` | Neerlandês | `Female` |  "nl-NL-HannaRUS" |
| `pl-PL` | Polaco | `Female` |  "pl-PL-PaulinaRUS" |
| `pt-BR` | Português (Brasil) | `Female` | "pt-BR-HeloisaRUS" |
|  |  | `Male` |  "pt-BR-Daniel-Apollo" |
| `pt-PT` | Português (Portugal) | `Female` | "pt-PT-HeliaRUS" |
| `ro-RO` | Romeno | `Male` | "ro-RO-Andrei" |
| `ru-RU` | Russo | `Female` |  "Ru-RU-Irina-Apollo" |
|  |  | `Male` | "Ru-RU-Pavel-Apollo" |
|  |  | `Female` |  ru-RU-EkaterinaRUS |
| `sk-SK` | Eslovaco | `Male` | "sk-SK-Filip" |
| `sl-SI` | Esloveno | `Male` |  "sl-SI-Lado" |
| `sv-SE` | Sueco | `Female` | "sv-SE-HedvigRUS" |
| `ta-IN` | Tamil (Índia) | `Male` |  "ta-IN-Valluvar" |
| `te-IN` | Telugu (Índia) | `Female` |  "te-IN-Chitra" |
| `th-TH` | Tailandês | `Male` |  "Th-TH-Pattara" |
| `tr-TR` | Turco (Turquia) | `Female` | "tr-TR-SedaRUS" |
| `vi-VN` | Vietnamita | `Male` |  "vi-VN-An" |
| `zh-CN` | Chinês (mandarim, simplificado) | `Female` |  "zh-CN-HuihuiRUS" |
|  |  | `Female` | "zh-CN-Yaoyao-Apollo" |
|  |  | `Male` | "zh-CN-Kangkang-Apollo" |
| `zh-HK` | Chinês (cantonês, tradicional) | `Female` |  "Zh-HK-Tracy-Apollo" |
|  |  | `Female` | "Zh-HK-TracyRUS" |
|  |  | `Male` |  "Zh-HK-Danny-Apollo" |
| `zh-TW` | Chinês (Mandarim taiwanês) | `Female` |  "Zh-TW-Yating-Apollo" |
|  |  | `Female` | "Zh-TW-HanHanRUS" |
|  |  | `Male` |  "Zh-TW-Zhiwei-Apollo" |

**1** *ar-EG suporta árabe moderno padrão (MSA).*

> [!IMPORTANT]
> A `en-US-Jessa` voz mudou `en-US-Aria` para. Se já usaste "Jessa" antes, converte-te em "Aria".

> [!TIP]
> Pode continuar a utilizar o mapeamento de nome de serviço completo como "Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)" nos seus pedidos de síntese de fala.

### <a name="customization"></a>Personalização

A personalização de voz está disponível `de-DE` para, `en-GB` , , , , , , e `en-IN` `en-US` `es-MX` `fr-FR` `it-IT` `pt-BR` `zh-CN` . Selecione o local certo que corresponda aos dados de treino que tem para treinar um modelo de voz personalizado. Por exemplo, se os dados de gravação que tem forem falados em inglês com sotaque britânico, selecione `en-GB` .

> [!NOTE]
> Não apoiamos a formação de modelos bilingues em Custom Voice, exceto o bi-lingual chinês-inglês. Selecione "bilingue chinês-inglês" se quiser treinar uma voz chinesa que também sabe falar inglês. A formação de voz em todos os locais começa com um conjunto de dados de mais de 2.000 expressões, exceto para o `en-US` e onde você pode começar com qualquer tamanho de `zh-CN` dados de treino.

## <a name="speech-translation"></a>Tradução de voz

A API **de Tradução de Discursos** apoia diferentes línguas para a tradução fala-a-fala e de discurso-a-texto. A língua de origem deve ser sempre da mesa de linguagem fala-a-texto. As línguas-alvo disponíveis dependem se o alvo de tradução é a fala ou o texto. Pode traduzir o discurso de entrada em mais de [60 línguas.](https://www.microsoft.com/translator/business/languages/) Um subconjunto de línguas está disponível para [síntese da fala.](language-support.md#text-languages)

### <a name="text-languages"></a>Línguas de texto

| Linguagem de texto           | Código do idioma |
|:------------------------|:-------------:|
| Afrikaans               | `af`          |
| Árabe                  | `ar`          |
| Bangla                  | `bn`          |
| Bósnio (Latim)         | `bs`          |
| Búlgaro               | `bg`          |
| Cantonês (Tradicional) | `yue`         |
| Catalão                 | `ca`          |
| Chinês simplificado      | `zh-Hans`     |
| Chinês tradicional     | `zh-Hant`     |
| Croata                | `hr`          |
| Checo                   | `cs`          |
| Dinamarquês                  | `da`          |
| Neerlandês                   | `nl`          |
| Inglês                 | `en`          |
| Estónio                | `et`          |
| Fijian                  | `fj`          |
| Filipino                | `fil`         |
| Finlandês                 | `fi`          |
| Francês                  | `fr`          |
| Alemão                  | `de`          |
| Grego                   | `el`          |
| Guzerate                | `gu`          |
| Crioulo haitiano          | `ht`          |
| Hebraico                  | `he`          |
| Hindi                   | `hi`          |
| Hmong Daw               | `mww`         |
| Húngaro               | `hu`          |
| Indonésio              | `id`          |
| Irlandês                   | `ga`          |
| Italiano                 | `it`          |
| Japonês                | `ja`          |
| Canarês                 | `kn`          |
| Rio Kiswahili               | `sw`          |
| Klingon                 | `tlh`         |
| Klingon (plqaD)         | `tlh-Qaak`    |
| Coreano                  | `ko`          |
| Letão                 | `lv`          |
| Lituano              | `lt`          |
| Malgaxe                | `mg`          |
| Malaio                   | `ms`          |
| Malaiala               | `ml`          |
| Maltês                 | `mt`          |
| Maori                   | `mi`          |
| Marata                 | `mr`          |
| Norueguês               | `nb`          |
| Persa                 | `fa`          |
| Polaco                  | `pl`          |
| Português (Brasil)     | `pt-br`       |
| Português (Portugal)   | `pt-pt`       |
| Punjabi                 | `pa`          |
| Queretaro Otomi         | `otq`         |
| Romeno                | `ro`          |
| Russo                 | `ru`          |
| Samoa                  | `sm`          |
| Sérvio (Cirílico)      | `sr-Cyrl`     |
| Sérvio (Latim)         | `sr-Latn`     |
| Eslovaco                  | `sk`          |
| Esloveno               | `sl`          |
| Espanhol                 | `es`          |
| Sueco                 | `sv`          |
| Taitiano                | `ty`          |
| Tâmil                   | `ta`          |
| Telugu                  | `te`          |
| Tailandês                    | `th`          |
| Tongan                  | `to`          |
| Turco                 | `tr`          |
| Ucraniano               | `uk`          |
| Urdu                    | `ur`          |
| Vietnamita              | `vi`          |
| Galês                   | `cy`          |
| Maia-de-iucatec            | `yua`         |

## <a name="next-steps"></a>Passos seguintes

* [Obtenha a subscrição do seu teste de serviço de discurso](https://azure.microsoft.com/try/cognitive-services/)
* [Veja como reconhecer a fala em C #](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp)
