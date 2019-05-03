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
ms.openlocfilehash: b370c6ef5be311ed9c8df2737fa1b01144d61011
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028774"
---
# <a name="assign-data-to-clusters"></a>Atribua os dados para Clusters

Este artigo descreve como utilizar o [dados de atribuir a Clusters](assign-data-to-clusters.md) módulo na interface visual, para gerar predições utilizando um modelo de clustering que foi preparado com o algoritmo de clustering de K-Means.

O módulo devolve um conjunto de dados que contém as atribuições de prováveis para cada ponto de dados nova. 


## <a name="how-to-use-assign-data-to-clusters"></a>Como utilizar os dados de atribuir a Clusters
  
1.  Na visual interface, localize um modelo de clustering preparado anteriormente. Pode criar e formar um modelo de clustering, utilizando qualquer um dos seguintes métodos:  
  
    - Configurar o algoritmo K-means a utilizar o [Clustering de K-Means](k-means-clustering.md) módulo e, em seguida, treinar o modelo utilizando um conjunto de dados e o [treinar o modelo de agrupamento](train-clustering-model.md) módulo.  
  
  
    Também pode adicionar um modelo de clustering preparado existente do **guardado modelos** grupo na sua área de trabalho.

2. Anexar o modelo preparado para a porta de entrada à esquerda dos [dados de atribuir a Clusters](assign-data-to-clusters.md).  

3. Anexe um novo conjunto de dados como entrada. Este conjunto de dados, as etiquetas são opcionais. Em geral, o clustering é um método de aprendizado não supervisionado pelo que não é esperado que saberá categorias com antecedência.

    No entanto, as colunas de entrada tem de ser o mesmo que as colunas que foram utilizadas no treinamento que o modelo de clustering ou um erro ocorre.

    > [!TIP]
    > Para reduzir o número de saída de colunas de previsões de cluster, utilize [Select Columns in Dataset](select-columns-in-dataset.md)e selecionar um subconjunto das colunas. 
    
4. Deixe a opção **Check para acrescentar ou desmarque para apenas resultado** selecionado se pretender que os resultados para conter o dataset de entrada completo, juntamente com uma coluna que indica os resultados (atribuições de cluster).
  
    Se desmarcar esta opção, obtém apenas os resultados. Isso pode ser útil durante a criação de predições como parte de um serviço web.
  
5.  Execute a experimentação.  
  
### <a name="results"></a>Resultados

 
+  Para ver os valores no conjunto de dados, o módulo com o botão direito, selecione **resultar conjuntos de dados**e clique em **Visualize**.

