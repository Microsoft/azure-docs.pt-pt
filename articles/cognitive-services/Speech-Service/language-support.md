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
ms.date: 07/05/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: abff902dc62ad7ae48f2ecedfbd52aeb96719093
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69970375"
---
# <a name="language-and-region-support-for-the-speech-services"></a>Suporte a idiomas e regiões para os serviços de fala

Diferentes idiomas têm suporte para diferentes funções de serviços de fala. As tabelas seguintes resumem o suporte de idiomas.

## <a name="speech-to-text"></a>Conversão de voz em texto

O SDK do reconhecimento de fala da Microsoft e a API REST dão suporte aos seguintes idiomas (localidades). Para melhorar a precisão, a personalização é oferecida para um subconjunto das linguagens por meio do carregamento de áudio + transcrições com rótulo humano ou texto relacionado: Frase.  Atualmente, a personalização de pronúncia está disponível apenas para en-US e de-DE. Saiba mais sobre a personalização [aqui](how-to-custom-speech.md).

  Código | Idioma | Suportadas | Personalizável
 ------|----------|---------------------|---------------------
 ar-por exemplo | Árabe (Egito), standard moderna | Sim | Sim
 ES de AC | Catalão | Sim | Não
 da-DK | Dinamarquês (Dinamarca) | Sim | Não
 de-DE | Alemão (Alemanha) | Sim | Sim
 EN-AU | Inglês (Austrália) | Sim | Sim
 en-CA | Inglês (Canadá) | Sim | Sim
 en-GB | Inglês (Reino Unido) | Sim | Sim
 EN-IN | English (India) | Sim | Sim
 EN-NZ | Inglês (Nova Zelândia) | Sim | Sim
 en-US | Inglês (Estados Unidos) | Sim | Sim
 es-ES | Espanhol (Espanha) | Sim | Sim
 es-MX | Espanhol (México) | Sim | Sim
 fi-FI | Finlandês (Finlândia) | Sim | Não
 fr-CA | Francês (Canadá) | Sim | Sim
 FR-FR | Francês (França) | Sim | Sim
 IN | Hindi (Índia) | Sim | Sim
 it-IT | Italiano (Itália) | Sim | Sim
 ja-JP | Japonês (Japão) | Sim | Sim
 ko-KR | Coreano (Coreia) | Sim | Sim
 nb-NO | Norueguês (Bokmål) (Noruega) | Sim | Não
 NL-NL | Holandês (Países Baixos) | Sim | Sim
 pl-PL | Polaco (Polónia) | Sim | Não
 pt-BR | Português (Brasil) | Sim | Sim
 pt-PT | Português (Portugal) | Sim | Sim
 ru-RU | Russo (Rússia) | Sim | Sim
 SV-SE | Sueco (Suécia) | Sim | Não
 zh-CN | Chinês (simplificado Mandarim) | Sim | Sim
 zh-HK | Chinês (Cantonês, tradicional) | Sim | Sim
 zh-TW | Chinês (Taiwanês Mandarim) | Sim | Sim
 th-TH | Tailandês (Tailândia) | Sim | Não 


## <a name="text-to-speech"></a>Conversão de texto em voz

A API REST de conversão de texto em fala dá suporte a essas vozes, cada uma delas com suporte para um idioma e dialeto específicos, identificados pela localidade.

> [!IMPORTANT]
> Os preços variam de acordo com as vozes padrão, personalizadas e neurais. Visite a página de [preços](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) para obter informações adicionais.

### <a name="neural-voices"></a>Vozes neurais

O texto para fala neural é um novo tipo de síntese de fala equipado por redes neurais profundas. Ao usar uma voz neural, a fala sintetizada é praticamente indistinguíveis das gravações humanas.

As vozes neurais podem ser usadas para fazer interações com chatbots e assistentes virtuais mais naturais e envolventes, converter textos digitais, como livros eletrônicos, em Audiobooks e aprimorar os sistemas de navegação no carro. Com a Prosody natural humana e a clara Articulation de palavras, as vozes neurais reduzem significativamente a escuta de fadiga quando os usuários interagem com sistemas de ia.

