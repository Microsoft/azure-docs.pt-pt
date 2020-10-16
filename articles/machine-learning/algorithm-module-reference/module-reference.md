---
title: Referência de algoritmos e módulos
description: Conheça os módulos disponíveis no Azure Machine Learning designer
titleSuffix: Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/19/2020
ms.openlocfilehash: 9a2e21696409c320b89d1fed54fefad953b955e1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90886392"
---
# <a name="algorithm--module-reference-for-azure-machine-learning-designer"></a>Algoritmo & referência do módulo para o designer de aprendizagem de máquinas Azure

Este conteúdo de referência fornece o fundo técnico de cada um dos algoritmos e módulos de machine learning disponíveis no designer de Machine Learning Azure.

Cada módulo representa um conjunto de código que pode funcionar de forma independente e executar uma tarefa de machine learning, dadas as entradas necessárias. Um módulo pode conter um algoritmo particular, ou executar uma tarefa que é importante na aprendizagem automática, como a substituição de valor em falta, ou a análise estatística.

Para ajudar na escolha de algoritmos, consulte 
* [Como selecionar algoritmos](../how-to-select-algorithms.md)
* [Folha de batota de algoritmo de aprendizagem de máquina de Azure](../algorithm-cheat-sheet.md)

> [!TIP]
> Em qualquer oleoduto do designer, você pode obter informações sobre um módulo específico. Selecione o link **Saiba mais** na placa do módulo quando pairar sobre o módulo na lista do módulo ou no painel direito do módulo.

## <a name="data-preparation-modules"></a>Módulos de preparação de dados


| Funcionalidade | Descrição | Módulo |
| --- |--- | --- |
| Entrada e saída de dados | Mova os dados de fontes de nuvem para o seu oleoduto. Escreva os seus resultados ou dados intermédios para Azure Storage, SQL Database ou Hive, enquanto executa um pipeline, ou utilize o armazenamento em nuvem para trocar dados entre os oleodutos.  | [Introduzir os Dados Manualmente](enter-data-manually.md) <br/> [Dados de Exportação](export-data.md) <br/> [Dados de Importação](import-data.md) |
| Transformação de Dados | Operações em dados exclusivos da aprendizagem automática, como normalizar ou binar dados, redução de dimensionalidade e conversão de dados entre vários formatos de ficheiros.| [Adicionar Colunas](add-columns.md) <br/> [Adicionar Linhas](add-rows.md) <br/> [Aplicar Operação Matemática](apply-math-operation.md) <br/> [Aplicar Transformação SQL](apply-sql-transformation.md) <br/> [Limpar Dados em Falta](clean-missing-data.md) <br/> [Valores de Clip](clip-values.md) <br/> [Converter para CSV](convert-to-csv.md) <br/> [Converter em Conjunto de Dados](convert-to-dataset.md) <br/> [Converter em Valores Indicadores](convert-to-indicator-values.md) <br/> [Editar Metadados](edit-metadata.md) <br/> [Agrupar Dados em Posições](group-data-into-bins.md) <br/> [Associar dados](join-data.md) <br/> [Normalizar Dados](normalize-data.md) <br/> [Partição e Amostra](partition-and-sample.md)  <br/> [Remover Linhas Duplicadas](remove-duplicate-rows.md) <br/> [SMOTE](smote.md) <br/> [Selecionar Transformação de Colunas](select-columns-transform.md) <br/> [Selecionar Colunas no Conjunto de Dados](select-columns-in-dataset.md) <br/> [Dividir Dados](split-data.md) |
| Seleção de Recursos | Selecione um subconjunto de funcionalidades relevantes e úteis para utilizar na construção de um modelo analítico. | [Seleção de funcionalidades baseadas em filtro](filter-based-feature-selection.md) <br/> [Importância da funcionalidade de permutação](permutation-feature-importance.md) |
| Funções Estatísticas | Fornecer uma grande variedade de métodos estatísticos relacionados com a ciência dos dados. | [Resumir Dados](summarize-data.md)|

## <a name="machine-learning-algorithms"></a>Algoritmos de aprendizagem automática

