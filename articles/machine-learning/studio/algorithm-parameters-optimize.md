---
title: Otimizar algoritmos
titleSuffix: ML Studio (classic) - Azure
description: Explica como escolher o parâmetro ideal definido para um algoritmo no Azure Machine Learning Studio (clássico).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 11/29/2017
ms.openlocfilehash: eeca7a51c1ff22f845b45f50f9fccee4b60f3221
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84119108"
---
# <a name="choose-parameters-to-optimize-your-algorithms-in-azure-machine-learning-studio-classic"></a>Escolha parâmetros para otimizar os seus algoritmos no Azure Machine Learning Studio (clássico)

Este tópico descreve como escolher o hiperparâmetro certo definido para um algoritmo no Azure Machine Learning Studio (clássico). A maioria dos algoritmos de aprendizagem automática têm parâmetros para definir. Quando treina um modelo, tem de fornecer valores para esses parâmetros. A eficácia do modelo treinado depende dos parâmetros do modelo que escolher. O processo de encontrar o conjunto ideal de parâmetros é conhecido como seleção de *modelos.*



Existem várias formas de fazer a seleção de modelos. No machine learning, a validação cruzada é um dos métodos mais utilizados para a seleção de modelos, e é o mecanismo de seleção de modelos padrão no Azure Machine Learning Studio (clássico). Como o Azure Machine Learning Studio (clássico) suporta tanto o R como o Python, podesempre implementar os seus próprios mecanismos de seleção de modelos utilizando R ou Python.

Existem quatro etapas no processo de encontrar o melhor conjunto de parâmetros:

1. **Defina o espaço do parâmetro**: Para o algoritmo, decida primeiro os valores exatos do parâmetro que pretende considerar.
2. **Defina as definições de validação cruzada**: Decida escolher dobras de validação cruzada para o conjunto de dados.
3. **Definir a métrica**: Decida qual a métrica a utilizar para determinar o melhor conjunto de parâmetros, tais como precisão, erro quadrado médio da raiz, precisão, recolha ou f-score.
4. **Treinar, avaliar e comparar:** Para cada combinação única dos valores dos parâmetros, a validação cruzada é realizada por e com base na métrica de erro que define. Após avaliação e comparação, pode escolher o modelo com melhor desempenho.

A imagem que se segue ilustra como isto pode ser alcançado no Azure Machine Learning Studio (clássico).

![Encontre o melhor conjunto de parâmetros](./media/algorithm-parameters-optimize/fig1.png)

## <a name="define-the-parameter-space"></a>Defina o espaço do parâmetro
Pode definir o parâmetro definido na fase de inicialização do modelo. A vidraça de todos os algoritmos de aprendizagem automática tem dois modos de treinador: *Single Parameter* e *Parameter Range*. Escolha o modo gama de parâmetros. No modo Gama de Parâmetros, pode introduzir vários valores para cada parâmetro. Pode introduzir valores separados de vírem na caixa de texto.

![Árvore de decisão impulsionada por duas classes, parâmetro único](./media/algorithm-parameters-optimize/fig2.png)

 Alternadamente, pode definir os pontos máximos e mínimos da grelha e o número total de pontos a gerar com o Construtor de **Gama de Utilização**. Por padrão, os valores do parâmetro são gerados numa escala linear. Mas se a **Escala de Registo** for verificada, os valores são gerados na escala de registo (isto é, a relação dos pontos adjacentes é constante em vez da sua diferença). Para parâmetros inteiros, pode definir uma gama utilizando um hífen. Por exemplo, "1-10" significa que todos os inteiros entre 1 e 10 (ambos inclusivos) formam o conjunto de parâmetros. Um modo misto também é suportado. Por exemplo, o parâmetro definido "1-10, 20, 50" incluiria os inteiros 1-10, 20 e 50.

![Árvore de decisão reforçada de duas classes, escala de parâmetros](./media/algorithm-parameters-optimize/fig3.png)

## <a name="define-cross-validation-folds"></a>Definir dobras de validação cruzada
O módulo [de partição e amostra][partition-and-sample] pode ser utilizado para atribuir dobras aleatoriamente aos dados. Na seguinte configuração da amostra para o módulo, definimos cinco dobras e atribuímos aleatoriamente um número de dobra às instâncias da amostra.

![Partição e amostra](./media/algorithm-parameters-optimize/fig4.png)

## <a name="define-the-metric"></a>Definir a métrica
O módulo [Tune Model Hyperparameters][tune-model-hyperparameters] fornece suporte para escolher empiricamente o melhor conjunto de parâmetros para um determinado algoritmo e conjunto de dados. Além de outras informações sobre a formação do modelo, o painel **Properties** deste módulo inclui a métrica para determinar o melhor conjunto de parâmetros. Tem duas diferentes caixas de listas para algoritmos de classificação e regressão, respectivamente. Se o algoritmo em consideração for um algoritmo de classificação, a métrica de regressão é ignorada e vice-versa. Neste exemplo específico, a métrica é **Precisão**.   

![Varrer parâmetros](./media/algorithm-parameters-optimize/fig5.png)

## <a name="train-evaluate-and-compare"></a>Treinar, avaliar e comparar
O mesmo módulo [Tune Model Hyperparameters][tune-model-hyperparameters] treina todos os modelos que correspondem ao conjunto de parâmetros, avalia várias métricas e cria o modelo mais bem treinado com base na métrica que escolher. Este módulo tem duas inputs obrigatórias:

* O aprendiz destreinado
* O conjunto de dados

O módulo também tem uma entrada opcional de conjunto de dados. Ligue o conjunto de dados com informações dobráveis à entrada obrigatória do conjunto de dados. Se o conjunto de dados não for atribuído qualquer informação dobrável, então uma validação cruzada de 10 vezes é executada automaticamente por padrão. Se a atribuição de dobranão for feita e for fornecido um conjunto de dados de validação na porta de conjunto de dados opcional, então é escolhido um modo de teste de comboio e o primeiro conjunto de dados é utilizado para treinar o modelo para cada combinação de parâmetros.

![Classificador de árvore de decisão reforçado](./media/algorithm-parameters-optimize/fig6a.png)

O modelo é então avaliado no conjunto de dados de validação. A porta de saída esquerda do módulo mostra métricas diferentes como funções dos valores dos parâmetros. A porta de saída direita dá o modelo treinado que corresponde ao modelo de melhor desempenho de acordo com a métrica escolhida **(Precisão** neste caso).  

![Conjunto de dados de validação](./media/algorithm-parameters-optimize/fig6b.png)

Pode ver os parâmetros exatos escolhidos visualizando a porta de saída certa. Este modelo pode ser utilizado na pontuação de um conjunto de testes ou num serviço web operacionalizado após a poupança como modelo treinado.

<!-- Module References -->
[partition-and-sample]: https://msdn.microsoft.com/library/azure/a8726e34-1b3e-4515-b59a-3e4a475654b8/
[tune-model-hyperparameters]: https://msdn.microsoft.com/library/azure/038d91b6-c2f2-42a1-9215-1f2c20ed1b40/
