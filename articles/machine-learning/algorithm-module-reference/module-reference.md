---
title: Referência de algoritmos e módulos
description: Conheça os módulos disponíveis no designer de Machine Learning Azure (pré-visualização)
titleSuffix: Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/13/2020
ms.openlocfilehash: c46c21b5662b924552f850f226a12c7350b4625a
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458205"
---
# <a name="algorithm--module-reference-for-azure-machine-learning-designer-preview"></a>Algoritmo & referência do módulo para designer de machine learning azure (pré-visualização)

Este conteúdo de referência fornece o pano de fundo técnico em cada um dos algoritmos e módulos de aprendizagem automática disponíveis no designer de Machine Learning Azure (pré-visualização).

Cada módulo representa um conjunto de códigos que podem funcionar de forma independente e executar uma tarefa de aprendizagem automática, dadas as inputs necessárias. Um módulo pode conter um algoritmo particular, ou executar uma tarefa que é importante na aprendizagem automática, como a falta de substituição de valor, ou a análise estatística.

Para ajuda na escolha de algoritmos, consulte 
* [Como selecionar algoritmos](../how-to-select-algorithms.md)
* [Folha de batota do algoritmo de aprendizagem de máquina seletiva Azure](../algorithm-cheat-sheet.md)

> [!TIP]
> Em qualquer pipeline do designer, você pode obter informações sobre um módulo específico. Selecione o módulo e, em seguida, selecione o link **mais ajuda** no painel de **ajuda rápida.**

## <a name="data-preparation-modules"></a>Módulos de preparação de dados


| Funcionalidade | Descrição | Módulo |
| --- |--- | --- |
| Entrada e saída de dados | Mova dados de fontes de nuvem para o seu oleoduto. Escreva os seus resultados ou dados intermédios para o Azure Storage, uma base de dados SQL, ou Hive, enquanto executa um oleoduto, ou utilize o armazenamento em nuvem para trocar dados entre oleodutos.  | [Introduzir os Dados Manualmente](enter-data-manually.md) <br/> [Dados de Exportação](export-data.md) <br/> [Dados de Importação](import-data.md) |
| Transformação de dados | Operações em dados exclusivos do machine learning, tais como normalizar ou ligar dados, redução de dimensionalidade e conversão de dados entre vários formatos de ficheiros.| [Adicionar Colunas](add-columns.md) <br/> [Adicionar Linhas](add-rows.md) <br/> [Aplicar Operação Matemática](apply-math-operation.md) <br/> [Aplicar Transformação SQL](apply-sql-transformation.md) <br/> [Limpar Dados em Falta](clean-missing-data.md) <br/> [Valores de Clip](clip-values.md) <br/> [Converter para CSV](convert-to-csv.md) <br/> [Converter em Conjunto de Dados](convert-to-dataset.md) <br/> [Converter em Valores Indicadores](convert-to-indicator-values.md) <br/> [Editar Metadados](edit-metadata.md) <br/> [Associar dados](join-data.md) <br/> [Normalizar Dados](normalize-data.md) <br/> [Partição e Amostra](partition-and-sample.md)  <br/> [Remover Linhas Duplicadas](remove-duplicate-rows.md) <br/> [SMOTE](smote.md) <br/> [Selecionar Transformação de Colunas](select-columns-transform.md) <br/> [Selecionar Colunas no Conjunto de Dados](select-columns-in-dataset.md) <br/> [Dividir Dados](split-data.md) |
| Seleção de Recursos | Selecione um subconjunto de funcionalidades relevantes e úteis para utilizar na construção de um modelo analítico. | [Seleção de funcionalidades baseadas em filtro](filter-based-feature-selection.md) <br/> [Importância da funcionalidade de permutação](permutation-feature-importance.md) |
| Funções Estatísticas | Fornecer uma grande variedade de métodos estatísticos relacionados com a ciência dos dados. | [Resumir Dados](summarize-data.md)|

## <a name="machine-learning-algorithms"></a>Algoritmos de aprendizagem automática

