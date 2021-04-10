---
title: 'Modelo de agrupamento de comboios: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Saiba como utilizar o módulo modelo de clustering de comboios em Azure Machine Learning para treinar modelos de clustering.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/17/2021
ms.openlocfilehash: ea6673a04bf9f5f568c660658e51036f2d2712e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104654735"
---
# <a name="train-clustering-model"></a>Train Clustering Model (Preparar Modelo de Clustering)

Este artigo descreve um módulo no designer de Aprendizagem automática Azure.

Utilize este módulo para treinar um modelo de agrupamento.

O módulo requer um modelo de clustering não treinado que já configurado utilizando o módulo [de Clustering K-Means,](k-means-clustering.md) e treina o modelo usando um conjunto de dados rotulado ou não rotulado. O módulo cria tanto um modelo treinado que pode usar para previsão, como um conjunto de atribuições de cluster para cada caso nos dados de treino.

> [!NOTE]
> Um modelo de agrupamento não pode ser treinado usando o módulo [Train Model,](train-model.md) que é o módulo genérico para modelos de aprendizagem automática de formação. Isto porque [o Train Model](train-model.md) funciona apenas com algoritmos de aprendizagem supervisionados. K-meios e outros algoritmos de clustering permitem uma aprendizagem não supervisionada, o que significa que o algoritmo pode aprender com dados não rotulados.  
  
## <a name="how-to-use-train-clustering-model"></a>Como usar o modelo de clustering do comboio  

1.  Adicione o módulo **modelo de clustering de comboio** ao seu oleoduto no designer. Pode encontrar o módulo em **Módulos de Aprendizagem automática,** na categoria **Comboio.**  
  
2. Adicione o módulo [de clustering K-Means,](k-means-clustering.md) ou outro módulo personalizado que cria um modelo de agrupamento compatível, e desa estale os parâmetros do modelo de agrupamento.  
    
3.  Anexar um conjunto de dados de treino à entrada direita do Modelo de Agrupamento de **Comboios.**
  
5.  No **Conjunto de Colunas**, selecione as colunas do conjunto de dados para utilizar em aglomerados de construção. Certifique-se de selecionar colunas que façam boas funcionalidades: por exemplo, evite usar IDs ou outras colunas com valores únicos, ou colunas que tenham todos os mesmos valores.

    Se houver uma etiqueta disponível, pode usá-la como recurso ou deixá-la de fora.  
  
6. Selecione a opção, Verifique apenas o **apêndice ou desmarcar o resultado**, se pretender descodificar os dados de formação juntamente com a nova etiqueta do cluster.

    Se desmarcar esta opção, apenas as atribuições de cluster são de saída. 

7. Envie o pipeline ou clique no módulo **Modelo de Agrupamento de Comboios** e selecione Run **Selected**.  
  
### <a name="results"></a>Resultados

Após o treino ter terminado:

+ Para guardar uma imagem do modelo treinado, selecione o **separador Saídas** no painel direito do módulo **modelo Train.** Selecione o ícone **do conjunto de dados registar** para guardar o modelo como um módulo reutilizável.

+ Para gerar pontuações a partir do modelo, utilize [atribuir dados a clusters](assign-data-to-clusters.md).

> [!NOTE]
> Se precisar de implementar o modelo treinado no designer, certifique-se de que a Atribuição de [Dados aos Clusters](assign-data-to-clusters.md) em vez do Modelo de **Pontuação** está ligada à entrada do módulo de saída de [serviço web](web-service-input-output.md) no pipeline de inferência.

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 