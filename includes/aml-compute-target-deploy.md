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
ms.date: 06/25/2020
ms.openlocfilehash: 82234719320f1ac707147c7c8393c0464956dd99
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85378389"
---
| Destino de computação | Utilizado para | Suporte gpu | Suporte FPGA | Descrição |
| ----- | ----- | ----- | ----- | ----- |
| [&nbsp;Serviço web local &nbsp;](../articles/machine-learning/how-to-deploy-and-where.md#local) | Teste/depuragem | &nbsp; | &nbsp; | Utilização para testes limitados e resolução de problemas. A aceleração do hardware depende da utilização de bibliotecas no sistema local.
| [Serviço web de computação de casos de aprendizagem de máquinas Azure &nbsp; &nbsp;](../articles/machine-learning/how-to-deploy-and-where.md#notebookvm) | Teste/depuragem | &nbsp; | &nbsp; | Utilização para testes limitados e resolução de problemas.
| [Azure Kubernetes Service (AKS)](../articles/machine-learning/how-to-deploy-and-where.md#aks) | Inferência em tempo real |  [Sim](../articles/machine-learning/how-to-deploy-inferencing-gpus.md) (implantação de serviço web) | [Sim](../articles/machine-learning/how-to-deploy-fpga-web-service.md)   |Utilização para implantações de produção de alta escala. Proporciona tempo de resposta rápida e autoscalagem do serviço implantado. A autoescalagem do cluster não é suportada através do Azure Machine Learning SDK. Para alterar os nós no cluster AKS, utilize o UI para o seu cluster AKS no portal Azure. AKS é a única opção disponível para o designer. |
| [Azure Container Instances](../articles/machine-learning/how-to-deploy-and-where.md#aci) | Teste ou desenvolvimento | &nbsp;  | &nbsp; | Utilize cargas de trabalho baseadas em CPU de baixa escala que exijam menos de 48 GB de RAM. |
| [Clusters de computação do Azure Machine Learning](../articles/machine-learning/how-to-use-parallel-run-step.md) | &nbsp;Inferência do lote | [Sim](../articles/machine-learning/how-to-use-parallel-run-step.md) (pipeline de aprendizagem automática) | &nbsp;  | Executar a pontuação do lote no cálculo sem servidor. Suporta VMs normais e de baixa prioridade. |
| [Funções do Azure](../articles/machine-learning/how-to-deploy-functions.md) | (Pré-visualização) Inferência em tempo real | &nbsp; | &nbsp; | &nbsp; |
| [Azure IoT Edge](../articles/machine-learning/how-to-deploy-and-where.md#iotedge) | (Pré-visualização) &nbsp;Módulo IoT |  &nbsp; | &nbsp; | Implementar e servir modelos ML em dispositivos IoT. |
| [Borda da caixa de dados Azure](../articles/databox-online/azure-stack-edge-overview.md)   | Via IoT Edge |  &nbsp; | Sim | Implementar e servir modelos ML em dispositivos IoT. |

> [!NOTE]
> Embora metas de cálculo como a instância local, Azure Machine Learning compute instance, e Azure Machine Learning clusters suportam GPU para treino e experimentação, usando GPU para inferência __quando implementado como um serviço web__ é suportado apenas no Serviço Azure Kubernetes.
>
> A utilização de uma GPU para inferência __ao marcar com um pipeline de aprendizagem automática__ é suportada apenas no Azure Machine Learning Compute.