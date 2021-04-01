---
title: 'Atribuir dados ao Cluster: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a utilizar o módulo De Atribuir dados ao cluster em Azure Machine Learning para marcar o modelo de agrupamento.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/19/2019
ms.openlocfilehash: e618395a2a4a11b4afb311d612cf5d0e27503dc6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "90898862"
---
# <a name="module-assign-data-to-clusters"></a>Módulo: Atribuir dados a clusters

Este artigo descreve como utilizar o módulo *Descreva dados a clusters* no designer de aprendizagem automática Azure. O módulo gera previsões através de um modelo de agrupamento que foi treinado com o algoritmo *de clustering K-significa.*

O módulo Dedesemesto dados a Clusters devolve um conjunto de dados que contém as atribuições prováveis para cada novo ponto de dados. 

## <a name="how-to-use-assign-data-to-clusters"></a>Como utilizar atribuir dados a clusters
  
1. No designer de Machine Learning Azure, localize um modelo de agrupamento previamente treinado. Pode criar e formar um modelo de agrupamento utilizando qualquer um dos seguintes métodos:  
  
    - Configure o algoritmo de agrupamento de meios K utilizando o módulo [de clustering K-Means](k-means-clustering.md) e treine o modelo utilizando um conjunto de dados e o módulo modelo de clustering de comboios (este artigo).  
  
    - Também pode adicionar um modelo de agrupamento treinado existente do grupo **Modelos Guardados** no seu espaço de trabalho.

2. Fixe o modelo treinado à porta de entrada esquerda da Atribuição de **Dados aos Clusters**.  

3. Anexar um novo conjunto de dados como entrada. 

   Neste conjunto de dados, as etiquetas são opcionais. Geralmente, o agrupamento é um método de aprendizagem não supervisionado. Não se espera que conheça as categorias com antecedência. No entanto, as colunas de entrada devem ser as mesmas que as colunas utilizadas no treino do modelo de agrupamento, ou ocorre um erro.

    > [!TIP]
    > Para reduzir o número de colunas escritas ao designer a partir das previsões do cluster, utilize [colunas Selecione no conjunto](select-columns-in-dataset.md)de dados e selecione um subconjunto das colunas. 
    
4. Deixe o **cheque para apêndice ou desmarcar o resultado apenas** selecione a caixa de verificação selecionada se quiser que os resultados contenham o conjunto completo de dados de entrada, incluindo uma coluna que apresente os resultados (atribuições de cluster).
  
    Se limpar esta caixa de verificação, apenas os resultados são devolvidos. Esta opção pode ser útil quando cria previsões como parte de um serviço web.
  
5.  Envie o oleoduto.  
  
### <a name="results"></a>Resultados

+  Para ver os valores no conjunto de dados, clique com o botão direito no módulo e, em seguida, **selecione Visualize**. Ou selecione o módulo e mude para o **separador Saídas** no painel direito, clique no ícone histograma nas **saídas** da Porta para visualizar o resultado.

