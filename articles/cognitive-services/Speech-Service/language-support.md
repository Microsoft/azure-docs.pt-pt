---
title: Suporte de idiomas - serviços de voz
titleSuffix: Azure Cognitive Services
description: Os serviços de voz do Azure suporta vários idiomas para conversão de voz em texto e texto para voz, juntamente com a tradução de voz. Este artigo fornece uma lista abrangente de suporte de idiomas pelo serviço.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 3188b5acc00f7f0463ae1154346447bee463e09c
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58225438"
---
# <a name="language-and-region-support-for-the-speech-services"></a>Suporte de idioma e região para os serviços de voz

Diferentes idiomas são suportados para diferentes funções de serviços de voz. As tabelas seguintes resumem o suporte de idiomas.

## <a name="speech-to-text"></a>Conversão de voz em texto

A API de reconhecimento de voz de Microsoft suporta os seguintes idiomas. Estão disponíveis para cada idioma diferentes níveis de personalização.

  Código | Idioma | [Adaptação acústica](how-to-customize-acoustic-models.md) | [Adaptação de idioma](how-to-customize-language-model.md) | [Adaptação de pronúncia](how-to-customize-pronunciation.md)
 ------|----------|---------------------|---------------------|-------------------------
 ar-por exemplo | Árabe (Egito), standard moderna | Não | Sim | Não
 ES de AC | Catalão | Não | Não | Não
 da-DK | Dinamarquês (Dinamarca) | Não | Não | Não
 de-DE | Alemão (Alemanha) | Sim | Sim | Não
 EN-AU | Inglês (Austrália) | Não | Sim | Sim
 en-CA | Inglês (Canadá) | Não | Sim | Sim
 en-GB | Inglês (Reino Unido) | Não | Sim | Sim
 EN-IN | English (India) | Sim | Sim | Sim
 EN-NZ | Inglês (Nova Zelândia) | Não | Sim | Sim  
 en-US | Inglês (Estados Unidos) | Sim | Sim | Sim
 es-ES | Espanhol (Espanha) | Sim | Sim | Não
 es-MX | Espanhol (México) | Não | Sim | Não
 fi-FI | Finlandês (Finlândia) | Não | Não | Não
 fr-CA | Francês (Canadá) | Não | Sim | Não
 FR-FR | Francês (França) | Sim | Sim | Não
 IN | Hindi (Índia) | Não | Sim | Não
 it-IT | Italiano (Itália) | Sim | Sim | Não
 ja-JP | Japonês (Japão) | Não | Sim | Não
 ko-KR | Coreano (Coreia) | Não | Sim | Não
 nb-NO | Norueguês (Bokmål) (Noruega) | Não | Não | Não
 NL-NL | Holandês (Países Baixos) | Não | Sim | Não
 pl-PL | Polaco (Polónia) | Não | Não | Não
 pt-BR | Português (Brasil) | Sim | Sim | Não
 pt-PT | Português (Portugal) | Não | Sim | Não
 ru-RU | Russo (Rússia) | Sim | Sim | Não
 SV-SE | Sueco (Suécia) | Não | Não | Não
 zh-CN | Chinês (simplificado Mandarim) | Sim | Sim | Não
 zh-HK | Chinês (Mandarim, tradicional) | Não | Sim | Não
 zh-TW | Chinês (Taiwanês Mandarim) | Não | Sim | Não
 th-TH | Tailandês (Tailândia) | Não | Não | Não


## <a name="text-to-speech"></a>Conversão de texto em voz

A API REST do texto para discurso dá suporte essas vozes, cada um deles suporta um idioma específico e dialect, identificado por localidade.

> [!IMPORTANT]
> Os preços variam de vozes neurais, padrão e personalizados. Visite o [preços](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) página para obter informações adicionais.

### <a name="neural-voices-preview"></a>Vozes neurais (pré-visualização)

Voz neural é um novo tipo de síntese de fala com tecnologia de redes neurais profundas. Quando utilizar uma voz neural, fala sintetizada é quase distinguir das gravações humanas.

Vozes neurais podem ser utilizados para tornar as interações com chatbots e assistentes virtual mais natural e envolventes converter textos digital, como o e-livros em audiobooks e melhorar a sistemas de navegação no carro. Com o prosody natural de semelhante à humana e articulation clara de palavras, o vozes neurais reduzem significativamente fadiga escuta quando os utilizadores interagem com sistemas de IA.

