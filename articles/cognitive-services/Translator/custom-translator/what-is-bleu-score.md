---
title: O que é uma pontuação de BLEU? -Tradutor personalizado
titleSuffix: Azure Cognitive Services
description: BLEU é uma medida das diferenças entre uma tradução automática e uma ou mais traduções de referência criadas por humanos da mesma sentença de origem. O algoritmo BLEU compara frases consecutivas da tradução automática com as frases consecutivas encontradas na tradução de referência e conta o número de correspondências de forma ponderada.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: swmachan
ms.openlocfilehash: 1993819ad227d7e9aa5ef899045e00447a6740b8
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595412"
---
# <a name="what-is-a-bleu-score"></a>O que é uma pontuação de BLEU?

[Bleu (antiestudo de avaliação bilíngüe)](https://en.wikipedia.org/wiki/BLEU) é uma medida das diferenças entre uma tradução automática e uma ou mais traduções de referência criadas por humanos da mesma frase de origem.

## <a name="scoring-process"></a>Processo de Pontuação

O algoritmo BLEU compara frases consecutivas da tradução automática com as frases consecutivas encontradas na tradução de referência e conta o número de correspondências de forma ponderada. Essas correspondências são independentes de posição. Um grau de correspondência maior indica um grau mais alto de semelhança com a tradução de referência e uma pontuação mais alta. A exatidão inteligibilidade e gramatical não são levadas em conta.

## <a name="how-bleu-works"></a>Como funciona o BLEU?

A força do BLEU é que ela se correlaciona bem com o julgamento humano por meio da média de erros de julgamento de frase individual em um corpus de teste, em vez de tentar desenvolver a opinião humana exata para cada frase.

Uma discussão mais abrangente sobre pontuações de BLEU está [aqui](https://youtu.be/-UqDljMymMg).

Os resultados do BLEU dependem muito da amplitude do seu domínio, da consistência dos dados de teste com os dados de treinamento e de ajuste e da quantidade de dados disponíveis para treinar. Se seus modelos tiverem sido treinados em um domínio estreito e os dados de treinamento estiverem consistentes com os dados de teste, você poderá esperar uma alta pontuação de BLEU.

>[!NOTE]
>Uma comparação entre pontuações de BLEU é apenas justificáveis quando os resultados de BLEU são comparados com o mesmo conjunto de testes, o mesmo par de idiomas e o mesmo mecanismo de MT. Uma pontuação de BLEU de um conjunto de testes diferente está associada a outro.
