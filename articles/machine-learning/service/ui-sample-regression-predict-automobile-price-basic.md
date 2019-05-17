---
title: 'Regressão: Prever o preço'
titleSuffix: Azure Machine Learning service
description: Saiba como criar um modelo de machine learning para prever o preço de um automóvel sem ter de escrever uma única linha de código.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: article
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: 9dfa4b62f5cb79a5716f6f29651e85d0f8a3a409
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65787848"
---
# <a name="sample-1---regression-predict-price"></a>Exemplo 1 - regressão: Prever o preço

Saiba como criar uma modelo de regressão de aprendizagem sem escrever uma única linha de código usando a interface visual.

Experimente isto treina uma **regressor foi de floresta de decisão** para prever um carro do preço com base nos recursos técnicos, como a marca, modelo, potência e tamanho. Uma vez que estamos tentando responder à pergunta "Quanto?" Isso é chamado um problema de regressão. No entanto, é possível aplicar as mesmas etapas fundamentais Nesse experimento para lidar com qualquer tipo de problema do machine learning, independentemente de serem regressão, classificação, clustering e assim por diante.

As etapas fundamentais de um modelo de aprendizagem automática de treinamento são:

1. Obter os dados
1. Pré-processar os dados
1. Dar formação sobre o modelo
1. Avaliar o modelo

Aqui está o gráfico da experimentação, que vamos trabalhar no final, se foi concluído. Iremos fornecer a lógica para todos os módulos para que possa tomar decisões semelhantes por conta própria.

![Gráfico da experimentação](media/ui-sample-regression-predict-automobile-price-basic/overall-graph.png)

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Selecione o **aberto** botão para a experimentação de exemplo 1:

    ![Abra a experimentação](media/ui-sample-regression-predict-automobile-price-basic/open-sample1.png)

## <a name="get-the-data"></a>Obter os dados

Nesse experimento, usamos o **dados de preço do automóvel (bruto)** conjunto de dados, o que é a partir do repositório de Aprendizado de máquina de UCI. O conjunto de dados contém 26 colunas que contêm informações sobre automóveis, incluindo a marca, modelo, preço, funcionalidades de veículos (como o número de cilindros), MPG e uma pontuação de risco de seguros. O objetivo desta experiência é prever o preço do carro.

## <a name="pre-process-the-data"></a>Pré-processar os dados

As tarefas de preparação de dados principal incluem limpeza de dados, integração, transformação, redução e discretização ou quantização. Na interface do visual, pode encontrar módulos para executar estas operações e outros tarefas de pré-processamento de dados do **transformação de dados** grupo no painel esquerdo.

Vamos utilizar o **Select Columns in Dataset** módulo para excluir perdas normalizadas que têm muitos valores em falta. Em seguida, usamos **Clean Missing Data** para remover as linhas com valores em falta. Isto ajuda a criar um conjunto de dados de treinamento de limpo.

![Processamento prévio de dados](./media/ui-sample-regression-predict-automobile-price-basic/data-processing.png)

## <a name="train-the-model"></a>Dar formação sobre o modelo

Problemas do Machine learning variam. Tarefas de machine learning incluem classificação, clustering, regressão e sistemas de recomendador, cada um dos quais pode exigir um algoritmo diferente. Sua escolha de algoritmo depende, muitas vezes, os requisitos de caso de utilização. Depois de escolher um algoritmo, terá de ajustar os parâmetros para preparar um modelo mais preciso. Em seguida, terá de avaliar todos os modelos com base em métricas, como a precisão, a inteligibilidade da e eficiência.

Como o objetivo desta experiência é prever preços de automóveis, e a coluna de etiqueta (preço) contém os números reais, um modelo de regressão é uma boa opção. Considerando que o número de recursos é relativamente pequeno (menos de 100) e esses recursos não estão dispersos, o limite de decisão é provavelmente será não-linear. Pelo que utilizamos **regressão de floresta de decisão** para esta fase experimental.

Vamos utilizar o **Split Data** módulo dividir aleatoriamente os dados de entrada para que o conjunto de dados de treinamento contém 70% dos dados originais e o conjunto de dados de teste contém 30% dos dados originais.

## <a name="test-evaluate-and-compare"></a>Testar, avaliar e comparar

 Vamos dividir o conjunto de dados e utilizar conjuntos de dados diferentes para treinar e testar o modelo para fazer a avaliação do modelo de objetivo de mais.

Depois que é preparado o modelo, usamos o **modelo de pontuação** e **Evaluate Model** módulos para gerar resultados previstos e avaliar os modelos.

**Modelo de pontuação** gera previsões para o conjunto de dados de teste usando o modelo treinado. Para verificar o resultado, selecione a porta de saída **modelo de pontuação** e, em seguida, selecione **Visualize**.

![Resultados de pontuação](./media/ui-sample-regression-predict-automobile-price-basic/score-result.png)

Em seguida, passamos as pontuações para o **Evaluate Model** módulo para gerar a métrica de avaliação. Para verificar o resultado, selecione a porta de saída a **Evaluate Model** e, em seguida, selecione **Visualize**.

![Avalie o resultado](./media/ui-sample-regression-predict-automobile-price-basic/evaluate-result.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Passos Seguintes

Explore os outros exemplos disponíveis para a interface visual:

- [Exemplo 2 - regressão: Compare os algoritmos de previsão de preços de automóveis](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [Exemplo 3 - classificação: Prever o risco de crédito](ui-sample-classification-predict-credit-risk-basic.md)
- [Exemplo 4 - classificação: Prever o risco de crédito (custo confidencial)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [Exemplo 5 - classificação: Prever o volume de alterações](ui-sample-classification-predict-churn.md)