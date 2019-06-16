---
title: 'Preparar o modelo de Clustering: Referência do módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como utilizar o módulo de modelo de Clustering de formação no serviço Azure Machine Learning para criar modelos de clusters.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 41cdec1d7f1c3932b17da6f9b1de518071f3f542
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65028084"
---
# <a name="train-clustering-model"></a>Preparar Modelo de Clustering

Este artigo descreve um módulo da interface visual (pré-visualização) para o serviço Azure Machine Learning.

Utilize este módulo para preparar um modelo de clustering.

O módulo de assume um modelo de clustering destreinado que já tenha configurado com o [Clustering de K-Means](k-means-clustering.md) módulo e prepara o modelo utilizando um conjunto de dados etiquetado ou sem etiqueta. O módulo cria tanto um modelo preparado, que pode utilizar para previsão e um conjunto de atribuições de cluster para cada caso nos dados de treinamento.

> [!NOTE]
> Ser um não é possível clustering do modelo preparado com o [Train Model](train-model.md) módulo, que é o módulo genérico para modelos de aprendizagem automática de formação. Isso ocorre porque [Train Model](train-model.md) funciona apenas com supervisionado algoritmos de aprendizagem. K-means e outros algoritmos de clustering permitem aprendizado não supervisionado, o que significa que o algoritmo de aprender com os dados sem etiqueta.  
  
## <a name="how-to-use-train-clustering-model"></a>Como utilizar o modelo de agrupamento Train  
  
1.  Adicionar a **Train Clustering Model** módulo à sua experimentação no Studio. Pode encontrar o módulo a ser **módulos do Machine Learning**, na **Train** categoria.  
  
2. Adicionar a [Clustering de K-Means](k-means-clustering.md) módulo ou outro módulo personalizado que cria um clustering compatível modelar e definir os parâmetros do modelo de clustering.  
    
3.  Anexar um conjunto de dados de formação à entrada da direita de **Train Clustering Model**.
  
5.  Na **coluna de conjunto**, selecione as colunas do conjunto de dados para utilizar na criação de clusters. Certifique-se de que selecionar colunas que fazem com boa funcionalidades: por exemplo, evite utilizar IDs ou outras colunas que têm valores exclusivos ou colunas que têm todos os mesmos valores.

    Se uma etiqueta estiver disponível, pode usá-lo como um recurso ou deixar isso de fora.  
  
6. Selecione a opção **Check para acrescentar ou desmarque para apenas resultado**, caso queira produzir os dados de treinamento em conjunto com a nova etiqueta de cluster.

    Se desmarcar esta opção, apenas as atribuições de cluster são de saída. 

7. Execute a experimentação, ou clique nas **Train Clustering Model** módulo e selecione **executar seleção**.  
  
### <a name="results"></a>Resultados

Após a conclusão da formação:


+  Para ver os valores no conjunto de dados, o módulo com o botão direito, selecione **resultar conjuntos de dados**e clique em **Visualize**.

+ Para guardar o modelo preparado para reutilização posterior, o módulo com o botão direito, selecione **modelo Trained**e clique em **guardar como modelo preparado**.

+ Para gerar as pontuações do modelo, utilize [dados de atribuir a Clusters](assign-data-to-clusters.md).



## <a name="next-steps"></a>Passos Seguintes

Consulte a [conjunto de módulos disponíveis](module-reference.md) ao serviço Azure Machine Learning. 