---
title: 'Modelo de deteção de anomalias do comboio: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a utilizar o módulo modelo de deteção de anomalias do comboio para criar um modelo de deteção de anomalias treinado.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 55c5b89eb23581543d57187ee4d7899d97eafe66
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2020
ms.locfileid: "77925959"
---
# <a name="train-anomaly-detection-model"></a>Modelo de deteção de anomalias do comboio

Este artigo descreve como usar o módulo modelo de deteção de **anomalias** do comboio em Azure Machine Learning designer (pré-visualização) para criar um modelo de deteção de anomalias treinado.

O módulo toma como entrada um conjunto de parâmetros de modelo para o modelo de deteção de anomalias e um conjunto de dados não rotulado. Devolve um modelo de deteção de anomalias treinado, juntamente com um conjunto de etiquetas para os dados de treino.  

Para obter mais informações sobre os algoritmos de deteção de anomalias fornecidos no designer, consulte estes tópicos: 

+ [Deteção de anomalias baseada em PCA](pca-based-anomaly-detection.md)  

## <a name="how-to-configure-train-anomaly-detection-model"></a>Como configurar o modelo de deteção de anomalias do comboio 

1.  Adicione o módulo modelo de deteção de **anomalias** do comboio ao seu pipeline no designer. Pode encontrar este módulo na categoria **Deteção** de Anomalias.

2. Ligue um dos módulos concebidos para deteção de anomalias, como [a Deteção de Anomalias baseada em PCA](pca-based-anomaly-detection.md)

    Outros tipos de modelos não são suportados; ao executar o gasoduto terá o erro: Todos os modelos devem ter o mesmo tipo de aprendizagem.  

3.  Configure o módulo de deteção de anomalias, escolhendo a coluna de etiquetas e definindo outros parâmetros específicos do algoritmo.  

4.  Fixe um conjunto de dados de treino à entrada direita do Modelo de Deteção de **Anomalias**do Comboio .  

5.  Executar o pipeline.  

## <a name="results"></a>Resultados

Após o treino estar completo:

+ Para ver os parâmetros do modelo, clique no módulo e **selecione Visualize**. 

+ Para criar previsões, utilize [o Score Model](score-model.md) com novos dados de entrada.

+ Para guardar uma imagem instantânea do modelo treinado, selecione o módulo e clique no ícone do conjunto de **dados Do Registo** sob o separador **Outputs+logs** no painel certo.   

 
## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 

Consulte [exceções e códigos de erro para o designer (pré-visualização)](designer-error-codes.md) para obter uma lista de erros específicos dos módulos de design.
