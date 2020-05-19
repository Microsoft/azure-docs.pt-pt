---
title: Apoio linguístico - Serviço de fala
titleSuffix: Azure Cognitive Services
description: O serviço da Fala apoia numerosas línguas para a conversão fala-a-texto e texto-a-fala, juntamente com a tradução da fala. Este artigo fornece uma lista completa de suporte linguístico por recurso de serviço.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: trbye
ms.custom: seodec18
ms.openlocfilehash: 3fe1b999fbdc03157778a1329e05e8c342183528
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83587379"
---
# <a name="language-and-voice-support-for-the-speech-service"></a>Apoio à linguagem e voz para o serviço da Fala

O suporte linguístico varia de acordo com a funcionalidade do serviço da Fala. As tabelas que se sintetizam o apoio linguístico às ofertas de serviços de tradução [de discurso sonâmlo,](#speech-to-text) [texto-a-fala](#text-to-speech)e [de tradução](#speech-translation) da fala.

## <a name="speech-to-text"></a>Conversão de voz em texto

Tanto o Microsoft Speech SDK como o REST API suportam os seguintes idiomas (locais). Para melhorar a precisão, a personalização é oferecida para um subconjunto das línguas através do upload de Audio + Transcrições com rótulo humano ou texto relacionado: Frases. A personalização da pronúncia está atualmente disponível apenas para `en-US` e `de-DE` . Saiba mais sobre personalização [aqui](how-to-custom-speech.md).

<!--
To get the AM and ML bits:
https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20models%3A/GetSupportedLocalesForModels

To get pronunciation bits:
https://cris.ai -> Click on Adaptation Data -> scroll down to section "Pronunciation Datasets" -> Click on Import -> Locale: the list of locales there correspond to the supported locales
-->

| Região  | Linguagem                          | Suportado | Personalizações                                    |
|---------|-----------------------------------|-----------|---------------------------------------------------|
| `ar-AE` | Árabe (EAU)                      | Sim       | Não                                                |
| `ar-BH` | Árabe (Bahrein), padrão moderno | Sim       | Modelo de linguagem                                    |
| `ar-EG` | Árabe (Egito)                    | Sim       | Modelo de linguagem                                    |
| `ar-KW` | Árabe (Kuwait)                   | Sim       | Não                                                |
| `ar-QA` | Árabe (Qatar)                    | Sim       | Não                                                |
| `ar-SA` | Árabe (Arábia Saudita)             | Sim       | Não                                                |
| `ar-SY` | Árabe (Síria)                    | Sim       | Modelo de linguagem                                    |
| `ca-ES` | Catalão                           | Sim       | Modelo de linguagem                                    |
| `da-DK` | Dinamarquês (Dinamarca)                  | Sim       | Modelo de linguagem                                    |
| `de-DE` | Alemão (Alemanha)                  | Sim       | Modelo acústico<br>Modelo de linguagem<br>Pronúncia |
| `en-AU` | Inglês (Austrália)               | Sim       | Modelo acústico<br>Modelo de linguagem                  |
| `en-CA` | Inglês (Canadá)                  | Sim       | Modelo acústico<br>Modelo de linguagem                  |
| `en-GB` | Inglês (Reino Unido)          | Sim       | Modelo acústico<br>Modelo de linguagem<br>Pronúncia |
| `en-IN` | Inglês (Índia)                   | Sim       | Modelo acústico<br>Modelo de linguagem                  |
| `en-NZ` | Inglês (Nova Zelândia)             | Sim       | Modelo acústico<br>Modelo de linguagem                  |
| `en-US` | Inglês (Estados Unidos)           | Sim       | Modelo acústico<br>Modelo de linguagem<br>Pronúncia |
| `es-ES` | Espanhol (Espanha)                   | Sim       | Modelo acústico<br>Modelo de linguagem                  |
| `es-MX` | Espanhol (México)                  | Sim       | Modelo acústico<br>Modelo de linguagem                  |
| `fi-FI` | Finlandês (Finlândia)                 | Sim       | Modelo de linguagem                                    |
| `fr-CA` | Francês (Canadá)                   | Sim       | Modelo acústico<br>Modelo de linguagem                  |
| `fr-FR` | Francês (França)                   | Sim       | Modelo acústico<br>Modelo de linguagem<br>Pronúncia |
| `gu-IN` | Gujarati (Indiano)                 | Sim       | Modelo de linguagem                                    |
| `hi-IN` | Hindi (Índia)                     | Sim       | Modelo acústico<br>Modelo de linguagem                  |
| `it-IT` | Italiano (Itália)                   | Sim       | Modelo acústico<br>Modelo de linguagem<br>Pronúncia |
| `ja-JP` | Japonês (Japão)                  | Sim       | Modelo de linguagem                                    |
| `ko-KR` | Coreano (Coreia)                    | Sim       | Modelo de linguagem                                    |
| `mr-IN` | Marathi (Índia)                   | Sim       | Modelo de linguagem                                    |
| `nb-NO` | Norueguês (Bokmål) (Noruega)       | Sim       | Modelo de linguagem                                    |
| `nl-NL` | Neerlandês (Países Baixos)               | Sim       | Modelo de linguagem                                    |
| `pl-PL` | Polaco (Polónia)                   | Sim       | Modelo de linguagem                                    |
| `pt-BR` | Português (Brasil)               | Sim       | Modelo acústico<br>Modelo de linguagem<br>Pronúncia |
| `pt-PT` | Português (Portugal)             | Sim       | Modelo de linguagem                                    |
| `ru-RU` | Russo (Rússia)                  | Sim       | Modelo acústico<br>Modelo de linguagem                  |
| `sv-SE` | Sueco (Suécia)                  | Sim       | Modelo de linguagem                                    |
| `ta-IN` | Tamil (Índia)                     | Sim       | Modelo de linguagem                                    |
| `te-IN` | Telugu (Índia)                    | Sim       | Não                                                |
| `th-TH` | Tailandês (Tailândia)                   | Sim       | Não                                                |
| `tr-TR` | Turco (Turquia)                  | Sim       | Não                                                |
| `zh-CN` | Chinês (mandarim, simplificado)    | Sim       | Modelo acústico<br>Modelo de linguagem                  |
| `zh-HK` | Chinês (cantonês, tradicional)  | Sim       | Modelo de linguagem                                    |
| `zh-TW` | Chinês (Mandarim taiwanês)      | Sim       | Modelo de linguagem                                    |

## <a name="text-to-speech"></a>Conversão de texto em voz

Tanto o Microsoft Speech SDK como o REST APIs suportam estas vozes, cada uma das quais suporta uma linguagem e dialeto específicos, identificados pelo local. Você também pode obter uma lista completa de línguas e vozes suportadas para cada região/ponto final específico através das [vozes/lista API](rest-text-to-speech.md#get-a-list-of-voices). 

> [!IMPORTANT]
> Os preços variam para vozes padrão, personalizadas e neurais. Visite a página [de Preços](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) para obter informações adicionais.

### <a name="neural-voices"></a>Vozes neurais

Texto-a-fala neural é um novo tipo de síntese da fala alimentada por redes neuronais profundas. Quando se usa uma voz neural, a fala sintetizada é quase indistinguível das gravações humanas.

As vozes neurais podem ser usadas para tornar as interações com chatbots e assistentes de voz mais naturais e envolventes, converter textos digitais como livros eletrónicos em audiolivros e melhorar os sistemas de navegação no carro. Com a prosódia natural humana e a clara articulação das palavras, as vozes neurais reduzem significativamente a fadiga auditiva quando os utilizadores interagem com sistemas de IA.

Para obter mais informações sobre a disponibilidade regional, consulte [regiões.](regions.md#standard-and-neural-voices)

|Região  | Linguagem            | Género | Nome de voz  | Apoio bilingual | Suporte de estilo |
|--|--|--|--|--|--|
| `de-DE` | Alemão (Alemanha)    | Mulheres | "de-DE-KatjaNeural" | Yes. Inglês (E.U.A.) | Geral |
| `en-AU` | Inglês (Austrália) | Mulheres |  "en-AU-NatashaNeural" | Não | Geral |
| `en-CA` | Inglês (Canadá)    | Mulheres |  "en-CA-ClaraNeural"| Não | Geral |
| `en-GB` | Inglês (Reino Unido)        | Mulheres |  "en-GB-LibbyNeural"| Não | Geral |
|  |      | Mulheres |  "en-GB-MiaNeural" | Não | Geral |
| `en-US` | Inglês (E.U.A.)        | Mulheres |  "en-US-AriaNeural"| Não | Estilos de voz gerais e múltiplos disponíveis |
|  |      | Homens   | "en-US-GuyNeural" | Não | Geral |
| `es-ES` | Espanhol (Espanha)     | Mulheres |  "es-ES-ElviraNeural"| Yes. Inglês (E.U.A.) | Geral |
| `es-MX` | Espanhol (México)    | Mulheres |  "es-MX-DaliaNeural" | Yes. Inglês (E.U.A.) | Geral |
| `fr-CA` | Francês (Canadá)     | Mulheres |  "fr-CA-SylvieNeural" | Yes. Inglês (E.U.A.) | Geral |
| `fr-FR` | Francês (França)     | Mulheres | "FR-FR-DeniseNeural"  | Yes. Inglês (E.U.A.) | Geral |
| `it-IT` | Italiano (Itália)     | Mulheres |  "IT-IT-ElsaNeural"  | Yes. Inglês (E.U.A.) | Geral |
| `ja-JP` | Japonês            | Mulheres |  "ja-JP-NanamiNeural" | Yes. Inglês (E.U.A.) | Geral |
| `ko-KR` | Coreano              | Mulheres |  "ko-KR-SunHiNeural" | Yes. Inglês (E.U.A.) | Geral |
| `nb-NO` | Norueguês           | Mulheres | "nb-NO-IselinNeural" | Não | Geral |
| `pt-BR` | Português (Brasil) | Mulheres |  "pt-BR-FranciscaNeural" | Não | Geral |
| `tr-TR` | Turco             | Mulheres | "Tr-TR-EmelNeural" | Não | Geral |
| `zh-CN` | Chinês (mandarim, simplificado)  | Mulheres |  "zh-CN-XiaoxiaoNeural"  | Yes. Inglês (E.U.A.) | Estilos de voz gerais e múltiplos disponíveis |
|  |  | Mulheres |  "zh-CN-XiaoyouNeural"  | Yes. Inglês (E.U.A.) | Voz de criança, otimizada para narração de história |
|  |  | Homens |  "zh-CN-YunyangNeural"  | Yes. Inglês (E.U.A.) | Otimizado para leitura de notícias, vários estilos de voz disponíveis |
|  |  | Homens |  "zh-CN-YunyeNeural"  | Não | Otimizado para narração de histórias |

> [!IMPORTANT]
> A `en-US-JessaNeural` voz mudou `en-US-AriaNeural` para. Se já estavas a usar "Jessa", convertes-te em "Aria".

Para saber como pode configurar e ajustar vozes neurais, consulte a linguagem de marcação da [síntese da fala.](speech-synthesis-markup.md#adjust-speaking-styles)

> [!TIP]
> Pode continuar a usar o mapeamento completo do nome do serviço como "Microsoft Server Speech Text to Speech Voice (en-US, AriaNeural)" nos pedidos de síntese de fala.

### <a name="standard-voices"></a>Vozes padrão

Mais de 75 vozes padrão estão disponíveis em mais de 45 línguas e locais, que permitem converter texto em discurso sintetizado. Para obter mais informações sobre a disponibilidade regional, consulte [regiões.](regions.md#standard-and-neural-voices)

| Região | Linguagem | Género | Nome de voz |
|--|--|--|--|
| <sup>1</sup>`ar-EG` | Árabe (Egito) | Mulheres | "ar-EG-Hoda" |
| `ar-SA` | Árabe (Arábia Saudita) | Homens | "ar-SA-Naayf" |
| `bg-BG` | Búlgaro | Homens |  "bg-BG-Ivan" |
| `ca-ES` | Catalão (Espanha) | Mulheres |  "ca-ES-HerenaRUS" |
| `cs-CZ` | Checo | Homens | "cs-CZ-Jakub" |
| `da-DK` | Dinamarquês | Mulheres |  "da-DK-HelleRUS" |
| `de-AT` | Alemão (Áustria) | Homens | "de-AT-Michael" |
| `de-CH` | Alemão (Suíça) | Homens |  "de-CH-Karsten" |
| `de-DE` | Alemão (Alemanha) | Mulheres |  "de-DE-Hedda" |
|  |  | Mulheres | "de-DE-HeddaRUS" |
|  |  | Homens |  "de-DE-Stefan-Apollo" |
| `el-GR` | Grego | Homens | "el-GR-Stefanos" |
| `en-AU` | Inglês (Austrália) | Mulheres |  "en-AU-Catherine" |
|  |  | Mulheres |  "en-AU-HayleyRUS" |
| `en-CA` | Inglês (Canadá) | Mulheres |  "en-CA-Linda" |
|  |  | Mulheres |  "en-CA-HeatherRUS" |
| `en-GB` | Inglês (Reino Unido) | Mulheres |  "en-GB-Susan-Apollo" |
|  |  | Mulheres |  "en-GB-HazelRUS" |
|  |  | Homens |  "en-GB-George-Apollo" |
| `en-IE` | Inglês (Irlanda) | Homens | "en-IE-Sean" |
| `en-IN` | Inglês (Índia) | Mulheres | "en-IN-Heera-Apollo" |
|  |  | Mulheres |  "en-IN-PriyaRUS" |
|  |  | Homens |  "en-IN-Ravi-Apollo" |
| `en-US` | Inglês (E.U.A.) | Mulheres |  "en-US-ZiraRUS" |
|  |  | Mulheres | "en-US-AriaRUS" |
|  |  | Homens | "en-US-BenjaminRUS" |
|  |  | Homens |  "en-US-Guy24kRUS" |
| `es-ES` | Espanhol (Espanha) | Mulheres |  "es-ES-Laura-Apollo" |
|  |  | Mulheres | "es-ES-HelenaRUS" |
|  |  | Homens | "es-ES-Pablo-Apollo" |
| `es-MX` | Espanhol (México) | Mulheres |  "es-MX-HildaRUS" |
|  |  | Homens | "es-MX-Raul-Apollo" |
| `fi-FI` | Finlandês | Mulheres | "FI-HeidiRUS" |
| `fr-CA` | Francês (Canadá) | Mulheres | "fr-CA-Caroline" |
|  |  | Mulheres | "fr-CA-HarmonieRUS" |
| `fr-CH` | Francês (Suíça) | Homens | "fr-CH-Guillaume" |
| `fr-FR` | Francês (França) | Mulheres |  "FR-FR-Julie-Apollo" |
|  |  | Mulheres |"FR-FR-HortenseRUS" |
|  |  | Homens |  "FR-FR-Paul-Apollo" |
| `he-IL` | Hebraico (Israel) | Homens |  "he-IL-Asaf" |
| `hi-IN` | Hindi (Índia) | Mulheres | "Hi-IN-Kalpana-Apollo" |
|  |  | Mulheres |  "Hi-IN-Kalpana" |
|  |  | Homens |  "Hi-IN-Hemant" |
| `hr-HR` | Croata | Homens | "HR-HR-Matej" |
| `hu-HU` | Húngaro | Homens |  "hu-HU-Szabolcs" |
| `id-ID` | Indonésio | Homens | "ID-ID-Andika" |
| `it-IT` | Italiano | Homens |  "IT-IT-Cosimo-Apollo" |
|  |  | Mulheres |  "IT-IT-LuciaRUS" |
| `ja-JP` | Japonês | Mulheres |  "ja-JP-Ayumi-Apollo" |
|  |  | Homens | "ja-JP-Ichiro-Apollo" |
|  |  | Mulheres |  "ja-JP-HarukaRUS" |
| `ko-KR` | Coreano | Mulheres | "ko-KR-HeamiRUS" |
| `ms-MY` | Malaio | Homens |  "Ms-MY-Rizwan" |
| `nb-NO` | Norueguês | Mulheres |  "nb-NO-HuldaRUS" |
| `nl-NL` | Neerlandês | Mulheres |  "NL-NL-HannaRUS" |
| `pl-PL` | Polaco | Mulheres |  "pl-PL-PaulinaRUS" |
| `pt-BR` | Português (Brasil) | Mulheres | "pt-BR-HeloisaRUS" |
|  |  | Homens |  "pt-BR-Daniel-Apollo" |
| `pt-PT` | Português (Portugal) | Mulheres | "pt-PT-HeliaRUS" |
| `ro-RO` | Romeno | Homens | "ro-RO-Andrei" |
| `ru-RU` | Russo | Mulheres |  "ru-RU-Irina-Apollo" |
|  |  | Homens | "ru-RU-Pavel-Apollo" |
|  |  | Mulheres |  ru-RU-EkaterinaRUS |
| `sk-SK` | Eslovaco | Homens | "Sk-SK-Filip" |
| `sl-SI` | Esloveno | Homens |  "sl-SI-Lado" |
| `sv-SE` | Sueco | Mulheres | "sv-SE-HedvigRUS" |
| `ta-IN` | Tamil (Índia) | Homens |  "ta-IN-Valluvar" |
| `te-IN` | Telugu (Índia) | Mulheres |  "te-IN-Chitra" |
| `th-TH` | Tailandês | Homens |  "TH-TH-Pattara" |
| `tr-TR` | Turco (Turquia) | Mulheres | "Tr-TR-SedaRUS" |
| `vi-VN` | Vietnamita | Homens |  "vi-VN-An" |
| `zh-CN` | Chinês (mandarim, simplificado) | Mulheres |  "zh-CN-HuihuiRUS" |
|  |  | Mulheres | "zh-CN-Yaoyao-Apollo" |
|  |  | Homens | "zh-CN-Kangkang-Apollo" |
| `zh-HK` | Chinês (cantonês, tradicional) | Mulheres |  "zh-HK-Tracy-Apollo" |
|  |  | Mulheres | "zh-HK-TracyRUS" |
|  |  | Homens |  "zh-HK-Danny-Apollo" |
| `zh-TW` | Chinês (Mandarim taiwanês) | Mulheres |  "zh-TW-Yating-Apollo" |
|  |  | Mulheres | "zh-TW-HanHanRUS" |
|  |  | Homens |  "zh-TW-Zhiwei-Apollo" |

**1** *ar-EG suporta o árabe moderno padrão (MSA).*

> [!IMPORTANT]
> A `en-US-Jessa` voz mudou `en-US-Aria` para. Se já estavas a usar "Jessa", convertes-te em "Aria".

> [!TIP]
> Pode continuar a usar o mapeamento completo do nome do serviço como "Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)" nos pedidos de síntese de discurso.

### <a name="customization"></a>Personalização

A personalização por voz está disponível `de-DE` para, `en-GB` , , , , e `en-IN` `en-US` `es-MX` `fr-FR` `it-IT` `pt-BR` `zh-CN` . Selecione o local certo que corresponda aos dados de treino que tem para treinar um modelo de voz personalizado. Por exemplo, se os dados de gravação que tiver tiver forem falados em inglês com sotaque britânico, selecione `en-GB` .

> [!NOTE]
> Não apoiamos o treino de modelos bilingues em Voz Personalizada, exceto para o bilingchinês chinês-inglês. Selecione "bilingue chinês-inglês" se quiser treinar uma voz chinesa que também fale inglês. A formação de voz em todos os locais começa com um conjunto de dados de mais de 2.000 expressões, exceto para o `en-US` e onde você pode começar com qualquer tamanho de dados de `zh-CN` treino.

## <a name="speech-translation"></a>Tradução de voz

A API de **Tradução da Fala** apoia diferentes línguas para a tradução fala-a-fala e de fala-a-texto. A linguagem fonte deve ser sempre da tabela linguística discurso-texto. As línguas-alvo disponíveis dependem se o alvo de tradução é fala ou texto. Pode traduzir o discurso de entrada em mais de [60 línguas.](https://www.microsoft.com/translator/business/languages/) Um subconjunto de línguas está disponível para [síntese da fala.](language-support.md#text-languages)

### <a name="text-languages"></a>Línguas de texto

| Linguagem de texto           | Código do idioma |
|:------------------------|:-------------:|
| Afrikaans               | `af`          |
| Árabe                  | `ar`          |
| Rio Bangla                  | `bn`          |
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
| Rio Klingon                 | `tlh`         |
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
| Samoano                  | `sm`          |
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
| Maia yucateca            | `yua`         |

## <a name="next-steps"></a>Passos seguintes

* [Obtenha a subscrição do teste do serviço Speech](https://azure.microsoft.com/try/cognitive-services/)
* [Veja como reconhecer o discurso em C #](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp)