| Funcionalidade | Descrição | Módulo |
| --- |--- | --- |
| Regressão | Prever um valor. | [Regressão da Árvore de Decisões Elevada](boosted-decision-tree-regression.md) <br/> [Regressão da Floresta de Decisão](decision-forest-regression.md) <br/> [Regressão Linear](linear-regression.md)  <br/> [Regressão da Rede Neural](neural-network-regression.md)  <br/> |
| Clustering | Dados de grupo juntos.| [Clustering K-Means](k-means-clustering.md)
| Classificação | Preveja uma aula.  Escolha entre algoritmos binários (de duas classes) ou multiclasse.| [Árvore de Decisões Elevada Multiclasse](multiclass-boosted-decision-tree.md) <br/> [Floresta de Decisão de Várias Classes](multiclass-decision-forest.md) <br/> [Regressão Logística de Várias Classes](multiclass-logistic-regression.md)  <br/> [Rede Neural de Várias Classes](multiclass-neural-network.md) <br/> [Um vs. All Multiclass](one-vs-all-multiclass.md) <br/> [Perceção Média de Duas Classes](two-class-averaged-perceptron.md) <br/>  [Árvore de Decisões Elevada de Duas Classes](two-class-boosted-decision-tree.md)  <br/> [Floresta de Decisão de Duas Classes](two-class-decision-forest.md) <br/>  [Regressão Logística de Duas Classes](two-class-logistic-regression.md) <br/> [Rede Neural de Duas Classes](two-class-neural-network.md) <br/> [Máquina de Vetores de Suporte de Duas Classes](two-class-support-vector-machine.md) | 

## <a name="modules-for-building-and-evaluating-models"></a>Módulos para construção e avaliação de modelos

| Funcionalidade | Descrição | Módulo |
| --- |--- | --- |
| Preparação de modelos | Analisar dados através do algoritmo. |  [Preparar Modelo de Clustering](train-clustering-model.md) <br/> [Modelo de comboio](train-model.md)  <br/> [Ajustar hiperparâmetros do modelo](tune-model-hyperparameters.md) |
| Model Scoreing and Evaluation | Meça a precisão do modelo treinado. | [Aplicar Transformação](apply-transformation.md) <br/> [Atribuir Dados a Clusters](assign-data-to-clusters.md) <br/> [Modelo de validação cruzada](cross-validate-model.md) <br/> [Modelo de avaliação](evaluate-model.md) <br/> [Modelo de pontuação](score-model.md) |
| Linguagem Python | Escreva código e inseri-o num módulo para integrar python com o seu oleoduto. | [Criar Modelo Python](create-python-model.md) <br/> [Executar Script do Python](execute-python-script.md) |
| Linguagem R | Escreva código e inseri-o num módulo para integrar R com o seu pipeline. | [Executar Script R](execute-r-script.md) |
| Análise de Texto | Forneça ferramentas computacionais especializadas para trabalhar com texto estruturado e não estruturado. | [Extrair Funcionalidades N-Grama a partir de Texto](extract-n-gram-features-from-text.md) <br/> [Hashing de Funcionalidade](feature-hashing.md) <br/> [Pré-processar Texto](preprocess-text.md) <br/> [Atribuição de Dirichlet latente](latent-dirichlet-allocation.md) |
| Recomendação | Construir modelos de recomendação. | [Avaliar Recomendador](evaluate-recommender.md) <br/> [Classificar Recomendador SVD](score-svd-recommender.md) <br/> [Preparar Recomendador SVD](train-SVD-recommender.md) |
| Deteção de Anomalias | Construir modelos de deteção de anomalias. | [Deteção de anomalias baseada em PCA](pca-based-anomaly-detection.md) <br/> [Modelo de deteção de anomalias do comboio](train-anomaly-detection-model.md) |


## <a name="web-service"></a>Serviço Web

Conheça os módulos de serviço web que são [necessários](web-service-input-output.md) para inferência em tempo real no designer de Machine Learning Azure.

## <a name="error-messages"></a>Mensagens de erro

Saiba mais sobre as mensagens de [erro e códigos](designer-error-codes.md) de exceção que poderá encontrar utilizando módulos em Azure Machine Learning designer.

## <a name="next-steps"></a>Passos seguintes

* [Tutorial: Construa um modelo em designer para prever preços de automóveis](../tutorial-designer-automobile-price-train-score.md)
