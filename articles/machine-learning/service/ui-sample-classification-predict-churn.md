---
title: 'Exemplo de interface visual #5: Classificação para prever a rotatividade + desejo + venda'
titleSuffix: Azure Machine Learning
description: Este experimento de exemplo de interface visual mostra a previsão de classificador binário de rotatividade, uma tarefa comum para o gerenciamento de relacionamento com o cliente (CRM).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: 054ac3ea5224a4ffea2e7bae9da329ba7bc81233
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/14/2019
ms.locfileid: "70997141"
---
# <a name="sample-5---classification-predict-churn-appetency-and-up-selling"></a>Exemplo 5-classificação: Prever a rotatividade, a desejo e a venda vertical 

Saiba como criar um experimento de aprendizado de máquina complexo sem escrever uma única linha de código usando a interface visual.

Este experimento treina três classificadores de **árvore de decisão aumentada de duas classes** para prever tarefas comuns para sistemas de CRM (gerenciamento de relacionamento com o cliente): rotatividade, desejo e venda. Os valores e rótulos de dados são divididos em várias fontes de dados e codificados para tornar as informações do cliente anônimas. no entanto, ainda podemos usar a interface visual para combinar conjuntos de dados e treinar um modelo usando os valores embaralhados.

Porque estamos tentando responder à pergunta "qual delas?" Isso é chamado de problema de classificação. No entanto, você pode aplicar as mesmas etapas neste experimento para lidar com qualquer tipo de problema de aprendizado de máquina, seja regressão, classificação, clustering e assim por diante.

Este é o grafo concluído para este experimento:

![Grafo de experimento](./media/ui-sample-classification-predict-churn/experiment-graph.png)

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Selecione o botão **abrir** para o experimento de exemplo 5.

    ![Abrir o experimento](media/ui-sample-classification-predict-churn/open-sample5.png)

## <a name="data"></a>Data

Os dados que usamos para esse experimento são da KDD Cup 2009. O conjunto de registros tem 50.000 linhas e 230 colunas de recursos. A tarefa é prever a rotatividade, a desejo e a venda vertical para clientes que usam esses recursos. Para obter mais informações sobre os dados e a tarefa, consulte o [site do KDD](https://www.kdd.org/kdd-cup/view/kdd-cup-2009).

## <a name="experiment-summary"></a>Resumo do experimento

Este experimento de exemplo de interface visual mostra a previsão de classificador binário de rotatividade, desejo e venda vertical, uma tarefa comum para o CRM (gerenciamento de relacionamento com o cliente).

Primeiro, fazemos um processamento de dados simples.

- O conjunto de conteúdo bruto contém muitos valores ausentes. Usamos o módulo **limpar dados ausentes** para substituir os valores ausentes por 0.

    ![Limpar o conjunto de um](./media/ui-sample-classification-predict-churn/cleaned-dataset.png)

- Os recursos e os rótulos de rotatividade, desejo e venda de backup correspondentes estão em conjuntos de os diferentes. Usamos o módulo **adicionar colunas** para acrescentar as colunas de rótulo às colunas de recurso. A primeira coluna, **Col1**, é a coluna de rótulo. O restante das colunas, **Var1**, **Var2**e assim por diante, são as colunas de recursos.

    ![Adicionar o conjunto de conjuntos de colunas](./media/ui-sample-classification-predict-churn/added-column1.png)

- Usamos o módulo **Split data** para dividir o conjunto de dados em conjuntos de treinamento e teste.

    Em seguida, usamos o classificador binário da árvore de decisão aumentada com os parâmetros padrão para criar os modelos de previsão. Criamos um modelo por tarefa, ou seja, um modelo para prever a venda, a desejo e a rotatividade.

## <a name="results"></a>Resultados

Visualize a saída do módulo **modelo de avaliação** para ver o desempenho do modelo no conjunto de teste. Para a tarefa de venda vertical, a curva ROC mostra que o modelo faz melhor do que um modelo aleatório. A área sob a curva (AUC) é 0,857. No limite 0,5, a precisão é 0,7, a RECALL é 0,463 e a pontuação F1 é 0,545.

![Avaliar os resultados](./media/ui-sample-classification-predict-churn/evaluate-result.png)

 Você pode mover o controle deslizante de **limite** e ver as métricas alteradas para a tarefa de classificação binária.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Explore os outros exemplos disponíveis para a interface visual:

- [Amostra 1-regressão: Prever o preço de um automóvel](ui-sample-regression-predict-automobile-price-basic.md)
- [Exemplo 2-regressão: Comparar algoritmos para previsão de preço de automóvel](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [Exemplo 3-classificação: Prever risco de crédito](ui-sample-classification-predict-credit-risk-basic.md)
- [Amostra 4-classificação: Prever o risco de crédito (sensível ao custo)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [Exemplo 6-classificação: Prever atrasos de voo](ui-sample-classification-predict-flight-delay.md)
