---
title: O que é uma pontuação bleu? - Tradutor Personalizado
titleSuffix: Azure Cognitive Services
description: BLEU é uma medição das diferenças entre a tradução automática e as traduções de referência criadas pelo homem da mesma frase de origem.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: swmachan
ms.openlocfilehash: 61c1efb7337bee5fe329c9d548e23f3931e6ce3c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88510781"
---
# <a name="what-is-a-bleu-score"></a>O que é uma pontuação bleu?

[BLEU (Subsududy de Avaliação Bilingue)](https://en.wikipedia.org/wiki/BLEU) é uma medição das diferenças entre uma tradução automática e uma ou mais traduções de referência criadas pelo homem da mesma frase de origem.

## <a name="scoring-process"></a>Processo de pontuação

O algoritmo BLEU compara frases consecutivas da tradução automática com as frases consecutivas que encontra na tradução de referência, e conta o número de partidas, de forma ponderada. Estes fósforos são independentes de posição. Um grau de correspondência superior indica um maior grau de semelhança com a tradução de referência, e uma pontuação mais alta. A inteligibilidade e a correção gramatical não são tidas em conta.

## <a name="how-bleu-works"></a>Como funciona a BLEU?

A força da BLEU é que se relaciona bem com o julgamento humano, calculando erros individuais de julgamento sobre um corpus de teste, em vez de tentar conceber o julgamento humano exato para cada frase.

Uma discussão mais alargada sobre as pontuações da BLEU está [aqui.](https://youtu.be/-UqDljMymMg)

Os resultados da BLEU dependem fortemente da amplitude do seu domínio, da consistência dos dados do teste com os dados de treino e afinação e da quantidade de dados disponíveis para treinar. Se os seus modelos foram treinados num domínio estreito e os seus dados de treino são consistentes com os dados do seu teste, pode esperar uma pontuação bleu elevada.

>[!NOTE]
>Uma comparação entre as pontuações bleu só é justificável quando os resultados da BLEU são comparados com o mesmo conjunto de teste, o mesmo par de idiomas e o mesmo motor MT. Uma pontuação BLEU de um conjunto de testes diferente é certamente diferente.
