---
title: 'Treinar modelo de clustering: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo treinar modelo de clustering em Azure Machine Learning para treinar modelos de clustering.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 11/19/2019
ms.openlocfilehash: 6b154cdbf6490abd935156e6d081d2260cfbc578
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76719369"
---
# <a name="train-clustering-model"></a>Preparar Modelo de Clustering

Este artigo descreve um módulo no designer de Azure Machine Learning (versão prévia).

Use este módulo para treinar um modelo de clustering.

O módulo pega num modelo de agrupamento não treinado que já configuraste utilizando o módulo [de agrupamento K-Means](k-means-clustering.md) e treina o modelo utilizando um conjunto de dados rotulado ou não rotulado. O módulo cria um modelo treinado que você pode usar para previsão e um conjunto de atribuições de cluster para cada caso nos dados de treinamento.

> [!NOTE]
> Um modelo de agrupamento não pode ser treinado utilizando o módulo [Train Model,](train-model.md) que é o módulo genérico para treinar modelos de aprendizagem automática. Isto porque [o Modelo de Comboio](train-model.md) funciona apenas com algoritmos de aprendizagem supervisionados. K-means e outros algoritmos de clustering permitem aprendizado não supervisionado, o que significa que o algoritmo pode aprender com dados não rotulados.  
  
## <a name="how-to-use-train-clustering-model"></a>Como usar o modelo treinar clustering  

1.  Adicione o módulo Modelo de **Clustering de Comboios** ao seu pipeline no designer. Pode encontrar o módulo em **Módulos de Aprendizagem automática,** na categoria **Comboio.**  
  
2. Adicione o módulo [de agrupamento K-Means,](k-means-clustering.md) ou outro módulo personalizado que cria um modelo de agrupamento compatível, e defina os parâmetros do modelo de agrupamento.  
    
3.  Fixe um conjunto de dados de treino à entrada direita do Modelo de Agrupamento de **Comboios**.
  
5.  No **Conjunto de Colunas,** selecione as colunas do conjunto de dados para utilizar em clusters de construção. Certifique-se de selecionar colunas que tenham bons recursos: por exemplo, evite usar IDs ou outras colunas que tenham valores exclusivos ou colunas que tenham os mesmos valores.

    Se um rótulo estiver disponível, você poderá usá-lo como um recurso ou deixá-lo fora.  
  
6. Selecione a opção, **verifique se o apêndice ou não verifique apenas**o resultado, se pretender obter os dados de treino juntamente com a nova etiqueta de cluster.

    Se você desmarcar essa opção, somente as atribuições de cluster serão geradas. 

7. Executar o gasoduto ou clicar no módulo Modelo de **Clustering de Comboios** e selecionar **Executar Selecionado**.  
  
### <a name="results"></a>Resultados

Após a conclusão do treinamento:

+ Para guardar uma imagem do modelo treinado, selecione o separador **Saídas** no painel direito do módulo **modelo Train.** Selecione o ícone do conjunto de **dados Register** para salvar o modelo como um módulo reutilizável.

+ Para gerar pontuações do modelo, utilize dados [de atribuição para Clusters](assign-data-to-clusters.md).

## <a name="next-steps"></a>Passos Seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 