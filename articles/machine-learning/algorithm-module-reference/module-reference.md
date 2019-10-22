---
title: Referência de algoritmos e módulos
titleSuffix: Azure Machine Learning service
description: Saiba mais sobre os módulos disponíveis na interface do Azure Machine Learning Visual
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 2d81c407c17c50c34afd15a99d2f8ac2f8c5361e
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72692843"
---
# <a name="algorithm--module-reference-overview"></a>Visão geral de referência do módulo & do algoritmo

Esse conteúdo de referência fornece o plano de fundo técnico em cada um dos módulos e algoritmos de aprendizado de máquina disponíveis na interface visual (versão prévia) do serviço Azure Machine Learning.

Cada módulo representa um conjunto de códigos que podem ser executados de forma independente e executar uma tarefa de aprendizado de máquina, dadas as entradas necessárias. Um módulo pode conter um algoritmo específico ou executar uma tarefa que seja importante no aprendizado de máquina, como a substituição de valor ausente ou análise estatística.

> [!TIP]
> Em qualquer pipeline na interface visual, você pode obter informações sobre um módulo específico. Selecione o módulo e, em seguida, selecione o link **mais ajuda** no painel **ajuda rápida** .

## <a name="modules"></a>Módulos

Os módulos são organizados por funcionalidade:

| Funcionalidade | Descrição | Módulo |
| --- |--- | ---- |
| Conversões de formato de dados | Converter dados entre vários formatos de arquivo usados no aprendizado de máquina, | [Converter em CSV](convert-to-csv.md) |
| Entrada e saída de dados | Mova dados de fontes de nuvem para seu experimento. Grave seus resultados ou dados intermediários no armazenamento do Azure, em um banco de dados SQL ou em um Hive, enquanto executa um experimento ou usa o armazenamento em nuvem do para trocar os dados entre os experimentos.  | [Importar dados](import-data.md)<br/>[Exportar dados](export-data.md)<br/>[Inserir dados manualmente](enter-data-manually.md) |
| Transformação de dados | Operações em dados que são exclusivos do aprendizado de máquina, como normalização ou compartimentalização de dados, seleção de recursos e redução de dimensionalidade.| [Selecionar colunas no conjunto de linhas](select-columns-in-dataset.md) <br/> [Editar metadados](edit-metadata.md) <br/> [Limpar dados ausentes](clean-missing-data.md) <br/>  [Hash de recurso](feature-hashing.md) <br/>  [Extrair N recursos de grama do texto](extract-n-gram-features-from-text.md) <br/> [Adicionar colunas](add-columns.md) <br/> [Adicionar linhas](add-rows.md) <br/> [Remover linhas duplicadas](remove-duplicate-rows.md) <br/> [Pré-processar texto](preprocess-text.md) <br/> [Dados de junção](join-data.md) <br/> [Dividir dados](split-data.md) <br/> [Normalizar dados](normalize-data.md) <br/> [Partição e exemplo](partition-and-sample.md) <br/> [Valores de clipe](clip-values.md) <br/> [Aplicar transformação SQL](apply-sql-transformation.md)|
| Módulos do Python e do R | Escreva o código e incorpore-o em um módulo para integrar o Python e o R ao seu experimento. | [Executar script Python](execute-python-script.md)   <br/> [Criar modelo Python](create-python-model.md) <br/> [Executar script R](execute-r-script.md)
|  | **Algoritmos de aprendizado de máquina**: | |
| Classificação | Prever uma classe.  Escolha entre os algoritmos binário (duas classes) ou multiclasse.| [Floresta de decisão multiclasse](multiclass-decision-forest.md) <br/> [Árvore de decisão aumentada multiclasse](multiclass-boosted-decision-tree.md) <br/> [Regressão logística multiclasse](multiclass-logistic-regression.md)  <br/> [Rede neural multiclasse](multiclass-neural-network.md)  <br/>  [Regressão logística de duas classes](two-class-logistic-regression.md)  <br/>[Perceptron média de duas classes](two-class-averaged-perceptron.md) <br/> [@No__t_2Decision de &nbsp;Boosted de duas classes &nbsp;Tree](two-class-boosted-decision-tree.md)  <br/> [Floresta de decisão de duas classes](two-class-decision-forest.md)  <br/> [Rede neural de duas classes](two-class-neural-network.md)  <br/> [@No__t_3Vector&#8209;&nbsp;Support de duas classes &nbsp;Machine](two-class-support-vector-machine.md) 
| Clustering | Agrupar dados juntos.| [Clustering de K-means](k-means-clustering.md)
| Regressão | Prever um valor. | [Regressão linear](linear-regression.md)  <br/> [Regressão de rede neural](neural-network-regression.md)  <br/> [Regressão de floresta de decisão](decision-forest-regression.md)  <br/> [@No__t_2Tree de &nbsp;Decision aumentada &nbsp;Regression](boosted-decision-tree-regression.md)
|  | **Criar e avaliar modelos**: | |
| Formação   | Execute os dados por meio do algoritmo. | [Modelo de treinamento](train-model.md)  <br/> [Treinar modelo de clustering](train-clustering-model.md)    |
| Avaliar Modelo | Meça a precisão do modelo treinado. |  [Avaliar modelo](evaluate-model.md)
| Classificação | Obtenha previsões do modelo que você acabou de treinar. | [Aplicar transformação](apply-transformation.md)<br/>[Atribuir &nbsp;Data &nbsp;to &nbsp;Clusters](assign-data-to-clusters.md) <br/>[Modelo de Pontuação](score-model.md)
| Funções estatísticas | Forneça uma ampla variedade de métodos numéricos relacionados à ciência de dados. | [Aplicar operação matemática](apply-math-operation.md) <br/> [Resumir dados](summarize-data.md)|

## <a name="error-messages"></a>Mensagens de erro

Saiba mais sobre as [mensagens de erro e os códigos de exceção](machine-learning-module-error-codes.md) que você pode encontrar usando módulos na interface visual do serviço Azure Machine Learning.
