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
ms.openlocfilehash: 0b6fea381bd6b4aa8ad3e7061e6f632176c41033
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82113838"
---
# <a name="language-and-voice-support-for-the-speech-service"></a>Apoio à linguagem e voz para o serviço da Fala

O suporte linguístico varia de acordo com a funcionalidade do serviço da Fala. As tabelas que se sintetizam o apoio linguístico às ofertas de serviços de tradução [de discurso sonâmlo,](#speech-to-text) [texto-a-fala](#text-to-speech)e [de tradução](#speech-translation) da fala.

## <a name="speech-to-text"></a>Conversão de voz em texto

Tanto o Microsoft Speech SDK como o REST API suportam os seguintes idiomas (locais). Para melhorar a precisão, a personalização é oferecida para um subconjunto das línguas através do upload de Audio + Transcrições com rótulo humano ou texto relacionado: Frases. A personalização da pronúncia `en-US` `de-DE`está atualmente disponível apenas para e . Saiba mais sobre personalização [aqui](how-to-custom-speech.md).

<!--
To get the AM and ML bits:
https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20models%3A/GetSupportedLocalesForModels

To get pronunciation bits:
https://cris.ai -> Click on Adaptation Data -> scroll down to section "Pronunciation Datasets" -> Click on Import -> Locale: the list of locales there correspond to the supported locales
-->

| Região  | Idioma                          | Suportado | Personalizações                                    |
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

Tanto o Microsoft Speech SDK como o REST APIs suportam estas vozes, cada uma das quais suporta uma linguagem e dialeto específicos, identificados pelo local.

> [!IMPORTANT]
> Os preços variam para vozes padrão, personalizadas e neurais. Visite a página [de Preços](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) para obter informações adicionais.

### <a name="neural-voices"></a>Vozes neurais

Texto-a-fala neural é um novo tipo de síntese da fala alimentada por redes neuronais profundas. Quando se usa uma voz neural, a fala sintetizada é quase indistinguível das gravações humanas.

As vozes neurais podem ser usadas para tornar as interações com chatbots e assistentes de voz mais naturais e envolventes, converter textos digitais como livros eletrónicos em audiolivros e melhorar os sistemas de navegação no carro. Com a prosódia natural humana e a clara articulação das palavras, as vozes neurais reduzem significativamente a fadiga auditiva quando os utilizadores interagem com sistemas de IA.

Para obter mais informações sobre a disponibilidade regional, consulte [regiões.](regions.md#standard-and-neural-voices)

| Região  | Idioma            | Género | Mapeamento de nome de serviço completo                                               | Nome de voz curto        |
|---------|---------------------|--------|-------------------------------------------------------------------------|-------------------------|
| `de-DE` | Alemão (Alemanha)    | Mulheres | "Texto de discurso do Servidor da Microsoft para voz de voz (de-DE, KatjaNeural)"     | "de-DE-KatjaNeural"     |
| `en-US` | Inglês (E.U.A.)        | Mulheres | "Texto de discurso do Servidor da Microsoft para voz de voz (en-EUA, AriaNeural)"      | "en-US-AriaNeural"      |
| `en-US` | Inglês (E.U.A.)        | Homens   | "Texto de discurso do Servidor da Microsoft para voz de voz (en-EUA, GuyNeural)"       | "en-US-GuyNeural"       |
| `it-IT` | Italiano (Itália)     | Mulheres | "Texto de discurso do Servidor da Microsoft para voz de voz (it-IT, ElsaNeural)"      | "IT-IT-ElsaNeural"      |
| `pt-BR` | Português (Brasil) | Mulheres | "Texto de discurso do Servidor da Microsoft para voz de voz (pt-BR, FranciscaNeural)" | "pt-BR-FranciscaNeural" |
| `zh-CN` | Chinês (mandarim, simplificado)  | Mulheres | "Texto de discurso do Servidor da Microsoft para voz de voz (zh-CN, XiaoxiaoNeural)"  | "zh-CN-XiaoxiaoNeural"  |

> [!IMPORTANT]
> A `en-US-JessaNeural` voz mudou para. `en-US-AriaNeural` Se já estavas a usar "Jessa", convertes-te em "Aria".

Para saber como pode configurar e ajustar vozes neurais, consulte a linguagem de marcação da [síntese da fala.](speech-synthesis-markup.md#adjust-speaking-styles)

> [!TIP]
> Pode utilizar o mapeamento completo do nome do serviço ou o nome de voz curto nos pedidos de síntese da sua fala.

### <a name="standard-voices"></a>Vozes padrão

Mais de 75 vozes padrão estão disponíveis em mais de 45 línguas e locais, que permitem converter texto em discurso sintetizado. Para obter mais informações sobre a disponibilidade regional, consulte [regiões.](regions.md#standard-and-neural-voices)

| Região | Idioma | Género | Mapeamento de nome de serviço completo | Nome abreviado |
|--|--|--|--|--|
| <sup>1</sup>`ar-EG` | Árabe (Egito) | Mulheres | "Texto de discurso do Servidor da Microsoft para voz de voz de voz (ar-EG, Hoda)" | "ar-EG-Hoda" |
| `ar-SA` | Árabe (Arábia Saudita) | Homens | "Texto de discurso do Servidor da Microsoft para voz de voz (ar-SA, Naayf)" | "ar-SA-Naayf" |
| `bg-BG` | Búlgaro | Homens | "Texto de discurso do Servidor da Microsoft para voz de voz (bg-BG, Ivan)" | "bg-BG-Ivan" |
| `ca-ES` | Catalão (Espanha) | Mulheres | "Texto de discurso do Servidor da Microsoft para voz de voz de voz (ca-ES, HerenaRUS)" | "ca-ES-HerenaRUS" |
| `cs-CZ` | Checo | Homens | "Texto de discurso do Servidor da Microsoft para voz de voz (cs-CZ, Jakub)" | "cs-CZ-Jakub" |
| `da-DK` | Dinamarquês | Mulheres | "Texto de discurso do Servidor da Microsoft para voz de voz (da-DK, HelleRUS)" | "da-DK-HelleRUS" |
| `de-AT` | Alemão (Áustria) | Homens | "Texto de discurso do Servidor da Microsoft para voz de voz de voz (de-AT, Michael)" | "de-AT-Michael" |
| `de-CH` | Alemão (Suíça) | Homens | "Texto de discurso do Servidor da Microsoft para voz de voz (de-CH, Karsten)" | "de-CH-Karsten" |
| `de-DE` | Alemão (Alemanha) | Mulheres | "Texto de discurso do Servidor da Microsoft para voz de voz de voz (de-DE, Hedda)" | "de-DE-Hedda" |
|  |  | Mulheres | "Texto de discurso do Servidor da Microsoft para voz de voz de voz (de-DE, HeddaRUS)" | "de-DE-HeddaRUS" |
|  |  | Homens | "Texto de discurso do Servidor da Microsoft para voz de voz (de-DE, Stefan, Apollo)" | "de-DE-Stefan-Apollo" |
| `el-GR` | Grego | Homens | "Texto de discurso do Servidor da Microsoft para voz de voz (el-GR, Stefanos)" | "el-GR-Stefanos" |
| `en-AU` | Inglês (Austrália) | Mulheres | "Texto de discurso do Servidor da Microsoft para voz de voz (en-UA, Catherine)" | "en-AU-Catherine" |
|  |  | Mulheres | "Texto de discurso do Servidor da Microsoft para voz de voz (en-AU, HayleyRUS)" | "en-AU-HayleyRUS" |
| `en-CA` | Inglês (Canadá) | Mulheres | "Texto de discurso do Servidor da Microsoft para voz de voz (en-CA, Linda)" | "en-CA-Linda" |
|  |  | Mulheres | "Texto de discurso do Servidor da Microsoft para voz de voz (en-CA, HeatherRUS)" | "en-CA-HeatherRUS" |
| `en-GB` | Inglês (Reino Unido) | Mulheres | "Texto de discurso do Servidor da Microsoft para voz de voz (en-GB, Susan, Apollo)" | "en-GB-Susan-Apollo" |
|  |  | Mulheres | "Texto de discurso do Servidor da Microsoft para voz de voz (en-GB, HazelRUS)" | "en-GB-HazelRUS" |
|  |  | Homens | "Texto de discurso do Servidor da Microsoft para voz de voz (en-GB, George, Apollo)" | "en-GB-George-Apollo" |
| `en-IE` | Inglês (Irlanda) | Homens | "Texto de discurso do Servidor da Microsoft para voz de voz (en-IE, Sean)" | "en-IE-Sean" |
| `en-IN` | Inglês (Índia) | Mulheres | "Texto de discurso do Servidor da Microsoft para voz de voz (en-IN, Heera, Apollo)" | "en-IN-Heera-Apollo" |
|  |  | Mulheres | "Texto de discurso do Servidor da Microsoft para voz de voz (en-IN, PriyaRUS)" | "en-IN-PriyaRUS" |
|  |  | Homens | "Texto de discurso do Servidor da Microsoft para voz de voz (en-IN, Ravi, Apollo)" | "en-IN-Ravi-Apollo" |
| `en-US` | Inglês (E.U.A.) | Mulheres | "Texto de discurso do Servidor da Microsoft para voz de voz (en-EUA, ZiraRUS)" | "en-US-ZiraRUS" |
|  |  | Mulheres | "Texto de discurso do Servidor da Microsoft para voz de voz (en-EUA, AriaRUS)" | "en-US-AriaRUS" |
|  |  | Homens | "Texto de discurso do Microsoft Server para voz de voz de voz (en-EUA, BenjaminRUS)" | "en-US-BenjaminRUS" |
|  |  | Homens | "Texto de discurso do Microsoft Server para voz de voz de voz (en-EUA, Guy24kRUS)" | "en-US-Guy24kRUS" |
| `es-ES` | Espanhol (Espanha) | Mulheres | "Texto de discurso do Servidor da Microsoft para voz de voz (es-ES, Laura, Apollo)" | "es-ES-Laura-Apollo" |
|  |  | Mulheres | "Texto de discurso do Servidor da Microsoft para voz de voz (es-ES, HelenaRUS)" | "es-ES-HelenaRUS" |
|  |  | Homens | "Texto de discurso do Servidor da Microsoft para voz de voz (es-ES, Pablo, Apollo)" | "es-ES-Pablo-Apollo" |
| `es-MX` | Espanhol (México) | Mulheres | "Texto de discurso do Servidor da Microsoft para voz de voz (es-MX, HildaRUS)" | "es-MX-HildaRUS" |
|  |  | Homens | "Texto de discurso do Servidor da Microsoft para voz de voz (es-MX, Raul, Apollo)" | "es-MX-Raul-Apollo" |
| `fi-FI` | Finlandês | Mulheres | "Texto de discurso do Servidor da Microsoft para voz de voz (fi-FI, HeidiRUS)" | "FI-HeidiRUS" |
| `fr-CA` | Francês (Canadá) | Mulheres | "Texto de discurso do Servidor da Microsoft para voz de voz (fr-CA, Caroline)" | "fr-CA-Caroline" |
|  |  | Mulheres | "Texto de discurso do Servidor da Microsoft para voz de voz (fr-CA, HarmonieRUS)" | "fr-CA-HarmonieRUS" |
| `fr-CH` | Francês (Suíça) | Homens | "Texto de discurso do Servidor da Microsoft para voz de voz (fr-CH, Guillaume)" | "fr-CH-Guillaume" |
| `fr-FR` | Francês (França) | Mulheres | "Texto de discurso do Servidor da Microsoft para voz de voz (fr-FR, Julie, Apollo)" | "FR-FR-Julie-Apollo" |
|  |  | Mulheres | "Texto de discurso do Servidor da Microsoft para voz de voz (fr-FR, HortenseRUS)" | "FR-FR-HortenseRUS" |
|  |  | Homens | "Texto de discurso do Servidor da Microsoft para voz de voz (fr-FR, Paul, Apollo)" | "FR-FR-Paul-Apollo" |
| `he-IL` | Hebraico (Israel) | Homens | "Texto de discurso do Servidor da Microsoft para voz de voz (he-IL, Asaf)" | "he-IL-Asaf" |
| `hi-IN` | Hindi (Índia) | Mulheres | "Texto de discurso do Servidor da Microsoft para voz de voz (hi-IN, Kalpana, Apollo)" | "Hi-IN-Kalpana-Apollo" |
|  |  | Mulheres | "Texto de discurso do Servidor da Microsoft para voz de voz (hi-IN, Kalpana)" | "Hi-IN-Kalpana" |
|  |  | Homens | "Texto de discurso do Servidor da Microsoft para voz de voz (hi-IN, Hemant)" | "Hi-IN-Hemant" |
| `hr-HR` | Croata | Homens | "Texto de discurso do Servidor da Microsoft para voz de voz (hr-HR, Matej)" | "HR-HR-Matej" |
| `hu-HU` | Húngaro | Homens | "Texto de discurso do Servidor da Microsoft para voz de voz (hu-HU, Szabolcs)" | "hu-HU-Szabolcs" |
| `id-ID` | Indonésio | Homens | "Texto de discurso do Servidor da Microsoft para voz de voz (id-ID, Andika)" | "ID-ID-Andika" |
| `it-IT` | Italiano | Homens | "Texto de discurso do Servidor da Microsoft para voz de voz (it-IT, Cosimo, Apollo)" | "IT-IT-Cosimo-Apollo" |
|  |  | Mulheres | "Texto de discurso do Servidor da Microsoft para voz de voz (it-IT, LuciaRUS)" | "IT-IT-LuciaRUS" |
| `ja-JP` | Japonês | Mulheres | "Texto de discurso do Servidor da Microsoft para voz de voz (ja-JP, Ayumi, Apollo)" | "ja-JP-Ayumi-Apollo" |
|  |  | Homens | "Texto de discurso do Servidor da Microsoft para voz de voz (ja-JP, Ichiro, Apollo)" | "ja-JP-Ichiro-Apollo" |
|  |  | Mulheres | "Texto de discurso do Servidor da Microsoft para voz de voz (ja-JP, HarukaRUS)" | "ja-JP-HarukaRUS" |
| `ko-KR` | Coreano | Mulheres | "Texto de discurso do Servidor da Microsoft para voz de voz de voz (ko-KR, HeamiRUS)" | "ko-KR-HeamiRUS" |
| `ms-MY` | Malaio | Homens | "Texto de discurso do Servidor da Microsoft para voz de voz (ms-MY, Rizwan)" | "Ms-MY-Rizwan" |
| `nb-NO` | Norueguês | Mulheres | "Texto de discurso do Servidor da Microsoft para voz de voz (nb-NO, HuldaRUS)" | "nb-NO-HuldaRUS" |
| `nl-NL` | Neerlandês | Mulheres | "Texto de discurso do Servidor da Microsoft para voz de voz (nl-NL, HannaRUS)" | "NL-NL-HannaRUS" |
| `pl-PL` | Polaco | Mulheres | "Texto de discurso do Servidor da Microsoft para voz de voz (pl-PL, PaulinaRUS)" | "pl-PL-PaulinaRUS" |
| `pt-BR` | Português (Brasil) | Mulheres | "Texto de discurso do Servidor da Microsoft para voz de voz (pt-BR, HeloisaRUS)" | "pt-BR-HeloisaRUS" |
|  |  | Homens | "Texto de discurso do Servidor da Microsoft para voz de voz (pt-BR, Daniel, Apollo)" | "pt-BR-Daniel-Apollo" |
| `pt-PT` | Português (Portugal) | Mulheres | "Texto de discurso do Servidor da Microsoft para voz de voz (pt-PT, HeliaRUS)" | "pt-PT-HeliaRUS" |
| `ro-RO` | Romeno | Homens | "Texto de discurso do Servidor da Microsoft para voz de voz (ro-RO, Andrei)" | "ro-RO-Andrei" |
| `ru-RU` | Russo | Mulheres | "Texto de discurso do Microsoft Server para voz de voz (ru-RU, Irina, Apollo)" | "ru-RU-Irina-Apollo" |
|  |  | Homens | "Texto de discurso do Servidor da Microsoft para voz de voz (ru-RU, Pavel, Apollo)" | "ru-RU-Pavel-Apollo" |
|  |  | Mulheres | "Texto de discurso do Servidor da Microsoft para voz de voz (ru-RU, EkaterinaRUS)" | ru-RU-EkaterinaRUS |
| `sk-SK` | Eslovaco | Homens | "Texto de discurso do Servidor da Microsoft para voz de voz (sk-SK, Filip)" | "Sk-SK-Filip" |
| `sl-SI` | Esloveno | Homens | "Texto de discurso do Servidor da Microsoft para voz de voz (sl-SI, Lado)" | "sl-SI-Lado" |
| `sv-SE` | Sueco | Mulheres | "Texto de discurso do Servidor da Microsoft para voz de voz (sv-SE, HedvigRUS)" | "sv-SE-HedvigRUS" |
| `ta-IN` | Tamil (Índia) | Homens | "Texto de discurso do Servidor da Microsoft para voz de voz (ta-IN, Valluvar)" | "ta-IN-Valluvar" |
| `te-IN` | Telugu (Índia) | Mulheres | "Texto de discurso do Servidor da Microsoft para voz de voz (te-IN, Chitra)" | "te-IN-Chitra" |
| `th-TH` | Tailandês | Homens | "Texto de discurso do Servidor da Microsoft para voz de voz (th-TH, Pattara)" | "TH-TH-Pattara" |
| `tr-TR` | Turco (Turquia) | Mulheres | "Texto de discurso do Servidor da Microsoft para voz de voz (tr-TR, SedaRUS)" | "Tr-TR-SedaRUS" |
| `vi-VN` | Vietnamita | Homens | "Texto de discurso do Servidor da Microsoft para voz de voz (vi-VN, An)" | "vi-VN-An" |
| `zh-CN` | Chinês (mandarim, simplificado) | Mulheres | "Texto de discurso do Servidor da Microsoft para voz de voz (zh-CN, HuihuiRUS)" | "zh-CN-HuihuiRUS" |
|  |  | Mulheres | "Texto de discurso do Microsoft Server para voz de voz (zh-CN, Yaoyao, Apollo)" | "zh-CN-Yaoyao-Apollo" |
|  |  | Homens | "Texto de discurso do Servidor da Microsoft para voz de voz (zh-CN, Kangkang, Apollo)" | "zh-CN-Kangkang-Apollo" |
| `zh-HK` | Chinês (cantonês, tradicional) | Mulheres | "Texto de discurso do Microsoft Server para voz de voz (zh-HK, Tracy, Apollo)" | "zh-HK-Tracy-Apollo" |
|  |  | Mulheres | "Texto de discurso do Servidor da Microsoft para voz de voz (zh-HK, TracyRUS)" | "zh-HK-TracyRUS" |
|  |  | Homens | "Texto de discurso do Microsoft Server para voz de voz de voz (zh-HK, Danny, Apollo)" | "zh-HK-Danny-Apollo" |
| `zh-TW` | Chinês (Mandarim taiwanês) | Mulheres | "Texto de discurso do Servidor da Microsoft para voz de voz (zh-TW, Yating, Apollo)" | "zh-TW-Yating-Apollo" |
|  |  | Mulheres | "Texto de discurso do Servidor da Microsoft para voz de voz (zh-TW, HanHanRUS)" | "zh-TW-HanHanRUS" |
|  |  | Homens | "Texto de discurso do Microsoft Server para voz de voz (zh-TW, Zhiwei, Apollo)" | "zh-TW-Zhiwei-Apollo" |

**1** *ar-EG suporta o árabe moderno padrão (MSA).*

> [!IMPORTANT]
> A `en-US-Jessa` voz mudou para. `en-US-Aria` Se já estavas a usar "Jessa", convertes-te em "Aria".

> [!TIP]
> Pode utilizar o mapeamento completo do nome do serviço ou o nome de voz curto nos pedidos de síntese da sua fala.

### <a name="customization"></a>Personalização

A personalização por `de-DE` `en-GB`voz `en-IN` `en-US`está `es-MX` `fr-FR`disponível para, , , , , `it-IT`e `pt-BR` `zh-CN`. Selecione o local certo que corresponda aos dados de treino que tem para treinar um modelo de voz personalizado. Por exemplo, se os dados de gravação que tiver `en-GB`tiver forem falados em inglês com sotaque britânico, selecione .

> [!NOTE]
> Não apoiamos o treino de modelos bilingues em Voz Personalizada, exceto para o bilingchinês chinês-inglês. Selecione "bilingue chinês-inglês" se quiser treinar uma voz chinesa que também fale inglês. A formação de voz em todos os locais começa com um conjunto `en-US` `zh-CN` de dados de mais de 2.000 expressões, exceto para o e onde você pode começar com qualquer tamanho de dados de treino.

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
