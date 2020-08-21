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
ms.date: 12/04/2019
ms.openlocfilehash: 6ddff84de6b8ffd5bc8f7c7dcaa7cb4df3d71f81
ms.sourcegitcommit: d18a59b2efff67934650f6ad3a2e1fe9f8269f21
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88703296"
---
**Os objetivos de cálculo podem ser reutilizados de um trabalho de treino para o seguinte**. Por exemplo, uma vez que você anexar um VM remoto ao seu espaço de trabalho, você pode reutilizá-lo para vários trabalhos.  Para os gasodutos de aprendizagem automática, utilize o passo de [gasoduto](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py) adequado para cada alvo de cálculo.

|Metas de formação &nbsp;|[ML Automatizado](../articles/machine-learning/concept-automated-ml.md) | [Pipelines de ML](../articles/machine-learning/concept-ml-pipelines.md) | [Designer de aprendizagem de máquinas Azure](../articles/machine-learning/concept-designer.md)
|----|:----:|:----:|:----:|
|[Computador local](../articles/machine-learning/how-to-set-up-training-targets.md#local)| sim | &nbsp; | &nbsp; |
|[Aglomerado de computação Azure Machine Learning](../articles/machine-learning/how-to-set-up-training-targets.md#amlcompute)| Sim, & <br/>sintonização &nbsp; hiperparímetro | sim | sim |
|[Instância de computação do Azure Machine Learning](../articles/machine-learning/how-to-set-up-training-targets.md#instance) | Sim, & <br/>sintonização hiperparímetro | sim |  |
|[VM remoto](../articles/machine-learning/how-to-set-up-training-targets.md#vm) | Sim, & <br/>sintonização hiperparímetro | sim | &nbsp; |
|[Azure &nbsp; Databricks](../articles/machine-learning/how-to-create-your-first-pipeline.md#databricks)| sim (apenas modo local SDK) | sim | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/how-to-create-your-first-pipeline.md#adla) | &nbsp; | sim | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/how-to-set-up-training-targets.md#hdinsight) | &nbsp; | sim | &nbsp; |
|[Azure Batch](../articles/machine-learning/how-to-set-up-training-targets.md#azbatch) | &nbsp; | sim | &nbsp; |
