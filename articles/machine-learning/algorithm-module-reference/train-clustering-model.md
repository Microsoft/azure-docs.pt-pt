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

O módulo usa um modelo de clustering não treinado que você já configurou usando o módulo de [clustering K-](k-means-clustering.md) means e treina o modelo usando um conjunto de dados rotulado ou sem rótulo. O módulo cria um modelo treinado que você pode usar para previsão e um conjunto de atribuições de cluster para cada caso nos dados de treinamento.

> [!NOTE]
> Um modelo de agrupamento não pode ser treinado utilizando o módulo [Train Model,](train-model.md) que é o módulo genérico para treinar modelos de aprendizagem automática. Isso ocorre porque o [modelo de treinamento](train-model.md) funciona apenas com algoritmos de aprendizado supervisionados. K-means e outros algoritmos de clustering permitem aprendizado não supervisionado, o que significa que o algoritmo pode aprender com dados não rotulados.  
  
## <a name="how-to-use-train-clustering-model"></a>Como usar o modelo treinar clustering  

1.  Adicione o módulo **treinar modelo de clustering** ao seu pipeline no designer. Você pode encontrar o módulo em **módulos Machine Learning**, na categoria **treinar** .  
  
2. Adicione o módulo de [clustering K-](k-means-clustering.md) means ou outro módulo personalizado que cria um modelo de clustering compatível e defina os parâmetros do modelo de clustering.  
    
3.  Anexe um conjunto de dados de treinamento à entrada à direita do **modelo Train clustering**.
  
5.  Em **conjunto de colunas**, selecione as colunas do conjunto de um para usar na criação de clusters. Certifique-se de selecionar colunas que tenham bons recursos: por exemplo, evite usar IDs ou outras colunas que tenham valores exclusivos ou colunas que tenham os mesmos valores.

    Se um rótulo estiver disponível, você poderá usá-lo como um recurso ou deixá-lo fora.  
  
6. Selecione a opção, **marque para acrescentar ou desmarque somente o resultado**, se desejar gerar os dados de treinamento junto com o novo rótulo de cluster.

    Se você desmarcar essa opção, somente as atribuições de cluster serão geradas. 

7. Execute o pipeline ou clique no módulo **treinar modelo de clustering** e selecione **executar selecionado**.  
  
### <a name="results"></a>Resultados

Após a conclusão do treinamento:

+ Para salvar um instantâneo do modelo treinado, selecione a guia **saídas** no painel direito do módulo modelo de **treinamento** . Selecione o ícone **registrar conjunto de registros** para salvar o modelo como um módulo reutilizável.

+ Para gerar pontuações do modelo, use [atribuir dados a clusters](assign-data-to-clusters.md).

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 