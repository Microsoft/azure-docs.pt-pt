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
ms.date: 12/11/2020
ms.openlocfilehash: 09dd6e9a9d69797c2c33270d1620e861a052efe2
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/15/2020
ms.locfileid: "97505125"
---
O alvo de computação que utiliza para hospedar o seu modelo irá afetar o custo e a disponibilidade do seu ponto final implantado. Utilize esta tabela para escolher um alvo de computação apropriado.

| Destino de computação | Utilizado para | Suporte gpu | Suporte FPGA | Descrição |
| ----- | ----- | ----- | ----- | ----- |
| [&nbsp;Serviço web local &nbsp;](../articles/machine-learning/how-to-deploy-local-container-notebook-vm.md) | Teste/depuragem | &nbsp; | &nbsp; | Utilização para testes limitados e resolução de problemas. A aceleração do hardware depende da utilização de bibliotecas no sistema local.
| [Azure Kubernetes Service (AKS)](../articles/machine-learning/how-to-deploy-azure-kubernetes-service.md) | Inferência em tempo real |  [Sim](../articles/machine-learning/how-to-deploy-inferencing-gpus.md) (implantação de serviço web) | [Sim](../articles/machine-learning/how-to-deploy-fpga-web-service.md)   |Utilização para implantações de produção de alta escala. Proporciona tempo de resposta rápida e autoscalagem do serviço implantado. A autoescalagem do cluster não é suportada através do Azure Machine Learning SDK. Para alterar os nós no cluster AKS, utilize o UI para o seu cluster AKS no portal Azure. <br/><br/> Apoiado no designer. |
| [Azure Container Instances](../articles/machine-learning/how-to-deploy-azure-container-instance.md) | Teste ou desenvolvimento | &nbsp;  | &nbsp; | Utilize cargas de trabalho baseadas em CPU de baixa escala que exijam menos de 48 GB de RAM. <br/><br/> Apoiado no designer. |
| [Clusters de computação do Azure Machine Learning](../articles/machine-learning/tutorial-pipeline-batch-scoring-classification.md) | &nbsp;Inferência do lote | [Sim](../articles/machine-learning/tutorial-pipeline-batch-scoring-classification.md) (pipeline de aprendizagem automática) | &nbsp;  | Executar a pontuação do lote no cálculo sem servidor. Suporta VMs normais e de baixa prioridade. Sem apoio para inferência em tempo real.|

> [!NOTE]
> Embora metas de cálculo como o local, o cálculo de Azure Machine Learning e os clusters de cálculo Azure Machine Learning apoiem a GPU para treino e experimentação, usando a GPU para inferência _quando implantada como um serviço web_ é suportada apenas em AKS.
>
> A utilização de uma GPU para inferência _ao marcar com um pipeline de aprendizagem automática_ é suportada apenas no cálculo Azure Machine Learning.
> 
> Ao escolher um cluster SKU, primeiro escalone e depois escale para fora. Comece com uma máquina que tenha 150% da RAM que o seu modelo necessita, perfile o resultado e encontre uma máquina que tenha o desempenho de que necessita. Assim que aprenderes isso, aumenta o número de máquinas para se adaptar à tua necessidade de inferência simultânea.

> [!NOTE]
> * As instâncias do contentor são adequadas apenas para pequenos modelos com menos de 1 GB de tamanho.
> * Utilize clusters AKS de nó único para dev/teste de modelos maiores.