| Funcionalidade | Descrição | Módulo |
| --- |--- | --- |
| Regressão | Prever um valor. | [Regressão da Árvore de Decisões Elevada](boosted-decision-tree-regression.md) <br/> [Regressão da Floresta de Decisão](decision-forest-regression.md) <br/> [Regressão de Quantil da Floresta Rápida](fast-forest-quantile-regression.md)  <br/> [Regressão Linear](linear-regression.md)  <br/> [Regressão da Rede Neural](neural-network-regression.md)  <br/> [Regressão de Poisson](poisson-regression.md)  <br/>|
| Clustering | Dados de grupo juntos.| [Clustering K-Means](k-means-clustering.md)
| Classificação | Prever uma aula.  Escolha entre algoritmos binários (de duas classes) ou multiclasses.| [Árvore de Decisões Elevada Multiclasse](multiclass-boosted-decision-tree.md) <br/> [Floresta de Decisão de Várias Classes](multiclass-decision-forest.md) <br/> [Regressão Logística de Várias Classes](multiclass-logistic-regression.md)  <br/> [Rede Neural de Várias Classes](multiclass-neural-network.md) <br/> [Uma vs. Todas as Multiclasses](one-vs-all-multiclass.md) <br/> [Perceção Média de Duas Classes](two-class-averaged-perceptron.md) <br/>  [Árvore de Decisões Elevada de Duas Classes](two-class-boosted-decision-tree.md)  <br/> [Floresta de Decisão de Duas Classes](two-class-decision-forest.md) <br/>  [Regressão Logística de Duas Classes](two-class-logistic-regression.md) <br/> [Rede Neural de Duas Classes](two-class-neural-network.md) <br/> [Máquina de Vetores de Suporte de Duas Classes](two-class-support-vector-machine.md) | 

## <a name="modules-for-building-and-evaluating-models"></a>Módulos para construção e avaliação de modelos

| Funcionalidade | Descrição | Módulo |
| --- |--- | --- |
| Formação de Modelos | Executar dados através do algoritmo. |  [Train Clustering Model (Preparar Modelo de Clustering)](train-clustering-model.md) <br/> [Preparar Modelo](train-model.md) <br/> [Modelo de Pytorch de comboio](train-pytorch-model.md) <br/> [Ajustar hiperparâmetros do modelo](tune-model-hyperparameters.md) |
| Pontuação e Avaliação do Modelo | Meça a precisão do modelo treinado. | [Aplicar Transformação](apply-transformation.md) <br/> [Assign Data to Clusters (Atribuir Dados a Clusters)](assign-data-to-clusters.md) <br/> [Modelo de validação cruzada](cross-validate-model.md) <br/> [Avaliar Modelo](evaluate-model.md) <br/> [Classificar o Modelo de Imagem](score-image-model.md) <br/> [Modelo de Classificação](score-model.md) |
| Língua python | Escreva código e incorpore-o num módulo para integrar python com o seu oleoduto. | [Criar Modelo Python](create-python-model.md) <br/> [Executar Script do Python](execute-python-script.md) |
| Língua R | Escreva código e incorpore-o num módulo para integrar R com o seu pipeline. | [Executar Script R](execute-r-script.md) |
| Análise de Texto | Fornecer ferramentas computacionais especializadas para trabalhar com textos estruturados e não estruturados. |  [Converter Word em Vetor](convert-word-to-vector.md) <br/> [Extrair Funcionalidades N-Grama a partir de Texto](extract-n-gram-features-from-text.md) <br/> [Hashing de Funcionalidade](feature-hashing.md) <br/> [Pré-processar Texto](preprocess-text.md) <br/> [Alocação Latente de Dirichlet](latent-dirichlet-allocation.md) <br/> [Score Vowpal Wabbit Model](score-vowpal-wabbit-model.md) <br/> [Train Vowpal Wabbit Model](train-vowpal-wabbit-model.md)|
| Imagem Digitalizada | Módulos relacionados com o pré-processamento de dados de imagem e reconhecimento de imagem. |  [Aplicar a Transformação da Imagem](apply-image-transformation.md) <br/> [Converter para o Diretório da Imagem](convert-to-image-directory.md) <br/> [Inicializar a Transformação da Imagem](init-image-transformation.md) <br/> [Dividir o Diretório da Imagem](split-image-directory.md) <br/> [DenseNet](densenet.md) <br/> [ResNet](resnet.md) |
| Recomendação | Construa modelos de recomendação. | [Avaliar Recomendador](evaluate-recommender.md) <br/> [Classificar Recomendador SVD](score-svd-recommender.md) <br/> [Score Wide and Deep Recommender](score-wide-and-deep-recommender.md)<br/> [Preparar Recomendador SVD](train-SVD-recommender.md) <br/> [Train Wide and Deep Recommender](train-wide-and-deep-recommender.md)|
| Deteção de Anomalias | Construa modelos de deteção de anomalias. | [Deteção de Anomalias Baseada em PCA](pca-based-anomaly-detection.md) <br/> [Preparar Modelo de Deteção de Anomalias](train-anomaly-detection-model.md) |


## <a name="web-service"></a>Serviço Web

Conheça os módulos de serviço web que são [necessários](web-service-input-output.md) para inferência em tempo real no designer de Machine Learning Azure.

## <a name="error-messages"></a>Mensagens de erro

Saiba mais sobre as [mensagens de erro e códigos de exceção](designer-error-codes.md) que poderá encontrar utilizando módulos no designer de Machine Learning Azure.

## <a name="next-steps"></a>Passos seguintes

* [Tutorial: Construa um modelo em designer para prever os preços dos automóveis](../tutorial-designer-automobile-price-train-score.md)
