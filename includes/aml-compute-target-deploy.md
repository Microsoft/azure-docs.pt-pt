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
ms.openlocfilehash: c5a6f957cd3f799f9d7eff1df22df2200779ac30
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/10/2019
ms.locfileid: "68946047"
---
| Destino de computação | Utilização | Suporte de GPU | Suporte do FPGA | Descrição |
| ----- | ----- | ----- | ----- | ----- |
| [Serviço&nbsp;Web&nbsp;local](../articles/machine-learning/service/how-to-deploy-and-where.md#local) | Teste/depuração | Eu | &nbsp; | Bom para teste e solução de problemas limitados. A aceleração de hardware depende do uso de bibliotecas no sistema local.
| [Serviço Web&nbsp;da&nbsp;VM do notebook](../articles/machine-learning/service/how-to-deploy-and-where.md#notebookvm) | Teste/depuração | Eu | &nbsp; | Bom para teste e solução de problemas limitados. 
| [Serviço Kubernetes do Azure (AKS)](../articles/machine-learning/service/how-to-deploy-and-where.md#aks) | Inferência em tempo real |  [Ok](../articles/machine-learning/service/how-to-deploy-inferencing-gpus.md)  | [Ok](../articles/machine-learning/service/how-to-deploy-fpga-web-service.md)   |Ideal para implementações de produção de grande escala. Fornece tempo de resposta rápido e dimensionamento automático do serviço implantado. O dimensionamento automático do cluster não tem suporte por meio do SDK do Azure Machine Learning. Para alterar os nós no cluster AKS, use a interface do usuário do cluster do AKS no portal do Azure. AKS é a única opção disponível para a interface visual. |
| [Azure Container Instances (ACI)](../articles/machine-learning/service/how-to-deploy-and-where.md#aci) | Teste ou desenvolvimento | &nbsp;  | &nbsp; | Boa para cargas de trabalho de baixa escala, com base na CPU que exigem < de 48 GB de RAM |
| [Computação do Azure Machine Learning](../articles/machine-learning/service/how-to-run-batch-predictions.md) | Apresentação Inferência de lote&nbsp; | sim | &nbsp;  | Executar a pontuação de lote em computação sem servidor. Dá suporte a VMs normais e de baixa prioridade. |
| [Azure IoT Edge](../articles/machine-learning/service/how-to-deploy-and-where.md#iotedge) | Apresentação Módulo&nbsp;IOT |  &nbsp; | &nbsp; | Implantar & servir modelos ML em dispositivos IoT. |
| [Azure Data Box Edge](../articles/databox-online/data-box-edge-overview.md)   | via IoT Edge |  &nbsp; | sim | Implantar & servir modelos ML em dispositivos IoT. |
