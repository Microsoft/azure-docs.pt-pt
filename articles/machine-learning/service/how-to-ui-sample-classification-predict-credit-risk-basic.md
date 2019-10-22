---
title: 'Exemplo de interface visual #3: classificação para prever o risco de crédito'
titleSuffix: Azure Machine Learning
description: Saiba como criar um classificador de aprendizado de máquina sem escrever uma única linha de código usando a interface visual.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 09/23/2019
ms.openlocfilehash: 4649303b8ee643130b8e254f01bfffbe8ad9eb2b
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693507"
---
# <a name="sample-3---classification-predict-credit-risk"></a>Exemplo 3-classificação: prever risco de crédito

Saiba como criar um classificador de aprendizado de máquina sem escrever uma única linha de código usando a interface visual. Este pipeline de exemplo uma **árvore de decisão aumentada de duas classes** para prever o risco de crédito (alta ou baixa) com base nas informações do aplicativo de crédito, como histórico de crédito, idade e número de cartões de crédito.

Porque a pergunta está respondendo "Qual é?" Isso é chamado de problema de classificação. No entanto, você pode aplicar o mesmo processo fundamental para lidar com qualquer tipo de problema de aprendizado de máquina, seja regressão, classificação, clustering e assim por diante.

Este é o grafo de pipeline final para este exemplo:

![Grafo do pipeline](media/how-to-ui-sample-classification-predict-credit-risk-basic/overall-graph.png)

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Selecione o botão **abrir** para o pipeline de exemplo 3:

    ![Abrir o pipeline](media/how-to-ui-sample-classification-predict-credit-risk-basic/open-sample3.png)

## <a name="related-sample"></a>Exemplo relacionado

[Amostra 4-classificação: Previsão de risco de crédito (sensível ao custo)](how-to-ui-sample-classification-predict-credit-risk-cost-sensitive.md) fornece um pipeline avançado que resolve o mesmo problema que este exemplo. Ele mostra como executar a classificação de *custo confidencial* usando um módulo **Executar script Python** e comparar o desempenho de dois algoritmos de classificação binária. Consulte-o se você quiser saber mais sobre como criar pipelines de classificação.


## <a name="data"></a>Dados

O exemplo usa o conjunto de teste de cartão de crédito alemão do repositório Irvine de UC. Ele contém 1.000 amostras com 20 recursos e 1 rótulo. Cada amostra representa uma pessoa. Os recursos incluem recursos numéricos e categóricos. Consulte o [site UCI](https://archive.ics.uci.edu/ml/datasets/Statlog+%28German+Credit+Data%29) para saber o significado dos recursos categóricos. A última coluna é o rótulo, que denota o risco de crédito e tem apenas dois valores possíveis: alto risco de crédito = 2 e baixo risco de crédito = 1.

## <a name="pipeline-summary"></a>Resumo do pipeline

Siga estas etapas para criar o pipeline:

1. Arraste o módulo conjunto de dados do cartão de crédito alemão UCI para a tela do pipeline.
1. Adicione um módulo **Editar metadados** para que possamos adicionar nomes significativos para cada coluna.
1. Adicione um módulo **dividir dados** para criar os conjuntos de treinamento e teste. Defina a fração de linhas no primeiro conjunto de registros de saída como 0,7. Essa configuração especifica que 70% dos dados serão gerados para a porta esquerda do módulo e a porta restante para a direita. Usamos o conjunto de espaço da esquerda para treinamento e o correto para teste.
1. Adicione um módulo de **árvore de decisão aumentada de duas classes** para inicializar um classificador de árvore de decisão aumentada.
1. Adicione um módulo **modelo de treinamento** . Conecte o classificador da etapa anterior à porta de entrada à esquerda do **modelo de treinamento**. Adicione o conjunto de treinamento (a porta de saída à esquerda dos **dados divididos**) à porta de entrada à direita do **modelo de treinamento**. O **modelo de treinamento** treinará o classificador.
1. Adicione um módulo **modelo de Pontuação** e conecte o módulo modelo de **treinamento** a ele. Em seguida, adicione o conjunto de teste (a porta certa dos **dados divididos**) ao **modelo de Pontuação**. O **modelo de Pontuação** fará as previsões. Você pode selecionar sua porta de saída para ver as previsões e as probabilidades de classe positivas.
1. Adicione um módulo **avaliar modelo** e conecte o conjunto de dados pontuado à sua porta de entrada à esquerda. Para ver os resultados da avaliação, selecione a porta de saída do módulo **avaliar modelo** e selecione **Visualizar**.

## <a name="results"></a>Resultados

![Avaliar os resultados](media/how-to-ui-sample-classification-predict-credit-risk-basic/evaluate-result.png)

Nos resultados da avaliação, você pode ver que o AUC do modelo é 0,776. No limite 0,5, a precisão é 0,621, a RECALL é 0,456 e a pontuação F1 é 0,526.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Explore os outros exemplos disponíveis para a interface visual:

- [Exemplo 1-regressão: prever o preço de um automóvel](how-to-ui-sample-regression-predict-automobile-price-basic.md)
- [Exemplo 2-regressão: comparar algoritmos para previsão de preço de automóvel](how-to-ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [Exemplo 4-classificação: prever o risco de crédito (sensível ao custo)](how-to-ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [Exemplo 5-classificação: Previsão de rotatividade](how-to-ui-sample-classification-predict-churn.md)
- [Exemplo 6-classificação: prever atrasos de voo](how-to-ui-sample-classification-predict-flight-delay.md)
- [Exemplo 7-classificação de texto: revisões de livros](how-to-ui-sample-text-classification.md)