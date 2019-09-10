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
ms.openlocfilehash: 29da4ab949cf38419bc7a4d0a3b5da6669441887
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70847724"
---
| Destino de computação | Utilizado para | Suporte de GPU | Suporte do FPGA | Descrição |
| ----- | ----- | ----- | ----- | ----- |
| [Serviço&nbsp;Web&nbsp;local](../articles/machine-learning/service/how-to-deploy-and-where.md#local) | Teste/depuração | &nbsp; | &nbsp; | Use para teste e solução de problemas limitados. A aceleração de hardware depende do uso de bibliotecas no sistema local.
| [Serviço Web&nbsp;da&nbsp;VM do notebook](../articles/machine-learning/service/how-to-deploy-and-where.md#notebookvm) | Teste/depuração | &nbsp; | &nbsp; | Use para teste e solução de problemas limitados.
| [Serviço Kubernetes do Azure (AKS)](../articles/machine-learning/service/how-to-deploy-and-where.md#aks) | Inferência em tempo real |  [Sim](../articles/machine-learning/service/how-to-deploy-inferencing-gpus.md) (implantação de serviço Web) | [Sim](../articles/machine-learning/service/how-to-deploy-fpga-web-service.md)   |Use para implantações de produção de grande escala. Fornece tempo de resposta rápido e dimensionamento automático do serviço implantado. O dimensionamento automático do cluster não tem suporte por meio do SDK do Azure Machine Learning. Para alterar os nós no cluster AKS, use a interface do usuário do cluster do AKS no portal do Azure. AKS é a única opção disponível para a interface visual. |
| [Azure Container Instances](../articles/machine-learning/service/how-to-deploy-and-where.md#aci) | Teste ou desenvolvimento | &nbsp;  | &nbsp; | Use para cargas de trabalho baseadas em CPU de baixa escala que exigem menos de 48 GB de RAM. |
| [Computação do Azure Machine Learning](../articles/machine-learning/service/how-to-run-batch-predictions.md) | Apresentação Inferência de lote&nbsp; | [Sim](../articles/machine-learning/service/how-to-run-batch-predictions.md) (pipeline do Machine Learning) | &nbsp;  | Executar a pontuação de lote em computação sem servidor. Dá suporte a VMs normais e de baixa prioridade. |
| [Azure IoT Edge](../articles/machine-learning/service/how-to-deploy-and-where.md#iotedge) | Apresentação Módulo&nbsp;IOT |  &nbsp; | &nbsp; | Implante e sirva modelos de ML em dispositivos IoT. |
| [Azure Data Box Edge](../articles/databox-online/data-box-edge-overview.md)   | Via IoT Edge |  &nbsp; | Sim | Implante e sirva modelos de ML em dispositivos IoT. |

> [!NOTE]
> Embora os destinos de computação, como local, VM de notebook, e Azure Machine Learning a GPU de suporte de computação para treinamento e experimentação, usar a GPU para inferência __quando implantado como um serviço Web__ tem suporte apenas no serviço kubernetes do Azure.
>
> Usar uma GPU para inferência __quando a pontuação com um pipeline de aprendizado de máquina__ tem suporte apenas em Azure Machine Learning computação.