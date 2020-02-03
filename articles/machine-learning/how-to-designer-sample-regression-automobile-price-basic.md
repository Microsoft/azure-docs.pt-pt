---
title: 'Designer: prever o exemplo de preços do carro (básico)'
titleSuffix: Azure Machine Learning
description: Crie um modelo de regressão ML para prever o preço de um automóvel sem escrever uma única linha de código com Azure Machine Learning designer.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.date: 12/25/2019
ms.openlocfilehash: 6f4fe941cc44211f9f5d5e77b11043257b43a8ea
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/02/2020
ms.locfileid: "76963302"
---
# <a name="use-regression-to-predict-car-prices-with-azure-machine-learning-designer"></a>Use a regressão para prever os preços de carro com o designer de Azure Machine Learning

**Amostra do designer (visualização) 1**

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Saiba como criar um modelo de regressão do Machine Learning sem escrever uma única linha de código usando o designer (versão prévia).

Esse pipeline treina um **regressor linear** para prever o preço de um carro com base em recursos técnicos, como marca, modelo, potência e tamanho. Porque você está tentando responder à pergunta "o quanto?" Isso é chamado de problema de regressão. No entanto, você pode aplicar as mesmas etapas fundamentais neste exemplo para lidar com qualquer tipo de problema de aprendizado de máquina, seja regressão, classificação, clustering e assim por diante.

As etapas fundamentais de um modelo de aprendizado de máquina de treinamento são:

1. Obter os dados
1. Pré-processar os dados
1. Formar o modelo
1. Avaliar o modelo

Aqui está o grafo final e concluído do pipeline. Este artigo fornece a lógica para todos os módulos para que você possa tomar decisões semelhantes por conta própria.

![Grafo do pipeline](./media/how-to-designer-sample-regression-automobile-price-basic/overall-graph.png)

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [aml-ui-prereq](../../includes/aml-ui-prereq.md)]

4. Clique no exemplo 1 para abri-lo Marketplace.


## <a name="get-the-data"></a>Obter os dados

Este exemplo usa o conjunto de **dados de preço do automóvel (bruto)** , que é do repositório do Machine Learning UCI. O conjunto de dados contém 26 colunas que contêm informações sobre autocelular, incluindo marca, modelo, preço, recursos de veículo (como o número de cilindros), MPG e uma pontuação de risco de seguro. O objetivo deste exemplo é prever o preço do carro.

## <a name="pre-process-the-data"></a>Pré-processar os dados

As principais tarefas de preparação de dados incluem limpeza, integração, transformação, redução e discretização ou Quantificação de dados. No designer, você pode encontrar módulos para executar essas operações e outras tarefas de pré-processamento de dados no grupo de **transformação de dados** no painel esquerdo.

Use o módulo **selecionar colunas no conjunto de DataSet** para excluir as perdas normalizadas que têm muitos valores ausentes. Em seguida, use **limpar dados ausentes** para remover as linhas que têm valores ausentes. Isso ajuda a criar um conjunto limpo de dados de treinamento.

![Pré-processamento de dados](./media/how-to-designer-sample-regression-automobile-price-basic/data-processing.png)

## <a name="train-the-model"></a>Formar o modelo

Os problemas de Machine Learning variam. As tarefas comuns de aprendizado de máquina incluem classificação, clustering, regressão e sistemas de recomendação, e cada uma delas pode exigir um algoritmo diferente. A escolha do algoritmo geralmente depende dos requisitos do caso de uso. Depois de escolher um algoritmo, você precisa ajustar seus parâmetros para treinar um modelo mais preciso. Em seguida, você precisa avaliar todos os modelos com base em métricas, como precisão, inteligibilidade e eficiência.

Como o objetivo deste exemplo é prever os preços do automóvel e, como a coluna de rótulo (preço) contém números reais, um modelo de regressão é uma boa opção. Considerando que o número de recursos é relativamente pequeno (menos de 100) e esses recursos não são esparsos, o limite de decisão provavelmente será não linear. Portanto, usamos a **regressão da floresta de decisão** para esse pipeline.

Utilize o módulo **De dados divididos** para dividir aleatoriamente os dados de entrada de modo a que o conjunto de dados de formação contenha 70% dos dados originais e o conjunto de dados de teste contenha 30% dos dados originais.

## <a name="test-evaluate-and-compare"></a>Testar, avaliar e comparar

Divida o conjunto de informações e use conjuntos de valores diferentes para treinar e testar o modelo para tornar a avaliação do modelo mais objetiva.

Depois que o modelo for treinado, você poderá usar o **modelo de Pontuação** e avaliar os módulos de **modelo** para gerar resultados previstos e avaliar os modelos.

O **modelo de Pontuação** gera previsões para o conjunto de teste usando o modelo treinado. Para verificar o resultado, selecione a porta de saída do **modelo de Pontuação** e, em seguida, selecione **Visualizar**.

![Resultado da Pontuação](./media/how-to-designer-sample-regression-automobile-price-basic/sample1-score-1225.png)

Passe as pontuações para o módulo **modelo** de avaliação para gerar métricas de avaliação. Para verificar o resultado, selecione a porta de saída do **modelo de avaliação** e, em seguida, selecione **Visualizar**.

![Avaliar resultado](./media/how-to-designer-sample-regression-automobile-price-basic/sample1-evaluate-1225.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Explore os outros exemplos disponíveis para o designer:

- [Exemplo 2-regressão: comparar algoritmos para previsão de preço de automóvel](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [Exemplo 3-classificação com seleção de recursos: Previsão de renda](how-to-designer-sample-classification-predict-income.md)
- [Exemplo 4-classificação: prever o risco de crédito (sensível ao custo)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [Exemplo 5-classificação: Previsão de rotatividade](how-to-designer-sample-classification-churn.md)
- [Exemplo 6-classificação: prever atrasos de voo](how-to-designer-sample-classification-flight-delay.md)
- [Exemplo de classificação de texto 7: conjunto de teste da Wikipédia SP 500](how-to-designer-sample-text-classification.md)