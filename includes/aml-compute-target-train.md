---
title: incluir ficheiro
description: incluir ficheiro
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 12/04/2019
ms.openlocfilehash: 764e4c33182499b922499c798d57fd05d8859742
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83673395"
---
**Os alvos computacionais podem ser reutilizados de um trabalho**de formação para outro . Por exemplo, uma vez anexado um VM remoto ao seu espaço de trabalho, pode reutilizá-lo para vários trabalhos.  Para os gasodutos de aprendizagem automática, utilize o passo de [pipeline](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py) adequado para cada objetivo de cálculo.

|Metas de formação &nbsp;|[ML Automatizado](../articles/machine-learning/concept-automated-ml.md) | [Pipelines de ML](../articles/machine-learning/concept-ml-pipelines.md) | [Designer de aprendizagem automática Azure](../articles/machine-learning/concept-designer.md)
|----|:----:|:----:|:----:|
|[Computador local](../articles/machine-learning/how-to-set-up-training-targets.md#local)| sim | &nbsp; | &nbsp; |
|[Cluster de computação azure machine learning](../articles/machine-learning/how-to-set-up-training-targets.md#amlcompute)| Sim, & <br/>&nbsp;afinação do hiperparâmetro | sim | sim |
|[Instância computacional de aprendizagem automática azure](../articles/machine-learning/concept-compute-instance.md) | Sim, & <br/>afinação do hiperparâmetro | sim |  |
|[VM remoto](../articles/machine-learning/how-to-set-up-training-targets.md#vm) | Sim, & <br/>afinação do hiperparâmetro | sim | &nbsp; |
|[Tijolos de dados azure &nbsp;](../articles/machine-learning/how-to-create-your-first-pipeline.md#databricks)| sim (apenas modo local SDK) | sim | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/how-to-create-your-first-pipeline.md#adla) | &nbsp; | sim | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/how-to-set-up-training-targets.md#hdinsight) | &nbsp; | sim | &nbsp; |
|[Azure Batch](../articles/machine-learning/how-to-set-up-training-targets.md#azbatch) | &nbsp; | sim | &nbsp; |
