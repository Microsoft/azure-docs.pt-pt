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
ms.date: 01/07/2021
ms.author: trbye
ms.custom: references_regions
ms.openlocfilehash: 52dd642c661aa60157876a89d41c771cabfe2f1d
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2021
ms.locfileid: "107256162"
---
# <a name="language-and-voice-support-for-the-speech-service"></a>Suporte linguístico e de voz para o serviço de fala

O suporte linguístico varia de acordo com a funcionalidade do serviço speech. As tabelas que se seguem resumem o apoio linguístico às ofertas de serviços [de](#speech-to-text) [tradução](#speech-translation) de voz e [de voz.](#text-to-speech)

## <a name="speech-to-text"></a>Conversão de voz em texto

Tanto o Microsoft Speech SDK como o REST API suportam os seguintes idiomas (locais). 

Para melhorar a precisão, a personalização é oferecida para um subconjunto das línguas através do upload **de Transcrições áudio + etiqueta humana** ou texto **relacionado: frases**. O suporte à personalização do modelo acústico com **Transcrições audio + com rótulo humano limita-se** aos modelos de base específicos listados abaixo. Outros modelos e idiomas base apenas usarão o texto das transcrições para formar modelos personalizados, tal como com **texto relacionado: Frases**. Para saber mais sobre personalização, consulte [Começar com Discurso Personalizado.](./custom-speech-overview.md)

<!--
To get the AM and ML bits:
https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetSupportedLocalesForModels

To get pronunciation bits:
https://cris.ai -> Click on Adaptation Data -> scroll down to section "Pronunciation Datasets" -> Click on Import -> Locale: the list of locales there correspond to the supported locales
-->

| Linguagem                 | Local (BCP-47) | Personalizações  | [Deteção linguística](how-to-automatic-language-detection.md) |
|------------------------------------|--------|---------------------------------------------------|-------------------------------|
| Árabe (Bahrein), padrão moderno  | `ar-BH` | Texto                                   |                           | 
| Árabe (Egito)                     | `ar-EG` | Texto                                   | Yes                          |
| Árabe (Iraque)                      | `ar-IQ` | Texto                                   |                           |
| Árabe (Israel)                    | `ar-IL` | Texto                                   |                           |
| Árabe (Jordânia)                    | `ar-JO` | Texto                                   |                           |
| Árabe (Kuwait)                    | `ar-KW` | Texto                                   |                           |
| Árabe (Líbano)                   | `ar-LB` | Texto                                   |                           |
| Árabe (Omã)                      | `ar-OM` | Texto                                   |                           |
| Árabe (Qatar)                     | `ar-QA` | Texto                                   |                           |
| Árabe (Arábia Saudita)              | `ar-SA` | Texto                                   |                           |
| Árabe (Estado da Palestina)        | `ar-PS` | Texto                                   |                           |
| Árabe (Síria)                     | `ar-SY` | Texto                                   |                           |
| Árabe (Emirados Árabes Unidos)      | `ar-AE` | Texto                                   |                           |
| Búlgaro (Bulgária)               | `bg-BG` | Texto                                   |                           |
| Catalão (Espanha)                    | `ca-ES` | Texto                                   | Yes                          |
| Chinês (cantonês, tradicional)   | `zh-HK` | Áudio (20201015)<br>Texto                 |        Yes                   |
| Chinês (Mandarim, Simplificado)     | `zh-CN` | Áudio (20200910)<br>Texto                 |     Yes                      |
| Chinês (Mandarim taiwanês)       | `zh-TW` | Áudio (20190701, 20201015)<br>Texto                 |           Yes                |
| Croata (Croácia)                 | `hr-HR` | Texto                                   |                           |
| Checo (República Checa)             | `cs-CZ` | Texto                                   |                           |
| Dinamarquês (Dinamarca)                   | `da-DK` | Texto                                   | Yes                          |
| Neerlandês (Países Baixos)                | `nl-NL` | Áudio (20201015)<br>Texto<br>Pronúncia|    Yes                       |
| Inglês (Austrália)                | `en-AU` | Áudio (20201019)<br>Texto                 | Yes                          |
| Inglês (Canadá)                   | `en-CA` | Áudio (20201019)<br>Texto                 | Yes                          |
| Inglês (Gana)                    | `en-GH` | Texto                                   |                           |
| Inglês (Hong Kong)                | `en-HK` | Texto                                   |                           |
| Inglês (Índia)                    | `en-IN` | Áudio (20200923)<br>Texto                 |                          |
| Inglês (Irlanda)                  | `en-IE` | Texto                                   |                           |
| Inglês (Quénia)                    | `en-KE` | Texto                                   |                           |
| Inglês (Nova Zelândia)              | `en-NZ` | Áudio (20201019)<br>Texto                 |                          |
| Inglês (Nigéria)                  | `en-NG` | Texto                                   |                           |
| Inglês (Filipinas)              | `en-PH` | Texto                                   |                           |
| Inglês (Singapura)                | `en-SG` | Texto                                   |                           |
| Inglês (África do Sul)             | `en-ZA` | Texto                                   |                           |
| Inglês (Tanzânia)                 | `en-TZ` | Texto                                   |                           |
| Inglês (Reino Unido)           | `en-GB` | Áudio (20201019)<br>Texto<br>Pronúncia| Yes                          |
| Inglês (Estados Unidos)            | `en-US` | Áudio (20201019)<br>Texto<br>Pronúncia| Yes                          |
| Estónia (Estónia)                  | `et-EE` | Texto                                   |                           |
| Filipino (Filipinas)             | `fil-PH`| Texto                                   |                           |
| Finlandês (Finlândia)                  | `fi-FI` | Texto                                   |     Yes                      |
| Francês (Canadá)                    | `fr-CA` | Áudio (20201015)<br>Texto<br>Pronúncia|     Yes                      |
| Francês (França)                    | `fr-FR` | Áudio (20201015)<br>Texto<br>Pronúncia|      Yes                     |
| Francês (Suíça)               | `fr-CH` | Texto<br>Pronúncia                  |                           |
| Alemão (Áustria)                   | `de-AT` | Texto<br>Pronúncia                  |                           |
| Alemão (Alemanha)                   | `de-DE` | Áudio (20190701, 20200619, 20201127)<br>Texto<br>Pronúncia|  Yes                         |
| Grego (Grécia)                     | `el-GR` | Texto                                   |  Yes                         |
| Gujarati (indiano)                  | `gu-IN` | Texto                                   |                           |
| Hindi (Índia)                      | `hi-IN` | Áudio (20200701)<br>Texto                 |     Yes                      |
| Húngaro (Hungria)                | `hu-HU` | Texto                                   |                           |
| Indonésio (Indonésia)             | `id-ID` | Texto                                   |                           |
| Irlanda (Irlanda)                     | `ga-IE` | Texto                                   |                           |
| Italiano (Itália)                    | `it-IT` | Áudio (20201016)<br>Texto<br>Pronúncia|      Yes                     |
| Japonês (Japão)                   | `ja-JP` | Texto                                   |      Yes                     |
| Coreano (Coreia)                     | `ko-KR` | Áudio (20201015)<br>Texto                 |      Yes                     |
| Letão (Letónia)                   | `lv-LV` | Texto                                   |                           |
| Lituano (Lituânia)             | `lt-LT` | Texto                                   |                           |
| Malaio (Malásia)                   | `ms-MY` | Texto                                   |                           |
| Maltês (Malta)                    | `mt-MT` | Texto                                   |                           |
| Marathi (Índia)                    | `mr-IN` | Texto                                   |                           |
| Norueguês (Bokmål, Noruega)         | `nb-NO` | Texto                                   |     Yes                      |
| Polaco (Polónia)                    | `pl-PL` | Texto                                   |       Yes                    |
| Português (Brasil)                | `pt-BR` | Áudio (20190620, 20201015)<br>Texto<br>Pronúncia|          Yes                 |
| Português (Portugal)              | `pt-PT` | Texto<br>Pronúncia                  |             Yes              |
| Romeno (Roménia)                 | `ro-RO` | Texto                                   |  Yes                         |
| Russo (Rússia)                   | `ru-RU` | Áudio (20200907)<br>Texto                 |                Yes           |
| Eslovaco (Eslováquia)                  | `sk-SK` | Texto                                   |                           |
| Esloveno (Eslovénia)               | `sl-SI` | Texto                                   |                           |
| Espanhol (Argentina)                | `es-AR` | Texto<br>Pronúncia                  |                           |
| Espanhol (Bolívia)                  | `es-BO` | Texto<br>Pronúncia                  |                           |
| Espanhol (Chile)                    | `es-CL` | Texto<br>Pronúncia                  |                           |
| Espanhol (Colômbia)                 | `es-CO` | Texto<br>Pronúncia                  |                           |
| Espanhol (Costa Rica)               | `es-CR` | Texto<br>Pronúncia                  |                           |
| Espanhol (Cuba)                     | `es-CU` | Texto<br>Pronúncia                  |                           |
| Espanhol (República Dominicana)       | `es-DO` | Texto<br>Pronúncia                  |                           |
| Espanhol (Equador)                  | `es-EC` | Texto<br>Pronúncia                  |                           |
| Espanhol (El Salvador)              | `es-SV` | Texto<br>Pronúncia                  |                           |
| Espanhol (Guiné Equatorial)        | `es-GQ` | Texto                                   |                           |
| Espanhol (Guatemala)                | `es-GT` | Texto<br>Pronúncia                  |                           |
| Espanhol (Honduras)                 | `es-HN` | Texto<br>Pronúncia                  |                           |
| Espanhol (México)                   | `es-MX` | Áudio (20200907)<br>Texto<br>Pronúncia|    Yes                       |
| Espanhol (Nicarágua)                | `es-NI` | Texto<br>Pronúncia                  |                           |
| Espanhol (Panamá)                   | `es-PA` | Texto<br>Pronúncia                  |                           |
| Espanhol (Paraguai)                 | `es-PY` | Texto<br>Pronúncia                  |                           |
| Espanhol (Peru)                     | `es-PE` | Texto<br>Pronúncia                  |                           |
| Espanhol (Porto Rico)              | `es-PR` | Texto<br>Pronúncia                  |                           |
| Espanhol (Espanha)                    | `es-ES` | Áudio (20201015)<br>Texto<br>Pronúncia|  Yes                         |
| Espanhol (Uruguai)                  | `es-UY` | Texto<br>Pronúncia                  |                           |
| Espanhol (EUA)                      | `es-US` | Texto<br>Pronúncia                  |                           |
| Espanhol (Venezuela)                | `es-VE` | Texto<br>Pronúncia                  |                           |
| Sueco (Suécia)                   | `sv-SE` | Texto                                   |   Yes                        |
| Tamil (Índia)                      | `ta-IN` | Texto                                   |                           |
| Telugu (Índia)                     | `te-IN` | Texto                                   |                           |
| Tailandês (Tailândia)                    | `th-TH` | Texto                                   |      Yes                     |
| Turco (Turquia)                   | `tr-TR` | Texto                                   |                           |
| Vietnamita (Vietname)               | `vi-VN` | Texto                                   |                           |

## <a name="text-to-speech"></a>Conversão de texto em voz

Tanto os Microsoft Speech SDK como os REST APIs suportam estas vozes, cada uma das quais suporta uma linguagem e dialeto específicos, identificados pelo local. Também pode obter uma lista completa de línguas e vozes apoiadas para cada região/ponto final específico através das [vozes/lista API](rest-text-to-speech.md#get-a-list-of-voices). 

> [!IMPORTANT]
> Os preços variam para vozes normais, personalizadas e neurais. Visite a página [de preços](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) para obter informações adicionais.

### <a name="neural-voices"></a>Vozes neurais

O texto-a-discurso neural é um novo tipo de síntese da fala alimentada por redes neuronais profundas. Quando se usa uma voz neural, o discurso sintetizado é quase indistinguível das gravações humanas.

As vozes neurais podem ser usadas para tornar as interações com chatbots e assistentes de voz mais naturais e envolventes, converter textos digitais como e-books em audiolivros e melhorar os sistemas de navegação no carro. Com a prosódia natural humana e a clara articulação das palavras, as vozes neurais reduzem significativamente a fadiga auditiva quando os utilizadores interagem com os sistemas de IA.

> [!NOTE]
> As vozes neurais são criadas a partir de amostras que usam uma taxa de amostra de 24 khz.
> Todas as vozes podem aumentar ou reduzir para outras taxas de amostra ao sintetizar.


| Linguagem | Região | Sexo | Nome de voz | Suporte de estilo |
|---|---|---|---|---|
| Árabe (Egito) | `ar-EG` | Mulheres | `ar-EG-SalmaNeural` | Geral |
| Árabe (Egito) | `ar-EG` | Homens | `ar-EG-ShakirNeural` | Geral |
| Árabe (Arábia Saudita) | `ar-SA` | Mulheres | `ar-SA-ZariyahNeural` | Geral |
| Árabe (Arábia Saudita) | `ar-SA` | Homens | `ar-SA-HamedNeural` | Geral |
| Búlgaro (Bulgária) | `bg-BG` | Mulheres | `bg-BG-KalinaNeural` | Geral |
| Búlgaro (Bulgária) | `bg-BG` | Homens | `bg-BG-BorislavNeural` | Geral |
| Catalão (Espanha) | `ca-ES` | Mulheres | `ca-ES-AlbaNeural` | Geral |
| Catalão (Espanha) | `ca-ES` | Mulheres | `ca-ES-JoanaNeural` | Geral |
| Catalão (Espanha) | `ca-ES` | Homens | `ca-ES-EnricNeural` | Geral |
| Chinês (cantonês, tradicional) | `zh-HK` | Mulheres | `zh-HK-HiuGaaiNeural` | Geral |
| Chinês (cantonês, tradicional) | `zh-HK` | Mulheres | `zh-HK-HiuMaanNeural` | Geral |
| Chinês (cantonês, tradicional) | `zh-HK` | Homens | `zh-HK-WanLungNeural` | Geral |
| Chinês (Mandarim, Simplificado) | `zh-CN` | Mulheres | `zh-CN-XiaoxiaoNeural` | Geral, vários estilos de voz disponíveis [usando SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Chinês (Mandarim, Simplificado) | `zh-CN` | Mulheres | `zh-CN-XiaoyouNeural` | Voz infantil, otimizada para narração de história |
| Chinês (Mandarim, Simplificado) | `zh-CN` | Homens | `zh-CN-YunyangNeural` | Otimizado para a leitura de notícias,<br /> vários estilos de voz disponíveis [usando SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Chinês (Mandarim, Simplificado) | `zh-CN` | Homens | `zh-CN-YunyeNeural` | Otimizado para narração de história |
| Chinês (Mandarim taiwanês) | `zh-TW` | Mulheres | `zh-TW-HsiaoChenNeural` | Geral |
| Chinês (Mandarim taiwanês) | `zh-TW` | Mulheres | `zh-TW-HsiaoYuNeural` | Geral |
| Chinês (Mandarim taiwanês) | `zh-TW` | Homens | `zh-TW-YunJheNeural` | Geral |
| Croata (Croácia) | `hr-HR` | Mulheres | `hr-HR-GabrijelaNeural` | Geral |
| Croata (Croácia) | `hr-HR` | Homens | `hr-HR-SreckoNeural` | Geral |
| Checo (checo) | `cs-CZ` | Mulheres | `cs-CZ-VlastaNeural` | Geral |
| Checo (checo) | `cs-CZ` | Homens | `cs-CZ-AntoninNeural` | Geral |
| Dinamarquês (Dinamarca) | `da-DK` | Mulheres | `da-DK-ChristelNeural` | Geral |
| Dinamarquês (Dinamarca) | `da-DK` | Homens | `da-DK-JeppeNeural` | Geral |
| Neerlandês (Bélgica) | `nl-BE` | Mulheres | `nl-BE-DenaNeural` <sup>Novo</sup> | Geral | 
| Neerlandês (Bélgica) | `nl-BE` | Homens | `nl-BE-ArnaudNeural` <sup>Novo</sup> | Geral | 
| Neerlandês (Países Baixos) | `nl-NL` | Mulheres | `nl-NL-ColetteNeural` | Geral |
| Neerlandês (Países Baixos) | `nl-NL` | Mulheres | `nl-NL-FennaNeural` | Geral |
| Neerlandês (Países Baixos) | `nl-NL` | Homens | `nl-NL-MaartenNeural` | Geral |
| Inglês (Austrália) | `en-AU` | Mulheres | `en-AU-NatashaNeural` | Geral |
| Inglês (Austrália) | `en-AU` | Homens | `en-AU-WilliamNeural` | Geral |
| Inglês (Canadá) | `en-CA` | Mulheres | `en-CA-ClaraNeural` | Geral |
| Inglês (Canadá) | `en-CA` | Homens | `en-CA-LiamNeural` | Geral |
| Inglês (Índia) | `en-IN` | Mulheres | `en-IN-NeerjaNeural` | Geral |
| Inglês (Índia) | `en-IN` | Homens | `en-IN-PrabhatNeural` | Geral |
| Inglês (Irlanda) | `en-IE` | Mulheres | `en-IE-EmilyNeural` | Geral |
| Inglês (Irlanda) | `en-IE` | Homens | `en-IE-ConnorNeural` | Geral |
| Inglês (Filipinas) | `en-PH` | Mulheres | `en-PH-RosaNeural` <sup>Novo</sup> | Geral | 
| Inglês (Filipinas) | `en-PH` | Homens | `en-PH-JamesNeural` <sup>Novo</sup> | Geral | 
| Inglês (Reino Unido) | `en-GB` | Mulheres | `en-GB-LibbyNeural` | Geral |
| Inglês (Reino Unido) | `en-GB` | Mulheres | `en-GB-MiaNeural` | Geral |
| Inglês (Reino Unido) | `en-GB` | Homens | `en-GB-RyanNeural` | Geral |
| Inglês (Estados Unidos) | `en-US` | Mulheres | `en-US-AriaNeural` | Geral, vários estilos de voz disponíveis [usando SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Inglês (Estados Unidos) | `en-US` | Mulheres | `en-US-JennyNeural` | Geral |
| Inglês (Estados Unidos) | `en-US` | Homens | `en-US-GuyNeural` | Geral |
| Estónio (Estónia) | `et-EE` | Mulheres | `et-EE-AnuNeural` | Geral |
| Estónio (Estónia) | `et-EE` | Homens | `et-EE-KertNeural` | Geral |
| Finlandês (Finlândia) | `fi-FI` | Mulheres | `fi-FI-NooraNeural` | Geral |
| Finlandês (Finlândia) | `fi-FI` | Mulheres | `fi-FI-SelmaNeural` | Geral |
| Finlandês (Finlândia) | `fi-FI` | Homens | `fi-FI-HarriNeural` | Geral |
| Francês (Bélgica) | `fr-BE` | Mulheres | `fr-BE-CharlineNeural` <sup>Novo</sup> | Geral | 
| Francês (Bélgica) | `fr-BE` | Homens | `fr-BE-GerardNeural` <sup>Novo</sup> | Geral | 
| Francês (Canadá) | `fr-CA` | Mulheres | `fr-CA-SylvieNeural` | Geral |
| Francês (Canadá) | `fr-CA` | Homens | `fr-CA-AntoineNeural` | Geral |
| Francês (Canadá) | `fr-CA` | Homens | `fr-CA-JeanNeural` | Geral |
| Francês (França) | `fr-FR` | Mulheres | `fr-FR-DeniseNeural` | Geral |
| Francês (França) | `fr-FR` | Homens | `fr-FR-HenriNeural` | Geral |
| Francês (Suíça) | `fr-CH` | Mulheres | `fr-CH-ArianeNeural` | Geral |
| Francês (Suíça) | `fr-CH` | Homens | `fr-CH-FabriceNeural` | Geral |
| Alemão (Áustria) | `de-AT` | Mulheres | `de-AT-IngridNeural` | Geral |
| Alemão (Áustria) | `de-AT` | Homens | `de-AT-JonasNeural` | Geral |
| Alemão (Alemanha) | `de-DE` | Mulheres | `de-DE-KatjaNeural` | Geral |
| Alemão (Alemanha) | `de-DE` | Homens | `de-DE-ConradNeural` | Geral |
| Alemão (Suíça) | `de-CH` | Mulheres | `de-CH-LeniNeural` | Geral |
| Alemão (Suíça) | `de-CH` | Homens | `de-CH-JanNeural` | Geral |
| Grego (Grécia) | `el-GR` | Mulheres | `el-GR-AthinaNeural` | Geral |
| Grego (Grécia) | `el-GR` | Homens | `el-GR-NestorasNeural` | Geral |
| Hebraico (Israel) | `he-IL` | Mulheres | `he-IL-HilaNeural` | Geral |
| Hebraico (Israel) | `he-IL` | Homens | `he-IL-AvriNeural` | Geral |
| Hindi (Índia) | `hi-IN` | Mulheres | `hi-IN-SwaraNeural` | Geral |
| Hindi (Índia) | `hi-IN` | Homens | `hi-IN-MadhurNeural` | Geral |
| Húngaro (Hungria) | `hu-HU` | Mulheres | `hu-HU-NoemiNeural` | Geral |
| Húngaro (Hungria) | `hu-HU` | Homens | `hu-HU-TamasNeural` | Geral |
| Indonésio (Indonésia) | `id-ID` | Mulheres | `id-ID-GadisNeural` | Geral |
| Indonésio (Indonésia) | `id-ID` | Homens | `id-ID-ArdiNeural` | Geral |
| Irlandês (Irlanda) | `ga-IE` | Mulheres | `ga-IE-OrlaNeural` | Geral |
| Irlandês (Irlanda) | `ga-IE` | Homens | `ga-IE-ColmNeural` | Geral |
| Italiano (Itália) | `it-IT` | Mulheres | `it-IT-ElsaNeural` | Geral |
| Italiano (Itália) | `it-IT` | Mulheres | `it-IT-IsabellaNeural` | Geral |
| Italiano (Itália) | `it-IT` | Homens | `it-IT-DiegoNeural` | Geral |
| Japonês (Japão) | `ja-JP` | Mulheres | `ja-JP-NanamiNeural` | Geral |
| Japonês (Japão) | `ja-JP` | Homens | `ja-JP-KeitaNeural` | Geral |
| Coreano (Coreia) | `ko-KR` | Mulheres | `ko-KR-SunHiNeural` | Geral |
| Coreano (Coreia) | `ko-KR` | Homens | `ko-KR-InJoonNeural` | Geral |
| Letão (Letónia) | `lv-LV` | Mulheres | `lv-LV-EveritaNeural` | Geral |
| Letão (Letónia) | `lv-LV` | Homens | `lv-LV-NilsNeural` | Geral |
| Lituano (Lituânia) | `lt-LT` | Mulheres | `lt-LT-OnaNeural` | Geral |
| Lituano (Lituânia) | `lt-LT` | Homens | `lt-LT-LeonasNeural` | Geral |
| Malaio (Malásia) | `ms-MY` | Mulheres | `ms-MY-YasminNeural` | Geral |
| Malaio (Malásia) | `ms-MY` | Homens | `ms-MY-OsmanNeural` | Geral |
| Maltês (Malta) | `mt-MT` | Mulheres | `mt-MT-GraceNeural` | Geral |
| Maltês (Malta) | `mt-MT` | Homens | `mt-MT-JosephNeural` | Geral |
| Norueguês (Bokmål, Noruega) | `nb-NO` | Mulheres | `nb-NO-IselinNeural` | Geral |
| Norueguês (Bokmål, Noruega) | `nb-NO` | Mulheres | `nb-NO-PernilleNeural` | Geral |
| Norueguês (Bokmål, Noruega) | `nb-NO` | Homens | `nb-NO-FinnNeural` | Geral |
| Polaco (Polónia) | `pl-PL` | Mulheres | `pl-PL-AgnieszkaNeural` | Geral |
| Polaco (Polónia) | `pl-PL` | Mulheres | `pl-PL-ZofiaNeural` | Geral |
| Polaco (Polónia) | `pl-PL` | Homens | `pl-PL-MarekNeural` | Geral |
| Português (Brasil) | `pt-BR` | Mulheres | `pt-BR-FranciscaNeural` | Geral, vários estilos de voz disponíveis [usando SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Português (Brasil) | `pt-BR` | Homens | `pt-BR-AntonioNeural` | Geral |
| Português (Portugal) | `pt-PT` | Mulheres | `pt-PT-FernandaNeural` | Geral |
| Português (Portugal) | `pt-PT` | Mulheres | `pt-PT-RaquelNeural` | Geral |
| Português (Portugal) | `pt-PT` | Homens | `pt-PT-DuarteNeural` | Geral |
| Romeno (Roménia) | `ro-RO` | Mulheres | `ro-RO-AlinaNeural` | Geral |
| Romeno (Roménia) | `ro-RO` | Homens | `ro-RO-EmilNeural` | Geral |
| Russo (Rússia) | `ru-RU` | Mulheres | `ru-RU-DariyaNeural` | Geral |
| Russo (Rússia) | `ru-RU` | Mulheres | `ru-RU-SvetlanaNeural` | Geral |
| Russo (Rússia) | `ru-RU` | Homens | `ru-RU-DmitryNeural` | Geral |
| Eslovaco (Eslováquia) | `sk-SK` | Mulheres | `sk-SK-ViktoriaNeural` | Geral |
| Eslovaco (Eslováquia) | `sk-SK` | Homens | `sk-SK-LukasNeural` | Geral |
| Esloveno (Eslovénia) | `sl-SI` | Mulheres | `sl-SI-PetraNeural` | Geral |
| Esloveno (Eslovénia) | `sl-SI` | Homens | `sl-SI-RokNeural` | Geral |
| Espanhol (México) | `es-MX` | Mulheres | `es-MX-DaliaNeural` | Geral |
| Espanhol (México) | `es-MX` | Homens | `es-MX-JorgeNeural` | Geral |
| Espanhol (Espanha) | `es-ES` | Mulheres | `es-ES-ElviraNeural` | Geral |
| Espanhol (Espanha) | `es-ES` | Homens | `es-ES-AlvaroNeural` | Geral |
| Sueco (Suécia) | `sv-SE` | Mulheres | `sv-SE-HilleviNeural` | Geral |
| Sueco (Suécia) | `sv-SE` | Mulheres | `sv-SE-SofieNeural` | Geral |
| Sueco (Suécia) | `sv-SE` | Homens | `sv-SE-MattiasNeural` | Geral |
| Tamil (Índia) | `ta-IN` | Mulheres | `ta-IN-PallaviNeural` | Geral |
| Tamil (Índia) | `ta-IN` | Homens | `ta-IN-ValluvarNeural` | Geral |
| Telugu (Índia) | `te-IN` | Mulheres | `te-IN-ShrutiNeural` | Geral |
| Telugu (Índia) | `te-IN` | Homens | `te-IN-MohanNeural` | Geral |
| Tailandês (Tailândia) | `th-TH` | Mulheres | `th-TH-AcharaNeural` | Geral |
| Tailandês (Tailândia) | `th-TH` | Mulheres | `th-TH-PremwadeeNeural` | Geral |
| Tailandês (Tailândia) | `th-TH` | Homens | `th-TH-NiwatNeural` | Geral |
| Turco (Turquia) | `tr-TR` | Mulheres | `tr-TR-EmelNeural` | Geral |
| Turco (Turquia) | `tr-TR` | Homens | `tr-TR-AhmetNeural` | Geral |
| Ucraniano (Ucrânia) | `uk-UA` | Mulheres | `uk-UA-PolinaNeural` <sup>Novo</sup> | Geral | 
| Ucraniano (Ucrânia) | `uk-UA` | Homens | `uk-UA-OstapNeural` <sup>Novo</sup> | Geral | 
| Urdu (Paquistão) | `ur-PK` | Mulheres | `ur-PK-UzmaNeural` <sup>Novo</sup>  | Geral | 
| Urdu (Paquistão) | `ur-PK` | Homens | `ur-PK-AsadNeural` <sup>Novo</sup> | Geral | 
| Vietnamita (Vietname) | `vi-VN` | Mulheres | `vi-VN-HoaiMyNeural` | Geral |
| Vietnamita (Vietname) | `vi-VN` | Homens | `vi-VN-NamMinhNeural` | Geral |
| Galês (Reino Unido) | `cy-GB` | Mulheres | `cy-GB-NiaNeural` <sup>Novo</sup> | Geral | 
| Galês (Reino Unido) | `cy-GB` | Homens | `cy-GB-AledNeural` <sup>Novo</sup> | Geral | 

#### <a name="neural-voices-in-preview"></a>Vozes neurais na pré-visualização

Abaixo as vozes neurais estão na pré-visualização pública. 

| Linguagem                         | Região  | Sexo | Nome de voz                             | Suporte de estilo |
|----------------------------------|---------|--------|----------------------------------------|---------------|
| Chinês (Mandarim, Simplificado) | `zh-CN` | Mulheres | `zh-CN-XiaohanNeural` | Geral, vários estilos disponíveis [usando SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Chinês (Mandarim, Simplificado) | `zh-CN` | Mulheres | `zh-CN-XiaomoNeural` | Geral, múltiplas role-play e estilos disponíveis [usando SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Chinês (Mandarim, Simplificado) | `zh-CN` | Mulheres | `zh-CN-XiaoruiNeural` | Voz sénior, vários estilos disponíveis [usando SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Chinês (Mandarim, Simplificado) | `zh-CN` | Mulheres | `zh-CN-XiaoxuanNeural` | Geral, múltiplas role-play e estilos disponíveis [usando SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Chinês (Mandarim, Simplificado) | `zh-CN` | Homens   | `zh-CN-YunxiNeural` | Geral, vários estilos disponíveis [usando SSML](speech-synthesis-markup.md#adjust-speaking-styles) |

> [!IMPORTANT]
> As vozes na pré-visualização pública só estão disponíveis em 3 regiões de serviço: Leste dos EUA, Europa Ocidental e Sudeste Asiático.

Para obter mais informações sobre a disponibilidade regional, consulte [as regiões.](regions.md#neural-and-standard-voices)

Para aprender como pode configurar e ajustar vozes neurais, tais como Estilos de Fala, consulte [a Linguagem de Marcação de Síntese de Fala](speech-synthesis-markup.md#adjust-speaking-styles).

> [!IMPORTANT]
> A `en-US-JessaNeural` voz mudou `en-US-AriaNeural` para. Se já usaste "Jessa" antes, converte-te em "Aria".

> [!TIP]
> Pode continuar a utilizar o mapeamento de nome de serviço completo como "Microsoft Server Speech Text to Speech Voice (en-US, AriaNeural)" nos seus pedidos de síntese de fala.

### <a name="standard-voices"></a>Vozes padrão

Mais de 75 vozes padrão estão disponíveis em mais de 45 línguas e locais, que permitem converter texto em discurso sintetizado. Para obter mais informações sobre a disponibilidade regional, consulte [as regiões.](regions.md#neural-and-standard-voices)

> [!NOTE]
> Com duas exceções, as vozes padrão são criadas a partir de amostras que usam uma taxa de amostra de 16 khz.
> As vozes **en-US-AriaRUS** e **en-US-GuyRUS** também são criadas a partir de amostras que usam uma taxa de amostra de 24 khz.
> Todas as vozes podem aumentar ou reduzir para outras taxas de amostra ao sintetizar.

| Linguagem | Local (BCP-47) | Sexo | Nome de voz |
|--|--|--|--|
| Árabe (árabe) | `ar-EG` | Mulheres | `ar-EG-Hoda`|
| Árabe (Arábia Saudita) | `ar-SA` | Homens | `ar-SA-Naayf`|
| Búlgaro (Bulgária) | `bg-BG` | Homens | `bg-BG-Ivan`|
| Catalão (Espanha) | `ca-ES` | Mulheres | `ca-ES-HerenaRUS`|
| Chinês (cantonês, tradicional) | `zh-HK` | Homens | `zh-HK-Danny`|
| Chinês (cantonês, tradicional) | `zh-HK` | Mulheres | `zh-HK-TracyRUS`|
| Chinês (Mandarim, Simplificado) | `zh-CN` | Mulheres | `zh-CN-HuihuiRUS`|
| Chinês (Mandarim, Simplificado) | `zh-CN` | Homens | `zh-CN-Kangkang`|
| Chinês (Mandarim, Simplificado) | `zh-CN` | Mulheres | `zh-CN-Yaoyao`|
| Chinês (Mandarim taiwanês) |  `zh-TW` | Mulheres | `zh-TW-HanHanRUS`|
| Chinês (Mandarim taiwanês) |  `zh-TW` | Mulheres | `zh-TW-Yating`|
| Chinês (Mandarim taiwanês) |  `zh-TW` | Homens | `zh-TW-Zhiwei`|
| Croata (Croácia) | `hr-HR` | Homens | `hr-HR-Matej`|
| Checo (República Checa) | `cs-CZ` | Homens | `cs-CZ-Jakub`|
| Dinamarquês (Dinamarca) | `da-DK` | Mulheres | `da-DK-HelleRUS`|
| Neerlandês (Países Baixos) | `nl-NL` | Mulheres | `nl-NL-HannaRUS`|
| Inglês (Austrália) | `en-AU` | Mulheres | `en-AU-Catherine`|
| Inglês (Austrália) | `en-AU` | Mulheres | `en-AU-HayleyRUS`|
| Inglês (Canadá) | `en-CA` | Mulheres | `en-CA-HeatherRUS`|
| Inglês (Canadá) | `en-CA` | Mulheres | `en-CA-Linda`|
| Inglês (Índia) | `en-IN` | Mulheres | `en-IN-Heera`|
| Inglês (Índia) | `en-IN` | Mulheres | `en-IN-PriyaRUS`|
| Inglês (Índia) | `en-IN` | Homens | `en-IN-Ravi`|
| Inglês (Irlanda) | `en-IE` | Homens | `en-IE-Sean`|
| Inglês (Reino Unido) | `en-GB` | Homens | `en-GB-George`|
| Inglês (Reino Unido) | `en-GB` | Mulheres | `en-GB-HazelRUS`|
| Inglês (Reino Unido) | `en-GB` | Mulheres | `en-GB-Susan`|
| Inglês (Estados Unidos) | `en-US` | Homens | `en-US-BenjaminRUS`|
| Inglês (Estados Unidos) | `en-US` | Homens | `en-US-GuyRUS`|
| Inglês (Estados Unidos) | `en-US` | Mulheres | `en-US-AriaRUS`|
| Inglês (Estados Unidos) | `en-US` | Mulheres | `en-US-ZiraRUS`|
| Finlandês (Finlândia) | `fi-FI` | Mulheres | `fi-FI-HeidiRUS`|
| Francês (Canadá) | `fr-CA` | Mulheres | `fr-CA-Caroline`|
| Francês (Canadá) | `fr-CA` | Mulheres | `fr-CA-HarmonieRUS`|
| Francês (França) | `fr-FR` | Mulheres | `fr-FR-HortenseRUS`|
| Francês (França) | `fr-FR` | Mulheres | `fr-FR-Julie`|
| Francês (França) | `fr-FR` | Homens | `fr-FR-Paul`|
| Francês (Suíça) | `fr-CH` | Homens | `fr-CH-Guillaume`|
| Alemão (Áustria) | `de-AT` | Homens | `de-AT-Michael`|
| Alemão (Alemanha) | `de-DE` | Mulheres | `de-DE-HeddaRUS`|
| Alemão (Alemanha) | `de-DE` | Homens | `de-DE-Stefan`|
| Alemão (Suíça) | `de-CH` | Homens | `de-CH-Karsten`|
| Grego (Grécia) | `el-GR` | Homens | `el-GR-Stefanos`|
| Hebraico (Israel) | `he-IL` | Homens | `he-IL-Asaf`|
| Hindi (Índia) | `hi-IN` | Homens | `hi-IN-Hemant`|
| Hindi (Índia) | `hi-IN` | Mulheres | `hi-IN-Kalpana`|
| Húngaro (Hungria) | `hu-HU` | Homens | `hu-HU-Szabolcs`|
| Indonésio (Indonésia) | `id-ID` | Homens | `id-ID-Andika`|
| Italiano (Itália) | `it-IT` | Homens | `it-IT-Cosimo`|
| Italiano (Itália) | `it-IT` | Mulheres | `it-IT-LuciaRUS`|
| Japonês (Japão) | `ja-JP` | Mulheres | `ja-JP-Ayumi`|
| Japonês (Japão) | `ja-JP` | Mulheres | `ja-JP-HarukaRUS`|
| Japonês (Japão) | `ja-JP` | Homens | `ja-JP-Ichiro`|
| Coreano (Coreia) | `ko-KR` | Mulheres | `ko-KR-HeamiRUS`|
| Malaio (Malásia) | `ms-MY` | Homens | `ms-MY-Rizwan`|
| Norueguês (Bokmål, Noruega) | `nb-NO` | Mulheres | `nb-NO-HuldaRUS`|
| Polaco (Polónia) | `pl-PL` | Mulheres | `pl-PL-PaulinaRUS`|
| Português (Brasil) | `pt-BR` | Homens | `pt-BR-Daniel`|
| Português (Brasil) | `pt-BR` | Mulheres | `pt-BR-HeloisaRUS`|
| Português (Portugal) | `pt-PT` | Mulheres | `pt-PT-HeliaRUS`|
| Romeno (Roménia) | `ro-RO` | Homens | `ro-RO-Andrei`|
| Russo (Rússia) | `ru-RU` | Mulheres | `ru-RU-EkaterinaRUS`|
| Russo (Rússia) | `ru-RU` | Mulheres | `ru-RU-Irina`|
| Russo (Rússia) | `ru-RU` | Homens | `ru-RU-Pavel`|
| Eslovaco (Eslováquia) | `sk-SK` | Homens | `sk-SK-Filip`|
| Esloveno (Eslovénia) | `sl-SI` | Homens | `sl-SI-Lado`|
| Espanhol (México) | `es-MX` | Mulheres | `es-MX-HildaRUS`|
| Espanhol (México) | `es-MX` | Homens | `es-MX-Raul`|
| Espanhol (Espanha) | `es-ES` | Mulheres | `es-ES-HelenaRUS`|
| Espanhol (Espanha) | `es-ES` | Mulheres | `es-ES-Laura`|
| Espanhol (Espanha) | `es-ES` | Homens | `es-ES-Pablo`|
| Sueco (Suécia) | `sv-SE` | Mulheres | `sv-SE-HedvigRUS`|
| Tamil (Índia) | `ta-IN` | Homens | `ta-IN-Valluvar`|
| Telugu (Índia) | `te-IN` | Mulheres | `te-IN-Chitra`|
| Tailandês (Tailândia) | `th-TH` | Homens | `th-TH-Pattara`|
| Turco (Turquia) | `tr-TR` | Mulheres | `tr-TR-SedaRUS`|
| Vietnamita (Vietname) | `vi-VN` | Homens | `vi-VN-An` |

> [!IMPORTANT]
> A `en-US-Jessa` voz mudou `en-US-Aria` para. Se já usaste "Jessa" antes, converte-te em "Aria".

> [!TIP]
> Pode continuar a utilizar o mapeamento de nome de serviço completo como "Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)" nos seus pedidos de síntese de fala.

### <a name="customization"></a>Personalização

A Voz Personalizada está disponível no padrão e no nível neural. As línguas suportadas são diferentes para estes dois níveis. 

| Linguagem | Região | Standard | Neural |
|--|--|--|--|
| Chinês (Mandarim, Simplificado) | `zh-CN` | Yes | Yes |
| Chinês (mandarim, simplificado), inglês bilingue | `zh-CN` bilingue | Yes | Yes |
| Inglês (Austrália) | `en-AU` | No | Yes |
| Inglês (Índia) | `en-IN` | Yes | Yes |
| Inglês (Reino Unido) | `en-GB` | Yes | Yes |
| Inglês (Estados Unidos) | `en-US` | Yes | Yes |
| Francês (Canadá) | `fr-CA` | No | Yes |
| Francês (França) | `fr-FR` | Yes | Yes |
| Alemão (Alemanha) | `de-DE` | Yes | Yes |
| Italiano (Itália) | `it-IT` | Yes | Yes |
| Japonês (Japão) | `ja-JP` | No | Yes |
| Coreano (Coreia) | `ko-KR` | No | Yes |
| Português (Brasil) | `pt-BR` | Yes | Yes |
| Espanhol (México) | `es-MX` | Yes | Yes |
| Espanhol (Espanha) | `es-ES` | No | Yes |

Selecione o local certo que corresponda aos dados de treino que tem para treinar um modelo de voz personalizado. Por exemplo, se os dados de gravação que tem forem falados em inglês com sotaque britânico, selecione `en-GB` .

> [!NOTE]
> Não apoiamos a formação de modelos bilingues em Voz Personalizada, exceto para o Chinese-English bi-lingual. Selecione "bilingue chinês-inglês" se quiser treinar uma voz chinesa que também sabe falar inglês. Chinese-English formação de modelo bilingue utilizando o método padrão está disponível apenas na Europa do Norte e nos EUA. O treino de Voz Neural Personalizado está disponível no Reino Unido Sul e Leste dos EUA.

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

| Linguagem | Local (BCP-47) | Verificação dependente de texto | Verificação independente de texto | Identificação independente de texto |
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
* [Veja como reconhecer a fala em C #](./get-started-speech-to-text.md?pivots=programming-language-chsarp)
