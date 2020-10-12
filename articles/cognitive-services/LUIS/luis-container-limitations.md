---
title: Limitações do contentor - LUIS
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
ms.openlocfilehash: 5c76e45f09a0f432d2775eb19d3dcaa668294e6f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84672668"
---
# <a name="language-understanding-luis-container-limitations"></a>Limitações do contentor de compreensão linguística (LUIS)

Os contentores LUIS têm algumas limitações notáveis. Desde dependências não apoiadas, até um subconjunto de línguas apoiadas, este artigo detalha estas restrições.

## <a name="supported-dependencies-for-latest-container"></a>Dependências apoiadas para `latest` contentor

O mais recente recipiente LUIS suporta:

* [Novos domínios pré-construídos](luis-reference-prebuilt-domains.md): estes domínios centrados na empresa incluem entidades, exemplos de expressões e padrões. Estenda estes domínios para uso próprio.

## <a name="unsupported-dependencies-for-latest-container"></a>Dependências não suportadas para `latest` contentor

Para [exportar para recipiente,](luis-container-howto.md#export-packaged-app-from-luis)deve remover dependências não suportadas da sua aplicação LUIS. Quando tenta exportar para contentor, o portal LUIS informa estas funcionalidades não apoiadas que precisa de remover.

Pode utilizar uma aplicação LUIS se **não incluir** nenhuma das seguintes dependências:

Configurações de aplicativos não suportados|Detalhes|
|--|--|
|Culturas de contentores não suportadas| As `nl-NL` línguas holandesas ( `ja-JP` japonesas ) e `de-DE` alemãs são apenas suportadas com o [tokenizer 1.0.2](luis-language-support.md#custom-tokenizer-versions).|
|Entidades não apoiadas para todas as culturas|Entidade pré-construída [KeyPhrase](luis-reference-prebuilt-keyphrase.md) para todas as culturas|
|Entidades não apoiadas para a cultura inglesa `en-US` ()|[GeografiaV2](luis-reference-prebuilt-geographyV2.md) entidades pré-construídas|
|Escorva da fala|As dependências externas não são suportadas no contentor.|
|Análise de sentimentos|As dependências externas não são suportadas no contentor.|
|Verificação ortográfica de Bing|As dependências externas não são suportadas no contentor.|

## <a name="languages-supported"></a>Idiomas suportados

Os recipientes LUIS suportam um subconjunto das [línguas suportadas](luis-language-support.md#languages-supported) pelo PRÓPRIO LUIS. Os recipientes LUIS são capazes de compreender as expressões nas seguintes línguas:

| Idioma | Região | Domínio pré-construído | Entidade pré-construída | Recomendações da lista de frases | **[Análise de texto](../text-analytics/language-support.md)<br>(Sentimento e<br>Palavras-chave)|
|--|--|:--:|:--:|:--:|:--:|
| Inglês (Estados Unidos) | `en-US` | ✔️ | ✔️ | ✔️ | ✔️ |
| Árabe (pré-visualização - árabe moderno padrão) |`ar-AR`|❌|❌|❌|❌|
| *[Chinês](#chinese-support-notes) |`zh-CN` | ✔️ | ✔️ | ✔️ | ❌ |
| Francês (França) |`fr-FR` | ✔️ | ✔️ | ✔️ | ✔️ |
| Francês (Canadá) |`fr-CA` | ❌ | ❌ | ❌ | ✔️ |
| Alemão |`de-DE` | ✔️ | ✔️ | ✔️ | ✔️ |
| Hindi | `hi-IN`| ❌ | ❌ | ❌ | ❌ |
| Italiano |`it-IT` | ✔️ | ✔️ | ✔️ | ✔️ |
| Coreano |`ko-KR` | ✔️ | ❌ | ❌ | *Frase-chave* apenas |
| Marata | `mr-IN`|❌|❌|❌|❌|
| Português (Brasil) |`pt-BR` | ✔️ | ✔️ | ✔️ | nem todas as subculturas |
| Espanhol (Espanha) |`es-ES` | ✔️ | ✔️ |✔️|✔️|
| Espanhol (México)|`es-MX` | ❌ | ❌ |✔️|✔️|
| Tâmil | `ta-IN`|❌|❌|❌|❌|
| Telugu | `te-IN`|❌|❌|❌|❌|
| Turco | `tr-TR` |✔️| ❌ | ❌ | *Sentimento* apenas |

[!INCLUDE [Chinese language support notes](includes/chinese-language-support-notes.md)]

[!INCLUDE [Text Analytics support notes](includes/text-analytics-support-notes.md)]
