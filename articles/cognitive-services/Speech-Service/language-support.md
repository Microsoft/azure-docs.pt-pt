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
ms.openlocfilehash: a864717304dacaf6cf4c77c92050827af619ed39
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92736675"
---
# <a name="language-and-voice-support-for-the-speech-service"></a>Suporte linguístico e de voz para o serviço de fala

O suporte linguístico varia de acordo com a funcionalidade do serviço speech. As tabelas que se seguem resumem o apoio linguístico às ofertas de serviços [de](#speech-to-text) [tradução](#speech-translation) de voz e [de voz.](#text-to-speech)

## <a name="speech-to-text"></a>Conversão de voz em texto

Tanto o Microsoft Speech SDK como o REST API suportam os seguintes idiomas (locais). 

Para melhorar a precisão, a personalização é oferecida para um subconjunto das línguas através do upload **de Transcrições áudio + etiqueta humana** ou texto **relacionado: frases** . Para saber mais sobre personalização, consulte [Começar com Discurso Personalizado.](how-to-custom-speech.md)

<!--
To get the AM and ML bits:
https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetSupportedLocalesForModels

To get pronunciation bits:
https://cris.ai -> Click on Adaptation Data -> scroll down to section "Pronunciation Datasets" -> Click on Import -> Locale: the list of locales there correspond to the supported locales
-->

| Linguagem                          | Região | Personalizações                                   |
|-----------------------------------|--------|--------------------------------------------------|
|Árabe (Bahrein), padrão moderno  |`ar-BH` | Modelo de linguagem                                   |
|Árabe (Egito)                     |`ar-EG` | Modelo de linguagem                                   |
|Árabe (Iraque)                      |`ar-IQ` | Modelo de linguagem                                   |
|Árabe (Jordânia)                    |`ar-JO` | Modelo de linguagem                                   |
|Árabe (Kuwait)                    |`ar-KW` | Modelo de linguagem                                   |
|Árabe (Líbano)                   |`ar-LB` | Modelo de linguagem                                   |
|Árabe (Omã)                      |`ar-OM` | Modelo de linguagem                                   |
|Árabe (Qatar)                     |`ar-QA` | Modelo de linguagem                                   |
|Árabe (Arábia Saudita)              |`ar-SA` | Modelo de linguagem                                   |
|Árabe (Síria)                     |`ar-SY` | Modelo de linguagem                                   |
|Árabe (Emirados Árabes Unidos)      |`ar-AE` | Modelo de linguagem                                   |
|Búlgaro (Bulgária)               |`bg-BG` | Modelo de linguagem                                   |
|Catalão (Espanha)                    |`ca-ES` | Modelo de linguagem                                   |
|Chinês (cantonês, tradicional)   |`zh-HK` | Modelo de linguagem                                   |
|Chinês (Mandarim, Simplificado)     |`zh-CN` | Modelo acústico<br>Modelo de linguagem                 |
|Chinês (Mandarim taiwanês)       |`zh-TW` | Modelo de linguagem                                   |
|Croata (Croácia)                 |`hr-HR` | Modelo de linguagem                                   |
|Checo (República Checa)             |`cs-CZ` | Modelo de linguagem                                   |
|Dinamarquês (Dinamarca)                   |`da-DK` | Modelo de linguagem                                   |
|Neerlandês (Países Baixos)                |`nl-NL` | Modelo de linguagem                                   |
|Inglês (Austrália)                |`en-AU` | Modelo acústico<br>Modelo de linguagem                 |
|Inglês (Canadá)                   |`en-CA` | Modelo acústico<br>Modelo de linguagem                 |
|Inglês (Hong Kong)                |`en-HK` | Modelo de linguagem                                   |
|Inglês (Índia)                    |`en-IN` | Modelo acústico<br>Modelo de linguagem                 |
|Inglês (Irlanda)                  |`en-IE` | Modelo de linguagem                                   |
|Inglês (Nova Zelândia)              |`en-NZ` | Modelo acústico<br>Modelo de linguagem                 |
|Inglês (Filipinas)              |`en-PH` | Modelo de linguagem                                   |
|Inglês (Singapura)                |`en-SG` | Modelo de linguagem                                   |
|Inglês (África do Sul)             |`en-ZA` | Modelo de linguagem                                   |
|Inglês (Reino Unido)           |`en-GB` | Modelo acústico<br>Modelo de linguagem<br>Pronúncia|
|Inglês (Estados Unidos)            |`en-US` | Modelo acústico<br>Modelo de linguagem<br>Pronúncia|
|Estónia (Estónia)                  |`et-EE` | Modelo de linguagem                                   |
|Finlandês (Finlândia)                  |`fi-FI` | Modelo de linguagem                                   |
|Francês (Canadá)                    |`fr-CA` | Modelo acústico<br>Modelo de linguagem                 |
|Francês (França)                    |`fr-FR` | Modelo acústico<br>Modelo de linguagem<br>Pronúncia|
|Alemão (Alemanha)                   |`de-DE` | Modelo acústico<br>Modelo de linguagem<br>Pronúncia|
|Grego (Grécia)                     |`el-GR` | Modelo de linguagem                                   |
|Gujarati (indiano)                  |`gu-IN` | Modelo de linguagem                                   |
|Hindi (Índia)                      |`hi-IN` | Modelo acústico<br>Modelo de linguagem                 |
|Húngaro (Hungria)                |`hu-HU` | Modelo de linguagem                                   |
|Irlanda (Irlanda)                     |`ga-IE` | Modelo de linguagem                                   |
|Italiano (Itália)                    |`it-IT` | Modelo acústico<br>Modelo de linguagem<br>Pronúncia|
|Japonês (Japão)                   |`ja-JP` | Modelo de linguagem                                   |
|Coreano (Coreia)                     |`ko-KR` | Modelo de linguagem                                   |
|Letão (Letónia)                   |`lv-LV` | Modelo de linguagem                                   |
|Lituano (Lituânia)             |`lt-LT` | Modelo de linguagem                                   |
|Maltês(Malta)                     |`mt-MT` | Modelo de linguagem                                   |
|Marathi (Índia)                    |`mr-IN` | Modelo de linguagem                                   |
|Norueguês (Bokmã-l) (Noruega)       |`nb-NO` | Modelo de linguagem                                   |
|Polaco (Polónia)                    |`pl-PL` | Modelo de linguagem                                   |
|Português (Brasil)                |`pt-BR` | Modelo acústico<br>Modelo de linguagem<br>Pronúncia|
|Português (Portugal)              |`pt-PT` | Modelo de linguagem                                   |
|Romeno (Roménia)                 |`ro-RO` | Modelo de linguagem                                   |
|Russo (Rússia)                   |`ru-RU` | Modelo acústico<br>Modelo de linguagem                 |
|Eslovaco (Eslováquia)                  |`sk-SK` | Modelo de linguagem                                   |
|Esloveno (Eslovénia)               |`sl-SI` | Modelo de linguagem                                   |
|Espanhol (Argentina)                |`es-AR` | Modelo de linguagem                                   |
|Espanhol (Bolívia)                  |`es-BO` | Modelo de linguagem                                   |
|Espanhol (Chile)                    |`es-CL` | Modelo de linguagem                                   |
|Espanhol (Colômbia)                 |`es-CO` | Modelo de linguagem                                   |
|Espanhol (Costa Rica)               |`es-CR` | Modelo de linguagem                                   |
|Espanhol (Cuba)                     |`es-CU` | Modelo de linguagem                                   |
|Espanhol (República Dominicana)       |`es-DO` | Modelo de linguagem                                   |
|Espanhol (Equador)                  |`es-EC` | Modelo de linguagem                                   |
|Espanhol (El Salvador)              |`es-SV` | Modelo de linguagem                                   |
|Espanhol (Guatemala)                |`es-GT` | Modelo de linguagem                                   |
|Espanhol (Honduras)                 |`es-HN` | Modelo de linguagem                                   |
|Espanhol (México)                   |`es-MX` | Modelo acústico<br>Modelo de linguagem                 |
|Espanhol (Nicarágua)                |`es-NI` | Modelo de linguagem                                   |
|Espanhol (Panamá)                   |`es-PA` | Modelo de linguagem                                   |
|Espanhol (Paraguai)                 |`es-PY` | Modelo de linguagem                                   |
|Espanhol (Peru)                     |`es-PE` | Modelo de linguagem                                   |
|Espanhol (Porto Rico)              |`es-PR` | Modelo de linguagem                                   |
|Espanhol (Espanha)                    |`es-ES` | Modelo acústico<br>Modelo de linguagem                 |
|Espanhol (Uruguai)                  |`es-UY` | Modelo de linguagem                                   |
|Espanhol (EUA)                      |`es-US` | Modelo de linguagem                                   |
|Espanhol (Venezuela)                |`es-VE` | Modelo de linguagem                                   |
|Sueco (Suécia)                   |`sv-SE` | Modelo de linguagem                                   |
|Tamil (Índia)                      |`ta-IN` | Modelo de linguagem                                   |
|Telugu (Índia)                     |`te-IN` | Modelo de linguagem                                   |
|Tailandês (Tailândia)                    |`th-TH` | Modelo de linguagem                                   |
|Turco (Turquia)                   |`tr-TR` | Modelo de linguagem                                   |

## <a name="text-to-speech"></a>Conversão de texto em voz

Tanto os Microsoft Speech SDK como os REST APIs suportam estas vozes, cada uma das quais suporta uma linguagem e dialeto específicos, identificados pelo local. Também pode obter uma lista completa de línguas e vozes apoiadas para cada região/ponto final específico através das [vozes/lista API](rest-text-to-speech.md#get-a-list-of-voices). 

> [!IMPORTANT]
> Os preços variam para vozes normais, personalizadas e neurais. Visite a página [de preços](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) para obter informações adicionais.

### <a name="neural-voices"></a>Vozes neurais

O texto-a-discurso neural é um novo tipo de síntese da fala alimentada por redes neuronais profundas. Quando se usa uma voz neural, o discurso sintetizado é quase indistinguível das gravações humanas.

As vozes neurais podem ser usadas para tornar as interações com chatbots e assistentes de voz mais naturais e envolventes, converter textos digitais como e-books em audiolivros e melhorar os sistemas de navegação no carro. Com a prosódia natural humana e a clara articulação das palavras, as vozes neurais reduzem significativamente a fadiga auditiva quando os utilizadores interagem com os sistemas de IA.

Para obter mais informações sobre a disponibilidade regional, consulte [as regiões.](regions.md#standard-and-neural-voices)

|Linguagem  | Região           | Sexo | Nome de voz | Suporte de estilo |
|--|--|--|--|--|
| Árabe (Egito) | `ar-EG` | Mulheres | `ar-EG-SalmaNeural` | Geral |
| Árabe (Arábia Saudita) | `ar-SA` | Mulheres | `ar-SA-ZariyahNeural` | Geral |
| Búlgaro (Bulgary) | `bg-BG` <sup>Novo</sup> | Mulheres | `bg-BG-KalinaNeural` | Geral |
| Cantonês (chinês tradicional, Hong Kong) | `zh-HK` | Mulheres | `zh-HK-HiuGaaiNeural` | Geral |
| Catalão (Espanha) | `ca-ES` | Mulheres | `ca-ES-AlbaNeural` | Geral |
| Croata (Croácia) | `hr-HR` <sup>Novo</sup> | Mulheres | `hr-HR-GabrijelaNeural` | Geral |
| Checo (checo) | `cs-CZ` <sup>Novo</sup> | Mulheres | `cs-CZ-VlastaNeural` | Geral |
| Dinamarquês (Dinamarca) | `da-DK` | Mulheres | `da-DK-ChristelNeural` | Geral |
| Neerlandês (Países Baixos) | `nl-NL` | Mulheres | `nl-NL-ColetteNeural` | Geral |
| Inglês (Austrália) | `en-AU` | Mulheres | `en-AU-NatashaNeural` | Geral |
| Inglês (Austrália) | `en-AU` <sup>Novo</sup> | Homens | `en-AU-WilliamNeural` | Geral |
| Inglês (Canadá) | `en-CA` | Mulheres | `en-CA-ClaraNeural` | Geral |
| Inglês (Índia) | `en-IN` | Mulheres | `en-IN-NeerjaNeural` | Geral |
| Inglês (Irlanda) | `en-IE` <sup>Novo</sup> | Mulheres | `en-IE-EmilyNeural` | Geral |
| Inglês (Reino Unido) | `en-GB` | Mulheres | `en-GB-LibbyNeural` | Geral |
| Inglês (Reino Unido) | `en-GB` | Mulheres | `en-GB-MiaNeural` | Geral |
| Inglês (Reino Unido) | `en-GB` <sup>Novo</sup> | Homens | `en-GB-RyanNeural` | Geral |
| Inglês (Estados Unidos) | `en-US` | Mulheres | `en-US-AriaNeural` | Geral, vários estilos de voz disponíveis |
| Inglês (Estados Unidos) | `en-US` | Homens | `en-US-GuyNeural` | Geral |
| Inglês (Estados Unidos) | `en-US` <sup>Novo</sup> | Mulheres | `en-US-JennyNeural` | Geral, vários estilos de voz disponíveis |
| Finlandês (Finlândia) | `fi-FI` | Mulheres | `fi-FI-NooraNeural` | Geral |
| Francês (Canadá) | `fr-CA` | Mulheres | `fr-CA-SylvieNeural` | Geral |
| Francês (Canadá) | `fr-CA` <sup>Novo</sup> | Homens | `fr-CA-JeanNeural` | Geral |
| Francês (França) | `fr-FR` | Mulheres | `fr-FR-DeniseNeural` | Geral |
| Francês (França) | `fr-FR` <sup>Novo</sup> | Homens | `fr-FR-HenriNeural` | Geral |
| Francês (Suíça) | `fr-CH` <sup>Novo</sup> | Mulheres | `fr-CH-ArianeNeural` | Geral |
| Alemão (Áustria) | `de-AT` <sup>Novo</sup> | Mulheres | `de-AT-IngridNeural` | Geral |
| Alemão (Alemanha) | `de-DE` | Mulheres | `de-DE-KatjaNeural` | Geral |
| Alemão (Alemanha) | `de-DE` <sup>Novo</sup> | Homens | `de-DE-ConradNeural` | Geral |
| Alemão (Suíça) | `de-CH` <sup>Novo</sup> | Mulheres | `de-CH-LeniNeural` | Geral |
| Grego (Grécia) | `el-GR` <sup>Novo</sup> | Mulheres | `el-GR-AthinaNeural` | Geral |
| Hebraico (Israel) | `he-IL` <sup>Novo</sup> | Mulheres | `he-IL-HilaNeural` | Geral |
| Hindi (Índia) | `hi-IN` | Mulheres | `hi-IN-SwaraNeural` | Geral |
| Húngaro (Hungria) | `hu-HU` <sup>Novo</sup> | Mulheres | `hu-HU-NoemiNeural` | Geral |
| Indonésio (Indonésia) | `id-ID` <sup>Novo</sup> | Homens | `id-ID-ArdiNeural` | Geral |
| Italiano (Itália) | `it-IT` | Mulheres | `it-IT-ElsaNeural` | Geral |
| Italiano (Itália) | `it-IT` <sup>Novo</sup> | Mulheres | `it-IT-IsabellaNeural` | Geral |
| Italiano (Itália) | `it-IT` <sup>Novo</sup> | Homens | `it-IT-DiegoNeural` | Geral |
| Japonês (Japão) | `ja-JP` | Mulheres | `ja-JP-NanamiNeural` | Geral |
| Japonês (Japão) | `ja-JP` <sup>Novo</sup> | Homens | `ja-JP-KeitaNeural` | Geral |
| Coreano (Coreia) | `ko-KR` | Mulheres | `ko-KR-SunHiNeural` | Geral |
| Coreano (Coreia) | `ko-KR` <sup>Novo</sup> | Homens | `ko-KR-InJoonNeural` | Geral |
| Malaio (Malásia) | `ms-MY` <sup>Novo</sup> | Mulheres | `ms-MY-YasminNeural` | Geral |
| Mandarim (chinês simplificado, China) | `zh-CN` | Mulheres | `zh-CN-XiaoxiaoNeural` | Geral, vários estilos de voz disponíveis |
| Mandarim (chinês simplificado, China) | `zh-CN` | Mulheres | `zh-CN-XiaoyouNeural` | Voz infantil, otimizada para narração de história |
| Mandarim (chinês simplificado, China) | `zh-CN` | Homens | `zh-CN-YunyangNeural` | Otimizado para leitura de notícias, vários estilos de voz disponíveis |
| Mandarim (chinês simplificado, China) | `zh-CN` | Homens | `zh-CN-YunyeNeural` | Otimizado para narração de história |
| Mandarim (chinês tradicional, Taiwan) | `zh-TW` | Mulheres | `zh-TW-HsiaoYuNeural` | Geral |
| Norueguês, Bokmã-l (Noruega) | `nb-NO` | Mulheres | `nb-NO-IselinNeural` | Geral |
| Polaco (Polónia) | `pl-PL` | Mulheres | `pl-PL-ZofiaNeural` | Geral |
| Português (Brasil) | `pt-BR` | Mulheres | `pt-BR-FranciscaNeural` | Geral, vários estilos de voz disponíveis |
| Português (Brasil) | `pt-BR` <sup>Novo</sup> | Homens | `pt-BR-AntonioNeural` | Geral |
| Português (Portugal) | `pt-PT` | Mulheres | `pt-PT-FernandaNeural` | Geral |
| Romeno (Roménia) | `ro-RO` <sup>Novo</sup> | Mulheres | `ro-RO-AlinaNeural` | Geral |
| Russo (Rússia) | `ru-RU` | Mulheres | `ru-RU-DariyaNeural` | Geral |
| Eslovaco (Eslováquia) | `sk-SK` <sup>Novo</sup> | Mulheres | `sk-SK-ViktoriaNeural` | Geral |
| Esloveno (Eslovénia) | `sl-SI` <sup>Novo</sup> | Mulheres | `sl-SI-PetraNeural` | Geral |
| Espanhol (México) | `es-MX` | Mulheres | `es-MX-DaliaNeural` | Geral |
| Espanhol (México) | `es-MX` <sup>Novo</sup> | Homens | `es-MX-JorgeNeural` | Geral |
| Espanhol (Espanha) | `es-ES` | Mulheres | `es-ES-ElviraNeural` | Geral |
| Espanhol (Espanha) | `es-ES` <sup>Novo</sup> | Homens | `es-ES-AlvaroNeural` | Geral |
| Sueco (Suécia) | `sv-SE` | Mulheres | `sv-SE-HilleviNeural` | Geral |
| Tamil (Índia) | `ta-IN` <sup>Novo</sup> | Mulheres | `ta-IN-PallaviNeural` | Geral |
| Telugu (Índia) | `te-IN` <sup>Novo</sup> | Mulheres | `te-IN-ShrutiNeural` | Geral |
| Tailandês (Tailândia) | `th-TH` | Mulheres | `th-TH-AcharaNeural` | Geral |
| Tailandês (Tailândia) | `th-TH` <sup>Novo</sup> | Mulheres | `th-TH-PremwadeeNeural` | Geral |
| Turco (Turquia) | `tr-TR` | Mulheres | `tr-TR-EmelNeural` | Geral |
| Vietnamita (Vietname) | `vi-VN` <sup>Novo</sup> | Mulheres | `vi-VN-HoaiMyNeural` | Geral|

> [!IMPORTANT]
> A `en-US-JessaNeural` voz mudou `en-US-AriaNeural` para. Se já usaste "Jessa" antes, converte-te em "Aria".

Para aprender como pode configurar e ajustar vozes neurais, consulte a [linguagem de marcação da síntese da fala](speech-synthesis-markup.md#adjust-speaking-styles).

> [!TIP]
> Pode continuar a utilizar o mapeamento de nome de serviço completo como "Microsoft Server Speech Text to Speech Voice (en-US, AriaNeural)" nos seus pedidos de síntese de fala.

### <a name="standard-voices"></a>Vozes padrão

Mais de 75 vozes padrão estão disponíveis em mais de 45 línguas e locais, que permitem converter texto em discurso sintetizado. Para obter mais informações sobre a disponibilidade regional, consulte [as regiões.](regions.md#standard-and-neural-voices)

| Linguagem | Região | Sexo | Nome de voz |
|--|--|--|--|
| Árabe (árabe)  |  `ar-EG`  |  Mulheres  |  `ar-EG-Hoda`|
| Árabe (Arábia Saudita)  |  `ar-SA`  |  Homens  |  `ar-SA-Naayf`|
| Búlgaro (Bulgária)  |  `bg-BG`  |  Homens  |  `bg-BG-Ivan`|
| Cantonês (chinês tradicional, Hong Kong)  |  `zh-HK`  |  Homens  |  `zh-HK-Danny`|
| Cantonês (chinês tradicional, Hong Kong)  |  `zh-HK`  |  Mulheres  |  `zh-HK-TracyRUS`|
| Catalão (Espanha)  |  `ca-ES`  |  Mulheres  |  `ca-ES-HerenaRUS`|
| Croata (Croácia)  |  `hr-HR`  |  Homens  |  `hr-HR-Matej`|
| Checo (República Checa)  |  `cs-CZ`  |  Homens  |  `cs-CZ-Jakub`|
| Dinamarquês (Dinamarca)  |  `da-DK`  |  Mulheres  |  `da-DK-HelleRUS`|
| Neerlandês (Países Baixos)  |  `nl-NL`  |  Mulheres  |  `nl-NL-HannaRUS`|
| Inglês (Austrália)  |  `en-AU`  |  Mulheres  |  `en-AU-Catherine`|
| Inglês (Austrália)  |  `en-AU`  |  Mulheres  |  `en-AU-HayleyRUS`|
| Inglês (Canadá)  |  `en-CA`  |  Mulheres  |  `en-CA-HeatherRUS`|
| Inglês (Canadá)  |  `en-CA`  |  Mulheres  |  `en-CA-Linda`|
| Inglês (Índia)  |  `en-IN`  |  Mulheres  |  `en-IN-Heera`|
| Inglês (Índia)  |  `en-IN`  |  Mulheres  |  `en-IN-PriyaRUS`|
| Inglês (Índia)  |  `en-IN`  |  Homens  |  `en-IN-Ravi`|
| Inglês (Irlanda)  |  `en-IE`  |  Homens  |  `en-IE-Sean`|
| Inglês (Reino Unido)  |  `en-GB`  |  Homens  |  `en-GB-George`|
| Inglês (Reino Unido)  |  `en-GB`  |  Mulheres  |  `en-GB-HazelRUS`|
| Inglês (Reino Unido)  |  `en-GB`  |  Mulheres  |  `en-GB-Susan`|
| Inglês (Estados Unidos)  |  `en-US`  |  Homens  |  `en-US-BenjaminRUS`|
| Inglês (Estados Unidos)  |  `en-US`  |  Homens  |  `en-US-GuyRUS`|
| Inglês (Estados Unidos)  |  `en-US`  |  Mulheres  |  `en-US-JessaRUS`|
| Inglês (Estados Unidos)  |  `en-US`  |  Mulheres  |  `en-US-ZiraRUS`|
| Finlandês (Finlândia)  |  `fi-FI`  |  Mulheres  |  `fi-FI-HeidiRUS`|
| Francês (Canadá)  |  `fr-CA`  |  Mulheres  |  `fr-CA-Caroline`|
| Francês (Canadá)  |  `fr-CA`  |  Mulheres  |  `fr-CA-HarmonieRUS`|
| Francês (França)  |  `fr-FR`  |  Mulheres  |  `fr-FR-HortenseRUS`|
| Francês (França)  |  `fr-FR`  |  Mulheres  |  `fr-FR-Julie`|
| Francês (França)  |  `fr-FR`  |  Homens  |  `fr-FR-Paul`|
| Francês (Suíça)  |  `fr-CH`  |  Homens  |  `fr-CH-Guillaume`|
| Alemão (Áustria)  |  `de-AT`  |  Homens  |  `de-AT-Michael`|
| Alemão (Alemanha)  |  `de-DE`  |  Mulheres  |  `de-DE-HeddaRUS`|
| Alemão (Alemanha)  |  `de-DE`  |  Homens  |  `de-DE-Stefan`|
| Alemão (Suíça)  |  `de-CH`  |  Homens  |  `de-CH-Karsten`|
| Grego (Grécia)  |  `el-GR`  |  Homens  |  `el-GR-Stefanos`|
| Hebraico (Israel)  |  `he-IL`  |  Homens  |  `he-IL-Asaf`|
| Hindi (Índia)  |  `hi-IN`  |  Homens  |  `hi-IN-Hemant`|
| Hindi (Índia)  |  `hi-IN`  |  Mulheres  |  `hi-IN-Kalpana`|
| Húngaro (Hungria)  |  `hu-HU`  |  Homens  |  `hu-HU-Szabolcs`|
| Indonésio (Indonésia)  |  `id-ID`  |  Homens  |  `id-ID-Andika`|
| Italiano (Itália)  |  `it-IT`  |  Homens  |  `it-IT-Cosimo`|
| Italiano (Itália)  |  `it-IT`  |  Mulheres  |  `it-IT-LuciaRUS`|
| Japonês (Japão)  |  `ja-JP`  |  Mulheres  |  `ja-JP-Ayumi`|
| Japonês (Japão)  |  `ja-JP`  |  Mulheres  |  `ja-JP-HarukaRUS`|
| Japonês (Japão)  |  `ja-JP`  |  Homens  |  `ja-JP-Ichiro`|
| Coreano (Coreia)  |  `ko-KR`  |  Mulheres  |  `ko-KR-HeamiRUS`|
| Malaio (Malásia)  |  `ms-MY`  |  Homens  |  `ms-MY-Rizwan`|
| Mandarim (chinês simplificado, China)  |  `zh-CN`  |  Mulheres  |  `zh-CN-HuihuiRUS`|
| Mandarim (chinês simplificado, China)  |  `zh-CN`  |  Homens  |  `zh-CN-Kangkang`|
| Mandarim (chinês simplificado, China)  |  `zh-CN`  |  Mulheres  |  `zh-CN-Yaoyao`|
| Mandarim (chinês tradicional, Taiwan)  |  `zh-TW`  |  Mulheres  |  `zh-TW-HanHanRUS`|
| Mandarim (chinês tradicional, Taiwan)  |  `zh-TW`  |  Mulheres  |  `zh-TW-Yating`|
| Mandarim (chinês tradicional, Taiwan)  |  `zh-TW`  |  Homens  |  `zh-TW-Zhiwei`|
| Norueguês, Bokmã-l (Noruega)  |  `nb-NO`  |  Mulheres  |  `nb-NO-HuldaRUS`|
| Polaco (Polónia)  |  `pl-PL`  |  Mulheres  |  `pl-PL-PaulinaRUS`|
| Português (Brasil)  |  `pt-BR`  |  Homens  |  `pt-BR-Daniel`|
| Português (Brasil)  |  `pt-BR`  |  Mulheres  |  `pt-BR-HeloisaRUS`|
| Português (Portugal)  |  `pt-PT`  |  Mulheres  |  `pt-PT-HeliaRUS`|
| Romeno (Roménia)  |  `ro-RO`  |  Homens  |  `ro-RO-Andrei`|
| Russo (Rússia)  |  `ru-RU`  |  Mulheres  |  `ru-RU-EkaterinaRUS`|
| Russo (Rússia)  |  `ru-RU`  |  Mulheres  |  `ru-RU-Irina`|
| Russo (Rússia)  |  `ru-RU`  |  Homens  |  `ru-RU-Pavel`|
| Eslovaco (Eslováquia)  |  `sk-SK`  |  Homens  |  `sk-SK-Filip`|
| Esloveno (Eslovénia)  |  `sl-SI`  |  Homens  |  `sl-SI-Lado`|
| Espanhol (México)  |  `es-MX`  |  Mulheres  |  `es-MX-HildaRUS`|
| Espanhol (México)  |  `es-MX`  |  Homens  |  `es-MX-Raul`|
| Espanhol (Espanha)  |  `es-ES`  |  Mulheres  |  `es-ES-HelenaRUS`|
| Espanhol (Espanha)  |  `es-ES`  |  Mulheres  |  `es-ES-Laura`|
| Espanhol (Espanha)  |  `es-ES`  |  Homens  |  `es-ES-Pablo`|
| Sueco (Suécia)  |  `sv-SE`  |  Mulheres  |  `sv-SE-HedvigRUS`|
| Tamil (Índia)  |  `ta-IN`  |  Homens  |  `ta-IN-Valluvar`|
| Telugu (Índia)  |  `te-IN`  |  Mulheres  |  `te-IN-Chitra`|
| Tailandês (Tailândia)  |  `th-TH`  |  Homens  |  `th-TH-Pattara`|
| Turco (Turquia)  |  `tr-TR`  |  Mulheres  |  `tr-TR-SedaRUS`|
| Vietnamita (Vietname)  |  `vi-VN`  |  Homens  |  `vi-VN-An`  |


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

| Linguagem | Região | Verificação dependente de texto | Verificação independente de texto | Identificação independente de texto |
|----|----|----|----|----|
|Inglês (E.U.A.)  |  en-PT  |  sim  |  sim  |  sim |
|Chinês (mandarim, simplificado) | zh-CN     |     n/a |     sim |     sim|
|Inglês (Austrália)     | en-AU     | n/a     | sim     | sim|
|Inglês (Canadá)     | en-CA     | n/a |     sim |     sim|
|Inglês (Reino Unido)     | en-GB     | n/a     | sim     | sim|
|Francês (Canadá)     | fr-CA     | n/a     | sim |     sim|
|Francês (França)     | fr-FR     | n/a     | sim     | sim|
|Alemão (Alemanha)     | de-DE     | n/a     | sim     | sim|
|Italiano | it-IT     |     n/a     | sim |     sim|
|Japonês     | ja-JP | n/a     | sim     | sim|
|Português (Brasil) | pt-BR |     n/a |     sim |     sim|
|Espanhol (México)     | es-MX     | n/a |     sim |     sim|
|Espanhol (Espanha)     | es-ES | n/a     | sim |     sim|

## <a name="next-steps"></a>Próximos passos

* [Criar uma conta do Azure gratuita](https://azure.microsoft.com/free/cognitive-services/)
* [Veja como reconhecer a fala em C #](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp)