Para obter uma lista completa de vozes neurais e a disponibilidade regional, consulte [regiões](regions.md#neural-voices).

| Região | Idioma | Género | Mapeamento de nome de serviço|
|--------|----------|--------|---------------------|
| de-DE | Alemão (Alemanha) | Feminino | "Microsoft Server voz texto para voz de voz (de-DE, KatjaNeural)" |
| en-US | English (US) | Masculino | "Microsoft Server voz texto para voz de voz (en-US, GuyNeural)" |
| en-US | English (US) | Feminino | "Microsoft Server voz texto para voz de voz (en-US, JessaNeural)" |
| it-IT | Italiano (Itália) | Feminino | "Microsoft Server voz texto para voz de voz (it-IT, ElsaNeural)" |
| zh-CN | Chinês | Feminino | "Microsoft Server voz texto para voz de voz (zh-CN, XiaoxiaoNeural)" |

> [!IMPORTANT]
> Microsoft Server voz texto para voz de voz (zh-CN, XiaoxiaoNeural) só está disponível através do ponto de extremidade do Sudeste asiático: https://southeastasia.tts.speech.microsoft.com/cognitiveservices/v1.

> [!IMPORTANT]
> Microsoft Server voz texto para voz de voz (de-DE, KatjaNeural) e Microsoft Server voz texto para voz de voz (it-IT, ElsaNeural) apenas estão disponíveis através do ponto de extremidade da Europa Ocidental: https://westeurope.tts.speech.microsoft.com/cognitiveservices/v1.

### <a name="standard-voices"></a>Vozes padrão

Mais de 75 vozes standard estão disponíveis em mais de 45 idiomas e localidades, que permitem converter texto em fala sintetizada. Para obter mais informações sobre a disponibilidade regional, consulte [regiões](regions.md#standard-voices).

Região | Idioma | Género | Mapeamento de nome de serviço
-------|----------|---------|--------------------
ar-por exemplo\* | Árabe (Egito) | Feminino | "Microsoft Server voz texto para voz de voz (ar-por exemplo, Hoda)"
ar SA | Árabe (Arábia Saudita) | Masculino | "Microsoft Server voz texto para voz de voz (ar-SA, Naayf)"
bg-BG | Búlgaro | Masculino | "Microsoft Server voz texto em voz voz (bg-BG, Ivan)"
ES de AC | Catalão | Feminino | "Microsoft Server voz texto em voz voz (AC-ES, HerenaRUS)"
cs-CZ | Checo | Masculino | "Microsoft Server voz texto para voz de voz (cs-CZ, Jakub)"
da-DK | Dinamarquês | Feminino | "Microsoft Server voz texto para voz de voz (da-DK, HelleRUS)"
Alemanha-AT | Alemão (Áustria) | Masculino | "Microsoft Server voz texto para voz de voz (Alemanha-AT, Michael)"
Alemanha-CH | Alemão (Suíça) | Masculino | "Microsoft Server voz texto para voz de voz (Alemanha-CH, Karsten)"
de-DE | Alemão (Alemanha) | Feminino | "Microsoft Server voz texto para voz de voz (de-DE, Hedda)"
| | | Feminino | "Microsoft Server voz texto para voz de voz (de-DE, HeddaRUS)"
| | | Masculino | "Microsoft Server voz texto para voz de voz (de-DE, Stefan, Apollo)"
el GR | Grego | Masculino | "Microsoft Server voz texto para voz de voz (el-GR, Stefanos)"
EN-AU | Inglês (Austrália) | Feminino | "Microsoft Server voz texto para voz de voz (en-AU, Catherine)"
| | | Feminino | "Microsoft Server voz texto para voz de voz (en-AU, HayleyRUS)"
en-CA | Inglês (Canadá) | Feminino | "Microsoft Server voz texto para voz de voz (en-CA, Linda)"
| | | Feminino | "Microsoft Server voz texto para voz de voz (en-CA, HeatherRUS)"
en-GB | English (UK) | Feminino | "Microsoft Server voz texto para voz de voz (en-GB, Susan, Apollo)"
| | |Feminino | "Microsoft Server voz texto para voz de voz (en-GB, HazelRUS)"
| | |Masculino | "Microsoft Server voz texto para voz de voz (en-GB, George, Apollo)"
EN-IE | Inglês (Irlanda) |Masculino | "Microsoft Server voz texto para voz de voz (en-IE, Sean)"
EN-IN | English (India) | Feminino | "Microsoft Server voz texto para voz de voz (en-IN, Heera, Apollo)"
| | |Feminino | "Microsoft Server voz texto para voz de voz (en-IN, PriyaRUS)"
| | |Masculino | "Microsoft Server voz texto para voz de voz (en-IN, Ravi, Apollo)"
en-US | English (US) |Feminino | "Microsoft Server voz texto para voz de voz (en-US, ZiraRUS)"
| | |Feminino | "Microsoft Server voz texto para voz de voz (en-US, JessaRUS)"
| | |Masculino | "Microsoft Server voz texto para voz de voz (en-US, BenjaminRUS)"
| | |Feminino | "Microsoft Server voz texto para voz de voz (en-US, Jessa24kRUS)"
| | |Masculino | "Microsoft Server voz texto para voz de voz (en-US, Guy24kRUS)"
es-ES | Espanhol (Espanha) |Feminino | "Microsoft Server voz texto em voz voz (es-ES, Laura, Apollo)"
| | |Feminino | "Microsoft Server voz texto em voz voz (es-ES, HelenaRUS)"
| | |Masculino | "Microsoft Server voz texto em voz voz (es-ES, Pablo, Apollo)"
es-MX | Espanhol (México) | Feminino | "Voz de texto em voz de voz do Microsoft Server (es-MX, HildaRUS)"
| | | Masculino | "Voz de texto em voz de voz do Microsoft Server (es-MX, Raul, Apollo)"
fi-FI | Finlandês | Feminino | "Microsoft Server voz texto para voz de voz (fi-FI, HeidiRUS)"
fr-CA | Francês (Canadá) |Feminino | "Microsoft Server voz texto em voz voz (fr-AC, Caroline)"
| | | Feminino | "Microsoft Server voz texto em voz voz (fr-AC, HarmonieRUS)"
FR-CH | Francês (Suíça)|Masculino | "Microsoft Server voz texto em voz voz (fr-CH, Guillaume)"
FR-FR | Francês (França)|Feminino | "Microsoft Server voz texto em voz voz (fr-FR, Julie, Apollo)"
| | |Feminino | "Microsoft Server voz texto em voz voz (fr-FR, HortenseRUS)"
| | |Masculino | "Microsoft Server voz texto em voz voz (fr-FR, Paul, Apollo)"
he-IL| Hebraico (Israel) | Masculino| "Microsoft Server voz texto para voz de voz (he-IL, Asaf)"
IN | Hindi (Índia) | Feminino | "Microsoft Server voz texto para voz de voz (hi-IN, Kalpana, Apollo)"
| | |Feminino | "Microsoft Server voz texto para voz de voz (hi-IN, Kalpana)"
| | | Masculino | "Microsoft Server voz texto para voz de voz (hi-IN, Hemant)"
RH de RH | Croata | Masculino | "Microsoft Server voz texto em voz voz (RH-RH, Matej)"
hu-HU | Húngaro | Masculino | "Microsoft Server voz texto para voz de voz (hu-HU, Szabolcs)"
id-ID | Indonésio| Masculino | "Microsoft Server voz texto para voz de voz (id-ID, Andika)"
it-IT | Italiano |Masculino | "Microsoft Server voz texto para voz de voz (it-IT, Cosimo, Apollo)"
| | |Feminino | "Microsoft Server voz texto para voz de voz (it-IT, LuciaRUS)"
ja-JP | Japonês |Feminino | "Microsoft Server voz texto em voz voz (ja-JP, Ayumi, Apollo)"
| | |Masculino | "Microsoft Server voz texto em voz voz (ja-JP, Ichiro, Apollo)"
| | |Feminino | "Microsoft Server voz texto em voz voz (ja-JP, HarukaRUS)"
ko-KR | Coreano |Feminino | "Microsoft Server voz texto para voz de voz (ko-KR, HeamiRUS)"
MS-MY | Malaio | Masculino | "Voz de texto em voz de voz do Microsoft Server (ms-MY, Rizwan)"
nb-NO | Norueguês | Feminino | "Voz de texto em voz de voz do Microsoft Server (não-nb, HuldaRUS)"
NL-NL | Neerlandês | Feminino | "Microsoft Server voz texto em voz voz (nl-NL, HannaRUS)"
pl-PL | Polaco | Feminino | "Microsoft Server voz texto em voz voz (pl-PL, PaulinaRUS)"
pt-BR | Português (Brasil) | Feminino | "Microsoft Server voz texto em voz voz (pt-BR, HeloisaRUS)"
| | |Masculino | "Microsoft Server voz texto em voz voz (pt-BR, Daniel, Apollo)"
pt-PT | Português (Portugal) | Feminino | "Microsoft Server voz texto em voz voz (pt-PT, HeliaRUS)"
RO-RO | Romeno | Masculino | "Microsoft Server voz texto para voz de voz (ro-RO, Andrei)"
ru-RU |Russo| Feminino | "Microsoft Server voz texto em voz voz (ru-RU, Irina, Apollo)"
| | |Masculino | "Microsoft Server voz texto em voz voz (ru-RU, Pavel, Apollo)"
| | |Feminino | "Microsoft Server voz texto em voz voz (ru-RU, EkaterinaRUS)"
sk SK | Eslovaco|Masculino | "Microsoft Server voz texto em voz voz (sk-SK, Filip)"
IS SL | Esloveno|Masculino | "Microsoft Server voz texto em voz voz (sl-SI, Lado)"
SV-SE | Sueco|Feminino | "Microsoft Server voz texto para voz de voz (sv-SE, HedvigRUS)"
es-IN | Tamil (Índia) |Masculino | "Microsoft Server voz texto para voz de voz (dados-IN, Valluvar)"
te-IN | Telugu (Índia) |Feminino | "Microsoft Server voz texto para voz de voz (te-IN, Chitra)"
th-TH | Tailandês|Masculino | "Microsoft Server voz texto em voz voz (th-TH, Pattara)"
tr-TR |Turco| Feminino | "Microsoft Server voz texto em voz voz (tr-TR, SedaRUS)"
vi-VN | Vietnamita|Masculino | "Voz de texto em voz de voz do Microsoft Server (vi-VN, um)"
zh-CN | Chinês (continental)|Feminino | "Microsoft Server voz texto para voz de voz (zh-CN, HuihuiRUS)"
| | |Feminino | "Microsoft Server voz texto para voz de voz (zh-CN, Yaoyao, Apollo)"
| | |Masculino | "Microsoft Server voz texto para voz de voz (zh-CN, Kangkang, Apollo)"
zh-HK | Chinês (Hong Kong)|Feminino | "Microsoft Server voz texto para voz de voz (zh-HK Tracy, Apollo)"
| | |Feminino | "Microsoft Server voz texto para voz de voz (zh-HK TracyRUS)"
| || Masculino | "Microsoft Server voz texto para voz de voz (zh-HK Danny, Apollo)"
zh-TW | Chinês (Taiwan)|Feminino | "Microsoft Server voz texto para voz de voz (zh-TW, Yating, Apollo)"
| || Feminino | "Microsoft Server voz texto para voz de voz (zh-TW, HanHanRUS)"
| || Masculino | "Microsoft Server voz texto para voz de voz (zh-TW, Zhiwei, Apollo)"

\* *ar-por exemplo, oferece suporte a Árabe padrão modernos (MSA).*

### <a name="customization"></a>Personalização

Personalização de voz está disponível para nós inglês (en-US), continente chinês (zh-CN), francês (fr-FR), alemão (Alemanha-DE) e italiano (it-IT).

> [!NOTE]
> Italiano, alemão e francês formação de voz começa com um conjunto de dados de expressões com mais de 2000. Modelos de bilingue chinês-inglês também são suportados com um conjunto de dados inicial de expressões com mais de 2000.

## <a name="speech-translation"></a>Tradução de voz

O **tradução por voz** API suporta idiomas diferentes para a tradução de voz para voz e conversão de voz em texto. O idioma de origem tem de ser sempre da tabela de idiomas de voz em texto. Os idiomas de destino disponíveis dependem se o destino de tradução é voz ou texto. Pode converter voz recebida em mais do que [60 idiomas](https://www.microsoft.com/translator/business/languages/). Um subconjunto de idiomas estão disponíveis para [síntese de fala](language-support.md#text-languages).

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
