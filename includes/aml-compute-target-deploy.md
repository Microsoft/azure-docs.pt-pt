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
ms.date: 08/23/2019
ms.openlocfilehash: ba1e0eee5de65364d8e5e762195f7de98384c2a7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76122593"
---
| Destino de computação | Utilizado para | Apoio à GPU | Apoio da FPGA | Descrição |
| ----- | ----- | ----- | ----- | ----- |
| [Serviço&nbsp;&nbsp;web local](../articles/machine-learning/how-to-deploy-and-where.md#local) | Teste/depuração | &nbsp; | &nbsp; | Utilizar para testes limitados e resolução de problemas. A aceleração do hardware depende da utilização de bibliotecas no sistema local.
| [Serviço web web&nbsp;&nbsp;de computação azure machine learning](../articles/machine-learning/how-to-deploy-and-where.md#notebookvm) | Teste/depuração | &nbsp; | &nbsp; | Utilizar para testes limitados e resolução de problemas.
| [Serviço de Kubernetes do Azure (AKS)](../articles/machine-learning/how-to-deploy-and-where.md#aks) | Inferência em tempo real |  [Sim](../articles/machine-learning/how-to-deploy-inferencing-gpus.md) (implantação do serviço web) | [Sim](../articles/machine-learning/how-to-deploy-fpga-web-service.md)   |Utilização para implantações de produção em grande escala. Proporciona tempo de resposta rápido e autoscalcificação do serviço implantado. A autoscalcificação do cluster não é suportada através do Azure Machine Learning SDK. Para alterar os nós no cluster AKS, use o UI para o seu cluster AKS no portal Azure. AKS é a única opção disponível para o designer. |
| [Azure Container Instances](../articles/machine-learning/how-to-deploy-and-where.md#aci) | Teste ou desenvolvimento | &nbsp;  | &nbsp; | Utilizar para cargas de trabalho baseadas em CPU de baixa escala que requerem menos de 48 GB de RAM. |
| [Clusters de computação do Azure Machine Learning](../articles/machine-learning/how-to-use-parallel-run-step.md) | (Pré-visualização) Inferência do lote&nbsp; | [Sim](../articles/machine-learning/how-to-use-parallel-run-step.md) (pipeline de aprendizagem automática) | &nbsp;  | Executar pontuação de lote em computação sem servidor. Suporta VMs normais e de baixa prioridade. |
| [Funções do Azure](../articles/machine-learning/how-to-deploy-functions.md) | (Pré-visualização) Inferência em tempo real | &nbsp; | &nbsp; | &nbsp; |
| [Azure IoT Edge](../articles/machine-learning/how-to-deploy-and-where.md#iotedge) | (Pré-visualização) Módulo IoT&nbsp; |  &nbsp; | &nbsp; | Implementar e servir modelos ML em dispositivos IoT. |
| [Azure Data Box Edge](../articles/databox-online/data-box-edge-overview.md)   | Via Borda IoT |  &nbsp; | Sim | Implementar e servir modelos ML em dispositivos IoT. |

> [!NOTE]
> Embora alvos computacionais como o local, o caso de computação de Machine Learning Azure, e os clusters de computação Azure Machine Learning suportem a GPU para formação e experimentação, usando GPU para inferência __quando implantado como um serviço web__ é suportado apenas no Serviço Azure Kubernetes.
>
> A utilização de um GPU para inferência __ao pontuar com um pipeline__ de aprendizagem automática é suportada apenas na Azure Machine Learning Compute.