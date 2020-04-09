---
title: Limitações de contentores - LUIS
titleSuffix: Azure Cognitive Services
description: As línguas de contentores LUIS que são suportadas.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 7fe773b35c5aba31b2fea66bd2be7b2745eac3ee
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879246"
---
# <a name="language-understanding-luis-container-limitations"></a>Limitações dos contentores de compreensão linguística (LUIS)

Os contentores LUIS têm algumas limitações notáveis. Desde dependências não apoiadas, até um subconjunto de línguas suportadas, este artigo detalha estas restrições.

## <a name="supported-dependencies-for-latest-container"></a>Dependências suportadas `latest` para contentores

O mais recente contentor LUIS, lançado em [//build/ 2019,](https://news.microsoft.com/build2019/)irá apoiar:

* [Novos domínios pré-construídos](luis-reference-prebuilt-domains.md): estes domínios centrados na empresa incluem entidades, expressões de exemplo e padrões. Estenda estes domínios para seu próprio uso.

## <a name="unsupported-dependencies-for-latest-container"></a>Dependências não suportadas para `latest` contentor

Para [exportar para recipiente,](luis-container-howto.md#export-packaged-app-from-luis)deve remover dependências não suportadas da sua aplicação LUIS. Quando tenta exportar para contentor, o portal LUIS informa estas características não suportadas que precisa de remover.

Pode utilizar uma aplicação LUIS se **não incluir** nenhuma das seguintes dependências:

Configurações de aplicativos não suportados|Detalhes|
|--|--|
|Culturas de contentores não suportadas| Holandês`nl-NL`( )<br>Japonês`ja-JP`( )<br>O alemão só é apoiado com o [tokenizer 1.0.2.](luis-language-support.md#custom-tokenizer-versions)|
|Entidades não apoiadas para todas as culturas|Entidade pré-construída [KeyPhrase](luis-reference-prebuilt-keyphrase.md) para todas as culturas|
|Entidades não apoiadas`en-US`para a cultura inglesa ( )|[GeografiaV2](luis-reference-prebuilt-geographyV2.md) entidades pré-construídas|
|Preparação do discurso|As dependências externas não são suportadas no recipiente.|
|Análise de sentimentos|As dependências externas não são suportadas no recipiente.|
|Verificação de feitiço sinuoso|As dependências externas não são suportadas no recipiente.|

## <a name="languages-supported"></a>Idiomas suportados

Os recipientes LUIS suportam um subconjunto das [línguas suportadas](luis-language-support.md#languages-supported) pelo PRÓPRIO LUIS. Os recipientes LUIS são capazes de compreender as expressões nas seguintes línguas:

| Idioma | Região | Domínio pré-construído | Entidade pré-construída | Recomendações de lista de frases | **[Análise de texto](../text-analytics/language-support.md)<br>(Sentimento e<br>Palavras-chave)|
|--|--|:--:|:--:|:--:|:--:|
| Inglês Americano | `en-US` | ✔️ | ✔️ | ✔️ | ✔️ |
| *[Chinês](#chinese-support-notes) |`zh-CN` | ✔️ | ✔️ | ✔️ | ❌ |
| Francês (França) |`fr-FR` | ✔️ | ✔️ | ✔️ | ✔️ |
| Francês (Canadá) |`fr-CA` | ❌ | ❌ | ❌ | ✔️ |
| Alemão |`de-DE` | ✔️ | ✔️ | ✔️ | ✔️ |
| Hindi | `hi-IN`| ❌ | ❌ | ❌ | ❌ |
| Italiano |`it-IT` | ✔️ | ✔️ | ✔️ | ✔️ |
| Coreano |`ko-KR` | ✔️ | ❌ | ❌ | *Só frase-chave* |
| Português (Brasil) |`pt-BR` | ✔️ | ✔️ | ✔️ | nem todas as subculturas |
| Espanhol (Espanha) |`es-ES` | ✔️ | ✔️ |✔️|✔️|
| Espanhol (México)|`es-MX` | ❌ | ❌ |✔️|✔️|
| Turco | `tr-TR` |✔️| ❌ | ❌ | *Apenas sentimento* |

[!INCLUDE [Chinese language support notes](includes/chinese-language-support-notes.md)]

[!INCLUDE [Text Analytics support notes](includes/text-analytics-support-notes.md)]