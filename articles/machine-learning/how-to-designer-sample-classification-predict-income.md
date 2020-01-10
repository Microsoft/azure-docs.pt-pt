---
title: 'Designer: classificar, exemplo de renda de previsão'
titleSuffix: Azure Machine Learning
description: Siga este exemplo criar um classificador sem código para prever a renda com o designer de Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.date: 12/25/2019
ms.openlocfilehash: 227bf490d1ac264bb54ff3d1ecf0ccc2b3ef2e0e
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75763557"
---
# <a name="build-a-classifier--use-feature-selection-to-predict-income-with-azure-machine-learning-designer"></a>Criar um classificador & usar a seleção de recursos para prever a renda com o designer de Azure Machine Learning

**Designer (visualização) exemplo 3**

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Saiba como criar um classificador de aprendizado de máquina sem escrever uma única linha de código usando o designer (versão prévia). Este exemplo treina uma **árvore de decisão aumentada de duas classes** para prever censo de renda de adulto (> = 50 mil ou < = 50 mil).

Porque a pergunta está respondendo "Qual é?" Isso é chamado de problema de classificação. No entanto, você pode aplicar o mesmo processo fundamental para lidar com qualquer tipo de problema de aprendizado de máquina – regressão, classificação, clustering e assim por diante.

Este é o grafo de pipeline final para este exemplo:

![Grafo do pipeline](./media/how-to-designer-sample-classification-predict-income/overall-graph.png)

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [aml-ui-prereq](../../includes/aml-ui-prereq.md)]

4. Clique no exemplo 3 para abri-lo.



## <a name="data"></a>Dados

O conjunto de conteúdo contém 14 recursos e uma coluna de rótulo. Há vários tipos de recursos, incluindo numéricos e categóricos. O diagrama a seguir mostra um trecho do conjunto de dados: ![data](media/how-to-designer-sample-classification-predict-income/sample3-dataset-1225.png)



## <a name="pipeline-summary"></a>Resumo do pipeline

Siga estas etapas para criar o pipeline:

1. Arraste o módulo conjunto de conteúdo binário censo de renda de adulto para a tela de pipeline.
1. Adicione um módulo **dividir dados** para criar os conjuntos de treinamento e teste. Defina a fração de linhas no primeiro conjunto de registros de saída como 0,7. Essa configuração especifica que 70% dos dados serão gerados para a porta esquerda do módulo e a porta restante para a direita. Usamos o conjunto de espaço da esquerda para treinamento e o correto para teste.
1. Adicione o módulo **seleção de recursos baseada em filtro** para selecionar 5 recursos por PearsonCorreclation. 
1. Adicione um módulo de **árvore de decisão aumentada de duas classes** para inicializar um classificador de árvore de decisão aumentada.
1. Adicione um módulo **modelo de treinamento** . Conecte o classificador da etapa anterior à porta de entrada à esquerda do **modelo de treinamento**. Conecte o conjunto de texto filtrado do módulo seleção de recursos com base em filtro como conjunto de módulos de treinamento.  O **modelo de treinamento** treinará o classificador.
1. Adicione a transformação selecionar colunas e aplique o módulo transformação para aplicar a mesma transformação (seleção de recursos com base em filtro) para testar o conjunto de testes.
![aplicar-transformação](./media/how-to-designer-sample-classification-predict-income/transformation.png)
1. Adicione o módulo **modelo de Pontuação** e conecte o módulo **modelo de treinamento** a ele. Em seguida, adicione o conjunto de teste (a saída do módulo aplicar transformação que aplica a seleção de recursos também ao conjunto de teste) ao **modelo de Pontuação**. O **modelo de Pontuação** fará as previsões. Você pode selecionar sua porta de saída para ver as previsões e as probabilidades de classe positivas.


    Esse pipeline tem dois módulos de pontuação, aquele à direita excluiu a coluna de rótulo antes de fazer a previsão. Isso está preparado para implantar um ponto de extremidade em tempo real, pois a entrada do serviço Web esperará apenas recursos que não são rotulados. 

1. Adicione um módulo **avaliar modelo** e conecte o conjunto de dados pontuado à sua porta de entrada à esquerda. Para ver os resultados da avaliação, selecione a porta de saída do módulo **avaliar modelo** e selecione **Visualizar**.

## <a name="results"></a>Resultados

![Avaliar os resultados](media/how-to-designer-sample-classification-predict-income/sample3-evaluate-1225.png)

Nos resultados da avaliação, você pode ver que as curvas, como o ROC, a recuperação de precisão e as métricas de confusão. 

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Explore os outros exemplos disponíveis para o designer:

- [Exemplo 1-regressão: prever o preço de um automóvel](how-to-designer-sample-regression-automobile-price-basic.md)
- [Exemplo 2-regressão: comparar algoritmos para previsão de preço de automóvel](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [Exemplo 4-classificação: prever o risco de crédito (sensível ao custo)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [Exemplo 5-classificação: Previsão de rotatividade](how-to-designer-sample-classification-churn.md)
- [Exemplo 6-classificação: prever atrasos de voo](how-to-designer-sample-classification-flight-delay.md)
- [Exemplo de classificação de texto 7: conjunto de teste da Wikipédia SP 500](how-to-designer-sample-text-classification.md)
