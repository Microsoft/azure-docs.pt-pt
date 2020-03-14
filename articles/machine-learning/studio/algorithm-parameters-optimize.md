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
ms.openlocfilehash: 04148b482cb07665f43df5bd86a77175cbbaf08b
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79218274"
---
# <a name="choose-parameters-to-optimize-your-algorithms-in-azure-machine-learning-studio-classic"></a>Escolha parâmetros para otimizar os seus algoritmos no Azure Machine Learning Studio (clássico)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Este tópico descreve como escolher o hiperparâmetro certo definido para um algoritmo no Azure Machine Learning Studio (clássico). A maioria dos algoritmos de machine learning tem parâmetros para definir. Quando preparar um modelo, terá de fornecer valores para esses parâmetros. A eficácia do modelo preparado depende dos parâmetros de modelo que escolher. O processo de encontrar o conjunto ideal de parâmetros é conhecido como seleção de *modelos.*



Existem várias formas para modelar a seleção. No machine learning, a validação cruzada é um dos métodos mais utilizados para a seleção de modelos, e é o mecanismo de seleção de modelos padrão no Azure Machine Learning Studio (clássico). Como o Azure Machine Learning Studio (clássico) suporta tanto o R como o Python, podesempre implementar os seus próprios mecanismos de seleção de modelos utilizando R ou Python.

Existem quatro passos no processo de encontrar o melhor conjunto de parâmetro:

1. **Defina o espaço do parâmetro**: Para o algoritmo, decida primeiro os valores exatos do parâmetro que pretende considerar.
2. **Defina as definições de validação cruzada**: Decida escolher dobras de validação cruzada para o conjunto de dados.
3. **Definir a métrica**: Decida qual a métrica a utilizar para determinar o melhor conjunto de parâmetros, tais como precisão, erro quadrado médio da raiz, precisão, recolha ou f-score.
4. **Treinar, avaliar e comparar:** Para cada combinação única dos valores dos parâmetros, a validação cruzada é realizada por e com base na métrica de erro que define. Após a avaliação e comparação, pode escolher o modelo com melhor desempenho.

A imagem que se segue ilustra como isto pode ser alcançado no Azure Machine Learning Studio (clássico).

![Encontrar o melhor conjunto de parâmetros](./media/algorithm-parameters-optimize/fig1.png)

## <a name="define-the-parameter-space"></a>Definir o espaço de parâmetro
Pode definir o parâmetro definido para o passo de inicialização do modelo. A vidraça de todos os algoritmos de aprendizagem automática tem dois modos de treinador: *Single Parameter* e *Parameter Range*. Escolha o modo de intervalo de parâmetro. No modo de intervalo de parâmetro, pode introduzir vários valores para cada parâmetro. Pode introduzir valores separados por vírgulas na caixa de texto.

![Árvore de decisões elevada de duas classes, o único parâmetro](./media/algorithm-parameters-optimize/fig2.png)

 Alternadamente, pode definir os pontos máximos e mínimos da grelha e o número total de pontos a gerar com o Construtor de **Gama de Utilização**. Por predefinição, os valores de parâmetro são gerados numa escala linear. Mas se a **Escala de Registo** for verificada, os valores são gerados na escala de registo (isto é, a relação dos pontos adjacentes é constante em vez da sua diferença). Para os parâmetros de número inteiro, pode definir um intervalo utilizando um hífen. Por exemplo, "1-10" significa que todos os inteiros entre 1 e 10 (ambos inclusivos) formam o conjunto de parâmetros. Também é suportado um modo misto. Por exemplo, o parâmetro definido "1-10, 20, 50" incluiria os inteiros 1-10, 20 e 50.

![Árvore de decisões elevada de duas classes, intervalo de parâmetro](./media/algorithm-parameters-optimize/fig3.png)

## <a name="define-cross-validation-folds"></a>Definir a subconjuntos de validação cruzada
O módulo [de partição e amostra][partition-and-sample] pode ser utilizado para atribuir dobras aleatoriamente aos dados. A seguinte configuração de exemplo para o módulo, vamos definir cinco subconjuntos e aleatoriamente, atribua um número de subconjuntos de validação para as instâncias de exemplo.

![Partição e amostras](./media/algorithm-parameters-optimize/fig4.png)

## <a name="define-the-metric"></a>Definir a métrica
O módulo [Tune Model Hyperparameters][tune-model-hyperparameters] fornece suporte para escolher empiricamente o melhor conjunto de parâmetros para um determinado algoritmo e conjunto de dados. Além de outras informações sobre a formação do modelo, o painel **Properties** deste módulo inclui a métrica para determinar o melhor conjunto de parâmetros. Ele tem duas caixas de diferentes na lista pendente para algoritmos de classificação e regressão, respectivamente. Se o algoritmo em consideração é um algoritmo de classificação, a métrica de regressão é ignorada e vice-versa. Neste exemplo específico, a métrica é **Precisão**.   

![Parâmetros de abertura](./media/algorithm-parameters-optimize/fig5.png)

## <a name="train-evaluate-and-compare"></a>Treinar, avaliar e comparar
O mesmo módulo [Tune Model Hyperparameters][tune-model-hyperparameters] treina todos os modelos que correspondem ao conjunto de parâmetros, avalia várias métricas e cria o modelo mais bem treinado com base na métrica que escolher. Este módulo tem duas entradas obrigatórias:

* O aprendiz destreinado
* O conjunto de dados

O módulo também tem um conjunto de dados opcional de entrada. Ligue-se o conjunto de dados com informações de subconjuntos de validação para a entrada de conjunto de dados obrigatório. Se o conjunto de dados não está atribuído a quaisquer informações de subconjuntos de validação, em seguida, uma 10-fold a validação cruzada é executada automaticamente por predefinição. Se não for efetuada a atribuição de subconjuntos de validação e a porta de conjunto de dados opcional é fornecido um conjunto de dados de validação, em seguida, é escolhido um modo de treinar-testar e o primeiro conjunto de dados é utilizado para preparar o modelo para cada combinação de parâmetro.

![Classificador de árvore de decisões elevada](./media/algorithm-parameters-optimize/fig6a.png)

O modelo, em seguida, é avaliado no conjunto de dados de validação. A porta de saída à esquerda do módulo mostra diferentes métricas, como funções de valores de parâmetros. A porta de saída direita dá o modelo treinado que corresponde ao modelo de melhor desempenho de acordo com a métrica escolhida **(Precisão** neste caso).  

![Conjunto de dados de validação](./media/algorithm-parameters-optimize/fig6b.png)

Pode ver os parâmetros exatos escolhidos por visualizar a porta de saída certo. Este modelo pode ser utilizado num conjunto de teste de classificação ou num serviço web operacionalizado depois de guardar como um modelo preparado.

<!-- Module References -->
[partition-and-sample]: https://msdn.microsoft.com/library/azure/a8726e34-1b3e-4515-b59a-3e4a475654b8/
[tune-model-hyperparameters]: https://msdn.microsoft.com/library/azure/038d91b6-c2f2-42a1-9215-1f2c20ed1b40/
