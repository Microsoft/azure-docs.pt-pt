---
title: 'Designer: Classificar, prever o exemplo do rendimento'
titleSuffix: Azure Machine Learning
description: Siga este exemplo construa um classificador sem código para prever o rendimento com o designer de Machine Learning Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.date: 02/22/2020
ms.openlocfilehash: 7fd51f587ff51e09254741615d3059d038e1205a
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2020
ms.locfileid: "77915916"
---
# <a name="build-a-classifier--use-feature-selection-to-predict-income-with-azure-machine-learning-designer"></a>Construa uma seleção de funcionalidades de designe e utilização para prever rendimento com o designer de Machine Learning Azure

**Amostra de designer (pré-visualização) 3**

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Aprenda a construir um classificador de machine learning sem escrever uma única linha de código usando o designer (pré-visualização). Esta amostra treina uma **árvore de decisão reforçada de duas classes** para prever o rendimento do recenseamento adulto (>=50K ou <=50K).

Porque a pergunta é responder "Qual?", isto é chamado de problema de classificação. No entanto, pode aplicar o mesmo processo fundamental para resolver qualquer tipo de problema de aprendizagem automática - regressão, classificação, agrupamento, e assim por diante.

Aqui está o gráfico final do pipeline para esta amostra:

![Gráfico do oleoduto](./media/how-to-designer-sample-classification-predict-income/overall-graph.png)

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [aml-ui-prereq](../../includes/aml-ui-prereq.md)]

4. Clique na amostra 3 para aabrir.



## <a name="data"></a>Dados

O conjunto de dados contém 14 funcionalidades e uma coluna de etiqueta. Existem vários tipos de funcionalidades, incluindo numérica e categórica. O diagrama seguinte mostra um excerto do conjunto de dados: ![dados](media/how-to-designer-sample-classification-predict-income/sample3-dataset-1225.png)



## <a name="pipeline-summary"></a>Resumo do gasoduto

Siga estes passos para criar o oleoduto:

1. Arraste o módulo de conjunto de dados binário de rendimento do recenseamento adulto para a tela do gasoduto.
1. Adicione um módulo **De dados divididos** para criar os conjuntos de treino e teste. Desloque a fração de linhas no primeiro conjunto de dados de saída para 0,7. Esta definição especifica que 70% dos dados serão de saída para a porta esquerda do módulo e os restantes para a porta direita. Usamos o conjunto de dados esquerdo para treino e o direito para testes.
1. Adicione o módulo de seleção de **funcionalidades baseado** no filtro para selecionar 5 funcionalidades por PearsonCorrelation. 
1. Adicione um módulo de **árvore de decisão impulsionada de duas classes** para inicializar um classificador de árvore de decisão reforçado.
1. Adicione um módulo **Modelo de Comboio.** Ligue o classificador do passo anterior à porta de entrada esquerda do Modelo de **Comboio**. Ligue o conjunto de dados filtrado do módulo de seleção de funcionalidades baseado no filtro como conjunto de dados de treino.  O **Modelo de Comboio** vai treinar o classificador.
1. Adicione o módulo de transformação de colunas selecionadas e aplique o módulo de transformação para aplicar a mesma transformação (seleção de funcionalidades baseada selada filtrada) para testar o conjunto de dados.
![](./media/how-to-designer-sample-classification-predict-income/transformation.png) de transformação de aplicações
1. Adicione o módulo **'Modelo de Pontuação'** e ligue o módulo **Modelo de Comboio** ao mesmo. Em seguida, adicione o conjunto de teste (a saída do módulo de transformação de aplicação que aplica a seleção de recursos ao conjunto de teste também) ao Modelo de **Pontuação**. O Modelo de **Pontuação** fará as previsões. Pode selecionar a sua porta de saída para ver as previsões e as probabilidades de classe positiva.


    Este gasoduto tem dois módulos de pontuação, o da direita excluiu a coluna de etiquetas antes de fazer a previsão. Isto está preparado para implementar um ponto final em tempo real, porque a entrada do serviço web espera apenas funcionalidades que não sejam etiquetadas. 

1. Adicione um módulo **'Modelo de Avaliação'** e ligue o conjunto de dados pontuado à sua porta de entrada esquerda. Para ver os resultados da avaliação, selecione a porta de saída do módulo **'Avaliar Modelo'** e selecione **Visualize**.

## <a name="results"></a>Resultados

![Avaliar os resultados](media/how-to-designer-sample-classification-predict-income/sample3-evaluate-1225.png)

Nos resultados da avaliação, pode ver-se que as curvas como ROC, Precisão-recall e métricas de confusão. 

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Explore as outras amostras disponíveis para o designer:

- [Amostra 1 - Regressão: Prever o preço de um automóvel](how-to-designer-sample-regression-automobile-price-basic.md)
- [Amostra 2 - Regressão: Comparar algoritmos para previsão de preços do automóvel](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [Amostra 4 - Classificação: Prever o risco de crédito (sensível ao custo)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [Amostra 5 - Classificação: Prever churn](how-to-designer-sample-classification-churn.md)
- [Amostra 6 - Classificação: Prever atrasos nos voos](how-to-designer-sample-classification-flight-delay.md)
- [Amostra 7 - Classificação de Texto: Wikipedia SP 500 Dataset](how-to-designer-sample-text-classification.md)
