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
ms.openlocfilehash: bd3ac8d512c1b9d151c0dc549ffeee6a05c7f94b
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/03/2020
ms.locfileid: "87542812"
---
O alvo de computação que utiliza para hospedar o seu modelo irá afetar o custo e a disponibilidade do seu ponto final implantado. Utilize a tabela abaixo para escolher um alvo de computação apropriado.

| Destino de computação | Utilizado para | Suporte gpu | Suporte FPGA | Descrição |
| ----- | ----- | ----- | ----- | ----- |
| [&nbsp;Serviço web local &nbsp;](../articles/machine-learning/how-to-deploy-local-container-notebook-vm.md) | Teste/depuragem | &nbsp; | &nbsp; | Utilização para testes limitados e resolução de problemas. A aceleração do hardware depende da utilização de bibliotecas no sistema local.
| [Serviço web de computação de casos de aprendizagem de máquinas Azure &nbsp; &nbsp;](../articles/machine-learning/how-to-deploy-local-container-notebook-vm.md) | Teste/depuragem | &nbsp; | &nbsp; | Utilização para testes limitados e resolução de problemas.
| [Azure Kubernetes Service (AKS)](../articles/machine-learning/how-to-deploy-azure-kubernetes-service.md) | Inferência em tempo real |  [Sim](../articles/machine-learning/how-to-deploy-inferencing-gpus.md) (implantação de serviço web) | [Sim](../articles/machine-learning/how-to-deploy-fpga-web-service.md)   |Utilização para implantações de produção de alta escala. Proporciona tempo de resposta rápida e autoscalagem do serviço implantado. A autoescalagem do cluster não é suportada através do Azure Machine Learning SDK. Para alterar os nós no cluster AKS, utilize o UI para o seu cluster AKS no portal Azure. AKS é a única opção disponível para o designer. |
| [Azure Container Instances](../articles/machine-learning/how-to-deploy-azure-container-instance.md) | Teste ou desenvolvimento | &nbsp;  | &nbsp; | Utilize cargas de trabalho baseadas em CPU de baixa escala que exijam menos de 48 GB de RAM. |
| [Clusters de computação do Azure Machine Learning](../articles/machine-learning/how-to-use-parallel-run-step.md) | &nbsp;Inferência do lote | [Sim](../articles/machine-learning/how-to-use-parallel-run-step.md) (pipeline de aprendizagem automática) | &nbsp;  | Executar a pontuação do lote no cálculo sem servidor. Suporta VMs normais e de baixa prioridade. |
| [Funções do Azure](../articles/machine-learning/how-to-deploy-functions.md) | (Pré-visualização) Inferência em tempo real | &nbsp; | &nbsp; | &nbsp; |
| [Azure IoT Edge](../articles/machine-learning/how-to-deploy-and-where.md#iotedge) | (Pré-visualização) &nbsp;Módulo IoT |  &nbsp; | &nbsp; | Implementar e servir modelos ML em dispositivos IoT. |
| [Borda da caixa de dados Azure](../articles/databox-online/azure-stack-edge-overview.md)   | Via IoT Edge |  &nbsp; | Sim | Implementar e servir modelos ML em dispositivos IoT. |

> [!NOTE]
> Embora metas de cálculo como a instância local, Azure Machine Learning compute instance, e Azure Machine Learning clusters suportam GPU para treino e experimentação, usando GPU para inferência __quando implementado como um serviço web__ é suportado apenas no Serviço Azure Kubernetes.
>
> A utilização de uma GPU para inferência __ao marcar com um pipeline de aprendizagem automática__ é suportada apenas no Azure Machine Learning Compute.

> [!NOTE]
> * As instâncias do recipiente Azure (ACI) são adequadas apenas para pequenos modelos com menos de 1 GB de tamanho. 
> * Recomendamos a utilização de clusters de serviço de azure Kubernetes (AKS) de nó único para teste dev de modelos maiores.