Para obter uma lista completa de vozes neurais e da disponibilidade regional, consulte [regiões](regions.md#standard-and-neural-voices).

Região | Idioma | Género | Mapeamento de nome de serviço completo | Nome curto da voz
--------|----------|--------|---------|------------
de-DE | Alemão (Alemanha) | Feminino | "Microsoft Server Speech Conversão de Texto em Fala voz (de-DE, KatjaNeural)" | "de-DE-KatjaNeural"
en-US | English (US) | Masculino | "Microsoft Server Speech Conversão de Texto em Fala voz (en-US, GuyNeural)" | "en-US-GuyNeural"
en-US | English (US) | Feminino | "Microsoft Server Speech Conversão de Texto em Fala voz (en-US, JessaNeural)" | "en-US-JessaNeural"
it-IT | Italiano (Itália) | Feminino |"Microsoft Server Speech Conversão de Texto em Fala Voice (IT-IT, ElsaNeural)" | "IT-IT-ElsaNeural"
zh-CN | Chinês (continental) | Feminino | "Microsoft Server Speech Conversão de Texto em Fala voz (ZH-CN, XiaoxiaoNeural)" | "zh-CN-XiaoxiaoNeural"

> [!NOTE]
> Você pode usar o mapeamento de nome de serviço completo ou o nome de voz curto em suas solicitações de síntese de fala.

### <a name="standard-voices"></a>Vozes padrão

Mais de 75 vozes padrão estão disponíveis em mais de 45 idiomas e localidades, que permitem converter texto em fala sintetizada. Para obter mais informações sobre a disponibilidade regional, consulte [regiões](regions.md#standard-and-neural-voices).

Região | Idioma | Género | Mapeamento de nome de serviço completo | Nome curto da voz
-------|----------|---------|----------|----------
ar-por exemplo\* | Árabe (Egito) | Feminino | "Microsoft Server voz texto para voz de voz (ar-por exemplo, Hoda)" | "ar-ex-Hoda"
ar SA | Árabe (Arábia Saudita) | Masculino | "Microsoft Server voz texto para voz de voz (ar-SA, Naayf)" | "ar-SA-Naayf"
bg-BG | Búlgaro | Masculino | "Microsoft Server voz texto em voz voz (bg-BG, Ivan)" | "bg-BG-Ivan"
ES de AC | Catalão | Feminino | "Microsoft Server voz texto em voz voz (AC-ES, HerenaRUS)" | "ca-ES-HerenaRUS"
cs-CZ | Checo | Masculino | "Microsoft Server voz texto para voz de voz (cs-CZ, Jakub)" | "CS-CZ-Jakub"
da-DK | Dinamarquês | Feminino | "Microsoft Server voz texto para voz de voz (da-DK, HelleRUS)" | "da-DK-HelleRUS"
Alemanha-AT | Alemão (Áustria) | Masculino | "Microsoft Server voz texto para voz de voz (Alemanha-AT, Michael)" | "de a Michael"
Alemanha-CH | Alemão (Suíça) | Masculino | "Microsoft Server voz texto para voz de voz (Alemanha-CH, Karsten)" | "de-CH-Karsten"
de-DE | Alemão (Alemanha) | Feminino | "Microsoft Server voz texto para voz de voz (de-DE, Hedda)" | "de-DE-Hedda"
| | | Feminino | "Microsoft Server voz texto para voz de voz (de-DE, HeddaRUS)" | "de-DE-HeddaRUS"
| | | Masculino | "Microsoft Server voz texto para voz de voz (de-DE, Stefan, Apollo)" | "de-DE-Rodrigo-Apollo"
el GR | Grego | Masculino | "Microsoft Server voz texto para voz de voz (el-GR, Stefanos)" | "El-GR-Stefanos"
EN-AU | Inglês (Austrália) | Feminino | "Microsoft Server voz texto para voz de voz (en-AU, Catherine)" | "en-AU-Catherine"
| | | Feminino | "Microsoft Server voz texto para voz de voz (en-AU, HayleyRUS)" | "en-AU-HayleyRUS"
en-CA | Inglês (Canadá) | Feminino | "Microsoft Server voz texto para voz de voz (en-CA, Linda)" | "en-CA-linda"
| | | Feminino | "Microsoft Server voz texto para voz de voz (en-CA, HeatherRUS)" | "en-CA-HeatherRUS"
en-GB | English (UK) | Feminino | "Microsoft Server voz texto para voz de voz (en-GB, Susan, Apollo)" | "en-GB-Susan-Apollo"
| | | Feminino | "Microsoft Server voz texto para voz de voz (en-GB, HazelRUS)" | "en-GB-HazelRUS"
| | | Masculino | "Microsoft Server voz texto para voz de voz (en-GB, George, Apollo)" | "en-GB-George-Apollo"
EN-IE | Inglês (Irlanda) | Masculino | "Microsoft Server voz texto para voz de voz (en-IE, Sean)" | "en-IE-Sílvio"
EN-IN | English (India) | Feminino | "Microsoft Server voz texto para voz de voz (en-IN, Heera, Apollo)" | "en-IN-Heera-Apollo"
| | | Feminino | "Microsoft Server voz texto para voz de voz (en-IN, PriyaRUS)" | "en-IN-PriyaRUS"
| | | Masculino | "Microsoft Server voz texto para voz de voz (en-IN, Ravi, Apollo)" | "en-IN-Ravi-Apollo"
en-US | English (US) | Feminino | "Microsoft Server voz texto para voz de voz (en-US, ZiraRUS)" | "en-US-ZiraRUS"
| | | Feminino | "Microsoft Server voz texto para voz de voz (en-US, JessaRUS)" | "en-US-JessaRUS"
| | | Masculino | "Microsoft Server voz texto para voz de voz (en-US, BenjaminRUS)" | "en-US-BenjaminRUS"
| | | Feminino | "Microsoft Server voz texto para voz de voz (en-US, Jessa24kRUS)" | "en-US-Jessa24kRUS"
| | | Masculino | "Microsoft Server voz texto para voz de voz (en-US, Guy24kRUS)" | "en-US-Guy24kRUS"
es-ES | Espanhol (Espanha) |Feminino | "Microsoft Server voz texto em voz voz (es-ES, Laura, Apollo)" | "es-ES-Laura-Apollo"
| | | Feminino | "Microsoft Server voz texto em voz voz (es-ES, HelenaRUS)" | "es-ES-HelenaRUS"
| | | Masculino | "Microsoft Server voz texto em voz voz (es-ES, Pablo, Apollo)" | "es-ES-Pablo-Apollo"
es-MX | Espanhol (México) | Feminino | "Voz de texto em voz de voz do Microsoft Server (es-MX, HildaRUS)" | "es-MX-HildaRUS"
| | | Masculino | "Voz de texto em voz de voz do Microsoft Server (es-MX, Raul, Apollo)" | "es-MX-Ricardo-Apollo"
fi-FI | Finlandês | Feminino | "Microsoft Server voz texto para voz de voz (fi-FI, HeidiRUS)" | "fi-FI-HeidiRUS"
fr-CA | Francês (Canadá) |Feminino | "Microsoft Server voz texto em voz voz (fr-AC, Caroline)" | "fr-CA-Carolina"
| | | Feminino | "Microsoft Server voz texto em voz voz (fr-AC, HarmonieRUS)" | "fr-CA-HarmonieRUS"
FR-CH | Francês (Suíça)| Masculino | "Microsoft Server voz texto em voz voz (fr-CH, Guillaume)" | "fr-CH-Guillaume"
FR-FR | Francês (França)| Feminino | "Microsoft Server voz texto em voz voz (fr-FR, Julie, Apollo)" | "fr-FR-Julie-Apollo"
| | | Feminino | "Microsoft Server voz texto em voz voz (fr-FR, HortenseRUS)" | "fr-FR-HortenseRUS"
| | | Masculino | "Microsoft Server voz texto em voz voz (fr-FR, Paul, Apollo)" | "fr-FR-Paul-Apollo"
he-IL| Hebraico (Israel) | Masculino| "Microsoft Server voz texto para voz de voz (he-IL, Asaf)" | "he-IL-Asaf"
IN | Hindi (Índia) | Feminino | "Microsoft Server voz texto para voz de voz (hi-IN, Kalpana, Apollo)" | "hi-IN-Kalpana-Apollo"
| | |Feminino | "Microsoft Server voz texto para voz de voz (hi-IN, Kalpana)" | "hi-IN-Kalpana"
| | | Masculino | "Microsoft Server voz texto para voz de voz (hi-IN, Hemant)" | "hi-IN-Hemant"
RH de RH | Croata | Masculino | "Microsoft Server voz texto em voz voz (RH-RH, Matej)" | "HR-HR-Matej"
hu-HU | Húngaro | Masculino | "Microsoft Server voz texto para voz de voz (hu-HU, Szabolcs)" | "hu-HU-Szabolcs"
id-ID | Indonésio| Masculino | "Microsoft Server voz texto para voz de voz (id-ID, Andika)" | "ID-ID-andika"
it-IT | Italiano | Masculino | "Microsoft Server voz texto para voz de voz (it-IT, Cosimo, Apollo)" | "IT-IT-Cosimo-Apollo"
| | | Feminino | "Microsoft Server voz texto para voz de voz (it-IT, LuciaRUS)" | "IT-IT-LuciaRUS"
ja-JP | Japonês | Feminino | "Microsoft Server voz texto em voz voz (ja-JP, Ayumi, Apollo)" | "ja-JP-Ayumi-Apollo"
| | | Masculino | "Microsoft Server voz texto em voz voz (ja-JP, Ichiro, Apollo)" | "ja-JP-Ichiro-Apollo"
| | | Feminino | "Microsoft Server voz texto em voz voz (ja-JP, HarukaRUS)" | "ja-JP-HarukaRUS"
ko-KR | Coreano | Feminino | "Microsoft Server voz texto para voz de voz (ko-KR, HeamiRUS)" | "ko-KR-HeamiRUS"
MS-MY | Malaio | Masculino | "Voz de texto em voz de voz do Microsoft Server (ms-MY, Rizwan)" | "MS-MY-Rizwan"
nb-NO | Norueguês | Feminino | "Voz de texto em voz de voz do Microsoft Server (não-nb, HuldaRUS)" | "NB-NO-HuldaRUS"
NL-NL | Neerlandês | Feminino | "Microsoft Server voz texto em voz voz (nl-NL, HannaRUS)" | "nl-NL-HannaRUS"
pl-PL | Polaco | Feminino | "Microsoft Server voz texto em voz voz (pl-PL, PaulinaRUS)" | "pl-PL-PaulinaRUS"
pt-BR | Português (Brasil) | Feminino | "Microsoft Server voz texto em voz voz (pt-BR, HeloisaRUS)" | "pt-BR-HeloisaRUS"
| | | Masculino |"Microsoft Server voz texto em voz voz (pt-BR, Daniel, Apollo)" | "pt-BR-Daniel-Apollo"
pt-PT | Português (Portugal) | Feminino | "Microsoft Server voz texto em voz voz (pt-PT, HeliaRUS)" | "pt-PT-HeliaRUS"
RO-RO | Romeno | Masculino | "Microsoft Server voz texto para voz de voz (ro-RO, Andrei)" | "RO-RO-Andrei"
ru-RU |Russo| Feminino | "Microsoft Server voz texto em voz voz (ru-RU, Irina, Apollo)" | "ru-RU-Irina-Apollo"
| | | Masculino | "Microsoft Server voz texto em voz voz (ru-RU, Pavel, Apollo)" | "ru-RU-Pavel-Apollo"
| | | Feminino | "Microsoft Server voz texto em voz voz (ru-RU, EkaterinaRUS)" | ru-RU-EkaterinaRUS
sk SK | Eslovaco | Masculino | "Microsoft Server voz texto em voz voz (sk-SK, Filip)" | "SK-SK-Filip"
IS SL | Esloveno | Masculino | "Microsoft Server voz texto em voz voz (sl-SI, Lado)" | "sl-SI-Lado"
SV-SE | Sueco | Feminino | "Microsoft Server voz texto para voz de voz (sv-SE, HedvigRUS)" | "sv-SE-HedvigRUS"
es-IN | Tamil (Índia) | Masculino | "Microsoft Server voz texto para voz de voz (dados-IN, Valluvar)" | "ta-IN-Valluvar"
te-IN | Telugu (Índia) | Feminino | "Microsoft Server voz texto para voz de voz (te-IN, Chitra)" | "Chitra"
th-TH | Tailandês | Masculino | "Microsoft Server voz texto em voz voz (th-TH, Pattara)" | "th-TH-Pattara"
tr-TR | Turco | Feminino | "Microsoft Server voz texto em voz voz (tr-TR, SedaRUS)" | "tr-TR-SedaRUS"
vi-VN | Vietnamita | Masculino | "Voz de texto em voz de voz do Microsoft Server (vi-VN, um)" | "vi-VN-um"
zh-CN | Chinês (continental) | Feminino | "Microsoft Server voz texto para voz de voz (zh-CN, HuihuiRUS)" | "zh-CN-HuihuiRUS"
| | | Feminino | "Microsoft Server voz texto para voz de voz (zh-CN, Yaoyao, Apollo)" | "zh-CN-Yaoyao-Apollo"
| | | Masculino | "Microsoft Server voz texto para voz de voz (zh-CN, Kangkang, Apollo)" | "zh-CN-Kangkang-Apollo"
zh-HK | Chinês (Hong Kong) | Feminino | "Microsoft Server voz texto para voz de voz (zh-HK Tracy, Apollo)" | "ZH-HK-Tracy-Apollo"
| | | Feminino | "Microsoft Server voz texto para voz de voz (zh-HK TracyRUS)" | "ZH-HK-TracyRUS"
| | | Masculino | "Microsoft Server voz texto para voz de voz (zh-HK Danny, Apollo)" | "ZH-HK-Danny-Apollo"
zh-TW | Chinês (Taiwan) | Feminino | "Microsoft Server voz texto para voz de voz (zh-TW, Yating, Apollo)" | "zh-TW-Yating-Apollo"
| | | Feminino | "Microsoft Server voz texto para voz de voz (zh-TW, HanHanRUS)" | "zh-TW-HanHanRUS"
| | | Masculino | "Microsoft Server voz texto para voz de voz (zh-TW, Zhiwei, Apollo)" | "zh-TW-Zhiwei-Apollo"

\* *ar-por exemplo, oferece suporte a Árabe padrão modernos (MSA).*

> [!NOTE]
> Você pode usar o mapeamento de nome de serviço completo ou o nome de voz curto em suas solicitações de síntese de fala.

### <a name="customization"></a>Personalização

A personalização de voz está disponível para de-DE, en-GB, en-IN, en-US, es-MX, fr-FR, it-IT, pt-BR e zh-CN. Selecione a localidade correta que corresponde aos dados de treinamento que você tem para treinar um modelo de voz personalizado. Por exemplo, se os dados de gravação que você tem são falados em inglês com um sotaque britânico, selecione en-GB.  

> [!NOTE]
> Não damos suporte ao treinamento de modelo de bi-idiomas em voz personalizada, exceto para o Português do idioma chinês-inglês. Selecione "chinês-inglês bilíngüe" se você quiser treinar uma voz chinesa que também pode falar em inglês. O treinamento de voz em todas as localidades começa com um conjunto de dados de 2.000 + declarações, exceto para o en-US e o zh-CN, onde você pode começar com qualquer tamanho de dados de treinamento.

## <a name="speech-translation"></a>Tradução de voz

O **tradução por voz** API suporta idiomas diferentes para a tradução de voz para voz e conversão de voz em texto. O idioma de origem sempre deve ser da tabela de linguagem de fala para texto. Os idiomas de destino disponíveis dependem se o destino de tradução é voz ou texto. Pode converter voz recebida em mais do que [60 idiomas](https://www.microsoft.com/translator/business/languages/). Um subconjunto de idiomas estão disponíveis para [síntese de fala](language-support.md#text-languages).

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
| Tailandês      | `th`          |
| Tongan      | `to`          |
| Turco      | `tr`          |
| Ucraniano      | `uk`          |
| Urdu      | `ur`          |
| Vietnamita      | `vi`          |
| Galês      | `cy`          |
| Yucatec Maya      | `yua`          |


## <a name="next-steps"></a>Passos Seguintes

* [Obter a subscrição de avaliação dos Serviços de Voz](https://azure.microsoft.com/try/cognitive-services/)
* [Veja como a reconhecer a conversão de voz em c#](quickstart-csharp-dotnet-windows.md)
