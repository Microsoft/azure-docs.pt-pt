---
title: Roteiro de Machine Learning de algoritmos
titleSuffix: Azure Machine Learning
description: Uma folha de consulta de algoritmo de Machine Learning imprimível ajuda a escolher o algoritmo certo para seu modelo de previsão no designer de Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: FrancescaLazzeri
ms.author: lazzeri
ms.date: 11/04/2019
ms.openlocfilehash: 82d4131170002a3d9ffd78933c9dc566c3b1d921
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2020
ms.locfileid: "76767635"
---
# <a name="machine-learning-algorithm-cheat-sheet-for-azure-machine-learning-designer"></a>Roteiro de Machine Learning de algoritmo para Azure Machine Learning designer

A **folha** de consulta do algoritmo de Azure Machine Learning ajuda a escolher o algoritmo certo para um modelo de análise preditiva.

O Azure Machine Learning tem uma grande biblioteca de algoritmos da ***classificação,*** ***sistemas de recomendação,*** ***agrupamento,*** ***deteção de anomalias,*** ***regressão***e famílias de análise de ***texto.*** Cada um foi projetado para resolver um tipo diferente de problema de aprendizado de máquina.

Para obter orientação adicional, consulte [Como selecionar algoritmos](how-to-select-algorithms.md)

## <a name="download-machine-learning-algorithm-cheat-sheet"></a>Download: roteiro de Machine Learning de algoritmos

**Baixe a folha de consulta aqui: [Machine Learning folha de consulta do algoritmo (11x17 em.)](https://download.microsoft.com/download/3/5/b/35bb997f-a8c7-485d-8c56-19444dafd757/azure-machine-learning-algorithm-cheat-sheet-nov2019.pdf?WT.mc_id=docs-article-lazzeri)**

![Roteiro de Machine Learning de algoritmos: saiba como escolher um algoritmo de Machine Learning.](./media/algorithm-cheat-sheet/machine-learning-algorithm-cheat-sheet.svg)

Baixe e imprima a folha de consulta do algoritmo de Machine Learning no tamanho de tablóide para mantê-la útil e obter ajuda para escolher um algoritmo.

## <a name="how-to-use-the-machine-learning-algorithm-cheat-sheet"></a>Como usar a folha de consulta do algoritmo de Machine Learning

As sugestões oferecidas nesta folha de consulta de algoritmo são regras aproximadas. Alguns podem ser tortos e alguns podem ser flagrantemente violados. Esta folha de batota destina-se a sugerir um ponto de partida. Não tenha medo de executar uma competição de cabeça a cabeça entre vários algoritmos em seus dados. Simplesmente não há nenhum substituto para entender os princípios de cada algoritmo e o sistema que gerou os dados.

Cada algoritmo de aprendizado de máquina tem seu próprio estilo ou tendência indutivo. Para um problema específico, vários algoritmos podem ser apropriados e um algoritmo pode ser um melhor ajuste do que outros. Mas nem sempre é possível saber com antecedência qual é a melhor opção. Em casos como esses, vários algoritmos são listados juntos na folha de consulta. Uma estratégia apropriada seria testar um algoritmo e, se os resultados ainda não forem satisfatórios, tente os outros. 

Para saber mais sobre os algoritmos no Azure Machine Learning, acesse o [algoritmo e a referência do módulo](algorithm-module-reference/module-reference.md).

## <a name="kinds-of-machine-learning"></a>Tipos de aprendizado de máquina

Há três categorias principais de aprendizado de máquina: *aprendizado supervisionado*, *aprendizado não supervisionado*e *aprendizado de reforço*.

### <a name="supervised-learning"></a>Aprendizagem supervisionada

No aprendizado supervisionado, cada ponto de dados é rotulado ou associado a uma categoria ou valor de interesse. Um exemplo de um rótulo categórico é atribuir uma imagem como um ' gato ' ou um ' cachorro '. Um exemplo de um rótulo de valor é o preço de venda associado a um carro usado. O objetivo do aprendizado supervisionado é estudar muitos exemplos rotulados como esses, e então ser capaz de fazer previsões sobre pontos de dados futuros. Por exemplo, identificar novas fotos com o animal correto ou atribuir preços de venda precisos a outros carros usados. Esse é um tipo popular e útil de aprendizado de máquina.

### <a name="unsupervised-learning"></a>Aprendizagem não supervisionada

No aprendizado não supervisionado, os pontos de dados não têm rótulos associados a eles. Em vez disso, o objetivo de um algoritmo de aprendizado não supervisionado é organizar os dados de alguma forma ou descrever sua estrutura. Os grupos de aprendizagem não supervisionados são dados em clusters, como o K-means faz, ou encontra diferentes formas de olhar para dados complexos para que pareça mais simples.

### <a name="reinforcement-learning"></a>Aprendizagem por reforço

No reforço Learning, o algoritmo Obtém a escolha de uma ação em resposta a cada ponto de dados. É uma abordagem comum em robótica, em que o conjunto de leituras de sensor em um ponto no tempo é um ponto de dados, e o algoritmo deve escolher a próxima ação do robô. Também é uma opção natural para Internet das Coisas aplicativos. O algoritmo de aprendizado também recebe um sinal de recompensa um pouco mais tarde, indicando quão boa é a decisão. Com base neste sinal, o algoritmo modifica a sua estratégia de forma a alcançar a maior recompensa. 

## <a name="next-steps"></a>Passos seguintes

* Consulte orientação adicional sobre [como selecionar algoritmos](how-to-select-algorithms.md)

* [Saiba mais sobre o estúdio em Azure Machine Learning e o portal do Azure](overview-what-is-azure-ml.md).

* [Tutorial: criar um modelo de previsão no designer de Azure Machine Learning](tutorial-designer-automobile-price-train-score.md).

* [Saiba mais sobre aprendizado profundo versus aprendizado de máquina](concept-deep-learning-vs-machine-learning.md).
