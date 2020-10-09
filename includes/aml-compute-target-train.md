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
ms.date: 09/17/2020
ms.openlocfilehash: cdf2c3d1840f64b267f1aac1f8877d5f0bf76082
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2020
ms.locfileid: "91841963"
---
**Os alvos de cálculo podem ser reutilizados de um trabalho de treino para o outro.** Por exemplo, depois de anexar um VM remoto ao seu espaço de trabalho, pode reutilizá-lo para vários trabalhos. Para os gasodutos de aprendizagem automática, utilize o passo de [gasoduto](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py&preserve-view=true) adequado para cada alvo de cálculo.

Você pode usar qualquer um dos seguintes recursos para um alvo de computação de formação para a maioria dos empregos. Nem todos os recursos podem ser usados para aprendizagem automática de máquinas, oleodutos de aprendizagem automática ou designer.

|Metas de formação &nbsp;|[Aprendizagem automática automatizada](../articles/machine-learning/concept-automated-ml.md) | [Pipelines de aprendizagem automática](../articles/machine-learning/concept-ml-pipelines.md) | [Estruturador do Azure Machine Learning](../articles/machine-learning/concept-designer.md)
|----|:----:|:----:|:----:|
|[Computador local](../articles/machine-learning/how-to-attach-compute-targets.md#local)| Sim | &nbsp; | &nbsp; |
|[Aglomerado de computação Azure Machine Learning](../articles/machine-learning/how-to-create-attach-compute-cluster.md)| Sim | Sim | Sim |
|[Instância de computação do Azure Machine Learning](../articles/machine-learning/how-to-create-manage-compute-instance.md) | Sim (através de SDK)  | Sim |  |
|[VM remoto](../articles/machine-learning/how-to-attach-compute-targets.md#vm) | Sim  | Sim | &nbsp; |
|[Azure &nbsp; Databricks](../articles/machine-learning/how-to-attach-compute-targets.md#databricks)| Sim (apenas modo local SDK) | Sim | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/how-to-attach-compute-targets.md#adla) | &nbsp; | Sim | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/how-to-attach-compute-targets.md#hdinsight) | &nbsp; | Sim | &nbsp; |
|[Azure Batch](../articles/machine-learning/how-to-attach-compute-targets.md#azbatch) | &nbsp; | Sim | &nbsp; |
