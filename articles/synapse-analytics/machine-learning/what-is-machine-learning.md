---
title: Machine Learning em Azure Synapse Analytics
description: Uma visão geral das capacidades de aprendizagem automática no Azure Synapse Analytics.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: overview
ms.reviewer: jrasnick, garye
ms.date: 09/25/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: 68b113de63cfefde805c1c46e9303829c4eb33a7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98222144"
---
# <a name="machine-learning-capabilities-in-azure-synapse-analytics"></a>Capacidades de Machine Learning no Azure Synapse Analytics

O Azure Synapse Analytics oferece várias capacidades de aprendizagem automática. Este artigo fornece uma visão geral de como pode aplicar Machine Learning no contexto do Azure Synapse.

Esta visão geral abrange as diferentes capacidades em Synapse relacionadas com a aprendizagem automática, numa perspetiva de processo de ciência de dados.

Pode estar familiarizado com o aspeto de um processo típico de ciência de dados. É um processo bem conhecido, que a maioria dos projetos de aprendizagem automática seguem.

A um nível elevado, o processo contém os seguintes passos:
* (Compreensão do negócio)
* Aquisição e compreensão de dados
* Modelação
* Implantação e pontuação do modelo

Este artigo descreve as capacidades de aprendizagem automática Azure Synapse em diferentes motores de análise, a partir de uma perspetiva de processo de ciência de dados. Para cada passo no processo de ciência de dados, as capacidades do Azure Synapse que podem ajudar são resumidas.

## <a name="azure-synapse-machine-learning-capabilities"></a>Capacidades de aprendizagem automática Azure Synapse

### <a name="data-acquisition-and-understanding"></a>Aquisição e compreensão de dados

A maioria dos projetos de aprendizagem automática envolvem passos bem estabelecidos, e uma dessas medidas é aceder e compreender os dados.

#### <a name="data-source-and-pipelines"></a>Fonte de dados e oleodutos

Graças à [Azure Data Factory](../../data-factory/introduction.md), uma parte integrada nativa do Azure Synapse, existe um poderoso conjunto de ferramentas disponíveis para os oleodutos de ingestão de dados e orquestração de dados. Isto permite-lhe construir facilmente pipelines de dados para aceder e transformar os dados num formato que pode ser consumido para machine learning. [Saiba mais sobre os oleodutos de dados](../../data-factory/concepts-pipelines-activities.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) em Synapse. 

#### <a name="data-preparation-and-explorationvisualization"></a>Preparação e exploração/visualização de dados

Uma parte importante do processo de aprendizagem automática é compreender os dados através da exploração e visualização.

Dependendo do local onde os dados são armazenados, a Synapse oferece um conjunto de ferramentas diferentes para explorar e preparar para análise e machine learning. Uma das formas mais rápidas de começar com a exploração de dados é usar piscinas de SQL apache spark ou sem servidor diretamente sobre dados no lago de dados.

* [Apache Spark for Azure Synapse](../spark/apache-spark-overview.md) oferece capacidades para transformar, preparar e explorar os seus dados em escala. Estas piscinas de faíscas oferecem ferramentas como PySpark/Python, Scala e .NET para processamento de dados em escala. Utilizando bibliotecas de visualização poderosas, a experiência de exploração de dados pode ser melhorada para ajudar a entender melhor os dados. [Saiba mais sobre como explorar e visualizar dados em Synapse usando a Spark.](../get-started-analyze-spark.md)

* [As piscinas SQL sem servidor](../sql/on-demand-workspace-overview.md) oferecem uma forma de explorar dados usando o TSQL diretamente sobre o lago de dados. As piscinas SQL sem servidor também oferecem algumas visualizações incorporadas no Synapse Studio. [Saiba mais sobre como explorar dados com piscinas SQL sem servidor.](../get-started-analyze-sql-on-demand.md)

### <a name="modeling"></a>Modelação

