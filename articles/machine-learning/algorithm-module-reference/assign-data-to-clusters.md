---
title: 'Atribuir dados ao cluster: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo atribuir dados ao cluster em Azure Machine Learning para pontuar o modelo de clustering.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/19/2019
ms.openlocfilehash: eff480d6763ae4bd277e6781663c559cc7c9169e
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152453"
---
# <a name="module-assign-data-to-clusters"></a>Módulo: atribuir dados a clusters

Este artigo descreve como usar o *módulo de atribuir dados a clusters* em azure machine learning designer (pré-visualização). O módulo gera previsões através de um modelo de agrupamento que foi treinado com o algoritmo *de agrupamento k-significa.*

O módulo atribuir dados a clusters retorna um DataSet que contém as atribuições prováveis para cada novo ponto de dados. 

## <a name="how-to-use-assign-data-to-clusters"></a>Como usar atribuir dados a clusters
  
1. No Azure Machine Learning designer, localize um modelo de clustering treinado anteriormente. Você pode criar e treinar um modelo de clustering usando um dos seguintes métodos:  
  
    - Configure o algoritmo de agrupamento k-significa utilizando o módulo [de agrupamento K-Means](k-means-clustering.md) e treine o modelo utilizando um conjunto de dados e o módulo modelo de clustering de comboios (este artigo).  
  
    - Também pode adicionar um modelo de clustering treinado existente do grupo **Save Models** no seu espaço de trabalho.

2. Fixe o modelo treinado à porta de entrada esquerda de Dados de **Atribuição a Clusters**.  

3. Anexe um novo conjunto de dados como entrada. 

   Nesse conjunto de DataSet, os rótulos são opcionais. Em geral, o clustering é um método de aprendizado não supervisionado. Você não deve conhecer as categorias com antecedência. No entanto, as colunas de entrada devem ser iguais às colunas usadas no treinamento do modelo de clustering ou ocorre um erro.

    > [!TIP]
    > Para reduzir o número de colunas escritas ao designer a partir das previsões do cluster, utilize [as colunas Select no conjunto](select-columns-in-dataset.md)de dados e selecione um subconjunto das colunas. 
    
4. Deixe o ponto de **verificação do apêndice ou não verifique se só** verifique a caixa selecionada se pretender que os resultados contenham o conjunto de dados de entrada completo, incluindo uma coluna que apresenta os resultados (atribuições de cluster).
  
    Se você desmarcar essa caixa de seleção, somente os resultados serão retornados. Essa opção pode ser útil quando você cria previsões como parte de um serviço Web.
  
5.  Executar o pipeline.  
  
### <a name="results"></a>Resultados

+  Para ver os valores no conjunto de dados, clique no módulo à direita e, em seguida, **selecione Visualize**. Ou Selecione o módulo e mude para o separador **Saídas** no painel direito, clique no ícone histograma nas **saídas da Porta** para visualizar o resultado.

