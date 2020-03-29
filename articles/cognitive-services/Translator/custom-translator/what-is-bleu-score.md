---
title: O que é uma pontuação bleu? - Tradutor Personalizado
titleSuffix: Azure Cognitive Services
description: Bleu é uma medição das diferenças entre tradução automática e traduções de referência criadas pelo homem da mesma frase de origem.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: swmachan
ms.openlocfilehash: 85c4ee27a828a05c64ca6cbf84bff438535328be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "73647364"
---
# <a name="what-is-a-bleu-score"></a>O que é uma pontuação bleu?

[BLEU (Subestudar de Avaliação Bilingue)](https://en.wikipedia.org/wiki/BLEU) é uma medição das diferenças entre uma tradução automática e uma ou mais traduções de referência criadas pelo homem da mesma frase de origem.

## <a name="scoring-process"></a>Processo de pontuação

O algoritmo BLEU compara frases consecutivas da tradução automática com as frases consecutivas que encontra na tradução de referência, e conta o número de fósforos, de forma ponderada. Estes fósforos são independentes. Um grau de correspondência mais elevado indica um maior grau de semelhança com a tradução de referência, e uma pontuação mais alta. A inteligibilidade e a correção gramatical não são tidas em conta.

## <a name="how-bleu-works"></a>Como funciona a BLEU?

A força da BLEU é que se relaciona bem com o julgamento humano, calculando erros de julgamento individuais sobre um corpo de teste, em vez de tentar conceber o julgamento humano exato para cada frase.

Uma discussão mais alargada sobre as pontuações do BLEU está [aqui.](https://youtu.be/-UqDljMymMg)

Os resultados da BLEU dependem fortemente da amplitude do seu domínio, da consistência dos dados de teste com os dados de treino e afinação, e quantos dados tem disponível para treinar. Se os seus modelos foram treinados num domínio estreito, e os seus dados de treino forem consistentes com os seus dados de teste, pode esperar uma pontuação alta de BLEU.

>[!NOTE]
>Uma comparação entre as pontuações de BLEU só é justificável quando os resultados do BLEU são comparados com o mesmo conjunto de teste, o mesmo par de idiomas e o mesmo motor MT. Uma pontuação BLEU de um conjunto de testes diferente é certamente diferente.
