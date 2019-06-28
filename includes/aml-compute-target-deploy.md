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
ms.openlocfilehash: 05082544830bad543d6743d016a91ef8565767f4
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/22/2019
ms.locfileid: "67331660"
---
| Destino de computação | Utilização | Suporte GPU | Suporte FPGA | Descrição |
| ----- | ----- | ----- | ----- | ----- |
| [Local&nbsp;web&nbsp;service](../articles/machine-learning/service/how-to-deploy-and-where.md#local) | Teste/depuração | Talvez | &nbsp; | Vale a limitado de teste e resolução de problemas. Aceleração de hardware depende da utilização de bibliotecas no sistema local.
| [Serviço Kubernetes do Azure (AKS)](../articles/machine-learning/service/how-to-deploy-and-where.md#aks) | Inferência de tipos em tempo real |  [Sim](../articles/machine-learning/service/how-to-deploy-inferencing-gpus.md)  | [Sim](../articles/machine-learning/service/how-to-deploy-fpga-web-service.md)   |Ideal para implementações de produção de grande escala. Fornece o tempo de resposta rápida e o dimensionamento automático do serviço implementado. Dimensionamento automático de cluster não é suportado através do SDK do Azure Machine Learning. Para alterar os nós do cluster do AKS, utilize a interface do Usuário do seu cluster do AKS no portal do Azure. AKS é a única opção disponível para a interface visual. |
| [Azure Container Instances (ACI)](../articles/machine-learning/service/how-to-deploy-and-where.md#aci) | Teste ou desenvolvimento | &nbsp;  | &nbsp; | Bom, de pequena escala, com base na CPU cargas de trabalho requerem < 48 GB de RAM |
| [Computação do Azure Machine Learning](../articles/machine-learning/service/how-to-run-batch-predictions.md) | (Pré-visualização) Batch&nbsp;inferência de tipos | sim | &nbsp;  | Execute a computação sem servidor de classificação de batch. Suporta VMs normais e de baixa prioridade. |
| [Azure IoT Edge](../articles/machine-learning/service/how-to-deploy-and-where.md#iotedge) | (Pré-visualização) IoT&nbsp;módulo |  &nbsp; | &nbsp; | Implementar e servir os modelos de ML em dispositivos IoT. |
| [Edge de caixa de dados do Azure](../articles/databox-online/data-box-edge-overview.md)   | via IoT Edge |  &nbsp; | sim | Implementar e servir os modelos de ML em dispositivos IoT. |
