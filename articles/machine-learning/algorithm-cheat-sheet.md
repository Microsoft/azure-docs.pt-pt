---
title: Machine Learning Algorithm Cheat Sheet - designer
titleSuffix: Azure Machine Learning
description: Uma folha de batota de algoritmo de aprendizagem de máquinas imprimível ajuda-o a escolher o algoritmo certo para o seu modelo preditivo no designer de Aprendizagem automática Azure Machine.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: FrancescaLazzeri
ms.author: lazzeri
ms.date: 03/05/2020
adobe-target: true
ms.openlocfilehash: 730fcf3fb552b92a02b255ad654f64d5ff7a92fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "100367061"
---
# <a name="machine-learning-algorithm-cheat-sheet-for-azure-machine-learning-designer"></a>Folha de batota de algoritmo de aprendizagem de máquina para designer de aprendizagem de máquinas Azure

A Folha de Batota do **Algoritmo de Aprendizagem de Máquinas Azure** ajuda-o a escolher o algoritmo certo do designer para um modelo de análise preditiva.

A Azure Machine Learning tem uma grande biblioteca de algoritmos da **classificação*** _, _*_sistemas recomendadores,_*_ _*_clustering,_*_ _*_deteção de_*_ _*_anomalias, regressão_*_ e _ *_análise de texto_** famílias. Cada um deles é projetado para resolver um tipo diferente de problema de aprendizagem automática.

Para obter orientação adicional, consulte [Como selecionar algoritmos](how-to-select-algorithms.md)

## <a name="download-machine-learning-algorithm-cheat-sheet"></a>Download: Machine Learning Algorithm Cheat Sheet

**Descarregue aqui a folha de batota: [Machine Learning Algorithm Cheat Sheet (11x17 in.)](https://download.microsoft.com/download/3/5/b/35bb997f-a8c7-485d-8c56-19444dafd757/azure-machine-learning-algorithm-cheat-sheet-nov2019.pdf?WT.mc_id=docs-article-lazzeri)**

![Machine Learning Algorithm Cheat Sheet: Saiba como escolher um algoritmo de aprendizagem automática.](./media/algorithm-cheat-sheet/machine-learning-algorithm-cheat-sheet.svg)

Descarregue e imprima a Folha de Batota do Algoritmo de Aprendizagem automática em tamanho tabloide para mantê-la à mão e obter ajuda para escolher um algoritmo.

## <a name="how-to-use-the-machine-learning-algorithm-cheat-sheet"></a>Como usar a folha de batota do algoritmo de aprendizagem automática

As sugestões oferecidas nesta folha de batota de algoritmo são regras aproximadas do polegar. Alguns podem ser dobrados, e outros podem ser flagrantemente violados. Esta folha de batota destina-se a sugerir um ponto de partida. Não tenha medo de fazer uma competição frente a frente entre vários algoritmos nos seus dados. Simplesmente não há substituto para a compreensão dos princípios de cada algoritmo e do sistema que gerou os seus dados.

Cada algoritmo de aprendizagem automática tem o seu próprio estilo ou viés indutivo. Para um problema específico, vários algoritmos podem ser apropriados, e um algoritmo pode ser melhor do que outros. Mas nem sempre é possível saber de antemão qual é o melhor encaixe. Em casos como este, vários algoritmos estão listados juntos na folha de batota. Uma estratégia apropriada seria tentar um algoritmo, e se os resultados ainda não forem satisfatórios, tente os outros. 

Para saber mais sobre os algoritmos no designer de Aprendizagem automática Azure, vá ao Algoritmo e à referência do [módulo.](algorithm-module-reference/module-reference.md)

## <a name="kinds-of-machine-learning"></a>Tipos de aprendizagem automática

Existem três categorias principais de aprendizagem automática: *aprendizagem supervisionada,* *aprendizagem sem supervisão* e *aprendizagem de reforço.*

### <a name="supervised-learning"></a>Aprendizagem supervisionada

Na aprendizagem supervisionada, cada ponto de dados é rotulado ou associado a uma categoria ou valor de interesse. Um exemplo de um rótulo categórico é atribuir uma imagem como um 'gato' ou um 'cão'. Um exemplo de uma etiqueta de valor é o preço de venda associado a um carro usado. O objetivo da aprendizagem supervisionada é estudar muitos exemplos rotulados como estes, e depois ser capaz de fazer previsões sobre futuros pontos de dados. Por exemplo, identificar novas fotos com o animal correto ou atribuir preços precisos de venda a outros carros usados. Este é um tipo popular e útil de aprendizagem automática.

### <a name="unsupervised-learning"></a>Aprendizagem não supervisionada

Na aprendizagem não supervisionada, os pontos de dados não têm rótulos associados a eles. Em vez disso, o objetivo de um algoritmo de aprendizagem não supervisionado é organizar os dados de alguma forma ou descrever a sua estrutura. A aprendizagem não supervisionada agrupa dados em clusters, como o K-significa, ou encontra diferentes formas de olhar para dados complexos para que pareça mais simples.

### <a name="reinforcement-learning"></a>Aprendizagem por reforço

Na aprendizagem de reforço, o algoritmo pode escolher uma ação em resposta a cada ponto de dados. É uma abordagem comum na robótica, onde o conjunto de leituras de sensores em um ponto do tempo é um ponto de dados, e o algoritmo deve escolher a próxima ação do robô. É também um ajuste natural para aplicações de Internet das Coisas. O algoritmo de aprendizagem também recebe um sinal de recompensa pouco tempo depois, indicando o quão boa foi a decisão. Com base neste sinal, o algoritmo modifica a sua estratégia para obter a maior recompensa. 

## <a name="next-steps"></a>Passos seguintes

* Consulte orientações adicionais sobre [como selecionar algoritmos](how-to-select-algorithms.md)

* [Conheça o estúdio em Azure Machine Learning e o portal Azure.](overview-what-is-azure-ml.md)

* [Tutorial: Construa um modelo de previsão no designer de Aprendizagem automática Azure.](tutorial-designer-automobile-price-train-score.md)

* [Saiba mais sobre aprendizagem profunda vs. aprendizagem automática.](concept-deep-learning-vs-machine-learning.md)
