---
title: Limitações de contêiner-LUIS
titleSuffix: Azure Cognitive Services
description: Os idiomas do contêiner LUIS com suporte.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: bd8a7a63113bcf4e972ab08655aa58b35ddff03d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73507860"
---
# <a name="language-understanding-luis-container-limitations"></a>Limitações de contêiner de Reconhecimento vocal (LUIS)

Os contêineres LUIS têm algumas limitações notáveis. De dependências sem suporte, a um subconjunto de idiomas com suporte, este artigo detalha essas restrições.

## <a name="supported-dependencies-for-latest-container"></a>Dependências com suporte para `latest` contêiner

O contêiner LUIS mais recente, lançado em [conferência//build/2019](https://news.microsoft.com/build2019/), dará suporte a:

* [Novos domínios pré-criados](luis-reference-prebuilt-domains.md): esses domínios voltados para a empresa incluem entidades, exemplos de declarações e padrões. Estenda esses domínios para seu próprio uso.

## <a name="unsupported-dependencies-for-latest-container"></a>Dependências sem suporte para `latest` contêiner

Para [exportar para o contêiner](luis-container-howto.md#export-packaged-app-from-luis), você deve remover dependências sem suporte do seu aplicativo Luis. Quando você tenta exportar para o contêiner, o portal do LUIS relata esses recursos sem suporte que você precisa remover.

Você pode usar um aplicativo LUIS se ele **não incluir** nenhuma das seguintes dependências:

Configurações de aplicativo sem suporte|Detalhes|
|--|--|
|Culturas de contêiner sem suporte| Holandês (`nl-NL`)<br>Japonês (`ja-JP`)<br>O alemão só tem suporte com o [criador 1.0.2](luis-language-support.md#custom-tokenizer-versions).|
|Entidades sem suporte para todas as culturas|[KeyPhrase](luis-reference-prebuilt-keyphrase.md) entidade predefinida para todas as culturas|
|Entidades sem suporte para cultura em inglês (`en-US`)|[GeographyV2](luis-reference-prebuilt-geographyV2.md) entidades predefinidas|
|Espriming de fala|Não há suporte para dependências externas no contêiner.|
|Análise de sentimentos|Não há suporte para dependências externas no contêiner.|
|Verificação ortográfica do Bing|Não há suporte para dependências externas no contêiner.|

## <a name="languages-supported"></a>Idiomas com suporte

Os contêineres LUIS dão suporte a um subconjunto dos [idiomas com suporte](luis-language-support.md#languages-supported) do Luis apropriado. Os contêineres LUIS são capazes de entender declarações nos seguintes idiomas:

| Idioma | Região | Domínio predefinido | Entidade predefinida | Recomendações da lista de frases | **[análise de texto](../text-analytics/language-support.md)<br>(Sentimentos e<br>Palavras-chave|
|--|--|:--:|:--:|:--:|:--:|
| Inglês americano | `en-US` | ✔️ | ✔️ | ✔️ | ✔️ |
| *[chinês](#chinese-support-notes) |`zh-CN` | ✔️ | ✔️ | ✔️ | ❌ |
| Francês (França) |`fr-FR` | ✔️ | ✔️ | ✔️ | ✔️ |
| Francês (Canadá) |`fr-CA` | ❌ | ❌ | ❌ | ✔️ |
| Alemão |`de-DE` | ✔️ | ✔️ | ✔️ | ✔️ |
| Hindi | `hi-IN`| ❌ | ❌ | ❌ | ❌ |
| Italiano |`it-IT` | ✔️ | ✔️ | ✔️ | ✔️ |
| Coreano |`ko-KR` | ✔️ | ❌ | ❌ | Somente *frase-chave* |
| Português (Brasil) |`pt-BR` | ✔️ | ✔️ | ✔️ | Nem todas as subculturas |
| Espanhol (Espanha) |`es-ES` | ✔️ | ✔️ |✔️|✔️|
| Espanhol (México)|`es-MX` | ❌ | ❌ |✔️|✔️|
| Turco | `tr-TR` |✔️| ❌ | ❌ | Somente *sentimentos* |

[!INCLUDE [Chinese language support notes](includes/chinese-language-support-notes.md)]

[!INCLUDE [Text Analytics support notes](includes/text-analytics-support-notes.md)]