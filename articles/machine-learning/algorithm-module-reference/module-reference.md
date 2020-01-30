---
title: Referência de algoritmos e módulos
description: Saiba mais sobre os módulos disponíveis no designer de Azure Machine Learning (versão prévia)
titleSuffix: Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: peterclu
ms.author: peterlu
ms.date: 12/17/2019
ms.openlocfilehash: 7f2d282541e8a5a3752a0784a3f00a48ad2fb303
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2020
ms.locfileid: "76769166"
---
# <a name="algorithm--module-reference-for-azure-machine-learning-designer"></a>Referência de módulo de & de algoritmo para o designer de Azure Machine Learning

Esse conteúdo de referência fornece a experiência técnica em cada um dos módulos e algoritmos de aprendizado de máquina disponíveis no designer de Azure Machine Learning (versão prévia).

Cada módulo representa um conjunto de códigos que podem ser executados de forma independente e executar uma tarefa de aprendizado de máquina, dadas as entradas necessárias. Um módulo pode conter um algoritmo específico ou executar uma tarefa que seja importante no aprendizado de máquina, como a substituição de valor ausente ou análise estatística.

Para ajuda na escolha de algoritmos, consulte 
* [Como selecionar algoritmos](../how-to-select-algorithms.md)
* [Folha de batota do algoritmo de aprendizagem de máquina seletiva Azure](../algorithm-cheat-sheet.md)

> [!TIP]
> Em qualquer pipeline no designer, você pode obter informações sobre um módulo específico. Selecione o módulo e, em seguida, selecione o link **mais ajuda** no painel **ajuda rápida** .

## <a name="data-preparation-modules"></a>Módulos de preparação de dados


| Funcionalidade | Descrição | Módulo |
| --- |--- | --- |
| Entrada e saída de dados | Mova dados de fontes de nuvem para seu pipeline. Grave os resultados ou os dados intermediários no armazenamento do Azure, em um banco de dados SQL ou Hive, durante a execução de um pipeline, ou use o armazenamento em nuvem do para o Exchange data entre pipelines.  | [Inserir dados manualmente](enter-data-manually.md) <br/> [Exportar dados](export-data.md) <br/> [Importar dados](import-data.md) |
| Transformação de dados | Operações em dados que são exclusivas do aprendizado de máquina, como normalização ou compartimentalização de dados, redução de dimensionalidade e conversão de dados entre vários formatos de arquivo.| [Adicionar colunas](add-columns.md) <br/> [Adicionar Linhas](add-rows.md) <br/> [Aplicar operação matemática](apply-math-operation.md) <br/> [Aplicar transformação SQL](apply-sql-transformation.md) <br/> [Limpar dados ausentes](clean-missing-data.md) <br/> [Valores de clipe](clip-values.md) <br/> [Converter em CSV](convert-to-csv.md) <br/> [Converter em conjunto de um](convert-to-dataset.md) <br/> [Editar metadados](edit-metadata.md) <br/> [Dados de junção](join-data.md) <br/> [Normalizar dados](normalize-data.md) <br/> [Partição e exemplo](partition-and-sample.md)  <br/> [Remover linhas duplicadas](remove-duplicate-rows.md) <br/> [SMOTE](smote.md) <br/> [Selecionar transformação de colunas](select-columns-transform.md) <br/> [Selecionar colunas no conjunto de linhas](select-columns-in-dataset.md) <br/> [Dividir dados](split-data.md) |
| Seleção de Recursos | Selecione um subconjunto de recursos relevantes e úteis a serem usados na criação de um modelo analítico. | [Seleção de recursos baseada em filtro](filter-based-feature-selection.md) <br/> [Importância do recurso de permuta](permutation-feature-importance.md) |
| Funções Estatísticas | Forneça uma ampla variedade de métodos estatísticos relacionados à ciência de dados. | [Resumir dados](summarize-data.md)|

## <a name="machine-learning-algorithms"></a>Algoritmos de machine learning

| Funcionalidade | Descrição | Módulo |
| --- |--- | --- |
| Regressão | Prever um valor. | [Regressão da árvore de decisão aumentada](boosted-decision-tree-regression.md) <br/> [Regressão de floresta de decisão](decision-forest-regression.md) <br/> [Regressão linear](linear-regression.md)  <br/> [Regressão de rede neural](neural-network-regression.md)  <br/> |
| Clustering | Agrupar dados juntos.| [Clustering de K-means](k-means-clustering.md)
| Classificação | Prever uma classe.  Escolha entre os algoritmos binário (duas classes) ou multiclasse.| [Árvore de decisão aumentada multiclasse](multiclass-boosted-decision-tree.md) <br/> [Floresta de decisão multiclasse](multiclass-decision-forest.md) <br/> [Regressão logística multiclasse](multiclass-logistic-regression.md)  <br/> [Rede neural multiclasse](multiclass-neural-network.md) <br/> [Um vs. todas as multiclasses](one-vs-all-multiclass.md) <br/> [Perceptron média de duas classes](two-class-averaged-perceptron.md) <br/>  [Árvore de decisão aumentada de duas classes](two-class-boosted-decision-tree.md)  <br/> [Floresta de decisão de duas classes](two-class-decision-forest.md) <br/>  [Regressão logística de duas classes](two-class-logistic-regression.md) <br/> [Rede neural de duas classes](two-class-neural-network.md) <br/> [Máquina de vetor de suporte de duas classes](two-class-support-vector-machine.md) | 

## <a name="modules-for-building-and-evaluating-models"></a>Módulos para construção e avaliação de modelos

| Funcionalidade | Descrição | Módulo |
| --- |--- | --- |
| Preparação de modelos | Execute os dados por meio do algoritmo. |  [Treinar modelo de clustering](train-clustering-model.md) <br/> [Modelo de treinamento](train-model.md)  <br/> [Ajustar hiperparâmetros de modelo](tune-model-hyperparameters.md) |
| Pontuação e avaliação do modelo | Meça a precisão do modelo treinado. | [Aplicar transformação](apply-transformation.md) <br/> [Atribuir dados a clusters](assign-data-to-clusters.md) <br/> [Modelo de validação cruzada](cross-validate-model.md) <br/> [Avaliar modelo](evaluate-model.md) <br/> [Modelo de Pontuação](score-model.md) |
| Linguagem Python | Escreva o código e incorpore-o em um módulo para integrar o Python ao seu pipeline. | [Criar modelo Python](create-python-model.md) <br/> [Executar script Python](execute-python-script.md) |
| Linguagem R | Escreva o código e incorpore-o em um módulo para integrar o R ao seu pipeline. | [Executar script R](execute-r-script.md) |
| Análise de Texto | Forneça ferramentas computacionais especializadas para trabalhar com texto estruturado e não estruturado. | [Extrair N recursos de grama do texto](extract-n-gram-features-from-text.md) <br/> [Hash de recurso](feature-hashing.md) <br/> [Pré-processar texto](preprocess-text.md) |
| Recomendação | Crie modelos de recomendação. | [Avaliar Recomendador](evaluate-recommender.md) <br/> [Pontuar Recomendador SVD](score-svd-recommender.md) <br/> [Treinar Recomendador SVD](train-SVD-recommender.md) |

## <a name="error-messages"></a>Mensagens de erro

Saiba mais sobre as [mensagens de erro e os códigos de exceção](designer-error-codes.md) que você pode encontrar usando módulos no designer de Azure Machine Learning.

## <a name="next-steps"></a>Passos seguintes

* [Tutorial: Construa um modelo em designer para prever preços de automóveis](../tutorial-designer-automobile-price-train-score.md)
