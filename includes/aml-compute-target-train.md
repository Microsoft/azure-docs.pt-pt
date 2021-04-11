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
ms.date: 09/17/2020
ms.openlocfilehash: 3eb5ea468a234aea228539c2390ab6cae9352948
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105630199"
---
**Os alvos de cálculo podem ser reutilizados de um trabalho de treino para o outro.** Por exemplo, depois de anexar um VM remoto ao seu espaço de trabalho, pode reutilizá-lo para vários trabalhos. Para os gasodutos de aprendizagem automática, utilize o passo de [gasoduto](/python/api/azureml-pipeline-steps/azureml.pipeline.steps) adequado para cada alvo de cálculo.

Você pode usar qualquer um dos seguintes recursos para um alvo de computação de formação para a maioria dos empregos. Nem todos os recursos podem ser usados para aprendizagem automática de máquinas, oleodutos de aprendizagem automática ou designer.

|Metas de formação &nbsp;|[Aprendizagem automática automatizada](../articles/machine-learning/concept-automated-ml.md) | [Pipelines de aprendizagem automática](../articles/machine-learning/concept-ml-pipelines.md) | [Estruturador do Azure Machine Learning](../articles/machine-learning/concept-designer.md)
|----|:----:|:----:|:----:|
|[Computador local](../articles/machine-learning/how-to-attach-compute-targets.md#local)| Yes | &nbsp; | &nbsp; |
|[Aglomerado de computação Azure Machine Learning](../articles/machine-learning/how-to-create-attach-compute-cluster.md)| Yes | Yes | Yes |
|[Instância de computação do Azure Machine Learning](../articles/machine-learning/how-to-create-manage-compute-instance.md) | Sim (através de SDK)  | Yes |  |
|[VM remoto](../articles/machine-learning/how-to-attach-compute-targets.md#vm) | Yes  | Yes | &nbsp; |
|[Azure &nbsp; Databricks](../articles/machine-learning/how-to-attach-compute-targets.md#databricks)| Sim (apenas modo local SDK) | Yes | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/how-to-attach-compute-targets.md#adla) | &nbsp; | Yes | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/how-to-attach-compute-targets.md#hdinsight) | &nbsp; | Yes | &nbsp; |
|[Azure Batch](../articles/machine-learning/how-to-attach-compute-targets.md#azbatch) | &nbsp; | Yes | &nbsp; |

> [!TIP]
> A instância compute tem disco OS de 120GB. Se ficar sem espaço em disco, [utilize o terminal](../articles/machine-learning/how-to-access-terminal.md) para limpar pelo menos 1-2 GB antes de parar ou [reiniciar](../articles/machine-learning/how-to-create-manage-compute-instance.md#manage) a instância de cálculo.
