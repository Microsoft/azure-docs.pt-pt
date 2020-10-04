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
ms.openlocfilehash: 5d312e5ccc0eeed3e17ffa855c496779c5769c5c
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/04/2020
ms.locfileid: "91707745"
---
**Os objetivos de cálculo podem ser reutilizados de um trabalho de treino para o seguinte**. Por exemplo, uma vez que você anexar um VM remoto ao seu espaço de trabalho, você pode reutilizá-lo para vários trabalhos.  Para os gasodutos de aprendizagem automática, utilize o passo de [gasoduto](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py&preserve-view=true) adequado para cada alvo de cálculo.

Você pode usar qualquer um dos recursos abaixo para um objetivo de computação de formação para a maioria dos empregos.  No entanto, nem todos os recursos podem ser utilizados para aprendizagem automática de máquinas, oleodutos de aprendizagem automática ou designer:

|Metas de formação &nbsp;|[ML Automatizado](../articles/machine-learning/concept-automated-ml.md) | [Pipelines de ML](../articles/machine-learning/concept-ml-pipelines.md) | [Estruturador do Azure Machine Learning](../articles/machine-learning/concept-designer.md)
|----|:----:|:----:|:----:|
|[Computador local](../articles/machine-learning/how-to-attach-compute-targets.md#local)| sim | &nbsp; | &nbsp; |
|[Aglomerado de computação Azure Machine Learning](../articles/machine-learning/how-to-create-attach-compute-cluster.md)| sim | sim | sim |
|[Instância de computação do Azure Machine Learning](../articles/machine-learning/how-to-create-manage-compute-instance.md) | sim (através de SDK)  | sim |  |
|[VM remoto](../articles/machine-learning/how-to-attach-compute-targets.md#vm) | sim  | sim | &nbsp; |
|[Azure &nbsp; Databricks](../articles/machine-learning/how-to-attach-compute-targets.md#databricks)| sim (apenas modo local SDK) | sim | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/how-to-attach-compute-targets.md#adla) | &nbsp; | sim | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/how-to-attach-compute-targets.md#hdinsight) | &nbsp; | sim | &nbsp; |
|[Azure Batch](../articles/machine-learning/how-to-attach-compute-targets.md#azbatch) | &nbsp; | sim | &nbsp; |
