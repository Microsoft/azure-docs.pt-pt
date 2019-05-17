---
title: 'Classificação: Prever o risco do crédito'
titleSuffix: Azure Machine Learning service
description: Saiba como criar uma classificador de aprendizagem sem escrever uma única linha de código usando a interface visual.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: article
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: f37c945758cfbd03889d79acf764e7f67022267a
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65789401"
---
# <a name="sample-3---classification-predict-credit-risk"></a>Exemplo 3 - classificação: Prever o risco do crédito

Saiba como criar uma classificador de aprendizagem sem escrever uma única linha de código usando a interface visual. Este exemplo prepara um **árvore de decisões elevada de duas classes** crédito de prever risco (alto ou baixo) com base nas informações de aplicação de crédito como o histórico de crédito, a idade e o número de cartões de crédito.

Uma vez que estamos tentando responder à pergunta "Qual delas?" Isso é chamado um problema de classificação. No entanto, pode aplicar o mesmo processo fundamental para lidar com qualquer tipo de problema do machine learning, independentemente de serem regressão, classificação, clustering e assim por diante.

Este é o gráfico concluído para esta fase experimental:

![Gráfico da experimentação](media/ui-sample-classification-predict-credit-risk-basic/overall-graph.png)

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Selecione o **aberto** botão para a experimentação de exemplo 3:

    ![Abra a experimentação](media/ui-sample-classification-predict-credit-risk-basic/open-sample3.png)

## <a name="related-sample"></a>Exemplo relacionado

[Exemplo 4 - classificação: Credit Risk Prediction (custo confidenciais)](ui-sample-classification-predict-credit-risk-cost-sensitive.md) fornece uma experiência avançada que resolve o mesmo problema que esta experiência. Ele mostra como realizar _custo confidencial_ classificação usando uma **executar Script do Python** módulo e comparar o desempenho de dois algoritmos de classificação binária. Fazer referência a ele, se quiser saber mais sobre como criar experimentações de classificação.

## <a name="data"></a>Dados

Usamos o conjunto de dados do cartão de crédito alemão do repositório de UC Irvine.
O conjunto de dados contém 1.000 exemplos com 20 recursos e 1 etiqueta. Cada exemplo representa uma pessoa. Os recursos incluem recursos numéricos e categóricos. Consulte a [Web site UCI](https://archive.ics.uci.edu/ml/datasets/Statlog+%28German+Credit+Data%29) para o significado dos recursos categóricos. A última coluna é a etiqueta que indica o risco de crédito e tem apenas dois valores possíveis: risco elevado crédito = 2 e o risco de crédito baixa = 1.

## <a name="experiment-summary"></a>Resumo de experimentação

Nós seguir estes passos para criar a experimentação:

1. Arraste o módulo de conjunto de dados de dados de UCI alemão cartões de crédito para tela de experimentação.
1. Adicionar uma **Editar metadados** módulo, pelo que pode adicionar nomes significativos para cada coluna.
1. Adicionar uma **Split Data** módulo para criar os conjuntos de formação e teste. Defina a fração de linhas no conjunto de dados de saída primeiro para 0,7. Esta definição especifica que 70% dos dados será a saída para a porta à esquerda do módulo e o resto para a porta à direita. Utilizamos o conjunto de dados à esquerda para treinamento e o direito para teste.
1. Adicionar uma **árvore de decisões elevada de duas classes** módulo para inicializar um classificador de árvore de decisões elevada.
1. Adicionar uma **Train Model** módulo. Ligar o classificador do passo anterior para a porta de entrada à esquerda do **Train Model**. Adicionar o conjunto de treinamento (à esquerda de porta de saída a **Split Data**) para a porta de entrada à direita da **Train Model**. O **modelo de formação** será treinar o classificador.
1. Adicionar uma **modelo de pontuação** módulo e ligue-se a **Train Model** módulo ao mesmo. Em seguida, adicione o conjunto de teste (a porta à direita do **Split Data**) para o **Score Model**. O **modelo de pontuação** fará com que as previsões. Pode selecionar sua porta de saída para ver as previsões e as probabilidades de classe positivo.
1. Adicionar uma **Evaluate Model** módulo e ligue-se o conjunto de dados com a pontuação para sua porta de entrada à esquerda. Para ver os resultados da avaliação, selecione a porta de saída a **Evaluate Model** módulo e selecione **Visualize**.

Este é o gráfico de experimentação concluída:

![Gráfico da experimentação](media/ui-sample-classification-predict-credit-risk-basic/overall-graph.png)

## <a name="results"></a>Resultados

![Avaliar os resultados](media/ui-sample-classification-predict-credit-risk-basic/evaluate-result.png)

Nos resultados da avaliação, pode ver que o AUC do modelo é 0.776. Com o limiar 0,5, a precisão é 0.621, a solicitação de recolhimento é 0.456 e a pontuação de F1 é 0.526.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Passos Seguintes

Explore os outros exemplos disponíveis para a interface visual:

- [Exemplo 1 - regressão: Prever o preço de um automóvel](ui-sample-regression-predict-automobile-price-basic.md)
- [Exemplo 2 - regressão: Compare os algoritmos de previsão de preços de automóveis](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [Exemplo 4 - classificação: Prever o risco de crédito (custo confidencial)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [Exemplo 5 - classificação: Prever o volume de alterações](ui-sample-classification-predict-churn.md)