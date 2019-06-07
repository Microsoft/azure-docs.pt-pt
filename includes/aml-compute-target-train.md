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
ms.date: 05/30/2019
ms.openlocfilehash: 50905eb987defac612f1055b450b682726f0a56f
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/07/2019
ms.locfileid: "66752978"
---
|Treinamento &nbsp;destinos| Suporte GPU |[ML automatizada](../articles/machine-learning/service/concept-automated-ml.md) | [Pipelines de ML](../articles/machine-learning/service/concept-ml-pipelines.md) | [Visual interface](../articles/machine-learning/service/ui-concept-visual-interface.md)
|----|:----:|:----:|:----:|:----:|
|[Computador local](../articles/machine-learning/service/how-to-set-up-training-targets.md#local)| Talvez | sim | &nbsp; | &nbsp; |
|[Computação do Azure Machine Learning](../articles/machine-learning/service/how-to-set-up-training-targets.md#amlcompute)| sim | Sim & <br/>hyperparameter&nbsp;tuning | sim | sim |
|[VM remota](../articles/machine-learning/service/how-to-set-up-training-targets.md#vm) |sim | Sim & <br/>a otimização de hiper-parâmetros | sim | &nbsp; |
|[Azure&nbsp;Databricks](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#databricks)| &nbsp; | sim | sim | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#adla)| &nbsp; | &nbsp; | sim | &nbsp; |
|[O Azure HDInsight](../articles/machine-learning/service/how-to-set-up-training-targets.md#hdinsight)| &nbsp; | &nbsp; | sim | &nbsp; |
|[Azure Batch](../articles/machine-learning/service/how-to-set-up-training-targets.md#azbatch)| &nbsp; | &nbsp; | sim | &nbsp; |

**Todos os computação destinos podem ser reutilizados para várias tarefas de formação**. Por exemplo, depois de anexar uma VM remota à área de trabalho, pode reutilizá-lo para várias tarefas.