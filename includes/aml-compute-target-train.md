---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 08/19/2020
ms.openlocfilehash: bbd32f7e40e3039a69a95b4458de86ff35f26867
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/01/2020
ms.locfileid: "91640616"
---
**Os objetivos de cálculo podem ser reutilizados de um trabalho de treino para o seguinte**. Por exemplo, uma vez que você anexar um VM remoto ao seu espaço de trabalho, você pode reutilizá-lo para vários trabalhos.  Para os gasodutos de aprendizagem automática, utilize o passo de [gasoduto](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py) adequado para cada alvo de cálculo.

|Metas de formação &nbsp;|[ML Automatizado](../articles/machine-learning/concept-automated-ml.md) | [Pipelines de ML](../articles/machine-learning/concept-ml-pipelines.md) | [Estruturador do Azure Machine Learning](../articles/machine-learning/concept-designer.md)
|----|:----:|:----:|:----:|
|[Computador local](../articles/machine-learning/how-to-create-attach-compute-sdk.md#local)| sim | &nbsp; | &nbsp; |
|[Aglomerado de computação Azure Machine Learning](../articles/machine-learning/how-to-create-attach-compute-sdk.md#amlcompute)| Sim, & <br/>sintonização &nbsp; hiperparímetro | sim | sim |
|[Instância de computação do Azure Machine Learning](../articles/machine-learning/how-to-create-attach-compute-sdk.md#instance) | sim (através de SDK) | sim |  |
|[VM remoto](../articles/machine-learning/how-to-create-attach-compute-sdk.md#vm) | Sim, & <br/>sintonização hiperparímetro | sim | &nbsp; |
|[Azure &nbsp; Databricks](../articles/machine-learning/how-to-create-attach-compute-sdk.md#databricks)| sim (apenas modo local SDK) | sim | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/how-to-create-attach-compute-sdk.md#adla) | &nbsp; | sim | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/how-to-create-attach-compute-sdk.md#hdinsight) | &nbsp; | sim | &nbsp; |
|[Azure Batch](../articles/machine-learning/how-to-create-attach-compute-sdk.md#azbatch) | &nbsp; | sim | &nbsp; |
