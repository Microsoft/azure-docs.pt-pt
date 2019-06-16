---
title: 'Regressão: Prever o preço e comparar algoritmos'
titleSuffix: Azure Machine Learning service
description: Este artigo mostra-lhe como criar uma experimentação complexos de machine learning sem ter de escrever uma única linha de código usando a interface visual. Saiba como dar formação e comparar vários modelos de regressão para prever o preço de um carro com base nos recursos técnicos
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: article
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: c8c813a2304797e71499a916e29c18f8bec2b389
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65787802"
---
# <a name="sample-2---regression-predict-price-and-compare-algorithms"></a>Exemplo 2 - regressão: Prever o preço e comparar algoritmos

Saiba como criar uma experimentação complexos de machine learning sem ter de escrever uma única linha de código usando a interface visual. Este exemplo prepara e compara os vários modelos de regressão para prever o preço de um carro com base nos seus recursos técnicos. Iremos fornecer a lógica para as escolhas feitas nesse experimento, portanto, pode lidar com a sua própria machine learning problemas.

Se estiver apenas a começar com o machine learning, pode examinar os [versão básica](ui-sample-regression-predict-automobile-price-basic.md) desta experiência para ver uma regressão básica de experimentação.

Este é o gráfico concluído para esta fase experimental:

[![Gráfico da experimentação](media/ui-sample-regression-predict-automobile-price-compare-algorithms/graph.png)](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Selecione o **aberto** botão para a experimentação de exemplo 2:

    ![Abra a experimentação](media/ui-sample-regression-predict-automobile-price-compare-algorithms/open-sample2.png)

## <a name="experiment-summary"></a>Resumo de experimentação

Utilizamos estes passos para criar a experimentação:

1. Obter os dados.
1. Pré-processe os dados.
1. Prepare o modelo.
1. Testar, avaliar e compare os modelos.

## <a name="get-the-data"></a>Obter os dados

Nesse experimento, usamos o **dados de preço do automóvel (bruto)** conjunto de dados, o que é a partir do repositório de Aprendizado de máquina de UCI. Este conjunto de dados contém 26 colunas que contêm informações sobre automóveis, incluindo a marca, modelo, preço, funcionalidades de veículos (como o número de cilindros), MPG e uma pontuação de risco de seguros. O objetivo desta experiência é prever o preço de um carro.

## <a name="pre-process-the-data"></a>Pré-processar os dados

As tarefas de preparação de dados principal incluem limpeza de dados, integração, transformação, redução e discretização ou quantização. No visual interface, pode encontrar módulos para executar estas operações e outros tarefas de pré-processamento de dados do **transformação de dados** grupo no painel esquerdo.

Nesse experimento, usamos o **Select Columns in Dataset** módulo para excluir perdas normalizadas que têm muitos valores em falta. Em seguida, usamos **Clean Missing Data** para remover as linhas com valores em falta. Isto ajuda a criar um conjunto de dados de treinamento de limpo.

![Processamento prévio de dados](media/ui-sample-regression-predict-automobile-price-compare-algorithms/data-processing.png)

## <a name="train-the-model"></a>Dar formação sobre o modelo

Problemas do Machine learning variam. Tarefas de machine learning incluem classificação, clustering, regressão e sistemas de recomendador, cada um dos quais pode exigir um algoritmo diferente. Sua escolha de algoritmo depende, muitas vezes, os requisitos de caso de utilização. Depois de escolher um algoritmo, terá de ajustar os parâmetros para preparar um modelo mais preciso. Em seguida, terá de avaliar todos os modelos com base em métricas, como a precisão, a inteligibilidade da e eficiência.

Como o objetivo desta experiência é prever preços de automóveis, e a coluna de etiqueta (preço) contém os números reais, um modelo de regressão é uma boa opção. Considerando que o número de recursos é relativamente pequeno (menos de 100) e esses recursos não estão dispersos, o limite de decisão é provavelmente será não-linear.

Para comparar o desempenho dos algoritmos diferentes, usamos dois algoritmos de não-lineares **regressão da árvore de decisão elevada** e **regressão de floresta de decisão**, para criar modelos. Ambos os algoritmos os parâmetros que podem ser alteradas, mas usamos os valores predefinidos para esta fase experimental.

Vamos utilizar o **Split Data** módulo dividir aleatoriamente os dados de entrada para que o conjunto de dados de treinamento contém 70% dos dados originais e o conjunto de dados de teste contém 30% dos dados originais.

## <a name="test-evaluate-and-compare-the-models"></a>Testar, avaliar e compare os modelos

Usamos dois diferentes conjuntos de dados escolhidos aleatoriamente para treinar e, em seguida, testar o modelo, conforme descrito na secção anterior. Vamos dividir o conjunto de dados e utilizar conjuntos de dados diferentes para treinar e testar o modelo para fazer a avaliação do modelo de objetivo de mais.

Depois que é preparado o modelo, usamos o **modelo de pontuação** e **Evaluate Model** módulos para gerar resultados previstos e avaliar os modelos. **Modelo de pontuação** gera previsões para o conjunto de dados de teste usando o modelo treinado. Em seguida, passamos as pontuações para **Evaluate Model** para gerar a métrica de avaliação.

Nesta experimentação, utilizamos duas instâncias da **Evaluate Model** para comparar dois pares de modelos.

Em primeiro lugar, vamos comparar dois algoritmos no conjunto de dados de treinamento.
Em segundo lugar, vamos comparar dois algoritmos no conjunto de dados de teste.
Aqui estão os resultados:

![Compare os resultados](media/ui-sample-regression-predict-automobile-price-compare-algorithms/result.png)

Esses resultados mostram que o modelo criado com **regressão da árvore de decisão elevada** tem uma raiz inferior significa erros ao quadrado do que o modelo criado nos **regressão de floresta de decisão**.

Ambos os algoritmos tem um erro de inferior no conjunto de dados treinamento que no conjunto de dados de teste que não foram visto.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Passos Seguintes

Explore os outros exemplos disponíveis para a interface visual:

- [Exemplo 1 - regressão: Prever o preço de um automóvel](ui-sample-regression-predict-automobile-price-basic.md)
- [Exemplo 3 - classificação: Prever o risco de crédito](ui-sample-classification-predict-credit-risk-basic.md)
- [Exemplo 4 - classificação: Prever o risco de crédito (custo confidencial)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [Exemplo 5 - classificação: Prever o volume de alterações](ui-sample-classification-predict-churn.md)
