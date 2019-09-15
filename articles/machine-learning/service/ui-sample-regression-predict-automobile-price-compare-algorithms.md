---
title: 'Exemplo de interface visual #3: regressão para algoritmos de preço e comparação'
titleSuffix: Azure Machine Learning
description: Este artigo mostra como criar um experimento de aprendizado de máquina complexo sem escrever uma única linha de código usando a interface visual. Saiba como treinar e comparar vários modelos de regressão para prever o preço de um carro com base nos recursos técnicos
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: 2c45ccf55b1adde7150dee17fd562b24ee4777ba
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/14/2019
ms.locfileid: "70997106"
---
# <a name="sample-2---regression-predict-price-and-compare-algorithms"></a>Exemplo 2-regressão: Prever o preço e comparar algoritmos

Saiba como criar um experimento de aprendizado de máquina complexo sem escrever uma única linha de código usando a interface visual. Este exemplo treina e compara vários modelos de regressão para prever o preço de um carro com base em seus recursos técnicos. Forneceremos a lógica para as escolhas feitas nesse experimento para que você possa lidar com seus próprios problemas de aprendizado de máquina.

Se você estiver apenas começando a usar o Machine Learning, poderá dar uma olhada na [versão básica](ui-sample-regression-predict-automobile-price-basic.md) desse experimento para ver um experimento de regressão básico.

Este é o grafo concluído para este experimento:

[![Grafo do experimento](media/ui-sample-regression-predict-automobile-price-compare-algorithms/graph.png)](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Selecione o botão **abrir** para o experimento de exemplo 2:

    ![Abrir o experimento](media/ui-sample-regression-predict-automobile-price-compare-algorithms/open-sample2.png)

## <a name="experiment-summary"></a>Resumo do experimento

Usamos estas etapas para criar o experimento:

1. Obter os dados.
1. Pré-processar os dados.
1. Treine o modelo.
1. Teste, avalie e compare os modelos.

## <a name="get-the-data"></a>Obter os dados

Neste experimento, usamos o conjunto de **dados de preço de automóvel (bruto)** , que é do repositório de Machine Learning de UCI. Esse conjunto de dados contém 26 colunas que contêm informações sobre autocelular, incluindo marca, modelo, preço, recursos de veículo (como o número de cilindros), MPG e uma pontuação de risco de seguro. O objetivo desse experimento é prever o preço de um carro.

## <a name="pre-process-the-data"></a>Pré-processar os dados

As principais tarefas de preparação de dados incluem limpeza, integração, transformação, redução e discretização ou Quantificação de dados. Na interface visual, você pode encontrar módulos para executar essas operações e outras tarefas de pré-processamento de dados no grupo de **transformação de dados** no painel esquerdo.

Neste experimento, usamos o módulo **selecionar colunas no conjunto** de módulos para excluir perdas normalizadas que têm muitos valores ausentes. Em seguida, usamos **dados ausentes limpos** para remover as linhas que têm valores ausentes. Isso ajuda a criar um conjunto limpo de dados de treinamento.

![Pré-processamento de dados](media/ui-sample-regression-predict-automobile-price-compare-algorithms/data-processing.png)

## <a name="train-the-model"></a>Dar formação sobre o modelo

Os problemas de Machine Learning variam. As tarefas comuns de aprendizado de máquina incluem classificação, clustering, regressão e sistemas de recomendação, e cada uma delas pode exigir um algoritmo diferente. A escolha do algoritmo geralmente depende dos requisitos do caso de uso. Depois de escolher um algoritmo, você precisa ajustar seus parâmetros para treinar um modelo mais preciso. Em seguida, você precisa avaliar todos os modelos com base em métricas, como precisão, inteligibilidade e eficiência.

Como o objetivo desse experimento é prever os preços do automóvel e, como a coluna de rótulo (preço) contém números reais, um modelo de regressão é uma boa opção. Considerando que o número de recursos é relativamente pequeno (menos de 100) e esses recursos não são esparsos, o limite de decisão provavelmente será não linear.

Para comparar o desempenho de algoritmos diferentes, usamos dois algoritmos não lineares, **regressão de árvore de decisão aumentada** e **regressão de floresta de decisão**, para criar modelos. Ambos os algoritmos têm parâmetros que podem ser alterados, mas usamos os valores padrão para esse experimento.

Usamos o módulo **dividir dados** para dividir aleatoriamente os dados de entrada, de forma que o conjunto de informações de treinamento contenha 70% dos dados originais e o conjunto de dados de teste contenha 30% das informações originais.

## <a name="test-evaluate-and-compare-the-models"></a>Testar, avaliar e comparar os modelos

Usamos dois conjuntos diferentes de dados escolhidos aleatoriamente para treinar e testar o modelo, conforme descrito na seção anterior. Dividimos o conjunto de informações e usamos conjuntos de valores diferentes para treinar e testar o modelo para tornar a avaliação do modelo mais objetiva.

Depois que o modelo é treinado, usamos o **modelo de Pontuação** e **avaliamos** os módulos de modelo para gerar resultados previstos e avaliar os modelos. O **modelo de Pontuação** gera previsões para o conjunto de teste usando o modelo treinado. Em seguida, passamos as pontuações para **avaliar o modelo** para gerar métricas de avaliação.

Neste experimento, usamos duas instâncias do modelo de **avaliação** para comparar dois pares de modelos.

Primeiro, comparamos dois algoritmos no conjunto de os de treinamento.
Em segundo lugar, comparamos dois algoritmos no conjunto de testes.
Estes são os resultados:

![Comparar os resultados](media/ui-sample-regression-predict-automobile-price-compare-algorithms/result.png)

Esses resultados mostram que o modelo criado com a **regressão da árvore de decisão aumentada** tem um erro de média de minúsculas raiz inferior ao modelo criado na **regressão da floresta de decisão**.

Ambos os algoritmos têm um erro inferior no conjunto de testes de treinamento do que no conjunto de teste não visto.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Passos Seguintes

Explore os outros exemplos disponíveis para a interface visual:

- [Amostra 1-regressão: Prever o preço de um automóvel](ui-sample-regression-predict-automobile-price-basic.md)
- [Exemplo 3-classificação: Prever risco de crédito](ui-sample-classification-predict-credit-risk-basic.md)
- [Amostra 4-classificação: Prever o risco de crédito (sensível ao custo)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [Exemplo 5-classificação: Previsão de rotatividade](ui-sample-classification-predict-churn.md)
- [Exemplo 6-classificação: Prever atrasos de voo](ui-sample-classification-predict-flight-delay.md)
