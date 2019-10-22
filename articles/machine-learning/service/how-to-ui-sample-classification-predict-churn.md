---
title: 'Exemplo de interface visual #5: classificação para prever rotatividade + desejo + venda up'
titleSuffix: Azure Machine Learning
description: Esse pipeline de exemplo de interface visual mostra a previsão de classificador binário de rotatividade, uma tarefa comum para o gerenciamento de relacionamento com o cliente (CRM).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 09/23/2019
ms.openlocfilehash: 82639779dde08bb1f71fb75dba62038dbf34d1b6
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693564"
---
# <a name="sample-5---classification-predict-churn-appetency-and-up-selling"></a>Exemplo 5-classificação: prever a rotatividade, a desejo e a venda vertical 

Saiba como criar um pipeline de aprendizado de máquina complexo sem escrever uma única linha de código usando a interface visual.

Esse pipeline treina três classificadores de **árvore de decisão aumentada de duas classes** para prever tarefas comuns para sistemas de CRM (gerenciamento de relacionamento com o cliente): rotatividade, desejo e venda. Os valores e rótulos de dados são divididos em várias fontes de dados e codificados para tornar as informações do cliente anônimas. no entanto, ainda podemos usar a interface visual para combinar conjuntos de dados e treinar um modelo usando os valores obscuros.

Porque você está tentando responder à pergunta "qual delas?" Isso é chamado de problema de classificação, mas você pode aplicar a mesma lógica mostrada neste exemplo para lidar com qualquer tipo de problema de aprendizado de máquina, seja regressão, classificação, clustering e assim por diante.

Este é o grafo concluído para este pipeline:

![Grafo de pipeline](./media/how-to-ui-sample-classification-predict-churn/pipeline-graph.png)

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Selecione o botão **abrir** para o pipeline de exemplo 5.

    ![Abrir o pipeline](media/how-to-ui-sample-classification-predict-churn/open-sample5.png)

## <a name="data"></a>Dados

Os dados para esse pipeline são do KDD Cup 2009. Ele tem 50.000 linhas e 230 colunas de recursos. A tarefa é prever a rotatividade, a desejo e a venda vertical para clientes que usam esses recursos. Para obter mais informações sobre os dados e a tarefa, consulte o [site do KDD](https://www.kdd.org/kdd-cup/view/kdd-cup-2009).

## <a name="pipeline-summary"></a>Resumo do pipeline

Esse pipeline de exemplo de interface visual mostra a previsão de classificador binário de rotatividade, desejo e venda vertical, uma tarefa comum para o CRM (gerenciamento de relacionamento com o cliente).

Primeiro, um processamento de dados simples.

- O DataSet bruto tem muitos valores ausentes. Use o módulo **limpar dados ausentes** para substituir os valores ausentes por 0.

    ![Limpar o conjunto de um](./media/how-to-ui-sample-classification-predict-churn/cleaned-dataset.png)

- Os recursos e os rótulos de rotatividade, desejo e venda de backup correspondentes estão em conjuntos de os diferentes. Use o módulo **adicionar colunas** para acrescentar as colunas de rótulo às colunas de recurso. A primeira coluna, **Col1**, é a coluna de rótulo. O restante das colunas, **Var1**, **Var2**e assim por diante, são as colunas de recursos.

    ![Adicionar o conjunto de conjuntos de colunas](./media/how-to-ui-sample-classification-predict-churn/added-column1.png)

- Use o módulo **dividir dados** para dividir o DataSet em conjuntos de treinamento e teste.

- Em seguida, use o classificador binário da árvore de decisão aumentada com os parâmetros padrão para criar os modelos de previsão. Crie um modelo por tarefa, ou seja, um modelo para prever a venda, a desejo e a rotatividade.

## <a name="results"></a>Resultados

Visualize a saída do módulo **modelo de avaliação** para ver o desempenho do modelo no conjunto de teste. Para a tarefa de venda vertical, a curva ROC mostra que o modelo faz melhor do que um modelo aleatório. A área sob a curva (AUC) é 0,857. No limite 0,5, a precisão é 0,7, a RECALL é 0,463 e a pontuação F1 é 0,545.

![Avaliar os resultados](./media/how-to-ui-sample-classification-predict-churn/evaluate-result.png)

 Você pode mover o controle deslizante de **limite** e ver as métricas alteradas para a tarefa de classificação binária.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Explore os outros exemplos disponíveis para a interface visual:

- [Exemplo 1-regressão: prever o preço de um automóvel](how-to-ui-sample-regression-predict-automobile-price-basic.md)
- [Exemplo 2-regressão: comparar algoritmos para previsão de preço de automóvel](how-to-ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [Exemplo 3-classificação: prever risco de crédito](how-to-ui-sample-classification-predict-credit-risk-basic.md)
- [Exemplo 4-classificação: prever o risco de crédito (sensível ao custo)](how-to-ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [Exemplo 6-classificação: prever atrasos de voo](how-to-ui-sample-classification-predict-flight-delay.md)
- [Exemplo 7-classificação de texto: revisões de livros](how-to-ui-sample-text-classification.md)
