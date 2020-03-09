---
title: Apoio linguístico - Serviço de fala
titleSuffix: Azure Cognitive Services
description: O serviço da Fala apoia numerosas línguas para a conversão fala-a-texto e texto-a-fala, juntamente com a tradução da fala. Este artigo fornece uma lista completa de suporte linguístico por recurso de serviço.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: dapine
ms.custom: seodec18
ms.openlocfilehash: 13d12bfbf64af9e19b66e63c88c9d7e0534571f3
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78379027"
---
# <a name="language-and-region-support-for-the-speech-service"></a>Apoio à língua e à região para o serviço da Fala

O suporte linguístico varia de acordo com a funcionalidade do serviço da Fala. As tabelas que se sintetizam o apoio linguístico às ofertas de serviços de tradução [de discurso sonâmlo,](#speech-to-text) [texto-a-fala](#text-to-speech)e [de tradução](#speech-translation) da fala.

## <a name="speech-to-text"></a>Conversão de voz em texto

Tanto o Microsoft Speech SDK como o REST API suportam os seguintes idiomas (locais). Para melhorar a precisão, a personalização é oferecida para um subconjunto das línguas através do upload de Audio + Transcrições com rótulo humano ou texto relacionado: Frases. Apersonalização da pronúncia está atualmente disponível apenas para `en-US` e `de-DE`. Saiba mais sobre personalização [aqui](how-to-custom-speech.md).

<!--
To get the AM and ML bits:
https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20models%3A/GetSupportedLocalesForModels

To get pronunciation bits:
https://cris.ai -> Click on Adaptation Data -> scroll down to section "Pronunciation Datasets" -> Click on Import -> Locale: the list of locales there correspond to the supported locales
-->

 Região | Idioma | Suportado | Personalizações
------|------------|-----------|-------------
`ar-AE` | Árabe (EAU) | Sim | Não
`ar-BH` | Árabe (Bahrein), padrão moderno | Sim | Modelo de linguagem
`ar-EG` | Árabe (Egito) | Sim | Modelo de linguagem
`ar-KW` | Árabe (Kuwait) | Sim | Não
`ar-QA` | Árabe (Qatar) | Sim | Não
`ar-SA` | Árabe (Arábia Saudita) | Sim | Não
`ca-ES` | Catalão | Sim | Modelo de linguagem
`da-DK` | Dinamarquês (Dinamarca) | Sim | Modelo de linguagem
`de-DE` | Alemão (Alemanha) | Sim | Modelo acústico<br>Modelo de linguagem<br>Pronúncia
`en-AU` | Inglês (Austrália) | Sim | Modelo acústico<br>Modelo de linguagem
`en-CA` | Inglês (Canadá) | Sim | Modelo acústico<br>Modelo de linguagem
`en-GB` | Inglês (Reino Unido) | Sim | Modelo acústico<br>Modelo de linguagem<br>Pronúncia
`en-IN` | English (India) | Sim | Modelo acústico<br>Modelo de linguagem
`en-NZ` | Inglês (Nova Zelândia) | Sim | Modelo acústico<br>Modelo de linguagem
`en-US` | Inglês (Estados Unidos) | Sim | Modelo acústico<br>Modelo de linguagem<br>Pronúncia
`es-ES` | Espanhol (Espanha) | Sim | Modelo acústico<br>Modelo de linguagem
`es-MX` | Espanhol (México) | Sim | Modelo acústico<br>Modelo de linguagem
`fi-FI` | Finlandês (Finlândia) | Sim | Modelo de linguagem
`fr-CA` | Francês (Canadá) | Sim | Modelo acústico<br>Modelo de linguagem
`fr-FR` | Francês (França) | Sim | Modelo acústico<br>Modelo de linguagem<br>Pronúncia
`gu-IN` | Gujarati (Indiano) | Sim | Modelo de linguagem
`hi-IN` | Hindi (Índia) | Sim | Modelo acústico<br>Modelo de linguagem
`it-IT` | Italiano (Itália) | Sim | Modelo acústico<br>Modelo de linguagem<br>Pronúncia
`ja-JP` | Japonês (Japão) | Sim | Modelo de linguagem
`ko-KR` | Coreano (Coreia) | Sim | Modelo de linguagem
`mr-IN` | Marathi (Índia) | Sim | Modelo de linguagem
`nb-NO` | Norueguês (Bokmål) (Noruega) | Sim | Modelo de linguagem
`nl-NL` | Holandês (Países Baixos) | Sim | Modelo de linguagem
`pl-PL` | Polaco (Polónia) | Sim | Modelo de linguagem
`pt-BR` | Português (Brasil) | Sim | Modelo acústico<br>Modelo de linguagem<br>Pronúncia
`pt-PT` | Português (Portugal) | Sim | Modelo de linguagem
`ru-RU` | Russo (Rússia) | Sim | Modelo acústico<br>Modelo de linguagem
`sv-SE` | Sueco (Suécia) | Sim | Modelo de linguagem
`ta-IN` | Tamil (Índia) | Sim | Modelo de linguagem
`te-IN` | Telugu (Índia) | Sim | Não
`th-TH` | Tailandês (Tailândia) | Sim | Não
`tr-TR` | Turco (Turquia) | Sim | Não
`zh-CN` | Chinês (simplificado Mandarim) | Sim | Modelo acústico<br>Modelo de linguagem
`zh-HK` | Chinês (cantonês, tradicional) | Sim | Modelo de linguagem
`zh-TW` | Chinês (Taiwanês Mandarim) | Sim | Modelo de linguagem

## <a name="text-to-speech"></a>Conversão de texto em voz

Tanto o Microsoft Speech SDK como o REST APIs suportam estas vozes, cada uma das quais suporta uma linguagem e dialeto específicos, identificados pelo local.

> [!IMPORTANT]
> Os preços variam para vozes padrão, personalizadas e neurais. Visite a página [de Preços](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) para obter informações adicionais.

### <a name="neural-voices"></a>Vozes neurais

Texto-a-fala neural é um novo tipo de síntese da fala alimentada por redes neuronais profundas. Quando se usa uma voz neural, a fala sintetizada é quase indistinguível das gravações humanas.

As vozes neurais podem ser usadas para tornar as interações com chatbots e assistentes de voz mais naturais e envolventes, converter textos digitais como livros eletrónicos em audiolivros e melhorar os sistemas de navegação no carro. Com a prosódia natural humana e a clara articulação das palavras, as vozes neurais reduzem significativamente a fadiga auditiva quando os utilizadores interagem com sistemas de IA.

Para obter mais informações sobre a disponibilidade regional, consulte [regiões.](regions.md#standard-and-neural-voices)

Região | Idioma | Género | Mapeamento de nome de serviço completo | Nome de voz curto
--------|----------|--------|---------|------------
`de-DE` | Alemão (Alemanha) | Feminino | "Texto de discurso do Servidor da Microsoft para voz de voz (de-DE, KatjaNeural)" | "de-DE-KatjaNeural"
`en-US` | English (US) | Feminino | "Texto de discurso do Microsoft Server para voz de voz (en-EUA, JessaNeural)" | "en-US-JessaNeural"
`en-US` | English (US) | Masculino | "Texto de discurso do Servidor da Microsoft para voz de voz (en-EUA, GuyNeural)" | "en-US-GuyNeural"
`it-IT` | Italiano (Itália) | Feminino |"Texto de discurso do Servidor da Microsoft para voz de voz (it-IT, ElsaNeural)" | "IT-IT-ElsaNeural"
`pt-BR` | Português (Brasil) | Feminino | "Texto de discurso do Servidor da Microsoft para voz de voz (pt-BR, FranciscaNeural)" | "pt-BR-FranciscaNeural"
`zh-CN` | Chinês (continental) | Feminino | "Texto de discurso do Servidor da Microsoft para voz de voz (zh-CN, XiaoxiaoNeural)" | "zh-CN-XiaoxiaoNeural"

Para saber como pode configurar e ajustar vozes neurais, consulte a linguagem de marcação da [síntese da fala.](speech-synthesis-markup.md#adjust-speaking-styles)

> [!NOTE]
> Pode utilizar o mapeamento completo do nome do serviço ou o nome de voz curto nos pedidos de síntese da sua fala.

### <a name="standard-voices"></a>Vozes padrão

Mais de 75 vozes padrão estão disponíveis em mais de 45 línguas e locais, que permitem converter texto em discurso sintetizado. Para obter mais informações sobre a disponibilidade regional, consulte [regiões.](regions.md#standard-and-neural-voices)

Região | Idioma | Género | Mapeamento de nome de serviço completo | Nome abreviado
-------|----------|---------|----------|----------
<sup>1</sup>`ar-EG` | Árabe (Egito) | Feminino | "Microsoft Server voz texto para voz de voz (ar-por exemplo, Hoda)" | "ar-EG-Hoda"
`ar-SA` | Árabe (Arábia Saudita) | Masculino | "Microsoft Server voz texto para voz de voz (ar-SA, Naayf)" | "ar-SA-Naayf"
`bg-BG` | Búlgaro | Masculino | "Microsoft Server voz texto em voz voz (bg-BG, Ivan)" | "bg-BG-Ivan"
`ca-ES` | Catalão | Feminino | "Microsoft Server voz texto em voz voz (AC-ES, HerenaRUS)" | "ca-ES-HerenaRUS"
`cs-CZ` | Checo | Masculino | "Microsoft Server voz texto para voz de voz (cs-CZ, Jakub)" | "cs-CZ-Jakub"
`da-DK` | Dinamarquês | Feminino | "Microsoft Server voz texto para voz de voz (da-DK, HelleRUS)" | "da-DK-HelleRUS"
`de-AT` | Alemão (Áustria) | Masculino | "Microsoft Server voz texto para voz de voz (Alemanha-AT, Michael)" | "de-AT-Michael"
`de-CH` | Alemão (Suíça) | Masculino | "Microsoft Server voz texto para voz de voz (Alemanha-CH, Karsten)" | "de-CH-Karsten"
`de-DE` | Alemão (Alemanha) | Feminino | "Microsoft Server voz texto para voz de voz (de-DE, Hedda)" | "de-DE-Hedda"
| | | Feminino | "Microsoft Server voz texto para voz de voz (de-DE, HeddaRUS)" | "de-DE-HeddaRUS"
| | | Masculino | "Microsoft Server voz texto para voz de voz (de-DE, Stefan, Apollo)" | "de-DE-Stefan-Apollo"
`el-GR` | Grego | Masculino | "Microsoft Server voz texto para voz de voz (el-GR, Stefanos)" | "el-GR-Stefanos"
`en-AU` | Inglês (Austrália) | Feminino | "Microsoft Server voz texto para voz de voz (en-AU, Catherine)" | "en-AU-Catherine"
| | | Feminino | "Microsoft Server voz texto para voz de voz (en-AU, HayleyRUS)" | "en-AU-HayleyRUS"
`en-CA` | Inglês (Canadá) | Feminino | "Microsoft Server voz texto para voz de voz (en-CA, Linda)" | "en-CA-Linda"
| | | Feminino | "Microsoft Server voz texto para voz de voz (en-CA, HeatherRUS)" | "en-CA-HeatherRUS"
`en-GB` | English (UK) | Feminino | "Microsoft Server voz texto para voz de voz (en-GB, Susan, Apollo)" | "en-GB-Susan-Apollo"
| | | Feminino | "Microsoft Server voz texto para voz de voz (en-GB, HazelRUS)" | "en-GB-HazelRUS"
| | | Masculino | "Microsoft Server voz texto para voz de voz (en-GB, George, Apollo)" | "en-GB-George-Apollo"
`en-IE` | Inglês (Irlanda) | Masculino | "Microsoft Server voz texto para voz de voz (en-IE, Sean)" | "en-IE-Sean"
`en-IN` | English (India) | Feminino | "Microsoft Server voz texto para voz de voz (en-IN, Heera, Apollo)" | "en-IN-Heera-Apollo"
| | | Feminino | "Microsoft Server voz texto para voz de voz (en-IN, PriyaRUS)" | "en-IN-PriyaRUS"
| | | Masculino | "Microsoft Server voz texto para voz de voz (en-IN, Ravi, Apollo)" | "en-IN-Ravi-Apollo"
`en-US` | English (US) | Feminino | "Microsoft Server voz texto para voz de voz (en-US, ZiraRUS)" | "en-US-ZiraRUS"
| | | Feminino | "Microsoft Server voz texto para voz de voz (en-US, JessaRUS)" | "en-US-JessaRUS"
| | | Masculino | "Microsoft Server voz texto para voz de voz (en-US, BenjaminRUS)" | "en-US-BenjaminRUS"
| | | Feminino | "Microsoft Server voz texto para voz de voz (en-US, Jessa24kRUS)" | "en-US-Jessa24kRUS"
| | | Masculino | "Microsoft Server voz texto para voz de voz (en-US, Guy24kRUS)" | "en-US-Guy24kRUS"
`es-ES` | Espanhol (Espanha) |Feminino | "Microsoft Server voz texto em voz voz (es-ES, Laura, Apollo)" | "es-ES-Laura-Apollo"
| | | Feminino | "Microsoft Server voz texto em voz voz (es-ES, HelenaRUS)" | "es-ES-HelenaRUS"
| | | Masculino | "Microsoft Server voz texto em voz voz (es-ES, Pablo, Apollo)" | "es-ES-Pablo-Apollo"
`es-MX` | Espanhol (México) | Feminino | "Voz de texto em voz de voz do Microsoft Server (es-MX, HildaRUS)" | "es-MX-HildaRUS"
| | | Masculino | "Voz de texto em voz de voz do Microsoft Server (es-MX, Raul, Apollo)" | "es-MX-Raul-Apollo"
`fi-FI` | Finlandês | Feminino | "Microsoft Server voz texto para voz de voz (fi-FI, HeidiRUS)" | "fi-FI-HeidiRUS"
`fr-CA` | Francês (Canadá) |Feminino | "Microsoft Server voz texto em voz voz (fr-AC, Caroline)" | "fr-CA-Caroline"
| | | Feminino | "Microsoft Server voz texto em voz voz (fr-AC, HarmonieRUS)" | "fr-CA-HarmonieRUS"
`fr-CH` | Francês (Suíça)| Masculino | "Microsoft Server voz texto em voz voz (fr-CH, Guillaume)" | "fr-CH-Guillaume"
`fr-FR` | Francês (França)| Feminino | "Microsoft Server voz texto em voz voz (fr-FR, Julie, Apollo)" | "FR-FR-Julie-Apollo"
| | | Feminino | "Microsoft Server voz texto em voz voz (fr-FR, HortenseRUS)" | "FR-FR-HortenseRUS"
| | | Masculino | "Microsoft Server voz texto em voz voz (fr-FR, Paul, Apollo)" | "FR-FR-Paul-Apollo"
`he-IL` | Hebraico (Israel) | Masculino| "Microsoft Server voz texto para voz de voz (he-IL, Asaf)" | "he-IL-Asaf"
`hi-IN` | Hindi (Índia) | Feminino | "Microsoft Server voz texto para voz de voz (hi-IN, Kalpana, Apollo)" | "Hi-IN-Kalpana-Apollo"
| | | Feminino | "Microsoft Server voz texto para voz de voz (hi-IN, Kalpana)" | "Hi-IN-Kalpana"
| | | Masculino | "Microsoft Server voz texto para voz de voz (hi-IN, Hemant)" | "Hi-IN-Hemant"
`hr-HR` | Croata | Masculino | "Microsoft Server voz texto em voz voz (RH-RH, Matej)" | "HR-HR-Matej"
`hu-HU` | Húngaro | Masculino | "Microsoft Server voz texto para voz de voz (hu-HU, Szabolcs)" | "hu-HU-Szabolcs"
`id-ID` | Indonésio| Masculino | "Microsoft Server voz texto para voz de voz (id-ID, Andika)" | "ID-ID-Andika"
`it-IT` | Italiano | Masculino | "Microsoft Server voz texto para voz de voz (it-IT, Cosimo, Apollo)" | "IT-IT-Cosimo-Apollo"
| | | Feminino | "Microsoft Server voz texto para voz de voz (it-IT, LuciaRUS)" | "IT-IT-LuciaRUS"
`ja-JP` | Japonês | Feminino | "Microsoft Server voz texto em voz voz (ja-JP, Ayumi, Apollo)" | "ja-JP-Ayumi-Apollo"
| | | Masculino | "Microsoft Server voz texto em voz voz (ja-JP, Ichiro, Apollo)" | "ja-JP-Ichiro-Apollo"
| | | Feminino | "Microsoft Server voz texto em voz voz (ja-JP, HarukaRUS)" | "ja-JP-HarukaRUS"
`ko-KR` | Coreano | Feminino | "Microsoft Server voz texto para voz de voz (ko-KR, HeamiRUS)" | "ko-KR-HeamiRUS"
`ms-MY` | Malaio | Masculino | "Voz de texto em voz de voz do Microsoft Server (ms-MY, Rizwan)" | "Ms-MY-Rizwan"
`nb-NO` | Norueguês | Feminino | "Voz de texto em voz de voz do Microsoft Server (não-nb, HuldaRUS)" | "nb-NO-HuldaRUS"
`nl-NL` | Neerlandês | Feminino | "Microsoft Server voz texto em voz voz (nl-NL, HannaRUS)" | "nl-NL-HannaRUS"
`pl-PL` | Polaco | Feminino | "Microsoft Server voz texto em voz voz (pl-PL, PaulinaRUS)" | "pl-PL-PaulinaRUS"
`pt-BR` | Português (Brasil) | Feminino | "Microsoft Server voz texto em voz voz (pt-BR, HeloisaRUS)" | "pt-BR-HeloisaRUS"
| | | Masculino |"Microsoft Server voz texto em voz voz (pt-BR, Daniel, Apollo)" | "pt-BR-Daniel-Apollo"
`pt-PT` | Português (Portugal) | Feminino | "Microsoft Server voz texto em voz voz (pt-PT, HeliaRUS)" | "pt-PT-HeliaRUS"
`ro-RO` | Romeno | Masculino | "Microsoft Server voz texto para voz de voz (ro-RO, Andrei)" | "ro-RO-Andrei"
`ru-RU` |Russo| Feminino | "Microsoft Server voz texto em voz voz (ru-RU, Irina, Apollo)" | "ru-RU-Irina-Apollo"
| | | Masculino | "Microsoft Server voz texto em voz voz (ru-RU, Pavel, Apollo)" | "ru-RU-Pavel-Apollo"
| | | Feminino | "Microsoft Server voz texto em voz voz (ru-RU, EkaterinaRUS)" | ru-RU-EkaterinaRUS
`sk-SK` | Eslovaco | Masculino | "Microsoft Server voz texto em voz voz (sk-SK, Filip)" | "Sk-SK-Filip"
`sl-SI` | Esloveno | Masculino | "Microsoft Server voz texto em voz voz (sl-SI, Lado)" | "sl-SI-Lado"
`sv-SE` | Sueco | Feminino | "Microsoft Server voz texto para voz de voz (sv-SE, HedvigRUS)" | "sv-SE-HedvigRUS"
`ta-IN` | Tamil (Índia) | Masculino | "Microsoft Server voz texto para voz de voz (dados-IN, Valluvar)" | "ta-IN-Valluvar"
`te-IN` | Telugu (Índia) | Feminino | "Microsoft Server voz texto para voz de voz (te-IN, Chitra)" | "te-IN-Chitra"
`th-TH` | Tailandês | Masculino | "Microsoft Server voz texto em voz voz (th-TH, Pattara)" | "TH-TH-Pattara"
`tr-TR` | Turco (Turquia) | Feminino | "Microsoft Server voz texto em voz voz (tr-TR, SedaRUS)" | "tr-TR-SedaRUS"
`vi-VN` | Vietnamita | Masculino | "Voz de texto em voz de voz do Microsoft Server (vi-VN, um)" | "vi-VN-An"
`zh-CN` | Chinês (continental) | Feminino | "Microsoft Server voz texto para voz de voz (zh-CN, HuihuiRUS)" | "zh-CN-HuihuiRUS"
| | | Feminino | "Microsoft Server voz texto para voz de voz (zh-CN, Yaoyao, Apollo)" | "zh-CN-Yaoyao-Apollo"
| | | Masculino | "Microsoft Server voz texto para voz de voz (zh-CN, Kangkang, Apollo)" | "zh-CN-Kangkang-Apollo"
`zh-HK` | Chinês (Hong Kong) | Feminino | "Microsoft Server voz texto para voz de voz (zh-HK Tracy, Apollo)" | "zh-HK-Tracy-Apollo"
| | | Feminino | "Microsoft Server voz texto para voz de voz (zh-HK TracyRUS)" | "zh-HK-TracyRUS"
| | | Masculino | "Microsoft Server voz texto para voz de voz (zh-HK Danny, Apollo)" | "zh-HK-Danny-Apollo"
`zh-TW` | Chinês (Taiwan) | Feminino | "Microsoft Server voz texto para voz de voz (zh-TW, Yating, Apollo)" | "zh-TW-Yating-Apollo"
| | | Feminino | "Microsoft Server voz texto para voz de voz (zh-TW, HanHanRUS)" | "zh-TW-HanHanRUS"
| | | Masculino | "Microsoft Server voz texto para voz de voz (zh-TW, Zhiwei, Apollo)" | "zh-TW-Zhiwei-Apollo"

**1** *ar-EG suporta o árabe moderno padrão (MSA).*

> [!NOTE]
> Pode utilizar o mapeamento completo do nome do serviço ou o nome de voz curto nos pedidos de síntese da sua fala.

### <a name="customization"></a>Personalização

A personalização por voz está disponível para `de-DE`, `en-GB`, `en-IN`, `en-US`, `es-MX`, `fr-FR`, `it-IT`, `pt-BR`e `zh-CN`. Selecione o local certo que corresponda aos dados de treino que tem para treinar um modelo de voz personalizado. Por exemplo, se os dados de gravação que tiver tiver são falados em inglês com sotaque britânico, selecione `en-GB`.

> [!NOTE]
> Não apoiamos o treino de modelos bilingues em Voz Personalizada, exceto para o bilingchinês chinês-inglês. Selecione "bilingue chinês-inglês" se quiser treinar uma voz chinesa que também fale inglês. A formação de voz em todos os locais começa com um conjunto de dados de mais de 2.000 expressões, exceto para o `en-US` e `zh-CN` onde você pode começar com qualquer tamanho de dados de treino.

## <a name="speech-translation"></a>Tradução de voz

A API de **Tradução da Fala** apoia diferentes línguas para a tradução fala-a-fala e de fala-a-texto. A linguagem fonte deve ser sempre da tabela linguística discurso-texto. Os idiomas de destino disponíveis dependem se o destino de tradução é voz ou texto. Pode traduzir o discurso de entrada em mais de [60 línguas.](https://www.microsoft.com/translator/business/languages/) Um subconjunto de línguas está disponível para [síntese da fala.](language-support.md#text-languages)

### <a name="text-languages"></a>Idiomas de texto

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
| Indonésio      | `id`          |
| Irlandês      | `ga`          |
| Italiano      | `it`          |
| Japonês      | `ja`          |
| Canarim      | `kn`          |
| Suaíli      | `sw`          |
| Klingon      | `tlh`          |
| Klingon (plqaD)      | `tlh-Qaak`          |
| Coreano      | `ko`          |
| Letão      | `lv`          |
| Lituano      | `lt`          |
| Ariary      | `mg`          |
| Malaio      | `ms`          |
| Malayalam      | `ml`          |
| Maltês      | `mt`          |
| Norueguês      | `nb`          |
| Persa      | `fa`          |
| Polaco      | `pl`          |
| Português (Brasil)      | `pt-br`          |
| Português (Portugal)      | `pt-pt`          |
| Punjabi      | `pa`          |
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
| Télego      | `te`          |
| Tailandês      | `th`          |
| Tongan      | `to`          |
| Turco      | `tr`          |
| Ucraniano      | `uk`          |
| Urdu      | `ur`          |
| Vietnamita      | `vi`          |
| Galês      | `cy`          |
| Yucatec Maya      | `yua`          |


## <a name="next-steps"></a>Passos seguintes

* [Obtenha a subscrição do teste do serviço Speech](https://azure.microsoft.com/try/cognitive-services/)
* [Veja como reconhecer o discurso emC#](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp)
