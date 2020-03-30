---
title: 'Modelo de clustering de comboio: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a utilizar o módulo modelo de clustering de comboios em Azure Machine Learning para treinar modelos de agrupamento.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/19/2019
ms.openlocfilehash: c29baf3cdda998a2ab78c84f3311b84d37086bcd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477430"
---
# <a name="train-clustering-model"></a>Preparar Modelo de Clustering

Este artigo descreve um módulo em Azure Machine Learning designer (pré-visualização).

Utilize este módulo para formar um modelo de agrupamento.

O módulo pega num modelo de agrupamento não treinado que já configuraste utilizando o módulo [de agrupamento K-Means](k-means-clustering.md) e treina o modelo utilizando um conjunto de dados rotulado ou não rotulado. O módulo cria um modelo treinado que pode utilizar para previsão, como um conjunto de atribuições de cluster para cada caso nos dados de treino.

> [!NOTE]
> Um modelo de agrupamento não pode ser treinado utilizando o módulo [Train Model,](train-model.md) que é o módulo genérico para treinar modelos de aprendizagem automática. Isto porque [o Modelo de Comboio](train-model.md) funciona apenas com algoritmos de aprendizagem supervisionados. Os meios K e outros algoritmos de agrupamento permitem uma aprendizagem sem supervisão, o que significa que o algoritmo pode aprender com dados não rotulados.  
  
## <a name="how-to-use-train-clustering-model"></a>Como usar o modelo de clustering de comboios  

1.  Adicione o módulo Modelo de **Clustering de Comboios** ao seu pipeline no designer. Pode encontrar o módulo em **Módulos de Aprendizagem automática,** na categoria **Comboio.**  
  
2. Adicione o módulo [de agrupamento K-Means,](k-means-clustering.md) ou outro módulo personalizado que cria um modelo de agrupamento compatível, e defina os parâmetros do modelo de agrupamento.  
    
3.  Fixe um conjunto de dados de treino à entrada direita do Modelo de Agrupamento de **Comboios**.
  
5.  No **Conjunto de Colunas,** selecione as colunas do conjunto de dados para utilizar em clusters de construção. Certifique-se de selecionar colunas que façam boas características: por exemplo, evite utilizar IDs ou outras colunas que tenham valores únicos, ou colunas com todos os mesmos valores.

    Se estiver disponível uma etiqueta, pode utilizá-la como recurso ou deixá-la de fora.  
  
6. Selecione a opção, **verifique se o apêndice ou não verifique apenas**o resultado, se pretender obter os dados de treino juntamente com a nova etiqueta de cluster.

    Se desseleccionar esta opção, apenas as atribuições de cluster são de saída. 

7. Submeta o gasoduto ou clique no módulo modelo de **clustering de comboios** e selecione **Executar Selecionado**.  
  
### <a name="results"></a>Resultados

Após o treino ter concluído:

+ Para guardar uma imagem do modelo treinado, selecione o separador **Saídas** no painel direito do módulo **modelo Train.** Selecione o ícone do conjunto de **dados Register** para salvar o modelo como um módulo reutilizável.

+ Para gerar pontuações do modelo, utilize dados [de atribuição para Clusters](assign-data-to-clusters.md).

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 