Em Azure Synapse, os modelos de aprendizagem automática de treinamento podem ser realizados nas Piscinas Apache Spark com ferramentas como PySpark/Python, Scala ou .NET.

#### <a name="train-models-on-spark-pools-with-mllib"></a>Modelos de trem em Piscinas de Faíscas com MLlib

Os modelos de machine learning podem ser treinados com a ajuda de vários algoritmos e bibliotecas. [Spark MLlib](http://spark.apache.org/docs/latest/ml-guide.html) oferece algoritmos de aprendizagem automática escaláveis que podem ajudar a resolver a maioria dos problemas clássicos de aprendizagem de máquinas. [Este tutorial](../spark/apache-spark-machine-learning-mllib-notebook.md) abrange como treinar um modelo usando MLlib em Synapse.

Além do MLlib, bibliotecas populares como [o Scikit Learn](https://scikit-learn.org/stable/) também podem ser usadas para desenvolver modelos. Consulte [as bibliotecas De Gestão de Apache Spark em Azure Synapse Analytics](../spark/apache-spark-azure-portal-add-libraries.md) para obter detalhes sobre como instalar bibliotecas em Piscinas de Faíscas Synapse.

#### <a name="train-models-with-azure-machine-learning-automated-ml"></a>Modelos de trem com Azure Machine Learning automatizado ML

Outra forma de treinar modelos de machine learning, que não requer muita familiaridade prévia com machine learning, é usar ML automatizado. [ML automatizado](../../machine-learning/concept-automated-ml.md) é uma funcionalidade que treina automaticamente um conjunto de modelos de machine learning e permite ao utilizador selecionar o melhor modelo com base em métricas específicas. Graças a uma integração perfeita com a Azure Machine Learning a partir de Cadernos Azure Synapse, os utilizadores podem facilmente aproveitar ml automatizado em Sinapse com a autenticação passthrough Azure Ative Directory.  Isto significa que só precisa de apontar para o seu espaço de trabalho Azure Machine Learning e não precisa de introduzir credenciais. Aqui está um [tutorial automatizado de ML](../spark/apache-spark-azure-machine-learning-tutorial.md) que descreve como treinar modelos usando ML automatizado Azure Machine Learning em Piscinas de Faíscas De Sinapse.

### <a name="model-deployment-and-scoring"></a>Implantação e pontuação do modelo

Os modelos treinados em Azure Synapse ou fora do Azure Synapse podem ser facilmente utilizados para a pontuação de lotes. Atualmente em Synapse, existem duas maneiras de executar a pontuação do lote.

* Pode utilizar a [função TSQL PREDICT](../sql-data-warehouse/sql-data-warehouse-predict.md) nas piscinas Sinaapse SQL para executar as suas previsões onde os seus dados vivem. Esta função poderosa e escalável permite-lhe enriquecer os seus dados sem retirar quaisquer dados do seu armazém de dados. Foi introduzida uma nova [experiência de modelo de machine learning guiado no Synapse Studio](./tutorial-sql-pool-model-scoring-wizard.md) onde é possível implementar um modelo ONNX a partir do registo de modelos Azure Machine Learning em Piscinas SQL synapse para pontuação de lotes usando o PREDICT.

* Outra opção para os modelos de machine learning de pontuação em Azure Synapse é aproveitar as Piscinas Apache Spark para Azure Synapse. Dependendo das bibliotecas utilizadas para treinar os modelos, pode utilizar uma experiência de código para executar a pontuação do seu lote.

## <a name="next-steps"></a>Passos seguintes

* [Introdução ao Azure Synapse Analytics](../get-started.md)
* [Criar uma área de trabalho](../get-started-create-workspace.md)
* [Quickstart: Criar um novo serviço de aprendizagem automática Azure em Synapse](quickstart-integrate-azure-machine-learning.md)
* [Tutorial: Assistente de pontuação de modelo de machine learning - piscina de SQL dedicada](tutorial-sql-pool-model-scoring-wizard.md)