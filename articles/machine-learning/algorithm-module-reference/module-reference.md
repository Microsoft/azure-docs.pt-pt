---
title: Referência de algoritmos e módulos
titleSuffix: Azure Machine Learning service
description: Saiba mais sobre os módulos disponíveis na interface visual do Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 6602eb4bacdc3b6382c1b6873a465cdfc0632693
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029344"
---
# <a name="algorithm--module-reference-overview"></a>Descrição geral de referência de algoritmos e módulos

Este conteúdo de referência fornece a formação técnica em cada um de módulos disponíveis na interface do visual (pré-visualização) do serviço Azure Machine Learning e algoritmos de aprendizagem automática. 

Cada módulo representa um conjunto de código que possa executar de forma independente e efetuar uma machine learning tarefa, tendo em conta as entradas necessárias. Um módulo pode conter um algoritmo específico, ou executar uma tarefa que é importante do Machine learning, como substituição de valor em falta ou análise estatística. 

> [!TIP]
> Em qualquer experiência na interface do visual, pode obter informações sobre um módulo específico. Selecione o módulo, em seguida, selecione o **mais ajuda** ligação na **ajuda rápida** painel.

Módulos são organizados por funcionalidade:

**Conversões de formato de dados**

  + [Converter para CSV ](convert-to-csv.md)

**Dados de entrada e saída módulos** fazem o trabalho de mover dados de origens na cloud na sua experimentação. Pode escrever seus resultados ou dados intermediários para armazenamento do Azure, base de dados SQL ou Hive, durante a execução de uma experimentação, ou usar o armazenamento na cloud para trocar dados entre experimentações.  

  + [Importar dados](import-data.md)

  + [Exportar dados](export-data.md)

  + [Introduzir dados manualmente](enter-data-manually.md)


**Módulos de transformação de dados** oferecem suporte a operações nos dados que são exclusivas ao machine learning, como normalizar ou compartimentação de dados, a seleção de funcionalidades e a redução de dimensionalidade.

  + [Selecionar colunas no conjunto de dados](select-columns-in-dataset.md)

  + [Editar metadados](edit-metadata.md)

  + [Limpar dados em falta](clean-missing-data.md)

  + [Adicionar colunas](add-columns.md)

  + [Adicionar linhas](add-rows.md)

  + [Remover linhas duplicadas](remove-duplicate-rows.md)

  + [Dividir os dados](split-data.md)

  + [Normalizar dados](normalize-data.md)

  + [Partição e amostras](partition-and-sample.md)


**Do Machine learning algoritmos** como clustering, máquina de vetor com suporte ou redes neurais, estão disponíveis nos módulos individuais que lhe permite personalizar a tarefa de aprendizagem automática com parâmetros apropriados.  
  + [Modelo de pontuação](score-model.md)

  + [Atribua os dados para Clusters ](assign-data-to-clusters.md)

  + [Preparar modelo](train-model.md)

  + [Preparar o modelo de Clustering](train-clustering-model.md)

  + [Avaliar modelo](evaluate-model.md)

  + [Aplicam-se de transformação](apply-transformation.md)

  + [Regressão linear](linear-regression.md)

  + [Regressão de rede neural](neural-network-regression.md)

  + [Regressão de floresta de decisão](decision-forest-regression.md)

  + [Regressão da árvore de decisões elevada](boosted-decision-tree-regression.md)

  + [Árvore de decisões elevada de duas classes](two-class-boosted-decision-tree.md)

  + [Regressão logística de duas classes](two-class-logistic-regression.md)

  + [Regressão logística várias classes](multiclass-logistic-regression.md)

  + [Várias classes rede Neural](multiclass-neural-network.md)

  + [Floresta de decisão de várias classes](multiclass-decision-forest.md)

  + [Perceptron uma média de duas classes](two-class-averaged-perceptron.md)

  + [Floresta de decisão de duas classes](two-class-decision-forest.md)

  + [Duas classes rede Neural](two-class-neural-network.md)

  + [Máquina de Vetor com suporte de duas classes](two-class-support-vector-machine.md)
  
  + [Clustering de K-Means](k-means-clustering.md)


**Módulo de Python** torna mais fácil executar uma função personalizada. Escrever o código e incorporá-lo num módulo, para integrar o Python com um serviço de experimentação.
  + [Executar o Script de Python](execute-python-script.md)

  + [Criar modelo de Python](create-python-model.md)


