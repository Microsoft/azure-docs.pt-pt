---
title: Referência de algoritmos e módulos
description: Conheça os módulos disponíveis no designer de Machine Learning Azure (pré-visualização)
titleSuffix: Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: peterclu
ms.author: peterlu
ms.date: 12/17/2019
ms.openlocfilehash: d3feb62c0c7fa24dd998add08d17ebd1d4e9ee6c
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77162587"
---
# <a name="algorithm--module-reference-for-azure-machine-learning-designer"></a>Referência de algoritmo e módulo para designer de machine learning Azure

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
| Entrada e saída de dados | Mova dados de fontes de nuvem para o seu oleoduto. Escreva os seus resultados ou dados intermédios para o Azure Storage, uma base de dados SQL, ou Hive, enquanto executa um oleoduto, ou utilize o armazenamento em nuvem para trocar dados entre oleodutos.  | [Introduza dados manualmente](enter-data-manually.md) <br/> [Dados de Exportação](export-data.md) <br/> [Dados de Importação](import-data.md) |
| Transformação de dados | Operações em dados exclusivos do machine learning, tais como normalizar ou ligar dados, redução de dimensionalidade e conversão de dados entre vários formatos de ficheiros.| [Adicionar Colunas](add-columns.md) <br/> [Adicionar Linhas](add-rows.md) <br/> [Aplicar Operação Matemática](apply-math-operation.md) <br/> [Aplicar transformação sql](apply-sql-transformation.md) <br/> [Dados desaparecidos limpos](clean-missing-data.md) <br/> [Valores de Clip](clip-values.md) <br/> [Converter em CSV](convert-to-csv.md) <br/> [Converter para Dataset](convert-to-dataset.md) <br/> [Converter em Valores Indicadores](convert-to-indicator-values.md) <br/> [Editar Metadados](edit-metadata.md) <br/> [Junte-se a Dados](join-data.md) <br/> [Normalizar dados](normalize-data.md) <br/> [Partição e Amostra](partition-and-sample.md)  <br/> [Remover linhas duplicadas](remove-duplicate-rows.md) <br/> [SMOTE](smote.md) <br/> [Selecione Colunas Transformar](select-columns-transform.md) <br/> [Selecione Colunas no Conjunto de Dados](select-columns-in-dataset.md) <br/> [Dados divididos](split-data.md) |
| Seleção de Recursos | Selecione um subconjunto de funcionalidades relevantes e úteis para utilizar na construção de um modelo analítico. | [Seleção de funcionalidades baseada seleção de filtros](filter-based-feature-selection.md) <br/> [Importância da característica de permutação](permutation-feature-importance.md) |
| Funções Estatísticas | Fornecer uma grande variedade de métodos estatísticos relacionados com a ciência dos dados. | [Resumo dados](summarize-data.md)|

## <a name="machine-learning-algorithms"></a>Algoritmos de aprendizagem automática

| Funcionalidade | Descrição | Módulo |
| --- |--- | --- |
| Regressão | Prever um valor. | [Regressão da árvore de decisão reforçada](boosted-decision-tree-regression.md) <br/> [Decisão Regressão Florestal](decision-forest-regression.md) <br/> [Regressão Linear](linear-regression.md)  <br/> [Regressão da Rede Neural](neural-network-regression.md)  <br/> |
| Clustering | Dados de grupo juntos.| [Agrupamento k-significa](k-means-clustering.md)
| Classificação | Preveja uma aula.  Escolha entre algoritmos binários (de duas classes) ou multiclasse.| [Árvore de decisão impulsionada multiclasse](multiclass-boosted-decision-tree.md) <br/> [Floresta de Decisão Multiclasse](multiclass-decision-forest.md) <br/> [Regressão Logística Multiclasse](multiclass-logistic-regression.md)  <br/> [Rede Neural Multiclasse](multiclass-neural-network.md) <br/> [Um vs. All Multiclass](one-vs-all-multiclass.md) <br/> [Perceptron médio de duas classes](two-class-averaged-perceptron.md) <br/>  [Árvore de decisão impulsionada por duas classes](two-class-boosted-decision-tree.md)  <br/> [Floresta de decisão de duas classes](two-class-decision-forest.md) <br/>  [Regressão Logística de duas classes](two-class-logistic-regression.md) <br/> [Rede Neural de duas classes](two-class-neural-network.md) <br/> [Máquina vetorial de suporte de duas classes](two-class-support-vector-machine.md) | 

## <a name="modules-for-building-and-evaluating-models"></a>Módulos para construção e avaliação de modelos

| Funcionalidade | Descrição | Módulo |
| --- |--- | --- |
| Preparação de modelos | Analisar dados através do algoritmo. |  [Modelo de clustering de trem](train-clustering-model.md) <br/> [Modelo de comboio](train-model.md)  <br/> [Hiperparâmetros modelo de sintonia](tune-model-hyperparameters.md) |
| Model Scoreing and Evaluation | Meça a precisão do modelo treinado. | [Aplicar Transformação](apply-transformation.md) <br/> [Atribuir dados a Clusters](assign-data-to-clusters.md) <br/> [Modelo de validação cruzada](cross-validate-model.md) <br/> [Modelo de avaliação](evaluate-model.md) <br/> [Modelo de pontuação](score-model.md) |
| Linguagem Python | Escreva código e inseri-o num módulo para integrar python com o seu oleoduto. | [Criar modelo Python](create-python-model.md) <br/> [Execute o Roteiro Python](execute-python-script.md) |
| Linguagem R | Escreva código e inseri-o num módulo para integrar R com o seu pipeline. | [Executar script R](execute-r-script.md) |
| Análise de Texto | Forneça ferramentas computacionais especializadas para trabalhar com texto estruturado e não estruturado. | [Extrair recursos n grama do texto](extract-n-gram-features-from-text.md) <br/> [Hashing de Recurso](feature-hashing.md) <br/> [Texto pré-processo](preprocess-text.md) |
| Recomendação | Construir modelos de recomendação. | [Avaliar o Recomendador](evaluate-recommender.md) <br/> [Pontuar Recomendador SVD](score-svd-recommender.md) <br/> [Recomendador SVD do comboio](train-SVD-recommender.md) |

## <a name="error-messages"></a>Mensagens de erro

Saiba mais sobre as mensagens de [erro e códigos](designer-error-codes.md) de exceção que poderá encontrar utilizando módulos em Azure Machine Learning designer.

## <a name="next-steps"></a>Passos seguintes

* [Tutorial: Construa um modelo em designer para prever preços de automóveis](../tutorial-designer-automobile-price-train-score.md)
