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
ms.openlocfilehash: e50dd636c1bd9403d5eca2157e4e46772418f92c
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83592904"
---
# <a name="language-understanding-luis-container-limitations"></a>Limitações dos contentores de compreensão linguística (LUIS)

Os contentores LUIS têm algumas limitações notáveis. Desde dependências não apoiadas, até um subconjunto de línguas suportadas, este artigo detalha estas restrições.

## <a name="supported-dependencies-for-latest-container"></a>Dependências suportadas para `latest` contentores

O mais recente contentor LUIS suporta:

* [Novos domínios pré-construídos](luis-reference-prebuilt-domains.md): estes domínios centrados na empresa incluem entidades, expressões de exemplo e padrões. Estenda estes domínios para seu próprio uso.

## <a name="unsupported-dependencies-for-latest-container"></a>Dependências não suportadas para `latest` contentor

Para [exportar para recipiente,](luis-container-howto.md#export-packaged-app-from-luis)deve remover dependências não suportadas da sua aplicação LUIS. Quando tenta exportar para contentor, o portal LUIS informa estas características não suportadas que precisa de remover.

Pode utilizar uma aplicação LUIS se **não incluir** nenhuma das seguintes dependências:

Configurações de aplicativos não suportados|Detalhes|
|--|--|
|Culturas de contentores não suportadas| Holandês ( `nl-NL` )<br>Japonês ( `ja-JP` )<br>O alemão só é apoiado com o [tokenizer 1.0.2.](luis-language-support.md#custom-tokenizer-versions)|
|Entidades não apoiadas para todas as culturas|Entidade pré-construída [KeyPhrase](luis-reference-prebuilt-keyphrase.md) para todas as culturas|
|Entidades não apoiadas para a cultura inglesa ( `en-US` )|[GeografiaV2](luis-reference-prebuilt-geographyV2.md) entidades pré-construídas|
|Preparação do discurso|As dependências externas não são suportadas no recipiente.|
|Análise de sentimentos|As dependências externas não são suportadas no recipiente.|
|Verificação de feitiço sinuoso|As dependências externas não são suportadas no recipiente.|

## <a name="languages-supported"></a>Idiomas suportados

Os recipientes LUIS suportam um subconjunto das [línguas suportadas](luis-language-support.md#languages-supported) pelo PRÓPRIO LUIS. Os recipientes LUIS são capazes de compreender as expressões nas seguintes línguas:

| Linguagem | Região | Domínio pré-construído | Entidade pré-construída | Recomendações de lista de frases | **[Análise de texto](../text-analytics/language-support.md)<br>(Sentimento e<br>Palavras-chave)|
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