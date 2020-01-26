---
title: Suporte a idiomas-serviço de fala
titleSuffix: Azure Cognitive Services
description: O serviço de fala dá suporte a vários idiomas para conversão de fala em texto e de texto em fala, juntamente com a tradução de fala. Este artigo fornece uma lista abrangente de suporte de idioma por recurso de serviço.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: dapine
ms.custom: seodec18
ms.openlocfilehash: 34d362a4b28ade5116c68e2243a2341bab9fca9d
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2020
ms.locfileid: "76760628"
---
# <a name="language-and-region-support-for-the-speech-service"></a>Suporte a idiomas e regiões para o serviço de fala

O suporte a idiomas varia de acordo com a funcionalidade do serviço de fala. As tabelas a seguir resumem o suporte a idiomas para ofertas de [voz para texto](#speech-to-text), conversão de [texto em fala](#text-to-speech)e serviço de [tradução de fala](#speech-translation) .

## <a name="speech-to-text"></a>Conversão de voz em texto

O SDK do Microsoft Speech e a API REST dão suporte aos seguintes idiomas (localidades). Para melhorar a precisão, a personalização é oferecida para um subconjunto das linguagens por meio do carregamento de áudio + transcrições com rótulo humano ou texto relacionado: frases. Atualmente, a personalização de pronúncia está disponível apenas para `en-US` e `de-DE`. Saiba mais sobre a personalização [aqui](how-to-custom-speech.md).

<!--
To get the AM and ML bits:
https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20models%3A/GetSupportedLocalesForModels

To get pronunciation bits:
https://cris.ai -> Click on Adaptation Data -> scroll down to section "Pronunciation Datasets" -> Click on Import -> Locale: the list of locales there correspond to the supported locales
-->

 Região | Linguagem | Suportadas | Personalizações
------|------------|-----------|-------------
`ar-AE` | Árabe (dos EAU) | Sim | Não
`ar-BH` | Árabe (Bahrein) | Sim | Modelo de linguagem
`ar-EG` | Árabe (Egito), standard moderna | Sim | Modelo de linguagem
`ar-KW` | Árabe (Kuwait) | Sim | Não
`ar-QA` | Árabe (catar) | Sim | Não
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
`gu-IN` | Guzerate (indiano) | Sim | Modelo de linguagem
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
`tr-TR` | Turquia | Sim | Não
`zh-CN` | Chinês (simplificado Mandarim) | Sim | Modelo acústico<br>Modelo de linguagem
`zh-HK` | Chinês (Cantonês, tradicional) | Sim | Modelo de linguagem
`zh-TW` | Chinês (Taiwanês Mandarim) | Sim | Modelo de linguagem

## <a name="text-to-speech"></a>Conversão de texto em voz

O SDK do Microsoft Speech e as APIs REST dão suporte a essas vozes, cada uma delas com suporte a um idioma e dialeto específicos, identificados pela localidade.

> [!IMPORTANT]
> Os preços variam de acordo com as vozes padrão, personalizadas e neurais. Visite a página de [preços](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) para obter informações adicionais.

### <a name="neural-voices"></a>Vozes neurais

O texto para fala neural é um novo tipo de síntese de fala equipado por redes neurais profundas. Ao usar uma voz neural, a fala sintetizada é praticamente indistinguíveis das gravações humanas.

As vozes neurais podem ser usadas para fazer interações com os assistentes de voz e chatbots mais naturais e envolventes, converter textos digitais, como livros eletrônicos, em Audiobooks e aprimorar os sistemas de navegação no carro. Com a Prosody natural humana e a clara Articulation de palavras, as vozes neurais reduzem significativamente a escuta de fadiga quando os usuários interagem com sistemas de ia.

Para obter mais informações sobre a disponibilidade regional, consulte [regiões](regions.md#standard-and-neural-voices).

Região | Linguagem | Género | Mapeamento de nome de serviço completo | Nome curto da voz
--------|----------|--------|---------|------------
`de-DE` | Alemão (Alemanha) | Feminino | "Microsoft Server Speech Conversão de Texto em Fala voz (de-DE, KatjaNeural)" | "de-DE-KatjaNeural"
`en-US` | English (US) | Masculino | "Microsoft Server Speech Conversão de Texto em Fala voz (en-US, GuyNeural)" | "en-US-GuyNeural"
`en-US` | English (US) | Feminino | "Microsoft Server Speech Conversão de Texto em Fala voz (en-US, JessaNeural)" | "en-US-JessaNeural"
`it-IT` | Italiano (Itália) | Feminino |"Microsoft Server Speech Conversão de Texto em Fala Voice (IT-IT, ElsaNeural)" | "IT-IT-ElsaNeural"
`zh-CN` | Chinês (continental) | Feminino | "Microsoft Server Speech Conversão de Texto em Fala voz (ZH-CN, XiaoxiaoNeural)" | "zh-CN-XiaoxiaoNeural"

Para saber como você pode configurar e ajustar as vozes neurais, consulte [linguagem de marcação de síntese de fala](speech-synthesis-markup.md#adjust-speaking-styles).

> [!NOTE]
> Você pode usar o mapeamento de nome de serviço completo ou o nome de voz curto em suas solicitações de síntese de fala.

### <a name="standard-voices"></a>Vozes padrão

Mais de 75 vozes padrão estão disponíveis em mais de 45 idiomas e localidades, que permitem converter texto em fala sintetizada. Para obter mais informações sobre a disponibilidade regional, consulte [regiões](regions.md#standard-and-neural-voices).

Região | Linguagem | Género | Mapeamento de nome de serviço completo | Nome abreviado
-------|----------|---------|----------|----------
<sup>**1**</sup>`ar-EG` | Árabe (Egito) | Feminino | "Microsoft Server voz texto para voz de voz (ar-por exemplo, Hoda)" | "ar-ex-Hoda"
`ar-SA` | Árabe (Arábia Saudita) | Masculino | "Microsoft Server voz texto para voz de voz (ar-SA, Naayf)" | "ar-SA-Naayf"
`bg-BG` | Búlgaro | Masculino | "Microsoft Server voz texto em voz voz (bg-BG, Ivan)" | "bg-BG-Ivan"
`ca-ES` | Catalão | Feminino | "Microsoft Server voz texto em voz voz (AC-ES, HerenaRUS)" | "ca-ES-HerenaRUS"
`cs-CZ` | Checo | Masculino | "Microsoft Server voz texto para voz de voz (cs-CZ, Jakub)" | "CS-CZ-Jakub"
`da-DK` | Dinamarquês | Feminino | "Microsoft Server voz texto para voz de voz (da-DK, HelleRUS)" | "da-DK-HelleRUS"
`de-AT` | Alemão (Áustria) | Masculino | "Microsoft Server voz texto para voz de voz (Alemanha-AT, Michael)" | "de a Michael"
`de-CH` | Alemão (Suíça) | Masculino | "Microsoft Server voz texto para voz de voz (Alemanha-CH, Karsten)" | "de-CH-Karsten"
`de-DE` | Alemão (Alemanha) | Feminino | "Microsoft Server voz texto para voz de voz (de-DE, Hedda)" | "de-DE-Hedda"
| | | Feminino | "Microsoft Server voz texto para voz de voz (de-DE, HeddaRUS)" | "de-DE-HeddaRUS"
| | | Masculino | "Microsoft Server voz texto para voz de voz (de-DE, Stefan, Apollo)" | "de-DE-Rodrigo-Apollo"
`el-GR` | Grego | Masculino | "Microsoft Server voz texto para voz de voz (el-GR, Stefanos)" | "El-GR-Stefanos"
`en-AU` | Inglês (Austrália) | Feminino | "Microsoft Server voz texto para voz de voz (en-AU, Catherine)" | "en-AU-Catherine"
| | | Feminino | "Microsoft Server voz texto para voz de voz (en-AU, HayleyRUS)" | "en-AU-HayleyRUS"
`en-CA` | Inglês (Canadá) | Feminino | "Microsoft Server voz texto para voz de voz (en-CA, Linda)" | "en-CA-linda"
| | | Feminino | "Microsoft Server voz texto para voz de voz (en-CA, HeatherRUS)" | "en-CA-HeatherRUS"
`en-GB` | English (UK) | Feminino | "Microsoft Server voz texto para voz de voz (en-GB, Susan, Apollo)" | "en-GB-Susan-Apollo"
| | | Feminino | "Microsoft Server voz texto para voz de voz (en-GB, HazelRUS)" | "en-GB-HazelRUS"
| | | Masculino | "Microsoft Server voz texto para voz de voz (en-GB, George, Apollo)" | "en-GB-George-Apollo"
`en-IE` | Inglês (Irlanda) | Masculino | "Microsoft Server voz texto para voz de voz (en-IE, Sean)" | "en-IE-Sílvio"
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
| | | Masculino | "Voz de texto em voz de voz do Microsoft Server (es-MX, Raul, Apollo)" | "es-MX-Ricardo-Apollo"
`fi-FI` | Finlandês | Feminino | "Microsoft Server voz texto para voz de voz (fi-FI, HeidiRUS)" | "fi-FI-HeidiRUS"
`fr-CA` | Francês (Canadá) |Feminino | "Microsoft Server voz texto em voz voz (fr-AC, Caroline)" | "fr-CA-Carolina"
| | | Feminino | "Microsoft Server voz texto em voz voz (fr-AC, HarmonieRUS)" | "fr-CA-HarmonieRUS"
`fr-CH` | Francês (Suíça)| Masculino | "Microsoft Server voz texto em voz voz (fr-CH, Guillaume)" | "fr-CH-Guillaume"
`fr-FR` | Francês (França)| Feminino | "Microsoft Server voz texto em voz voz (fr-FR, Julie, Apollo)" | "fr-FR-Julie-Apollo"
| | | Feminino | "Microsoft Server voz texto em voz voz (fr-FR, HortenseRUS)" | "fr-FR-HortenseRUS"
| | | Masculino | "Microsoft Server voz texto em voz voz (fr-FR, Paul, Apollo)" | "fr-FR-Paul-Apollo"
`he-IL` | Hebraico (Israel) | Masculino| "Microsoft Server voz texto para voz de voz (he-IL, Asaf)" | "he-IL-Asaf"
`hi-IN` | Hindi (Índia) | Feminino | "Microsoft Server voz texto para voz de voz (hi-IN, Kalpana, Apollo)" | "hi-IN-Kalpana-Apollo"
| | | Feminino | "Microsoft Server voz texto para voz de voz (hi-IN, Kalpana)" | "hi-IN-Kalpana"
| | | Masculino | "Microsoft Server voz texto para voz de voz (hi-IN, Hemant)" | "hi-IN-Hemant"
`hr-HR` | Croata | Masculino | "Microsoft Server voz texto em voz voz (RH-RH, Matej)" | "HR-HR-Matej"
`hu-HU` | Húngaro | Masculino | "Microsoft Server voz texto para voz de voz (hu-HU, Szabolcs)" | "hu-HU-Szabolcs"
`id-ID` | Indonésio| Masculino | "Microsoft Server voz texto para voz de voz (id-ID, Andika)" | "ID-ID-andika"
`it-IT` | Italiano | Masculino | "Microsoft Server voz texto para voz de voz (it-IT, Cosimo, Apollo)" | "IT-IT-Cosimo-Apollo"
| | | Feminino | "Microsoft Server voz texto para voz de voz (it-IT, LuciaRUS)" | "IT-IT-LuciaRUS"
`ja-JP` | Japonês | Feminino | "Microsoft Server voz texto em voz voz (ja-JP, Ayumi, Apollo)" | "ja-JP-Ayumi-Apollo"
| | | Masculino | "Microsoft Server voz texto em voz voz (ja-JP, Ichiro, Apollo)" | "ja-JP-Ichiro-Apollo"
| | | Feminino | "Microsoft Server voz texto em voz voz (ja-JP, HarukaRUS)" | "ja-JP-HarukaRUS"
`ko-KR` | Coreano | Feminino | "Microsoft Server voz texto para voz de voz (ko-KR, HeamiRUS)" | "ko-KR-HeamiRUS"
`ms-MY` | Malaio | Masculino | "Voz de texto em voz de voz do Microsoft Server (ms-MY, Rizwan)" | "MS-MY-Rizwan"
`nb-NO` | Norueguês | Feminino | "Voz de texto em voz de voz do Microsoft Server (não-nb, HuldaRUS)" | "NB-NO-HuldaRUS"
`nl-NL` | Neerlandês | Feminino | "Microsoft Server voz texto em voz voz (nl-NL, HannaRUS)" | "nl-NL-HannaRUS"
`pl-PL` | Polaco | Feminino | "Microsoft Server voz texto em voz voz (pl-PL, PaulinaRUS)" | "pl-PL-PaulinaRUS"
`pt-BR` | Português (Brasil) | Feminino | "Microsoft Server voz texto em voz voz (pt-BR, HeloisaRUS)" | "pt-BR-HeloisaRUS"
| | | Masculino |"Microsoft Server voz texto em voz voz (pt-BR, Daniel, Apollo)" | "pt-BR-Daniel-Apollo"
`pt-PT` | Português (Portugal) | Feminino | "Microsoft Server voz texto em voz voz (pt-PT, HeliaRUS)" | "pt-PT-HeliaRUS"
`ro-RO` | Romeno | Masculino | "Microsoft Server voz texto para voz de voz (ro-RO, Andrei)" | "RO-RO-Andrei"
`ru-RU` |Russo| Feminino | "Microsoft Server voz texto em voz voz (ru-RU, Irina, Apollo)" | "ru-RU-Irina-Apollo"
| | | Masculino | "Microsoft Server voz texto em voz voz (ru-RU, Pavel, Apollo)" | "ru-RU-Pavel-Apollo"
| | | Feminino | "Microsoft Server voz texto em voz voz (ru-RU, EkaterinaRUS)" | ru-RU-EkaterinaRUS
`sk-SK` | Eslovaco | Masculino | "Microsoft Server voz texto em voz voz (sk-SK, Filip)" | "SK-SK-Filip"
`sl-SI` | Esloveno | Masculino | "Microsoft Server voz texto em voz voz (sl-SI, Lado)" | "sl-SI-Lado"
`sv-SE` | Sueco | Feminino | "Microsoft Server voz texto para voz de voz (sv-SE, HedvigRUS)" | "sv-SE-HedvigRUS"
`ta-IN` | Tamil (Índia) | Masculino | "Microsoft Server voz texto para voz de voz (dados-IN, Valluvar)" | "ta-IN-Valluvar"
`te-IN` | Telugu (Índia) | Feminino | "Microsoft Server voz texto para voz de voz (te-IN, Chitra)" | "Chitra"
`th-TH` | Tailandês | Masculino | "Microsoft Server voz texto em voz voz (th-TH, Pattara)" | "th-TH-Pattara"
`tr-TR` | Turco | Feminino | "Microsoft Server voz texto em voz voz (tr-TR, SedaRUS)" | "tr-TR-SedaRUS"
`vi-VN` | Vietnamita | Masculino | "Voz de texto em voz de voz do Microsoft Server (vi-VN, um)" | "vi-VN-um"
`zh-CN` | Chinês (continental) | Feminino | "Microsoft Server voz texto para voz de voz (zh-CN, HuihuiRUS)" | "zh-CN-HuihuiRUS"
| | | Feminino | "Microsoft Server voz texto para voz de voz (zh-CN, Yaoyao, Apollo)" | "zh-CN-Yaoyao-Apollo"
| | | Masculino | "Microsoft Server voz texto para voz de voz (zh-CN, Kangkang, Apollo)" | "zh-CN-Kangkang-Apollo"
`zh-HK` | Chinês (Hong Kong) | Feminino | "Microsoft Server voz texto para voz de voz (zh-HK Tracy, Apollo)" | "ZH-HK-Tracy-Apollo"
| | | Feminino | "Microsoft Server voz texto para voz de voz (zh-HK TracyRUS)" | "ZH-HK-TracyRUS"
| | | Masculino | "Microsoft Server voz texto para voz de voz (zh-HK Danny, Apollo)" | "ZH-HK-Danny-Apollo"
`zh-TW` | Chinês (Taiwan) | Feminino | "Microsoft Server voz texto para voz de voz (zh-TW, Yating, Apollo)" | "zh-TW-Yating-Apollo"
| | | Feminino | "Microsoft Server voz texto para voz de voz (zh-TW, HanHanRUS)" | "zh-TW-HanHanRUS"
| | | Masculino | "Microsoft Server voz texto para voz de voz (zh-TW, Zhiwei, Apollo)" | "zh-TW-Zhiwei-Apollo"

**1** *ar-EG suporta o árabe moderno padrão (MSA).*

> [!NOTE]
> Você pode usar o mapeamento de nome de serviço completo ou o nome de voz curto em suas solicitações de síntese de fala.

### <a name="customization"></a>Personalização

A personalização de voz está disponível para `de-DE`, `en-GB`, `en-IN`, `en-US`, `es-MX`, `fr-FR`, `it-IT`, `pt-BR`e `zh-CN`. Selecione a localidade correta que corresponde aos dados de treinamento que você tem para treinar um modelo de voz personalizado. Por exemplo, se os dados de gravação que você tem são falados em inglês com um sotaque britânico, selecione `en-GB`.

> [!NOTE]
> Não damos suporte ao treinamento de modelo de bi-idiomas em voz personalizada, exceto para o Português do idioma chinês-inglês. Selecione "chinês-inglês bilíngüe" se você quiser treinar uma voz chinesa que também pode falar em inglês. O treinamento de voz em todas as localidades começa com um conjunto de dados de 2.000 + declarações, exceto pelo `en-US` e `zh-CN` em que você pode começar com qualquer tamanho de dados de treinamento.

## <a name="speech-translation"></a>Tradução de voz

O **tradução por voz** API suporta idiomas diferentes para a tradução de voz para voz e conversão de voz em texto. O idioma de origem sempre deve ser da tabela de linguagem de fala para texto. Os idiomas de destino disponíveis dependem se o destino de tradução é voz ou texto. Pode converter voz recebida em mais do que [60 idiomas](https://www.microsoft.com/translator/business/languages/). Um subconjunto de idiomas está disponível para [síntese de fala](language-support.md#text-languages).

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

* [Obtenha sua assinatura de avaliação do serviço de fala](https://azure.microsoft.com/try/cognitive-services/)
* [Veja como a reconhecer a conversão de voz em c#](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp)
