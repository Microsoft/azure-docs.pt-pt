---
title: 'Designer: prever os preços do carro (regressão)'
titleSuffix: Azure Machine Learning
description: Este artigo mostra como criar um pipeline de aprendizado de máquina complexo sem escrever uma única linha de código usando o designer (versão prévia). Saiba como treinar e comparar vários modelos de regressão para prever o preço de um carro com base nos recursos técnicos
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: peterlu
ms.date: 11/04/2019
ms.openlocfilehash: d73f6d67afff13696de78d026ff65228fd68fb28
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73647960"
---
# <a name="sample-2---regression-predict-price-and-compare-algorithms"></a>Exemplo 2-regressão: prever o preço e comparar algoritmos
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku.md)]

Saiba como criar um pipeline de Machine Learning sem escrever uma única linha de código usando o designer (versão prévia). Este exemplo treina e compara vários modelos de regressão para prever o preço de um carro com base em seus recursos técnicos. Forneceremos a lógica para as escolhas feitas nesse pipeline para que você possa lidar com seus próprios problemas de aprendizado de máquina.

Se você estiver apenas começando a usar o Machine Learning, dê uma olhada na [versão básica](how-to-designer-sample-regression-automobile-price-basic.md) desse pipeline.

Este é o grafo concluído para este pipeline:

[![grafo do pipeline](media/how-to-ui-sample-regression-predict-automobile-price-compare-algorithms/graph.png)](media/how-to-ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Clique em exemplo 2 para abri-lo. 

## <a name="pipeline-summary"></a>Resumo do pipeline

Use as etapas a seguir para criar o pipeline do Machine Learning:

1. Obter os dados.
1. Pré-processar os dados.
1. Treine o modelo.
1. Teste, avalie e compare os modelos.

## <a name="get-the-data"></a>Obter os dados

Este exemplo usa o conjunto de **dados de preço do automóvel (bruto)** , que é do repositório do Machine Learning UCI. Esse conjunto de dados contém 26 colunas que contêm informações sobre autocelular, incluindo marca, modelo, preço, recursos de veículo (como o número de cilindros), MPG e uma pontuação de risco de seguro.

## <a name="pre-process-the-data"></a>Pré-processar os dados

As principais tarefas de preparação de dados incluem limpeza, integração, transformação, redução e discretização ou Quantificação de dados. No designer, você pode encontrar módulos para executar essas operações e outras tarefas de pré-processamento de dados no grupo de **transformação de dados** no painel esquerdo.

Use o módulo **selecionar colunas no conjunto de DataSet** para excluir as perdas normalizadas que têm muitos valores ausentes. Em seguida, usamos **dados ausentes limpos** para remover as linhas que têm valores ausentes. Isso ajuda a criar um conjunto limpo de dados de treinamento.

![Pré-processamento de dados](media/how-to-ui-sample-regression-predict-automobile-price-compare-algorithms/data-processing.png)

## <a name="train-the-model"></a>Dar formação sobre o modelo

Os problemas de Machine Learning variam. As tarefas comuns de aprendizado de máquina incluem classificação, clustering, regressão e sistemas de recomendação, e cada uma delas pode exigir um algoritmo diferente. A escolha do algoritmo geralmente depende dos requisitos do caso de uso. Depois de escolher um algoritmo, você precisa ajustar seus parâmetros para treinar um modelo mais preciso. Em seguida, você precisa avaliar todos os modelos com base em métricas, como precisão, inteligibilidade e eficiência.

Como o objetivo desse pipeline é prever os preços do automóvel e, como a coluna de rótulo (preço) contém números reais, um modelo de regressão é uma boa opção. Considerando que o número de recursos é relativamente pequeno (menos de 100) e esses recursos não são esparsos, o limite de decisão provavelmente será não linear.

Para comparar o desempenho de algoritmos diferentes, usamos dois algoritmos não lineares, **regressão de árvore de decisão aumentada** e **regressão de floresta de decisão**, para criar modelos. Ambos os algoritmos têm parâmetros que podem ser alterados, mas este exemplo usa os valores padrão para esse pipeline.

Use o módulo **dividir dados** para dividir aleatoriamente os dados de entrada, de forma que o conjunto de informações de treinamento contenha 70% dos dados originais e o conjunto de dados de teste contenha 30% do dado original.

## <a name="test-evaluate-and-compare-the-models"></a>Testar, avaliar e comparar os modelos

Você usa dois conjuntos diferentes de dados escolhidos aleatoriamente para treinar e testar o modelo, conforme descrito na seção anterior. Divida o conjunto de informações e use conjuntos de valores diferentes para treinar e testar o modelo para tornar a avaliação do modelo mais objetiva.

Depois que o modelo for treinado, use o **modelo de Pontuação** e avalie os módulos de **modelo** para gerar resultados previstos e avaliar os modelos. O **modelo de Pontuação** gera previsões para o conjunto de teste usando o modelo treinado. Em seguida, passe as pontuações para **avaliar o modelo** para gerar métricas de avaliação.



Estes são os resultados:

![Comparar os resultados](media/how-to-ui-sample-regression-predict-automobile-price-compare-algorithms/result.png)

Esses resultados mostram que o modelo criado com a **regressão da árvore de decisão aumentada** tem um erro de média de minúsculas raiz inferior ao modelo criado na **regressão da floresta de decisão**.

Ambos os algoritmos têm um erro inferior no conjunto de testes de treinamento do que no conjunto de teste não visto.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Explore os outros exemplos disponíveis para o designer:

- [Exemplo 1-regressão: prever o preço de um automóvel](how-to-designer-sample-regression-automobile-price-basic.md)
- [Exemplo 3-classificação com seleção de recursos: Previsão de renda](how-to-designer-sample-classification-predict-income.md)
- [Exemplo 4-classificação: prever o risco de crédito (sensível ao custo)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [Exemplo 5-classificação: Previsão de rotatividade](how-to-designer-sample-classification-churn.md)
- [Exemplo 6-classificação: prever atrasos de voo](how-to-designer-sample-classification-flight-delay.md)
- [Exemplo de classificação de texto 7: conjunto de teste da Wikipédia SP 500](how-to-designer-sample-text-classification.md)
