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
ms.openlocfilehash: 7d5dd7d2d0aa3d10f439b5cf3604e2d595456f86
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92172265"
---
# <a name="language-and-voice-support-for-the-speech-service"></a>Suporte linguístico e de voz para o serviço de fala

O suporte linguístico varia de acordo com a funcionalidade do serviço speech. As tabelas que se seguem resumem o apoio linguístico às ofertas de serviços [de](#speech-to-text) [tradução](#speech-translation) de voz e [de voz.](#text-to-speech)

## <a name="speech-to-text"></a>Conversão de voz em texto

Tanto o Microsoft Speech SDK como o REST API suportam os seguintes idiomas (locais). 

Para melhorar a precisão, a personalização é oferecida para um subconjunto das línguas através do upload **de Transcrições áudio + etiqueta humana** ou texto **relacionado: frases**. Para saber mais sobre personalização, consulte [Começar com Discurso Personalizado.](how-to-custom-speech.md)

<!--
To get the AM and ML bits:
https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetSupportedLocalesForModels

To get pronunciation bits:
https://cris.ai -> Click on Adaptation Data -> scroll down to section "Pronunciation Datasets" -> Click on Import -> Locale: the list of locales there correspond to the supported locales
-->

| Região  | Linguagem                          | Personalizações                                    |
|---------|-----------------------------------|---------------------------------------------------|
| `ar-AE` | Árabe (Emirados Árabes Unidos)     | Modelo de linguagem                                    |
| `ar-BH` | Árabe (Bahrein), padrão moderno | Modelo de linguagem                                    |
| `ar-EG` | Árabe (Egito)                    | Modelo de linguagem                                    |
| `ar-IQ` | Árabe (Iraque)                     | Modelo de linguagem                                    |
| `ar-JO` | Árabe (Jordânia)                   | Modelo de linguagem                                    |
| `ar-KW` | Árabe (Kuwait)                   | Modelo de linguagem                                    |
| `ar-LB` | Árabe (Líbano)                  | Modelo de linguagem                                    |
| `ar-OM` | Árabe (Omã)                     | Modelo de linguagem                                    |
| `ar-QA` | Árabe (Qatar)                    | Modelo de linguagem                                    |
| `ar-SA` | Árabe (Arábia Saudita)             | Modelo de linguagem                                    |
| `ar-SY` | Árabe (Síria)                    | Modelo de linguagem                                    |
| `bg-BG` | Búlgaro (Bulgária)              | Modelo de linguagem                                    |
| `ca-ES` | Catalão (Espanha)                   | Modelo de linguagem                                    |
| `cs-CZ` | Checo (República Checa)            | Modelo de linguagem                                    | 
| `da-DK` | Dinamarquês (Dinamarca)                  | Modelo de linguagem                                    |
| `de-DE` | Alemão (Alemanha)                  | Modelo acústico<br>Modelo de linguagem<br>Pronúncia |
| `el-GR` | Grego (Grécia)                    | Modelo de linguagem                                    |
| `en-AU` | Inglês (Austrália)               | Modelo acústico<br>Modelo de linguagem                  |
| `en-CA` | Inglês (Canadá)                  | Modelo acústico<br>Modelo de linguagem                  |
| `en-GB` | Inglês (Reino Unido)          | Modelo acústico<br>Modelo de linguagem<br>Pronúncia |
| `en-HK` | Inglês (Hong Kong)               | Modelo de linguagem                                    | 
| `en-IE` | Inglês (Irlanda)                 | Modelo de linguagem                                    | 
| `en-IN` | Inglês (Índia)                   | Modelo acústico<br>Modelo de linguagem                  |
| `en-NZ` | Inglês (Nova Zelândia)             | Modelo acústico<br>Modelo de linguagem                  |
| `en-PH` | Inglês (Filipinas)             | Modelo de linguagem                                    | 
| `en-SG` | Inglês (Singapura)               | Modelo de linguagem                                    | 
| `en-US` | Inglês (Estados Unidos)           | Modelo acústico<br>Modelo de linguagem<br>Pronúncia |
| `en-ZA` | Inglês (África do Sul)            | Modelo de linguagem                                    | 
| `es-AR` | Espanhol (Argentina)               | Modelo de linguagem                                    | 
| `es-BO` | Espanhol (Bolívia)                 | Modelo de linguagem                                    | 
| `es-CL` | Espanhol (Chile)                   | Modelo de linguagem                                    | 
| `es-CO` | Espanhol (Colômbia)                | Modelo de linguagem                                    | 
| `es-CR` | Espanhol (Costa Rica)              | Modelo de linguagem                                    | 
| `es-CU` | Espanhol (Cuba)                    | Modelo de linguagem                                    | 
| `es-DO` | Espanhol (República Dominicana)      | Modelo de linguagem                                    | 
| `es-EC` | Espanhol (Equador)                 | Modelo de linguagem                                    | 
| `es-ES` | Espanhol (Espanha)                   | Modelo acústico<br>Modelo de linguagem                  |
| `es-GT` | Espanhol (Guatemala)               | Modelo de linguagem                                    | 
| `es-HN` | Espanhol (Honduras)                | Modelo de linguagem                                    | 
| `es-MX` | Espanhol (México)                  | Modelo acústico<br>Modelo de linguagem                  |
| `es-NI` | Espanhol (Nicarágua)               | Modelo de linguagem                                    | 
| `es-PA` | Espanhol (Panamá)                  | Modelo de linguagem                                    | 
| `es-PE` | Espanhol (Peru)                    | Modelo de linguagem                                    | 
| `es-PR` | Espanhol (Porto Rico)             | Modelo de linguagem                                    | 
| `es-PY` | Espanhol (Paraguai)                | Modelo de linguagem                                    | 
| `es-SV` | Espanhol (El Salvador)             | Modelo de linguagem                                    | 
| `es-US` | Espanhol (EUA)                     | Modelo de linguagem                                    | 
| `es-UY` | Espanhol (Uruguai)                 | Modelo de linguagem                                    | 
| `es-VE` | Espanhol (Venezuela)               | Modelo de linguagem                                    |
| `et-EE` | Estónia (Estónia)                 | Modelo de linguagem                                    | 
| `fi-FI` | Finlandês (Finlândia)                 | Modelo de linguagem                                    |
| `fr-CA` | Francês (Canadá)                   | Modelo acústico<br>Modelo de linguagem                  |
| `fr-FR` | Francês (França)                   | Modelo acústico<br>Modelo de linguagem<br>Pronúncia |
| `ga-IE` | Irlanda (Irlanda)                    | Modelo de linguagem                                    |
| `gu-IN` | Gujarati (indiano)                 | Modelo de linguagem                                    |
| `hi-IN` | Hindi (Índia)                     | Modelo acústico<br>Modelo de linguagem                  |
| `hr-HR` | Croata (Croácia)                | Modelo de linguagem                                    |
| `hu-HU` | Húngaro (Hungria)               | Modelo de linguagem                                    | 
| `it-IT` | Italiano (Itália)                   | Modelo acústico<br>Modelo de linguagem<br>Pronúncia |
| `ja-JP` | Japonês (Japão)                  | Modelo de linguagem                                    |
| `ko-KR` | Coreano (Coreia)                    | Modelo de linguagem                                    |
| `lt-LT` | Lituano (Lituânia)            | Modelo de linguagem                                    |
| `lv-LV` | Letão (Letónia)                  | Modelo de linguagem                                    |
| `mr-IN` | Marathi (Índia)                   | Modelo de linguagem                                    |
| `mt-MT` | Maltês(Malta)                    | Modelo de linguagem                                    |
| `nb-NO` | Norueguês (Bokmål) (Noruega)       | Modelo de linguagem                                    |
| `nl-NL` | Neerlandês (Países Baixos)               | Modelo de linguagem                                    |
| `pl-PL` | Polaco (Polónia)                   | Modelo de linguagem                                    |
| `pt-BR` | Português (Brasil)               | Modelo acústico<br>Modelo de linguagem<br>Pronúncia |
| `pt-PT` | Português (Portugal)             | Modelo de linguagem                                    |
| `ro-RO` | Romeno (Roménia)                | Modelo de linguagem                                    |
| `ru-RU` | Russo (Rússia)                  | Modelo acústico<br>Modelo de linguagem                  |
| `sk-SK` | Eslovaco (Eslováquia)                 | Modelo de linguagem                                    |
| `sl-SI` | Esloveno (Eslovénia)              | Modelo de linguagem                                    |
| `sv-SE` | Sueco (Suécia)                  | Modelo de linguagem                                    |
| `ta-IN` | Tamil (Índia)                     | Modelo de linguagem                                    |
| `te-IN` | Telugu (Índia)                    | Modelo de linguagem                                    |
| `th-TH` | Tailandês (Tailândia)                   | Modelo de linguagem                                    |
| `tr-TR` | Turco (Turquia)                  | Modelo de linguagem                                    |
| `zh-CN` | Chinês (Mandarim, Simplificado)    | Modelo acústico<br>Modelo de linguagem                  |
| `zh-HK` | Chinês (cantonês, tradicional)  | Modelo de linguagem                                    |
| `zh-TW` | Chinês (Mandarim taiwanês)      | Modelo de linguagem                                    |

## <a name="text-to-speech"></a>Conversão de texto em voz

Tanto os Microsoft Speech SDK como os REST APIs suportam estas vozes, cada uma das quais suporta uma linguagem e dialeto específicos, identificados pelo local. Também pode obter uma lista completa de línguas e vozes apoiadas para cada região/ponto final específico através das [vozes/lista API](rest-text-to-speech.md#get-a-list-of-voices). 

> [!IMPORTANT]
> Os preços variam para vozes normais, personalizadas e neurais. Visite a página [de preços](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) para obter informações adicionais.

### <a name="neural-voices"></a>Vozes neurais

O texto-a-discurso neural é um novo tipo de síntese da fala alimentada por redes neuronais profundas. Quando se usa uma voz neural, o discurso sintetizado é quase indistinguível das gravações humanas.

As vozes neurais podem ser usadas para tornar as interações com chatbots e assistentes de voz mais naturais e envolventes, converter textos digitais como e-books em audiolivros e melhorar os sistemas de navegação no carro. Com a prosódia natural humana e a clara articulação das palavras, as vozes neurais reduzem significativamente a fadiga auditiva quando os utilizadores interagem com os sistemas de IA.

Para obter mais informações sobre a disponibilidade regional, consulte [as regiões.](regions.md#standard-and-neural-voices)

|Região  | Linguagem            | Sexo | Nome de voz | Suporte de estilo |
|--|--|--|--|--|
| `ar-EG` | Árabe (Egito) | Mulheres | `ar-EG-SalmaNeural` | Geral |
| `ar-SA` | Árabe (Arábia Saudita) | Mulheres | `ar-SA-ZariyahNeural` | Geral |
| `bg-BG` <sup>Novo</sup> | Búlgaro (Bulgary) | Mulheres | `bg-BG-KalinaNeural` | Geral |
| `ca-ES` | Catalão (Espanha) | Mulheres | `ca-ES-AlbaNeural` | Geral |
| `cs-CZ` <sup>Novo</sup> | Checo (checo)  | Mulheres | `cs-CZ-VlastaNeural` | Geral |
| `da-DK` | Dinamarquês (Dinamarca) | Mulheres | `da-DK-ChristelNeural` | Geral |
| `de-AT` <sup>Novo</sup> | Alemão (Áustria) | Mulheres | `de-AT-IngridNeural` | Geral |
| `de-CH` <sup>Novo</sup> | Alemão (Suíça) | Mulheres | `de-CH-LeniNeural` | Geral |
| `de-DE` | Alemão (Alemanha) | Mulheres | `de-DE-KatjaNeural` | Geral |
| `de-DE` <sup>Novo</sup> | Alemão (Alemanha) | Homens | `de-DE-ConradNeural` | Geral |
| `el-GR` <sup>Novo</sup> | Grego (Grécia) | Mulheres | `el-GR-AthinaNeural` | Geral |
| `en-AU` | Inglês (Austrália) | Mulheres | `en-AU-NatashaNeural` | Geral |
| `en-AU` <sup>Novo</sup> | Australiano (Austrália) | Homens | `en-AU-WilliamNeural` | Geral |
| `en-CA` | Inglês (Canadá) | Mulheres | `en-CA-ClaraNeural` | Geral |
| `en-GB` | Inglês (Reino Unido) | Mulheres | `en-GB-LibbyNeural` | Geral |
| `en-GB` | Inglês (Reino Unido) | Mulheres | `en-GB-MiaNeural` | Geral |
| `en-GB` <sup>Novo</sup> | Inglês (Reino Unido) | Homens | `en-GB-RyanNeural` | Geral |
| `en-IE` <sup>Novo</sup> | Inglês irlandês (Irlanda) | Mulheres | `en-IE-EmilyNeural` | Geral |
| `en-IN` | Inglês (Índia) | Mulheres | `en-IN-NeerjaNeural` | Geral |
| `en-US` | Inglês (Estados Unidos) | Mulheres | `en-US-AriaNeural` | Geral, vários estilos de voz disponíveis |
| `en-US` | Inglês (Estados Unidos) | Homens | `en-US-GuyNeural` | Geral |
| `en-US` <sup>Novo</sup> | Inglês (Estados Unidos) | Mulheres | `en-US-JennyNeural` | Geral, vários estilos de voz disponíveis |
| `es-ES` | Espanhol (Espanha) | Mulheres | `es-ES-ElviraNeural` | Geral |
| `es-ES` <sup>Novo</sup> | Espanhol (Espanha) | Homens | `es-ES-AlvaroNeural` | Geral |
| `es-MX` | Espanhol (México) | Mulheres | `es-MX-DaliaNeural` | Geral |
| `es-MX` <sup>Novo</sup> | Espanhol (México) | Homens | `es-MX-JorgeNeural` | Geral |
| `fi-FI` | Finlandês (Finlândia) | Mulheres | `fi-FI-NooraNeural` | Geral |
| `fr-CA` | Francês (Canadá) | Mulheres | `fr-CA-SylvieNeural` | Geral |
| `fr-CA` <sup>Novo</sup> | Francês (Canadá) | Homens | `fr-CA-JeanNeural` | Geral |
| `fr-CH` <sup>Novo</sup> | Francês (Suíça) | Mulheres | `fr-CH-ArianeNeural` | Geral |
| `fr-FR` | Francês (França) | Mulheres | `fr-FR-DeniseNeural` | Geral |
| `fr-FR` <sup>Novo</sup> | Francês (França) | Homens | `fr-FR-HenriNeural` | Geral |
| `he-IL` <sup>Novo</sup> | Hebraico (Israel) | Mulheres | `he-IL-HilaNeural` | Geral |
| `hi-IN` | Hindi (Índia) | Mulheres | `hi-IN-SwaraNeural` | Geral |
| `hr-HR` <sup>Novo</sup> | Croata (Croácia) | Mulheres | `hr-HR-GabrijelaNeural` | Geral |
| `hu-HU` <sup>Novo</sup> | Húngaro (Hungria) | Mulheres | `hu-HU-NoemiNeural` | Geral |
| `id-ID` <sup>Novo</sup> | Bahasa Indonésia (Indonésia) | Homens | `id-ID-ArdiNeural` | Geral |
| `it-IT` | Italiano (Itália) | Mulheres | `it-IT-ElsaNeural` | Geral |
| `it-IT` <sup>Novo</sup> | Italiano (Itália) | Mulheres | `it-IT-IsabellaNeural` | Geral |
| `it-IT` <sup>Novo</sup> | Italiano (Itália) | Homens | `it-IT-DiegoNeural` | Geral |
| `ja-JP` | Japonês (Japão) | Mulheres | `ja-JP-NanamiNeural` | Geral |
| `ja-JP` <sup>Novo</sup> | Japonês (Japão) | Homens | `ja-JP-KeitaNeural` | Geral |
| `ko-KR` | Coreano (Coreia) | Mulheres | `ko-KR-SunHiNeural` | Geral |
| `ko-KR` <sup>Novo</sup> | Coreano (Coreia) | Homens | `ko-KR-InJoonNeural` | Geral |
| `ms-MY` <sup>Novo</sup> | Malaio (Malásia) | Mulheres | `ms-MY-YasminNeural` | Geral |
| `nb-NO` | Norueguês, Bokmål (Noruega) | Mulheres | `nb-NO-IselinNeural` | Geral |
| `nl-NL` | Holandês (Holanda) | Mulheres | `nl-NL-ColetteNeural` | Geral |
| `pl-PL` | Polaco (Polónia) | Mulheres | `pl-PL-ZofiaNeural` | Geral |
| `pt-BR` | Português (Brasil) | Mulheres | `pt-BR-FranciscaNeural` | Geral, vários estilos de voz disponíveis |
| `pt-BR` <sup>Novo</sup> | Português Brasileiro (Brasil) | Homens | `pt-BR-AntonioNeural` | Geral |
| `pt-PT` | Português (Portugal) | Mulheres | `pt-PT-FernandaNeural` | Geral |
| `ro-RO` <sup>Novo</sup> | Romeno (Roménia) | Mulheres | `ro-RO-AlinaNeural` | Geral |
| `ru-RU` | Russo (Rússia) | Mulheres | `ru-RU-DariyaNeural` | Geral |
| `sk-SK` <sup>Novo</sup> | Eslovaco (Eslováquia) | Mulheres | `sk-SK-ViktoriaNeural` | Geral |
| `sl-SI` <sup>Novo</sup> | Esloveno (Eslovénia) | Mulheres | `sl-SI-PetraNeural` | Geral |
| `sv-SE` | Sueco (Suécia) | Mulheres | `sv-SE-HilleviNeural` | Geral |
| `ta-IN` <sup>Novo</sup> | Tamil (Índia) | Mulheres | `ta-IN-PallaviNeural` | Geral |
| `te-IN` <sup>Novo</sup> | Telugu (Índia) | Mulheres | `te-IN-ShrutiNeural` | Geral |
| `th-TH` | Tailandês (Tailândia) | Mulheres | `th-TH-AcharaNeural` | Geral |
| `th-TH` <sup>Novo</sup> | Tailandês (Tailândia) | Mulheres | `th-TH-PremwadeeNeural` | Geral |
| `tr-TR` | Turco (Turquia) | Mulheres | `tr-TR-EmelNeural` | Geral |
| `vi-VN` <sup>Novo</sup> | Vietnamita (Vietname) | Mulheres | `vi-VN-HoaiMyNeural` | Geral |
| `zh-CN` | Mandarim (chinês simplificado, China) | Mulheres | `zh-CN-XiaoxiaoNeural` | Geral, vários estilos de voz disponíveis |
| `zh-CN` | Mandarim (chinês simplificado, China) | Mulheres | `zh-CN-XiaoyouNeural` | Voz infantil, otimizada para narração de história |
| `zh-CN` | Mandarim (chinês simplificado, China) | Homens | `zh-CN-YunyangNeural` | Otimizado para leitura de notícias, vários estilos de voz disponíveis |
| `zh-CN` | Mandarim (chinês simplificado, China) | Homens | `zh-CN-YunyeNeural` | Otimizado para narração de história |
| `zh-HK` | Cantonês (chinês tradicional, Hong Kong) | Mulheres | `zh-HK-HiuGaaiNeural` | Geral |
| `zh-TW` | Mandarim (chinês tradicional, Taiwan) | Mulheres | `zh-TW-HsiaoYuNeural` | Geral |


> [!IMPORTANT]
> A `en-US-JessaNeural` voz mudou `en-US-AriaNeural` para. Se já usaste "Jessa" antes, converte-te em "Aria".

Para aprender como pode configurar e ajustar vozes neurais, consulte a [linguagem de marcação da síntese da fala](speech-synthesis-markup.md#adjust-speaking-styles).

> [!TIP]
> Pode continuar a utilizar o mapeamento de nome de serviço completo como "Microsoft Server Speech Text to Speech Voice (en-US, AriaNeural)" nos seus pedidos de síntese de fala.

### <a name="standard-voices"></a>Vozes padrão

Mais de 75 vozes padrão estão disponíveis em mais de 45 línguas e locais, que permitem converter texto em discurso sintetizado. Para obter mais informações sobre a disponibilidade regional, consulte [as regiões.](regions.md#standard-and-neural-voices)

| Região | Linguagem | Sexo | Nome de voz |
|--|--|--|--|
| `ar-EG` | Árabe (árabe) | Mulheres | `ar-EG-Hoda` |
| `ar-SA` | Árabe (Arábia Saudita) | Homens | `ar-SA-Naayf` |
| `bg-BG` | Búlgaro (Bulgária) | Homens | `bg-BG-Ivan` |
| `ca-ES` | Catalão (Espanha) | Mulheres | `ca-ES-HerenaRUS` |
| `cs-CZ` | Checo (República Checa) | Homens | `cs-CZ-Jakub` |
| `da-DK` | Dinamarquês (Dinamarca) | Mulheres | `da-DK-HelleRUS` |
| `de-AT` | Alemão (Áustria) | Homens | `de-AT-Michael` |
| `de-CH` | Alemão (Suíça) | Homens | `de-CH-Karsten` |
| `de-DE` | Alemão (Alemanha) | Mulheres | `de-DE-HeddaRUS` |
| `de-DE` | Alemão (Alemanha) | Homens | `de-DE-Stefan` |
| `el-GR` | Grego (Grécia) | Homens | `el-GR-Stefanos` |
| `en-AU` | Inglês (Austrália) | Mulheres | `en-AU-Catherine` |
| `en-AU` | Inglês (Austrália) | Mulheres | `en-AU-HayleyRUS` |
| `en-CA` | Inglês (Canadá) | Mulheres | `en-CA-HeatherRUS` |
| `en-CA` | Inglês (Canadá) | Mulheres | `en-CA-Linda` |
| `en-GB` | Inglês (Reino Unido) | Homens | `en-GB-George` |
| `en-GB` | Inglês (Reino Unido) | Mulheres | `en-GB-HazelRUS` |
| `en-GB` | Inglês (Reino Unido) | Mulheres | `en-GB-Susan` |
| `en-IE` | Inglês (Irlanda) | Homens | `en-IE-Sean` |
| `en-IN` | Inglês (Índia) | Mulheres | `en-IN-Heera` |
| `en-IN` | Inglês (Índia) | Mulheres | `en-IN-PriyaRUS` |
| `en-IN` | Inglês (Índia) | Homens | `en-IN-Ravi` |
| `en-US` | Inglês (Estados Unidos) | Homens | `en-US-BenjaminRUS` |
| `en-US` | Inglês (Estados Unidos) | Homens | `en-US-GuyRUS` |
| `en-US` | Inglês (Estados Unidos) | Mulheres | `en-US-JessaRUS` |
| `en-US` | Inglês (Estados Unidos) | Mulheres | `en-US-ZiraRUS` |
| `es-ES` | Espanhol (Espanha) | Mulheres | `es-ES-HelenaRUS` |
| `es-ES` | Espanhol (Espanha) | Mulheres | `es-ES-Laura` |
| `es-ES` | Espanhol (Espanha) | Homens | `es-ES-Pablo` |
| `es-MX` | Espanhol (México) | Mulheres | `es-MX-HildaRUS` |
| `es-MX` | Espanhol (México) | Homens | `es-MX-Raul` |
| `fi-FI` | Finlandês (Finlândia) | Mulheres | `fi-FI-HeidiRUS` |
| `fr-CA` | Francês (Canadá) | Mulheres | `fr-CA-Caroline` |
| `fr-CA` | Francês (Canadá) | Mulheres | `fr-CA-HarmonieRUS` |
| `fr-CH` | Francês (Suíça) | Homens | `fr-CH-Guillaume` |
| `fr-FR` | Francês (França) | Mulheres | `fr-FR-HortenseRUS` |
| `fr-FR` | Francês (França) | Mulheres | `fr-FR-Julie` |
| `fr-FR` | Francês (França) | Homens | `fr-FR-Paul` |
| `he-IL` | Hebraico (Israel) | Homens | `he-IL-Asaf` |
| `hi-IN` | Hindi (Índia) | Homens | `hi-IN-Hemant` |
| `hi-IN` | Hindi (Índia) | Mulheres | `hi-IN-Kalpana` |
| `hr-HR` | Croata (Croácia) | Homens | `hr-HR-Matej` |
| `hu-HU` | Húngaro (Hungria) | Homens | `hu-HU-Szabolcs` |
| `id-ID` | Indonésio (Indonésia) | Homens | `id-ID-Andika` |
| `it-IT` | Italiano (Itália) | Homens | `it-IT-Cosimo` |
| `it-IT` | Italiano (Itália) | Mulheres | `it-IT-LuciaRUS` |
| `ja-JP` | Japonês (Japão) | Mulheres | `ja-JP-Ayumi` |
| `ja-JP` | Japonês (Japão) | Mulheres | `ja-JP-HarukaRUS` |
| `ja-JP` | Japonês (Japão) | Homens | `ja-JP-Ichiro` |
| `ko-KR` | Coreano (Coreia) | Mulheres | `ko-KR-HeamiRUS` |
| `ms-MY` | Malaio (Malásia) | Homens | `ms-MY-Rizwan` |
| `nb-NO` | Norueguês, Bokmål (Noruega) | Mulheres | `nb-NO-HuldaRUS` |
| `nl-NL` | Neerlandês (Países Baixos) | Mulheres | `nl-NL-HannaRUS` |
| `pl-PL` | Polaco (Polónia) | Mulheres | `pl-PL-PaulinaRUS` |
| `pt-BR` | Português (Brasil) | Homens | `pt-BR-Daniel` |
| `pt-BR` | Português (Brasil) | Mulheres | `pt-BR-HeloisaRUS` |
| `pt-PT` | Português (Portugal) | Mulheres | `pt-PT-HeliaRUS` |
| `ro-RO` | Romeno (Roménia) | Homens | `ro-RO-Andrei` |
| `ru-RU` | Russo (Rússia) | Mulheres | `ru-RU-EkaterinaRUS` |
| `ru-RU` | Russo (Rússia) | Mulheres | `ru-RU-Irina` |
| `ru-RU` | Russo (Rússia) | Homens | `ru-RU-Pavel` |
| `sk-SK` | Eslovaco (Eslováquia) | Homens | `sk-SK-Filip` |
| `sl-SI` | Esloveno (Eslovénia) | Homens | `sl-SI-Lado` |
| `sv-SE` | Sueco (Suécia) | Mulheres | `sv-SE-HedvigRUS` |
| `ta-IN` | Tamil (Índia) | Homens | `ta-IN-Valluvar` |
| `te-IN` | Telugu (Índia) | Mulheres | `te-IN-Chitra` |
| `th-TH` | Tailandês (Tailândia) | Homens | `th-TH-Pattara` |
| `tr-TR` | Turco (Turquia) | Mulheres | `tr-TR-SedaRUS` |
| `vi-VN` | Vietnamita (Vietname) | Homens | `vi-VN-An` |
| `zh-CN` | Mandarim (chinês simplificado, China) | Mulheres | `zh-CN-HuihuiRUS` |
| `zh-CN` | Mandarim (chinês simplificado, China) | Homens | `zh-CN-Kangkang` |
| `zh-CN` | Mandarim (chinês simplificado, China) | Mulheres | `zh-CN-Yaoyao` |
| `zh-HK` | Cantonês (chinês tradicional, Hong Kong) | Homens | `zh-HK-Danny` |
| `zh-HK` | Cantonês (chinês tradicional, Hong Kong) | Mulheres | `zh-HK-TracyRUS` |
| `zh-TW` | Mandarim (chinês tradicional, Taiwan) | Mulheres | `zh-TW-HanHanRUS` |
| `zh-TW` | Mandarim (chinês tradicional, Taiwan) | Mulheres | `zh-TW-Yating` |
| `zh-TW` | Mandarim (chinês tradicional, Taiwan) | Homens | `zh-TW-Zhiwei` |

> [!IMPORTANT]
> A `en-US-Jessa` voz mudou `en-US-Aria` para. Se já usaste "Jessa" antes, converte-te em "Aria".

> [!TIP]
> Pode continuar a utilizar o mapeamento de nome de serviço completo como "Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)" nos seus pedidos de síntese de fala.

### <a name="customization"></a>Personalização

A personalização de voz está disponível `de-DE` para, `en-GB` , , , , , , e `en-IN` `en-US` `es-MX` `fr-FR` `it-IT` `pt-BR` `zh-CN` . Selecione o local certo que corresponda aos dados de treino que tem para treinar um modelo de voz personalizado. Por exemplo, se os dados de gravação que tem forem falados em inglês com sotaque britânico, selecione `en-GB` .

> [!NOTE]
> Não apoiamos a formação de modelos bilingues em Voz Personalizada, exceto para o Chinese-English bi-lingual. Selecione "bilingue chinês-inglês" se quiser treinar uma voz chinesa que também sabe falar inglês. A formação de voz em todos os locais começa com um conjunto de dados de mais de 2.000 expressões, exceto para o `en-US` e onde você pode começar com qualquer tamanho de `zh-CN` dados de treino.

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
| Klingon                 | `tlh-Latn`    |
| Klingon (plqaD)         | `tlh-Piqd`    |
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

## <a name="speaker-recognition"></a>Reconhecimento de Orador

Consulte a tabela seguinte para as línguas suportadas para as várias APIs de reconhecimento de altifalantes. Consulte a [visão geral](speaker-recognition-overview.md) para obter informações adicionais sobre o reconhecimento de colunas.

| Região | Linguagem | Verificação dependente de texto | Verificação independente de texto | Identificação independente de texto |
|----|----|----|----|----|
| en-PT | Inglês (E.U.A.) | sim | sim | sim |
|zh-CN    |Chinês (mandarim, simplificado)|    n/a|    sim|    sim|
|de-DE    |Alemão (Alemanha)    |n/a    |sim    |sim|
|en-GB    |Inglês (Reino Unido)    |n/a    |sim    |sim|
|fr-FR    |Francês (França)    |n/a    |sim    |sim|
|en-AU    |Inglês (Austrália)    |n/a    |sim    |sim|
|en-CA    |Inglês (Canadá)    |n/a|    sim|    sim|
|fr-CA    |Francês (Canadá)    |n/a    |sim|    sim|
|it-IT    |Italiano|    n/a    |sim|    sim|
|es-ES|    Espanhol (Espanha)    |n/a    |sim|    sim|
|es-MX    |Espanhol (México)    |n/a|    sim|    sim|
|ja-JP|    Japonês    |n/a    |sim    |sim|
|pt-BR|    Português (Brasil)|    n/a|    sim|    sim|

## <a name="next-steps"></a>Próximos passos

* [Criar uma conta do Azure gratuita](https://azure.microsoft.com/free/cognitive-services/)
* [Veja como reconhecer a fala em C #](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp)
