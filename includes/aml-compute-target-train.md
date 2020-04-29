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
ms.openlocfilehash: 6106d4e0801500b0429e634651f3de342646b754
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77155983"
---
**Os alvos computacionais podem ser reutilizados de um trabalho**de formação para outro . Por exemplo, uma vez anexado um VM remoto ao seu espaço de trabalho, pode reutilizá-lo para vários trabalhos.  Para os gasodutos de aprendizagem automática, utilize o passo de [pipeline](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py) adequado para cada objetivo de cálculo.

|Metas &nbsp;de formação|[ML Automatizado](../articles/machine-learning/concept-automated-ml.md) | [Pipelines de ML](../articles/machine-learning/concept-ml-pipelines.md) | [Designer de aprendizagem automática Azure](../articles/machine-learning/concept-designer.md)
|----|:----:|:----:|:----:|
|[Computador local](../articles/machine-learning/how-to-set-up-training-targets.md#local)| sim | &nbsp; | &nbsp; |
|[Cluster de computação azure machine learning](../articles/machine-learning/how-to-set-up-training-targets.md#amlcompute)| Sim, & <br/>afinação&nbsp;do hiperparâmetro | sim | sim |
|[VM remoto](../articles/machine-learning/how-to-set-up-training-targets.md#vm) | Sim, & <br/>afinação do hiperparâmetro | sim | &nbsp; |
|[Tijolos&nbsp;de dados azure](../articles/machine-learning/how-to-create-your-first-pipeline.md#databricks)| sim (apenas modo local SDK) | sim | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/how-to-create-your-first-pipeline.md#adla) | &nbsp; | sim | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/how-to-set-up-training-targets.md#hdinsight) | &nbsp; | sim | &nbsp; |
|[Azure Batch](../articles/machine-learning/how-to-set-up-training-targets.md#azbatch) | &nbsp; | sim | &nbsp; |
