---
title: 'Modelo de deteção de anomalias de comboio: referência do módulo'
titleSuffix: Azure Machine Learning
description: Saiba como utilizar o módulo modelo de deteção de anomalias de comboio para criar um modelo de deteção de anomalias treinado.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 73e8f0de9f879498acb71e97fa47c81b3447252d
ms.sourcegitcommit: 4ac596f284a239a9b3d8ed42f89ed546290f4128
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/12/2020
ms.locfileid: "84749619"
---
# <a name="train-anomaly-detection-model-module"></a>Módulo de modelo de deteção de anomalias de comboio

Este artigo descreve como usar o módulo modelo de deteção de anomalias de comboio no designer de aprendizagem automática Azure (pré-visualização) para criar um modelo de deteção de anomalias treinado.

O módulo toma como entrada um conjunto de parâmetros para um modelo de deteção de anomalias e um conjunto de dados não rotulado. Devolve um modelo de deteção de anomalias treinado, juntamente com um conjunto de rótulos para os dados de treino.  

Para obter mais informações sobre os algoritmos de deteção de anomalias fornecidos no designer, consulte [deteção de anomalias baseadas em PCA.](pca-based-anomaly-detection.md)  

## <a name="how-to-configure-train-anomaly-detection-model"></a>Como configurar o modelo de deteção de anomalias de comboio 

1.  Adicione o módulo **modelo de deteção de anomalias** de comboio ao seu oleoduto no designer. Pode encontrar este módulo na categoria **deteção de anomalias.**

2. Ligue um dos módulos concebidos para a deteção de anomalias, como [a deteção de anomalias baseadas em PCA.](pca-based-anomaly-detection.md)

    Outros tipos de modelos não são suportados. Quando executar o oleoduto, terá o erro "Todos os modelos devem ter o mesmo tipo de aprendiz."  

3.  Configure o módulo de deteção de anomalias escolhendo a coluna do rótulo e definindo outros parâmetros específicos do algoritmo.  

4.  Anexar um conjunto de dados de treino à entrada do lado direito do Modelo de Deteção de **Anomalias**de Comboio .  

5.  Envie o oleoduto.  

## <a name="results"></a>Resultados

Após o treino estar completo:

+ Para visualizar os parâmetros do modelo, clique com o botão direito no módulo e **selecione Visualize**. 

+ Para criar previsões, utilize o módulo ['Modelo de Pontuação'](score-model.md) com novos dados de entrada.

+ Para guardar uma imagem do modelo treinado, selecione o módulo. Em seguida, selecione o ícone **do conjunto de dados 'Registar'** no **separador Saídas+Logs** no painel direito.   

 
## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 

Consulte [exceções e códigos de erro para o designer (pré-visualização)](designer-error-codes.md) para obter uma lista de erros específicos dos módulos do designer.
'