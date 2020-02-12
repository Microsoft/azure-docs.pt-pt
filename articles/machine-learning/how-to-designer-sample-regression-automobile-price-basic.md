---
title: 'Designer: Prever preços dos automóveis (básico) exemplo'
titleSuffix: Azure Machine Learning
description: Construa um modelo de regressão ML para prever o preço de um automóvel sem escrever uma única linha de código com o designer de Machine Learning Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.date: 02/11/2020
ms.openlocfilehash: 58adbc7607b0b32e79123b701c37f55ce7cc1d2e
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77138117"
---
# <a name="use-regression-to-predict-car-prices-with-azure-machine-learning-designer"></a>Use a regressão para prever os preços dos carros com o designer de machine learning Azure

**Amostra de designer (pré-visualização) 1**

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Aprenda a construir um modelo de regressão de aprendizagem automática sem escrever uma única linha de código usando o designer (pré-visualização).

Este gasoduto treina um **regresso linear** para prever o preço de um carro com base em características técnicas como make, model, horsepower e size. Porque estás a tentar responder à pergunta "Quanto?" isto é chamado de um problema de regressão. No entanto, pode aplicar os mesmos passos fundamentais neste exemplo para resolver qualquer tipo de problema de aprendizagem automática, quer se trate de regressão, classificação, agrupamento, e assim por diante.

Os passos fundamentais de um modelo de aprendizagem automática de formação são:

1. Obter os dados
1. Pré-processar os dados
1. Dar formação sobre o modelo
1. Avaliar o modelo

Aqui está o gráfico final e completo do oleoduto. Este artigo fornece a razão para todos os módulos para que possa tomar decisões semelhantes por conta própria.

![Gráfico do oleoduto](./media/how-to-designer-sample-regression-automobile-price-basic/overall-graph.png)

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [aml-ui-prereq](../../includes/aml-ui-prereq.md)]

4. Clique na amostra 1 para abri-la.


## <a name="get-the-data"></a>Obter os dados

Esta amostra utiliza o conjunto de dados sobre os preços do **Automóvel (Raw),** que é do Repositório de Aprendizagem Automática uci. O conjunto de dados contém 26 colunas que contêm informações sobre automóveis, incluindo fabricação, modelo, preço, características do veículo (como o número de cilindros), MPG e uma pontuação de risco de seguro. O objetivo desta amostra é prever o preço do carro.

## <a name="pre-process-the-data"></a>Pré-processar os dados

As principais tarefas de preparação de dados incluem limpeza de dados, integração, transformação, redução e discretização ou quantificação. No designer, pode encontrar módulos para realizar estas operações e outras tarefas de pré-processamento de dados no grupo **de Transformação** de Dados no painel esquerdo.

Utilize as **Colunas Select no** módulo Dataset para excluir perdas normalizadas que tenham muitos valores em falta. Em seguida, utilize **dados de falta limpos** para remover as linhas que têm valores em falta. Isto ajuda a criar um conjunto limpo de dados de treino.

![Pré-processamento de dados](./media/how-to-designer-sample-regression-automobile-price-basic/data-processing.png)

## <a name="train-the-model"></a>Dar formação sobre o modelo

Os problemas de aprendizagem automática variam. As tarefas comuns de aprendizagem automática incluem sistemas de classificação, agrupamento, regressão e recomendadores, cada um dos quais pode exigir um algoritmo diferente. A sua escolha de algoritmo depende frequentemente dos requisitos do caso de utilização. Depois de escolher um algoritmo, precisa de afinar os seus parâmetros para treinar um modelo mais preciso. Em seguida, é necessário avaliar todos os modelos com base em métricas como precisão, inteligibilidade e eficiência.

Uma vez que o objetivo desta amostra é prever os preços dos automóveis, e porque a coluna de etiquetas (preço) é dados contínuos, um modelo de regressão pode ser uma boa escolha. Usamos **regressão linear** para este oleoduto.

Utilize o módulo **De dados divididos** para dividir aleatoriamente os dados de entrada de modo a que o conjunto de dados de formação contenha 70% dos dados originais e o conjunto de dados de teste contenha 30% dos dados originais.

## <a name="test-evaluate-and-compare"></a>Testar, avaliar e comparar

Divida o conjunto de dados e use diferentes conjuntos de dados para treinar e testar o modelo para tornar a avaliação do modelo mais objetiva.

Depois de o modelo ser treinado, pode utilizar os módulos **'Modelo de Pontuação'** e Avaliar os módulos de Modelo de **Pontuação** para gerar resultados previstos e avaliar os modelos.

**O Modelo de Pontuação** gera previsões para o conjunto de dados de teste utilizando o modelo treinado. Para verificar o resultado, selecione a porta de saída do **Modelo de Pontuação** e, em seguida, selecione **Visualize**.

![Resultado da pontuação](./media/how-to-designer-sample-regression-automobile-price-basic/sample1-score-1225.png)

Passe as pontuações para o módulo **'Avaliar Modelo'** para gerar métricas de avaliação. Para verificar o resultado, selecione a porta de saída do **Modelo de Avaliação** e, em seguida, selecione **Visualize**.

![Avaliar resultado](./media/how-to-designer-sample-regression-automobile-price-basic/sample1-evaluate-1225.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Explore as outras amostras disponíveis para o designer:

- [Amostra 2 - Regressão: Comparar algoritmos para previsão de preços do automóvel](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [Amostra 3 - Classificação com seleção de características: Previsão do Rendimento](how-to-designer-sample-classification-predict-income.md)
- [Amostra 4 - Classificação: Prever o risco de crédito (sensível ao custo)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [Amostra 5 - Classificação: Prever churn](how-to-designer-sample-classification-churn.md)
- [Amostra 6 - Classificação: Prever atrasos nos voos](how-to-designer-sample-classification-flight-delay.md)
- [Amostra 7 - Classificação de Texto: Wikipedia SP 500 Dataset](how-to-designer-sample-text-classification.md)