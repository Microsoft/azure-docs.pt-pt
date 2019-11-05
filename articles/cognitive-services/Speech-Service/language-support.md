---
title: Suporte a idiomas-serviço de fala
titleSuffix: Azure Cognitive Services
description: O serviço de fala dá suporte a vários idiomas para conversão de fala em texto e de texto em fala, juntamente com a tradução de fala. Este artigo fornece uma lista abrangente de suporte de idioma por recurso de serviço.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: af8bb24862c05b232b7bb5d831b1eb3b1add3a7f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73468801"
---
# <a name="language-and-region-support-for-the-speech-services"></a>Suporte a idiomas e regiões para os serviços de fala

Diferentes idiomas têm suporte para diferentes funções de serviços de fala. As tabelas a seguir resumem o suporte a idiomas.

## <a name="speech-to-text"></a>Conversão de voz em texto

O SDK do Microsoft Speech e a API REST dão suporte aos seguintes idiomas (localidades). Para melhorar a precisão, a personalização é oferecida para um subconjunto das linguagens por meio do carregamento de áudio + transcrições com rótulo humano ou texto relacionado: frases.  Atualmente, a personalização de pronúncia está disponível apenas para en-US e de-DE. Saiba mais sobre a personalização [aqui](how-to-custom-speech.md).

  Região | Idioma | Suportado | Personalizável
 ------|----------|---------------------|---------------------
 ar-ex. | Árabe (Egito), padrão moderno | Sim | Sim
 ar-SA | Árabe (Arábia Saudita) | Sim | Sim
 ar-AE | Árabe (dos EAU) | Sim | Sim
 ar-KW | Árabe (Kuwait) | Sim | Sim
 ar-QA | Árabe (catar) | Sim | Sim
 CA-ES | Catalão | Sim | Não
 da-DK | Dinamarquês (Dinamarca) | Sim | Não
 de-DE | Alemão (Alemanha) | Sim | Sim
 en-AU | Inglês (Austrália) | Sim | Sim
 EN-AC | Inglês (Canadá) | Sim | Sim
 en-GB | Inglês (Reino Unido) | Sim | Sim
 en-IN | English (India) | Sim | Sim
 en-NZ | Inglês (Nova Zelândia) | Sim | Sim
 en-US | Inglês (Estados Unidos) | Sim | Sim
 es-ES | Espanhol (Espanha) | Sim | Sim
 es-MX | Espanhol (México) | Sim | Sim
 fi-FI | Finlandês (Finlândia) | Sim | Não
 fr-AC | Francês (Canadá) | Sim | Sim
 fr-FR | Francês (França) | Sim | Sim
 Gu-IN | Guzerate (indiano) | Sim | Sim
 hi-IN | Híndi (Índia) | Sim | Sim
 ti-ti | Italiano (Itália) | Sim | Sim
 ja-JP | Japonês (Japão) | Sim | Sim
 ko-KR | Coreano (Coreia) | Sim | Sim
 Mr-IN | Marati (Índia) | Sim | Sim
 NB-não | Norueguês (bokmål) (Noruega) | Sim | Não
 NL-NL | Holandês (Países Baixos) | Sim | Sim
 pl-PL | Polonês (Polônia) | Sim | Não
 pt-BR | Português (Brasil) | Sim | Sim
 pt-PT | Português (Portugal) | Sim | Sim
 RU-RU | Russo (Rússia) | Sim | Sim
 VA-SE | Sueco (Suécia) | Sim | Não
 ta-IN | Tâmil (Índia) | Sim | Sim
 te-IN | Télugo (Índia) | Sim | Sim
 ZH-CN | Chinês (mandarim, simplificado) | Sim | Sim
 ZH-HK | Chinês (Cantonês, tradicional) | Sim | Sim
 zh-TW | Chinês (dólar taiwanês mandarim) | Sim | Sim
 th-ÉSIMO | Tailandês (Tailândia) | Sim | Não
 TR-TR | Turquia | Sim | Sim |


## <a name="text-to-speech"></a>Conversão de texto em voz

O Microsoft Speech SDK e a API REST dão suporte a essas vozes, cada uma delas com suporte a um idioma e dialeto específicos, identificados pela localidade.

