---
title: 'Designer: exemplo de rotatividade de previsão'
titleSuffix: Azure Machine Learning
description: Siga este exemplo de classificação para prever a rotatividade com Azure Machine Learning designer & árvores de decisão aumentadas.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.reviewer: sgilley
ms.date: 12/25/2019
ms.openlocfilehash: 88f688608a0ae3d435699362f9326c7c02d494a4
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76311121"
---
# <a name="use-boosted-decision-tree-to-predict-churn-with-azure-machine-learning-designer"></a>Use a árvore de decisão aumentada para prever a rotatividade com o designer de Azure Machine Learning

**Exemplo de designer 5**

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Saiba como criar um pipeline de aprendizado de máquina complexo sem escrever uma única linha de código usando o designer.

Esse pipeline treina dois classificadores de **árvore de decisão aumentada de duas classes** para prever tarefas comuns para sistemas CRM (gerenciamento de relacionamento com o cliente)-rotatividade de clientes. Os valores e rótulos de dados são divididos em várias fontes de dados e codificados para tornar as informações do cliente anônimas, no entanto, ainda podemos usar o designer para combinar conjuntos de dados e treinar um modelo usando os valores obscuros.

Porque você está tentando responder à pergunta "qual delas?" Isso é chamado de problema de classificação, mas você pode aplicar a mesma lógica mostrada neste exemplo para lidar com qualquer tipo de problema de aprendizado de máquina, seja regressão, classificação, clustering e assim por diante.

Este é o grafo concluído para este pipeline:

![Grafo de pipeline](./media/how-to-designer-sample-classification-churn/pipeline-graph.png)

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [aml-ui-prereq](../../includes/aml-ui-prereq.md)]

4. Clique em exemplo 5 para abri-lo. 

## <a name="data"></a>Dados

Os dados para esse pipeline são do KDD Cup 2009. Ele tem 50.000 linhas e 230 colunas de recursos. A tarefa é prever a rotatividade, a desejo e a venda vertical para clientes que usam esses recursos. Para obter mais informações sobre os dados e a tarefa, consulte o [site do KDD](https://www.kdd.org/kdd-cup/view/kdd-cup-2009).

## <a name="pipeline-summary"></a>Resumo do pipeline

Este pipeline de exemplo no designer mostra a previsão do classificador binário de rotatividade, desejo e venda vertical, uma tarefa comum para o CRM (gerenciamento de relacionamento com o cliente).

Primeiro, um processamento de dados simples.

- O DataSet bruto tem muitos valores ausentes. Use o módulo **limpar dados ausentes** para substituir os valores ausentes por 0.

    ![Limpar o conjunto de um](media/how-to-designer-sample-classification-churn/sample5-dataset-1225.png)

- Os recursos e a rotatividade correspondente estão em conjuntos de os diferentes. Use o módulo **adicionar colunas** para acrescentar as colunas de rótulo às colunas de recurso. A primeira coluna, **Col1**, é a coluna de rótulo. A partir do resultado da visualização, podemos ver que o conjunto de resultados está desbalanceado. Há mais exemplos negativos (-1) do que exemplos positivos (+ 1). Usaremos o módulo **SMOTE** para aumentar os casos subrepresentados posteriormente.

    ![Adicionar o conjunto de conjuntos de colunas](./media/how-to-designer-sample-classification-churn/sample5-addcol-1225.png)



- Use o módulo **dividir dados** para dividir o DataSet em conjuntos de treinamento e teste.

- Em seguida, use o classificador binário da árvore de decisão aumentada com os parâmetros padrão para criar os modelos de previsão. Crie um modelo por tarefa, ou seja, um modelo para prever a venda, a desejo e a rotatividade.

- Na parte direita do pipeline, usamos o módulo **SMOTE** para aumentar a porcentagem de exemplos positivos. O percentual de SMOTE é definido como 100 para dobrar os exemplos positivos. Saiba mais sobre como o módulo SMOTE funciona com o [módulo SMOTE reference0](algorithm-module-reference/smote.md).

## <a name="results"></a>Resultados

Visualize a saída do módulo **modelo de avaliação** para ver o desempenho do modelo no conjunto de teste. 

![Avaliar os resultados](./media/how-to-designer-sample-classification-churn/sample5-evaluate-1225.png)

 Você pode mover o controle deslizante de **limite** e ver as métricas alteradas para a tarefa de classificação binária. 

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Explore os outros exemplos disponíveis para o designer:

- [Exemplo 1-regressão: prever o preço de um automóvel](how-to-designer-sample-regression-automobile-price-basic.md)
- [Exemplo 2-regressão: comparar algoritmos para previsão de preço de automóvel](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [Exemplo 3-classificação com seleção de recursos: Previsão de renda](how-to-designer-sample-classification-predict-income.md)
- [Exemplo 4-classificação: prever o risco de crédito (sensível ao custo)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [Exemplo 6-classificação: prever atrasos de voo](how-to-designer-sample-classification-flight-delay.md)
- [Exemplo de classificação de texto 7: conjunto de teste da Wikipédia SP 500](how-to-designer-sample-text-classification.md)
