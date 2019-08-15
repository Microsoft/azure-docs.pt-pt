---
title: 'Exemplo de interface visual #1: regressão para prever o preço'
titleSuffix: Azure Machine Learning service
description: Saiba como criar um modelo de aprendizado de máquina para prever o preço de um automóvel sem escrever uma única linha de código.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: 0c86955e0b10111bf9b6db0d884b73867a4467c5
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68990413"
---
# <a name="sample-1---regression-predict-price"></a>Amostra 1-regressão: Prever o preço

Saiba como criar um modelo de regressão de Machine Learning sem escrever uma única linha de código usando a interface visual.

Esse experimento treina um **regressor de floresta de decisão** para prever o preço de um carro com base em recursos técnicos, como marca, modelo, potência e tamanho. Porque estamos tentando responder à pergunta "o quanto?" Isso é chamado de problema de regressão. No entanto, você pode aplicar as mesmas etapas fundamentais nesse experimento para lidar com qualquer tipo de problema de aprendizado de máquina, seja regressão, classificação, clustering e assim por diante.

As etapas fundamentais de um modelo de aprendizado de máquina de treinamento são:

1. Obter os dados
1. Pré-processar os dados
1. Dar formação sobre o modelo
1. Avaliar o modelo

Este é o grafo final e concluído do experimento em que estamos trabalhando. Forneceremos a lógica para todos os módulos para que você possa tomar decisões semelhantes por conta própria.

![Grafo do experimento](media/ui-sample-regression-predict-automobile-price-basic/overall-graph.png)

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Selecione o botão **abrir** para o teste de exemplo 1:

    ![Abrir o experimento](media/ui-sample-regression-predict-automobile-price-basic/open-sample1.png)

## <a name="get-the-data"></a>Obter os dados

Neste experimento, usamos o conjunto de **dados de preço de automóvel (bruto)** , que é do repositório de Machine Learning de UCI. O conjunto de dados contém 26 colunas que contêm informações sobre autocelular, incluindo marca, modelo, preço, recursos de veículo (como o número de cilindros), MPG e uma pontuação de risco de seguro. O objetivo desse experimento é prever o preço do carro.

## <a name="pre-process-the-data"></a>Pré-processar os dados

As principais tarefas de preparação de dados incluem limpeza, integração, transformação, redução e discretização ou Quantificação de dados. Na interface visual, você pode encontrar módulos para executar essas operações e outras tarefas de pré-processamento de dados no grupo de **transformação de dados** no painel esquerdo.

Usamos o módulo **selecionar colunas no conjunto** de módulos para excluir perdas normalizadas que têm muitos valores ausentes. Em seguida, usamos **dados ausentes limpos** para remover as linhas que têm valores ausentes. Isso ajuda a criar um conjunto limpo de dados de treinamento.

![Pré-processamento de dados](./media/ui-sample-regression-predict-automobile-price-basic/data-processing.png)

## <a name="train-the-model"></a>Dar formação sobre o modelo

Os problemas de Machine Learning variam. As tarefas comuns de aprendizado de máquina incluem classificação, clustering, regressão e sistemas de recomendação, e cada uma delas pode exigir um algoritmo diferente. A escolha do algoritmo geralmente depende dos requisitos do caso de uso. Depois de escolher um algoritmo, você precisa ajustar seus parâmetros para treinar um modelo mais preciso. Em seguida, você precisa avaliar todos os modelos com base em métricas, como precisão, inteligibilidade e eficiência.

Como o objetivo desse experimento é prever os preços do automóvel e, como a coluna de rótulo (preço) contém números reais, um modelo de regressão é uma boa opção. Considerando que o número de recursos é relativamente pequeno (menos de 100) e esses recursos não são esparsos, o limite de decisão provavelmente será não linear. Portanto, usamos a regressão da **floresta de decisão** para esse experimento.

Usamos o módulo **dividir dados** para dividir aleatoriamente os dados de entrada, de forma que o conjunto de informações de treinamento contenha 70% dos dados originais e o conjunto de dados de teste contenha 30% das informações originais.

## <a name="test-evaluate-and-compare"></a>Testar, avaliar e comparar

 Dividimos o conjunto de informações e usamos conjuntos de valores diferentes para treinar e testar o modelo para tornar a avaliação do modelo mais objetiva.

Depois que o modelo é treinado, usamos o **modelo de Pontuação** e avaliamos os módulos de **modelo** para gerar resultados previstos e avaliar os modelos.

O **modelo de Pontuação** gera previsões para o conjunto de teste usando o modelo treinado. Para verificar o resultado, selecione a porta de saída do **modelo de Pontuação** e, em seguida, selecione **Visualizar**.

![Resultado da Pontuação](./media/ui-sample-regression-predict-automobile-price-basic/score-result.png)

Em seguida, passamos as pontuações para o módulo **modelo** de avaliação para gerar métricas de avaliação. Para verificar o resultado, selecione a porta de saída do **modelo de avaliação** e, em seguida, selecione **Visualizar**.

![Avaliar resultado](./media/ui-sample-regression-predict-automobile-price-basic/evaluate-result.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Passos Seguintes

Explore os outros exemplos disponíveis para a interface visual:

- [Exemplo 2-regressão: Comparar algoritmos para previsão de preço de automóvel](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [Exemplo 3-classificação: Prever risco de crédito](ui-sample-classification-predict-credit-risk-basic.md)
- [Amostra 4-classificação: Prever o risco de crédito (sensível ao custo)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [Exemplo 5-classificação: Previsão de rotatividade](ui-sample-classification-predict-churn.md)
- [Exemplo 6-classificação: Prever atrasos de voo](ui-sample-classification-predict-flight-delay.md)
