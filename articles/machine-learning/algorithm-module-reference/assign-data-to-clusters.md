---
title: 'Atribuir dados ao cluster: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a utilizar o módulo de atribuir dados ao cluster em Azure Machine Learning para obter o modelo de clustering.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/19/2019
ms.openlocfilehash: 207172f10277589af2b22ae2f41b07234a0925b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477719"
---
# <a name="module-assign-data-to-clusters"></a>Módulo: Atribuir dados a clusters

Este artigo descreve como usar o *módulo de atribuir dados a clusters* em azure machine learning designer (pré-visualização). O módulo gera previsões através de um modelo de agrupamento que foi treinado com o algoritmo *de agrupamento k-significa.*

O módulo de atribuir dados aos Clusters devolve um conjunto de dados que contém as atribuições prováveis para cada novo ponto de dados. 

## <a name="how-to-use-assign-data-to-clusters"></a>Como utilizar dados de atribuição a clusters
  
1. No azure machine learning designer, localize um modelo de clustering previamente treinado. Pode criar e treinar um modelo de agrupamento utilizando qualquer um dos seguintes métodos:  
  
    - Configure o algoritmo de agrupamento k-significa utilizando o módulo [de agrupamento K-Means](k-means-clustering.md) e treine o modelo utilizando um conjunto de dados e o módulo modelo de clustering de comboios (este artigo).  
  
    - Também pode adicionar um modelo de clustering treinado existente do grupo **Save Models** no seu espaço de trabalho.

2. Fixe o modelo treinado à porta de entrada esquerda de Dados de **Atribuição a Clusters**.  

3. Anexe um novo conjunto de dados como entrada. 

   Neste conjunto de dados, as etiquetas são opcionais. Geralmente, o agrupamento é um método de aprendizagem não supervisionado. Não se espera que conheça antecipadamente as categorias. No entanto, as colunas de entrada devem ser as mesmas que as colunas utilizadas na formação do modelo de agrupamento, ou ocorre um erro.

    > [!TIP]
    > Para reduzir o número de colunas escritas ao designer a partir das previsões do cluster, utilize [as colunas Select no conjunto](select-columns-in-dataset.md)de dados e selecione um subconjunto das colunas. 
    
4. Deixe o ponto de **verificação do apêndice ou não verifique se só** verifique a caixa selecionada se pretender que os resultados contenham o conjunto de dados de entrada completo, incluindo uma coluna que apresenta os resultados (atribuições de cluster).
  
    Se limpar esta caixa de verificação, apenas os resultados são devolvidos. Esta opção pode ser útil quando cria previsões como parte de um serviço web.
  
5.  Submeta o oleoduto.  
  
### <a name="results"></a>Resultados

+  Para ver os valores no conjunto de dados, clique no módulo à direita e, em seguida, **selecione Visualize**. Ou Selecione o módulo e mude para o separador **Saídas** no painel direito, clique no ícone histograma nas **saídas da Porta** para visualizar o resultado.

