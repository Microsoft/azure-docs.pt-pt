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
ms.openlocfilehash: 8825f1dc3b66a5c4981ba25a90813aec63975b1f
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65145142"
---
# <a name="algorithm--module-reference-overview"></a>Descrição geral de referência de algoritmos e módulos

Este conteúdo de referência fornece a formação técnica em cada um de módulos disponíveis na interface do visual (pré-visualização) do serviço Azure Machine Learning e algoritmos de aprendizagem automática.

Cada módulo representa um conjunto de código que possa executar de forma independente e efetuar uma machine learning tarefa, tendo em conta as entradas necessárias. Um módulo pode conter um algoritmo específico, ou executar uma tarefa que é importante do Machine learning, como substituição de valor em falta ou análise estatística.

> [!TIP]
> Em qualquer experiência na interface do visual, pode obter informações sobre um módulo específico. Selecione o módulo, em seguida, selecione o **mais ajuda** ligação na **ajuda rápida** painel.

## <a name="modules"></a>Módulos

Módulos são organizados por funcionalidade:

| Funcionalidade | Descrição | Módulo |
| --- |--- | ---- |
| Conversões de formato de dados | Converter os dados entre vários formatos de arquivo usados do Machine learning, | [Converter para CSV](convert-to-csv.md) |
| Dados de entrada e saída | Mova dados de origens na cloud na sua experimentação. Escrever seus resultados ou dados intermediários para armazenamento do Azure, base de dados SQL ou Hive, durante a execução de uma experimentação, ou usar o armazenamento na cloud para trocar dados entre experimentações.  | [Importar dados](import-data.md)<br/>[Exportar dados](export-data.md)<br/>[Introduzir dados manualmente](enter-data-manually.md) |
| Transformação de dados | Operações nos dados que são exclusivas ao machine learning, como normalizar ou compartimentação de dados, a seleção de funcionalidades e a redução de dimensionalidade.| [Selecionar colunas no conjunto de dados](select-columns-in-dataset.md) <br/> [Editar metadados](edit-metadata.md) <br/> [Limpar dados em falta](clean-missing-data.md) <br/> [Adicionar colunas](add-columns.md) <br/> [Adicionar linhas](add-rows.md) <br/> [Remover linhas duplicadas](remove-duplicate-rows.md) <br/> [Dividir os dados](split-data.md) <br/> [Normalizar dados](normalize-data.md) <br/> [Partição e amostras](partition-and-sample.md) |
| Módulo de Python | Escrever código e incorporá-lo num módulo para integrar o Python com sua experimentação. | [Executar o Script de Python](execute-python-script.md)   <br/> [Criar modelo de Python](create-python-model.md)
|  | **Do Machine learning algoritmos**: | |
| Classificação | Prever uma classe.  Escolha de binário (duas classes) ou algoritmos de várias classes.| [Floresta de decisão de várias classes](multiclass-decision-forest.md) <br/> [Regressão logística várias classes](multiclass-logistic-regression.md)  <br/> [Várias classes rede Neural](multiclass-neural-network.md)  <br/>  [Regressão logística de duas classes](two-class-logistic-regression.md)  <br/>[Perceptron uma média de duas classes](two-class-averaged-perceptron.md) <br/> [Duas classes&nbsp;aumentado&nbsp;decisão&nbsp;árvore](two-class-boosted-decision-tree.md)  <br/> [Floresta de decisão de duas classes](two-class-decision-forest.md)  <br/> [Duas classes rede Neural](two-class-neural-network.md)  <br/> [Dois&#8209;classe&nbsp;suporte&nbsp;Vetor&nbsp;máquina](two-class-support-vector-machine.md) 
| Clustering | Agrupe dados em conjunto.| [Clustering de K-Means](k-means-clustering.md)
| Regressão | Prever um valor. | [Regressão linear](linear-regression.md)  <br/> [Regressão de rede neural](neural-network-regression.md)  <br/> [Regressão de floresta de decisão](decision-forest-regression.md)  <br/> [Aumentado&nbsp;decisão&nbsp;árvore&nbsp;regressão](boosted-decision-tree-regression.md)
|  | **Criar e avaliar modelos**: | |
| Preparar   | Percorrer o algoritmo de dados. | [Preparar modelo](train-model.md)  <br/> [Preparar o modelo de Clustering](train-clustering-model.md)    |
| Avaliar Modelo | Medir a precisão do modelo preparado. |  [Avaliar modelo](evaluate-model.md)
| Classificação | Obter as previsões do modelo que Treinou apenas. | [Aplicam-se de transformação](apply-transformation.md)<br/>[Atribua&nbsp;dados&nbsp;para&nbsp;Clusters](assign-data-to-clusters.md) <br/>[Modelo de pontuação](score-model.md)

## <a name="error-messages"></a>Mensagens de erro

Saiba mais sobre o [mensagens de erro e códigos de exceção](machine-learning-module-error-codes.md) que poderá encontrar a utilização de módulos na interface do visual do serviço Azure Machine Learning.
