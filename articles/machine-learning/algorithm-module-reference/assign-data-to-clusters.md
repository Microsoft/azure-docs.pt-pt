---
title: 'Atribua os dados para o Cluster: Referência do módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como utilizar os dados de atribuir ao módulo de Cluster no serviço Azure Machine Learning para pontuar o modelo de clustering.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 1c2d2a02ecfb617551dd9174b87f363d57b151a8
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/09/2019
ms.locfileid: "65467205"
---
# <a name="module-assign-data-to-clusters"></a>Módulo: Atribua os dados para Clusters

Este artigo descreve como utilizar o *dados de atribuir a Clusters* módulo na interface visual do Azure Machine Learning. O módulo gera previsões de indisponibilidade por meio de um modelo de clustering que foi preparado com o *clustering de K-means* algoritmo.

Os dados de atribuir ao módulo de Clusters devolve um conjunto de dados que contém as atribuições de prováveis para cada ponto de dados nova. 


## <a name="how-to-use-assign-data-to-clusters"></a>Como utilizar os dados de atribuir a Clusters
  
1. Na interface visual do Azure Machine Learning, localize um modelo de clustering preparado anteriormente. Pode criar e formar um modelo de clustering, utilizando qualquer um dos seguintes métodos:  
  
    - Configurar o algoritmo de clustering de K-means utilizando o [Clustering de K-Means](k-means-clustering.md) módulo e preparar o modelo com um conjunto de dados e o módulo preparar modelo de agrupamento (Este artigo).  
  
    - Também pode adicionar um modelo de clustering preparado existente do **guardado modelos** grupo na sua área de trabalho.

2. Anexar o modelo preparado para a porta de entrada à esquerda dos **dados de atribuir a Clusters**.  

3. Anexe um novo conjunto de dados como entrada. 

   Este conjunto de dados, as etiquetas são opcionais. Em geral, o clustering é um método de aprendizado não supervisionado. Não espera souber de antemão as categorias. No entanto, as colunas de entrada tem de ser o mesmo que as colunas que foram utilizadas no treinamento que o modelo de clustering ou um erro ocorre.

    > [!TIP]
    > Para reduzir o número de colunas que são escritos para a interface das previsões de cluster, utilize [selecionar colunas no conjunto de dados](select-columns-in-dataset.md)e selecionar um subconjunto das colunas. 
    
4. Deixe o **Check para acrescentar ou desmarque para apenas resultado** caixa de verificação selecionada se pretender que os resultados para conter o conjunto de dados entrado completo, incluindo uma coluna que apresenta os resultados (atribuições de cluster).
  
    Se desmarcar esta caixa de verificação, apenas os resultados são devolvidos. Esta opção poderá ser útil quando cria previsões como parte de um serviço web.
  
5.  Execute a experimentação.  
  
### <a name="results"></a>Resultados

+  Para ver os valores no conjunto de dados, o módulo com o botão direito, selecione **resultar conjuntos de dados**e, em seguida, selecione **Visualize**.

