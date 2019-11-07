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
ms.openlocfilehash: 2591ab6984ebe4f864540ab290881a6e47f0be71
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73580604"
---
**Os destinos de computação podem ser reutilizados de um trabalho de treinamento para o próximo**. Por exemplo, depois de anexar uma VM remota ao seu espaço de trabalho, você pode reutilizá-la para vários trabalhos.

|Treinamento &nbsp;destinos| Suporte de GPU |[ML automatizado](../articles/machine-learning/service/concept-automated-ml.md) | [Pipelines de ML](../articles/machine-learning/service/concept-ml-pipelines.md) | [Designer de Azure Machine Learning](../articles/machine-learning/service/concept-designer.md)
|----|:----:|:----:|:----:|:----:|
|[Computador local](../articles/machine-learning/service/how-to-set-up-training-targets.md#local)| Eu | sim | &nbsp; | &nbsp; |
|[Azure Machine Learning cluster de computação](../articles/machine-learning/service/how-to-set-up-training-targets.md#amlcompute)| sim | Sim & <br/>ajuste de&nbsp;de hiperparâmetro | sim | sim |
|[VM remota](../articles/machine-learning/service/how-to-set-up-training-targets.md#vm) |sim | Sim & <br/>ajuste de hiperparâmetro | sim | &nbsp; |
|[Azure&nbsp;databricks](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#databricks)| &nbsp; | sim | sim | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#adla)| &nbsp; | &nbsp; | sim | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/service/how-to-set-up-training-targets.md#hdinsight)| &nbsp; | &nbsp; | sim | &nbsp; |
|[Azure Batch](../articles/machine-learning/service/how-to-set-up-training-targets.md#azbatch)| &nbsp; | &nbsp; | sim | &nbsp; |
