---
title: Otimizar algoritmos
titleSuffix: Azure Machine Learning Studio
description: Explica como escolher o parâmetro ideal definido para um algoritmo no Azure Machine Learning Studio.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 11/29/2017
ms.openlocfilehash: 51fa59006b180663362044ba598d73c680291457
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56822298"
---
# <a name="choose-parameters-to-optimize-your-algorithms-in-azure-machine-learning-studio"></a>Escolher parâmetros para otimizar os seus algoritmos no Azure Machine Learning Studio

Este tópico descreve como escolher o certo hiper-parâmetros definido para um algoritmo no Azure Machine Learning Studio. A maioria dos algoritmos de machine learning tem parâmetros para definir. Quando preparar um modelo, terá de fornecer valores para esses parâmetros. A eficácia do modelo preparado depende dos parâmetros de modelo que escolher. O processo de localizar o conjunto ótimo de parâmetros é conhecido como *seleção de modelos*.



Existem várias formas para modelar a seleção. No machine learning, a validação cruzada é um dos métodos mais amplamente utilizados para a seleção de modelo e é o mecanismo de seleção de modelo predefinido no Azure Machine Learning Studio. Como o Azure Machine Learning Studio suporta R e Python, sempre é possível implementar seus próprios mecanismos de seleção de modelo ao utilizar o R ou Python.

Existem quatro passos no processo de encontrar o melhor conjunto de parâmetro:

1. **Definir o espaço de parâmetro**: Para o algoritmo, decida primeiro os valores de parâmetros exatos que queira considerar.
2. **Definir as definições de validação cruzada**: Decida como escolher a subconjuntos de validação cruzada do conjunto de dados.
3. **Definir a métrica**: Decida quais métrica a utilizar para determinar o melhor conjunto de parâmetros, como precisão, média de raiz ao quadrado, erro, precisão, lembre-se ou pontuação f.
4. **Treinar, avaliar e comparar**: Para cada combinação exclusiva de valores de parâmetros, a validação cruzada é transportada por e com base na métrica de erro que definir. Após a avaliação e comparação, pode escolher o modelo com melhor desempenho.

A imagem seguinte ilustra mostra como isso pode ser obtido no Azure Machine Learning Studio.

![Encontrar o melhor conjunto de parâmetros](./media/algorithm-parameters-optimize/fig1.png)

## <a name="define-the-parameter-space"></a>Definir o espaço de parâmetro
Pode definir o parâmetro definido para o passo de inicialização do modelo. O painel de parâmetros de algoritmos de todos os machine learning tem dois modos de instrutor: *Único parâmetro* e *intervalo do parâmetro*. Escolha o modo de intervalo de parâmetro. No modo de intervalo de parâmetro, pode introduzir vários valores para cada parâmetro. Pode introduzir valores separados por vírgulas na caixa de texto.

![Árvore de decisões elevada de duas classes, o único parâmetro](./media/algorithm-parameters-optimize/fig2.png)

 Em alternativa, pode definir os pontos máximos e mínimos da grade e o número total de pontos para ser gerada com **Builder do intervalo de utilização**. Por predefinição, os valores de parâmetro são gerados numa escala linear. Mas se **escala logarítmica** estiver marcada, os valores são gerados na escala de registo (ou seja, a proporção dos pontos adjacentes é constante em vez de seus diferença). Para os parâmetros de número inteiro, pode definir um intervalo utilizando um hífen. Por exemplo, "1 a 10" significa que todos os números inteiros entre 1 e 10 (ambos inclusivo) o conjunto de parâmetros de formulário. Também é suportado um modo misto. Por exemplo, defina o parâmetro "-1 a 10, 20, 50" incluiria inteiros 1 a 10, 20 e 50.

![Árvore de decisões elevada de duas classes, intervalo de parâmetro](./media/algorithm-parameters-optimize/fig3.png)

## <a name="define-cross-validation-folds"></a>Definir a subconjuntos de validação cruzada
O [partição e amostras] [ partition-and-sample] módulo pode ser utilizado para atribuir aleatoriamente subconjuntos aos dados. A seguinte configuração de exemplo para o módulo, vamos definir cinco subconjuntos e aleatoriamente, atribua um número de subconjuntos de validação para as instâncias de exemplo.

![Partição e amostras](./media/algorithm-parameters-optimize/fig4.png)

## <a name="define-the-metric"></a>Definir a métrica
O [Hiperparâmetros do modelo] [ tune-model-hyperparameters] módulo fornece suporte para empiricamente escolher o melhor conjunto de parâmetros para um determinado algoritmo e o conjunto de dados. Além de outras informações relativas ao preparar o modelo, o **propriedades** painel deste módulo inclui a métrica para determinar o melhor conjunto de parâmetros. Ele tem duas caixas de diferentes na lista pendente para algoritmos de classificação e regressão, respectivamente. Se o algoritmo em consideração é um algoritmo de classificação, a métrica de regressão é ignorada e vice-versa. Neste exemplo específico, a métrica é **precisão**.   

![Parâmetros de abertura](./media/algorithm-parameters-optimize/fig5.png)

## <a name="train-evaluate-and-compare"></a>Treinar, avaliar e comparar
O mesmo [Hiperparâmetros do modelo] [ tune-model-hyperparameters] trens módulo todos os modelos que correspondem ao parâmetro definido, avalia a várias métricas e, em seguida, cria o modelo mais preparado com base na métrica escolha. Este módulo tem duas entradas obrigatórias:

* O aprendiz destreinado
* O conjunto de dados

O módulo também tem um conjunto de dados opcional de entrada. Ligue-se o conjunto de dados com informações de subconjuntos de validação para a entrada de conjunto de dados obrigatório. Se o conjunto de dados não está atribuído a quaisquer informações de subconjuntos de validação, em seguida, uma 10-fold a validação cruzada é executada automaticamente por predefinição. Se não for efetuada a atribuição de subconjuntos de validação e a porta de conjunto de dados opcional é fornecido um conjunto de dados de validação, em seguida, é escolhido um modo de treinar-testar e o primeiro conjunto de dados é utilizado para preparar o modelo para cada combinação de parâmetro.

![Classificador de árvore de decisões elevada](./media/algorithm-parameters-optimize/fig6a.png)

O modelo, em seguida, é avaliado no conjunto de dados de validação. A porta de saída à esquerda do módulo mostra diferentes métricas, como funções de valores de parâmetros. A porta de saída certo dá o modelo treinado que corresponde ao modelo com melhor desempenho, de acordo com a métrica escolhido (**precisão** neste caso).  

![Conjunto de dados de validação](./media/algorithm-parameters-optimize/fig6b.png)

Pode ver os parâmetros exatos escolhidos por visualizar a porta de saída certo. Este modelo pode ser utilizado num conjunto de teste de classificação ou num serviço web operacionalizado depois de guardar como um modelo preparado.

<!-- Module References -->
[partition-and-sample]: https://msdn.microsoft.com/library/azure/a8726e34-1b3e-4515-b59a-3e4a475654b8/
[tune-model-hyperparameters]: https://msdn.microsoft.com/library/azure/038d91b6-c2f2-42a1-9215-1f2c20ed1b40/