> [!IMPORTANT]
> Os preços variam de acordo com as vozes padrão, personalizadas e neurais. Visite a página de [preços](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) para obter informações adicionais.

### <a name="neural-voices"></a>Vozes neurais

O texto para fala neural é um novo tipo de síntese de fala equipado por redes neurais profundas. Ao usar uma voz neural, a fala sintetizada é praticamente indistinguíveis das gravações humanas.

As vozes neurais podem ser usadas para fazer interações com os assistentes de voz e chatbots mais naturais e envolventes, converter textos digitais, como livros eletrônicos, em Audiobooks e aprimorar os sistemas de navegação no carro. Com a Prosody natural humana e a clara Articulation de palavras, as vozes neurais reduzem significativamente a escuta de fadiga quando os usuários interagem com sistemas de ia.

Para obter uma lista completa de vozes neurais e da disponibilidade regional, consulte [regiões](regions.md#standard-and-neural-voices).

Região | Idioma | Género | Mapeamento de nome de serviço completo | Nome curto da voz
--------|----------|--------|---------|------------
de-DE | Alemão (Alemanha) | Feminino | "Microsoft Server Speech Conversão de Texto em Fala voz (de-DE, KatjaNeural)" | "de-DE-KatjaNeural"
en-US | English (US) | Masculino | "Microsoft Server Speech Conversão de Texto em Fala voz (en-US, GuyNeural)" | "en-US-GuyNeural"
en-US | English (US) | Feminino | "Microsoft Server Speech Conversão de Texto em Fala voz (en-US, JessaNeural)" | "en-US-JessaNeural"
ti-ti | Italiano (Itália) | Feminino |"Microsoft Server Speech Conversão de Texto em Fala Voice (IT-IT, ElsaNeural)" | "IT-IT-ElsaNeural"
ZH-CN | Chinês (continente) | Feminino | "Microsoft Server Speech Conversão de Texto em Fala voz (ZH-CN, XiaoxiaoNeural)" | "zh-CN-XiaoxiaoNeural"

> [!NOTE]
> Você pode usar o mapeamento de nome de serviço completo ou o nome de voz curto em suas solicitações de síntese de fala.

### <a name="standard-voices"></a>Vozes padrão

Mais de 75 vozes padrão estão disponíveis em mais de 45 idiomas e localidades, que permitem converter texto em fala sintetizada. Para obter mais informações sobre a disponibilidade regional, consulte [regiões](regions.md#standard-and-neural-voices).

Região | Idioma | Género | Mapeamento de nome de serviço completo | Nome curto da voz
-------|----------|---------|----------|----------
ar-ex\* | Árabe (Egito) | Feminino | "Microsoft Server Speech Conversão de Texto em Fala voz (ar-ex, Hoda)" | "ar-ex-Hoda"
ar-SA | Árabe (Arábia Saudita) | Masculino | "Microsoft Server Speech Conversão de Texto em Fala voz (ar-SA, Naayf)" | "ar-SA-Naayf"
bg-BG | Búlgaro | Masculino | "Microsoft Server Speech Conversão de Texto em Fala voz (bg-BG, Ivan)" | "bg-BG-Ivan"
CA-ES | Catalão (Espanha) | Feminino | "Microsoft Server Speech Conversão de Texto em Fala voz (CA-ES, HerenaRUS)" | "CA-ES-HerenaRUS"
CS-CZ | Tcheco | Masculino | "Microsoft Server Speech Conversão de Texto em Fala voz (CS-CZ, Jakub)" | "CS-CZ-Jakub"
da-DK | Dinamarquês | Feminino | "Microsoft Server Speech Conversão de Texto em Fala voz (da-DK, HelleRUS)" | "da-DK-HelleRUS"
de em | Alemão (Áustria) | Masculino | "Microsoft Server Speech Conversão de Texto em Fala voz (de-em, Michael)" | "de a Michael"
de-CH | Alemão (Suíça) | Masculino | "Microsoft Server Speech Conversão de Texto em Fala voz (de-CH, Karsten)" | "de-CH-Karsten"
de-DE | Alemão (Alemanha) | Feminino | "Microsoft Server Speech Conversão de Texto em Fala voz (de-DE, Hedda)" | "de-DE-Hedda"
| | | Feminino | "Microsoft Server Speech Conversão de Texto em Fala voz (de-DE, HeddaRUS)" | "de-DE-HeddaRUS"
| | | Masculino | "Microsoft Server Speech Conversão de Texto em Fala voz (de-DE, Rodrigo, Apollo)" | "de-DE-Rodrigo-Apollo"
El-GR | Grego | Masculino | "Microsoft Server Speech Conversão de Texto em Fala voz (El-GR, Stefanos)" | "El-GR-Stefanos"
en-AU | Inglês (Austrália) | Feminino | "Microsoft Server Speech Conversão de Texto em Fala voz (en-AU, Catherine)" | "en-AU-Catherine"
| | | Feminino | "Microsoft Server Speech Conversão de Texto em Fala voz (en-AU, HayleyRUS)" | "en-AU-HayleyRUS"
EN-AC | Inglês (Canadá) | Feminino | "Microsoft Server Speech Conversão de Texto em Fala voz (en-CA, linda)" | "en-CA-linda"
| | | Feminino | "Microsoft Server Speech Conversão de Texto em Fala voz (en-CA, HeatherRUS)" | "en-CA-HeatherRUS"
en-GB | English (UK) | Feminino | "Microsoft Server Speech Conversão de Texto em Fala voz (en-GB, Susan, Apollo)" | "en-GB-Susan-Apollo"
| | | Feminino | "Microsoft Server Speech Conversão de Texto em Fala voz (en-GB, HazelRUS)" | "en-GB-HazelRUS"
| | | Masculino | "Microsoft Server Speech Conversão de Texto em Fala voz (en-GB, George, Apollo)" | "en-GB-George-Apollo"
EN-IE | Inglês (Irlanda) | Masculino | "Microsoft Server Speech Conversão de Texto em Fala voz (EN-IE, Sílvio)" | "en-IE-Sílvio"
en-IN | English (India) | Feminino | "Microsoft Server Speech Conversão de Texto em Fala voz (en-IN, Heera, Apollo)" | "en-IN-Heera-Apollo"
| | | Feminino | "Microsoft Server Speech Conversão de Texto em Fala voz (en-IN, PriyaRUS)" | "en-IN-PriyaRUS"
| | | Masculino | "Microsoft Server Speech Conversão de Texto em Fala voz (en-IN, Ravi, Apollo)" | "en-IN-Ravi-Apollo"
en-US | English (US) | Feminino | "Microsoft Server Speech Conversão de Texto em Fala voz (en-US, ZiraRUS)" | "en-US-ZiraRUS"
| | | Feminino | "Microsoft Server Speech Conversão de Texto em Fala voz (en-US, JessaRUS)" | "en-US-JessaRUS"
| | | Masculino | "Microsoft Server Speech Conversão de Texto em Fala voz (en-US, BenjaminRUS)" | "en-US-BenjaminRUS"
| | | Feminino | "Microsoft Server Speech Conversão de Texto em Fala voz (en-US, Jessa24kRUS)" | "en-US-Jessa24kRUS"
| | | Masculino | "Microsoft Server Speech Conversão de Texto em Fala voz (en-US, Guy24kRUS)" | "en-US-Guy24kRUS"
es-ES | Espanhol (Espanha) |Feminino | "Microsoft Server Speech Conversão de Texto em Fala voz (es-ES, Laura, Apollo)" | "es-ES-Laura-Apollo"
| | | Feminino | "Microsoft Server Speech Conversão de Texto em Fala voz (es-ES, HelenaRUS)" | "es-ES-HelenaRUS"
| | | Masculino | "Microsoft Server Speech Conversão de Texto em Fala voz (es-ES, Pablo, Apollo)" | "es-ES-Pablo-Apollo"
es-MX | Espanhol (México) | Feminino | "Microsoft Server Speech Conversão de Texto em Fala voz (es-MX, HildaRUS)" | "es-MX-HildaRUS"
| | | Masculino | "Microsoft Server Speech Conversão de Texto em Fala voz (es-MX, Ricardo, Apollo)" | "es-MX-Ricardo-Apollo"
fi-FI | Finlandês | Feminino | "Microsoft Server Speech Conversão de Texto em Fala voz (fi-FI, HeidiRUS)" | "fi-FI-HeidiRUS"
fr-AC | Francês (Canadá) |Feminino | "Microsoft Server Speech Conversão de Texto em Fala voz (fr-CA, Carolina)" | "fr-CA-Carolina"
| | | Feminino | "Microsoft Server Speech Conversão de Texto em Fala voz (fr-CA, HarmonieRUS)" | "fr-CA-HarmonieRUS"
fr-CH | Francês (Suíça)| Masculino | "Microsoft Server Speech Conversão de Texto em Fala voz (fr-CH, Guillaume)" | "fr-CH-Guillaume"
fr-FR | Francês (França)| Feminino | "Microsoft Server Speech Conversão de Texto em Fala voz (fr-FR, Julie, Apollo)" | "fr-FR-Julie-Apollo"
| | | Feminino | "Microsoft Server Speech Conversão de Texto em Fala voz (fr-FR, HortenseRUS)" | "fr-FR-HortenseRUS"
| | | Masculino | "Microsoft Server Speech Conversão de Texto em Fala voz (fr-FR, Paul, Apollo)" | "fr-FR-Paul-Apollo"
ti-IL| Hebraico (Israel) | Masculino| "Microsoft Server Speech Conversão de Texto em Fala voz (he-IL, Asaf)" | "he-IL-Asaf"
hi-IN | Híndi (Índia) | Feminino | "Microsoft Server Speech Conversão de Texto em Fala voz (Hi-IN, Kalpana, Apollo)" | "hi-IN-Kalpana-Apollo"
| | |Feminino | "Microsoft Server Speech Conversão de Texto em Fala voz (Hi-IN, Kalpana)" | "hi-IN-Kalpana"
| | | Masculino | "Microsoft Server Speech Conversão de Texto em Fala voz (Hi-IN, Hemant)" | "hi-IN-Hemant"
HR-HR | Croata | Masculino | "Microsoft Server Speech Conversão de Texto em Fala voz (HR-HR, Matej)" | "HR-HR-Matej"
hu-HU | Húngaro | Masculino | "Microsoft Server Speech Conversão de Texto em Fala voz (hu-HU, Szabolcs)" | "hu-HU-Szabolcs"
ID-ID | Indonésio| Masculino | "Microsoft Server Speech Conversão de Texto em Fala voz (ID-ID, andika)" | "ID-ID-andika"
ti-ti | Italiano | Masculino | "Microsoft Server Speech Conversão de Texto em Fala voz (IT-IT, Cosimo, Apollo)" | "IT-IT-Cosimo-Apollo"
| | | Feminino | "Microsoft Server Speech Conversão de Texto em Fala Voice (IT-IT, LuciaRUS)" | "IT-IT-LuciaRUS"
ja-JP | Japonês | Feminino | "Microsoft Server Speech Conversão de Texto em Fala voz (ja-JP, Ayumi, Apollo)" | "ja-JP-Ayumi-Apollo"
| | | Masculino | "Microsoft Server Speech Conversão de Texto em Fala voz (ja-JP, Ichiro, Apollo)" | "ja-JP-Ichiro-Apollo"
| | | Feminino | "Microsoft Server Speech Conversão de Texto em Fala voz (ja-JP, HarukaRUS)" | "ja-JP-HarukaRUS"
ko-KR | Coreano | Feminino | "Microsoft Server Speech Conversão de Texto em Fala voz (ko-KR, HeamiRUS)" | "ko-KR-HeamiRUS"
MS-MY | Malaio | Masculino | "Microsoft Server Speech Conversão de Texto em Fala voz (MS-MY, Rizwan)" | "MS-MY-Rizwan"
NB-não | Norueguês | Feminino | "Microsoft Server Speech Conversão de Texto em Fala voz (NB-NO, HuldaRUS)" | "NB-NO-HuldaRUS"
NL-NL | Holandês | Feminino | "Microsoft Server Speech Conversão de Texto em Fala voz (NL-NL, HannaRUS)" | "nl-NL-HannaRUS"
pl-PL | Polaco | Feminino | "Microsoft Server Speech Conversão de Texto em Fala voz (pl-PL, PaulinaRUS)" | "pl-PL-PaulinaRUS"
pt-BR | Português (Brasil) | Feminino | "Microsoft Server Speech Conversão de Texto em Fala voz (pt-BR, HeloisaRUS)" | "pt-BR-HeloisaRUS"
| | | Masculino |"Microsoft Server Speech Conversão de Texto em Fala voz (pt-BR, Daniel, Apollo)" | "pt-BR-Daniel-Apollo"
pt-PT | Português (Portugal) | Feminino | "Microsoft Server Speech Conversão de Texto em Fala voz (pt-PT, HeliaRUS)" | "pt-PT-HeliaRUS"
RO-RO | Romeno | Masculino | "Microsoft Server Speech Conversão de Texto em Fala voz (RO-RO, Andrei)" | "RO-RO-Andrei"
RU-RU |Russo| Feminino | "Microsoft Server Speech Conversão de Texto em Fala voz (RU-RU, Irina, Apollo)" | "ru-RU-Irina-Apollo"
| | | Masculino | "Microsoft Server Speech Conversão de Texto em Fala voz (RU-RU, Pavel, Apollo)" | "ru-RU-Pavel-Apollo"
| | | Feminino | "Microsoft Server Speech Conversão de Texto em Fala voz (RU-RU, EkaterinaRUS)" | RU-RU-EkaterinaRUS
SK-SK | Eslovaco | Masculino | "Microsoft Server Speech Conversão de Texto em Fala voz (SK-SK, Filip)" | "SK-SK-Filip"
SL-SI | Esloveno | Masculino | "Microsoft Server Speech Conversão de Texto em Fala voz (SL-SI, lado)" | "SL-SI-lado"
VA-SE | Sueco | Feminino | "Microsoft Server Speech Conversão de Texto em Fala voz (SV-SE, HedvigRUS)" | "SV-SE-HedvigRUS"
ta-IN | Tâmil (Índia) | Masculino | "Microsoft Server Speech Conversão de Texto em Fala voz (ta-IN, Valluvar)" | "ta-IN-Valluvar"
te-IN | Télugo (Índia) | Feminino | "Microsoft Server Speech Conversão de Texto em Fala voz (te-IN, Chitra)" | "Chitra"
th-ÉSIMO | Tailandês | Masculino | "Microsoft Server Speech Conversão de Texto em Fala voz (th-TH, Pattara)" | "th-TH-Pattara"
TR-TR | Turco | Feminino | "Microsoft Server Speech Conversão de Texto em Fala voz (TR-TR, SedaRUS)" | "tr-TR-SedaRUS"
vi-VN | Vietnamita | Masculino | "Microsoft Server Speech Conversão de Texto em Fala voz (vi-VN, a)" | "vi-VN-um"
ZH-CN | Chinês (continente) | Feminino | "Microsoft Server Speech Conversão de Texto em Fala voz (ZH-CN, HuihuiRUS)" | "zh-CN-HuihuiRUS"
| | | Feminino | "Microsoft Server Speech Conversão de Texto em Fala voz (ZH-CN, Yaoyao, Apollo)" | "zh-CN-Yaoyao-Apollo"
| | | Masculino | "Microsoft Server Speech Conversão de Texto em Fala voz (ZH-CN, Kangkang, Apollo)" | "zh-CN-Kangkang-Apollo"
ZH-HK | Chinês (Hong Kong) | Feminino | "Microsoft Server Speech Conversão de Texto em Fala voz (ZH-HK, Tracy, Apollo)" | "ZH-HK-Tracy-Apollo"
| | | Feminino | "Microsoft Server Speech Conversão de Texto em Fala voz (ZH-HK, TracyRUS)" | "ZH-HK-TracyRUS"
| | | Masculino | "Microsoft Server Speech Conversão de Texto em Fala voz (ZH-HK, Danny, Apollo)" | "ZH-HK-Danny-Apollo"
zh-TW | Chinês (Taiwan) | Feminino | "Microsoft Server Speech Conversão de Texto em Fala voz (zh-TW, Yating, Apollo)" | "zh-TW-Yating-Apollo"
| | | Feminino | "Microsoft Server Speech Conversão de Texto em Fala voz (zh-TW, HanHanRUS)" | "zh-TW-HanHanRUS"
| | | Masculino | "Microsoft Server Speech Conversão de Texto em Fala voz (zh-TW, Zhiwei, Apollo)" | "zh-TW-Zhiwei-Apollo"

\* *ar-ex suporta o moderno padrão árabe (MSA).*

> [!NOTE]
> Você pode usar o mapeamento de nome de serviço completo ou o nome de voz curto em suas solicitações de síntese de fala.

### <a name="customization"></a>Personalização

A personalização de voz está disponível para de-DE, en-GB, en-IN, en-US, es-MX, fr-FR, it-IT, pt-BR e zh-CN. Selecione a localidade correta que corresponde aos dados de treinamento que você tem para treinar um modelo de voz personalizado. Por exemplo, se os dados de gravação que você tem são falados em inglês com um sotaque britânico, selecione en-GB.  

> [!NOTE]
> Não damos suporte ao treinamento de modelo de bi-idiomas em voz personalizada, exceto para o Português do idioma chinês-inglês. Selecione "chinês-inglês bilíngüe" se você quiser treinar uma voz chinesa que também pode falar em inglês. O treinamento de voz em todas as localidades começa com um conjunto de dados de 2.000 + declarações, exceto para o en-US e o zh-CN, onde você pode começar com qualquer tamanho de dados de treinamento.

## <a name="speech-translation"></a>Tradução de voz

A API de **tradução de fala** dá suporte a idiomas diferentes para conversão de fala em fala e de fala em texto. O idioma de origem sempre deve ser da tabela de linguagem de fala para texto. Os idiomas de destino disponíveis dependem se o destino da tradução é fala ou texto. Você pode traduzir a fala de entrada em mais de [60 idiomas](https://www.microsoft.com/translator/business/languages/). Um subconjunto desses idiomas está disponível para [síntese de fala](language-support.md#text-languages).

### <a name="text-languages"></a>Idiomas de texto

| Idioma do texto    | Código do idioma |
|:----------- |:-------------:|
| Africâner      | `af`          |
| Árabe       | `ar`          |
| Bengali      | `bn`          |
| Bósnio (latino)      | `bs`          |
| Búlgaro      | `bg`          |
| Cantonês (tradicional)      | `yue`          |
| Catalão      | `ca`          |
| Chinês simplificado      | `zh-Hans`          |
| Chinês tradicional      | `zh-Hant`          |
| Croata      | `hr`          |
| Tcheco      | `cs`          |
| Dinamarquês      | `da`          |
| Holandês      | `nl`          |
| Português      | `en`          |
| Estoniano      | `et`          |
| Fijiano      | `fj`          |
| Filipinas      | `fil`          |
| Finlandês      | `fi`          |
| Francês      | `fr`          |
| Alemão      | `de`          |
| Grego      | `el`          |
| Haitiano      | `ht`          |
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
| Malagache      | `mg`          |
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
| Sérvio (latino)      | `sr-Latn`          |
| Eslovaco     | `sk`          |
| Esloveno      | `sl`          |
| Espanhol      | `es`          |
| Sueco      | `sv`          |
| Tahitian      | `ty`          |
| Tâmil      | `ta`          |
| Tailandês      | `th`          |
| Tonganês      | `to`          |
| Turco      | `tr`          |
| Ucraniano      | `uk`          |
| Urdu      | `ur`          |
| Vietnamita      | `vi`          |
| Galês      | `cy`          |
| Yucatec Maya      | `yua`          |


## <a name="next-steps"></a>Passos seguintes

* [Obter a subscrição de avaliação dos Serviços de Voz](https://azure.microsoft.com/try/cognitive-services/)
* [Veja como reconhecer a fala em c #](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp)
