---
title: 'Designer: Prever os preços dos automóveis (avançado) exemplo'
titleSuffix: Azure Machine Learning
description: Build & compare vários modelos de regressão ml para prever o preço de um automóvel com base em características técnicas com o designer de Machine Learning Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.date: 12/25/2019
ms.openlocfilehash: a80a1567c84ff3c2eda8ad22391aa862bb7d9d82
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2020
ms.locfileid: "77915836"
---
# <a name="train--compare-multiple-regression-models-to-predict-car-prices-with-azure-machine-learning-designer"></a>Comboio & comparar vários modelos de regressão para prever preços de carro com designer de Machine Learning Azure

**Amostra de designer (pré-visualização) 2**

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Aprenda a construir um pipeline de aprendizagem automática sem escrever uma única linha de código usando o designer (pré-visualização). Esta amostra treina e compara vários modelos de regressão para prever o preço de um carro com base nas suas características técnicas. Vamos fornecer a lógica das escolhas feitas neste oleoduto para que possa sosseada dos seus próprios problemas de aprendizagem automática.

Se está saindo com machine learning, veja a [versão básica](how-to-designer-sample-regression-automobile-price-basic.md) deste oleoduto.

Aqui está o gráfico completo para este oleoduto:

[gráfico ![do oleoduto](./media/how-to-designer-sample-regression-automobile-price-compare-algorithms/graph.png)](./media/how-to-designer-sample-regression-automobile-price-compare-algorithms/graph.png#lightbox)

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [aml-ui-prereq](../../includes/aml-ui-prereq.md)]

4. Clique na amostra 2 para abri-la. 

## <a name="pipeline-summary"></a>Resumo do gasoduto

Utilize os seguintes passos para construir o gasoduto de aprendizagem automática:

1. Pegue os dados.
1. Pré-processar os dados.
1. Treine o modelo.
1. Teste, avalie e compare os modelos.

## <a name="get-the-data"></a>Obter os dados

Esta amostra utiliza o conjunto de dados sobre os preços do **Automóvel (Raw),** que é do Repositório de Aprendizagem Automática uci. Este conjunto de dados contém 26 colunas que contêm informações sobre automóveis, incluindo fabricação, modelo, preço, características do veículo (como o número de cilindros), MPG e uma pontuação de risco de seguro.

## <a name="pre-process-the-data"></a>Pré-processar os dados

As principais tarefas de preparação de dados incluem limpeza de dados, integração, transformação, redução e discretização ou quantificação. No designer, pode encontrar módulos para realizar estas operações e outras tarefas de pré-processamento de dados no grupo **de Transformação** de Dados no painel esquerdo.

Utilize as **Colunas Select no** módulo Dataset para excluir perdas normalizadas que tenham muitos valores em falta. Em seguida, usamos **Dados Limpos Desaparecidos** para remover as linhas que têm valores em falta. Isto ajuda a criar um conjunto limpo de dados de treino.

![Pré-processamento de dados](./media/how-to-designer-sample-regression-automobile-price-compare-algorithms/data-processing.png)

## <a name="train-the-model"></a>Dar formação sobre o modelo

Os problemas de aprendizagem automática variam. As tarefas comuns de aprendizagem automática incluem sistemas de classificação, agrupamento, regressão e recomendadores, cada um dos quais pode exigir um algoritmo diferente. A sua escolha de algoritmo depende frequentemente dos requisitos do caso de utilização. Depois de escolher um algoritmo, precisa de afinar os seus parâmetros para treinar um modelo mais preciso. Em seguida, é necessário avaliar todos os modelos com base em métricas como precisão, inteligibilidade e eficiência.

Porque o objetivo deste oleoduto é prever os preços dos automóveis, e porque a coluna de etiquetas (preço) contém números reais, um modelo de regressão é uma boa escolha.

Para comparar o desempenho de diferentes algoritmos, usamos dois algoritmos não lineares, **a Regressão** da Árvore de Decisão Impulsionada e a **Regressão da Floresta**de Decisão, para construir modelos. Ambos os algoritmos têm parâmetros que pode alterar, mas esta amostra utiliza os valores padrão para este pipeline.

Utilize o módulo **De dados divididos** para dividir aleatoriamente os dados de entrada de modo a que o conjunto de dados de formação contenha 70% dos dados originais e o conjunto de dados de teste contenha 30% dos dados originais.

## <a name="test-evaluate-and-compare-the-models"></a>Testar, avaliar e comparar os modelos

Utiliza dois conjuntos diferentes de dados escolhidos aleatoriamente para treinar e depois testar o modelo, como descrito na secção anterior. Divida o conjunto de dados e use diferentes conjuntos de dados para treinar e testar o modelo para tornar a avaliação do modelo mais objetiva.

Depois de o modelo ser treinado, utilize os módulos **'Modelo de Pontuação'** e avalie os módulos de Modelo de **Pontuação** para gerar resultados previstos e avaliar os modelos. **O Modelo de Pontuação** gera previsões para o conjunto de dados de teste utilizando o modelo treinado. Em seguida, passe as pontuações para **Avaliar O Modelo** para gerar métricas de avaliação.



Aqui estão os resultados:

![Compare os resultados](./media/how-to-designer-sample-regression-automobile-price-compare-algorithms/result.png)

Estes resultados mostram que o modelo construído com **a Boosted Decision Tree Regression** tem um erro quadrado de raiz inferior do que o modelo construído sobre a **Regressão da Floresta**de Decisão.



## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Explore as outras amostras disponíveis para o designer:

- [Amostra 1 - Regressão: Prever o preço de um automóvel](how-to-designer-sample-regression-automobile-price-basic.md)
- [Amostra 3 - Classificação com seleção de características: Previsão do Rendimento](how-to-designer-sample-classification-predict-income.md)
- [Amostra 4 - Classificação: Prever o risco de crédito (sensível ao custo)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [Amostra 5 - Classificação: Prever churn](how-to-designer-sample-classification-churn.md)
- [Amostra 6 - Classificação: Prever atrasos nos voos](how-to-designer-sample-classification-flight-delay.md)
- [Amostra 7 - Classificação de Texto: Wikipedia SP 500 Dataset](how-to-designer-sample-text-classification.md)
