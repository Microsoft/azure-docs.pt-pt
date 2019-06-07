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
ms.openlocfilehash: a463ac9f9584cb13cadbcf79674d56b2f8e47c2c
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/07/2019
ms.locfileid: "66753094"
---
| Destino de computação | Utilização | Suporte GPU | Suporte FPGA | Descrição |
| ----- | ----- | ----- | ----- | ----- |
| [Serviço local web](../articles/machine-learning/service/how-to-deploy-and-where.md#local) | Teste/depuração | Talvez | &nbsp; | Vale a limitado de teste e resolução de problemas. Aceleração de hardware depende da utilização de bibliotecas no sistema local.
| [Serviço Kubernetes do Azure (AKS)](../articles/machine-learning/service/how-to-deploy-and-where.md#aks) | Inferência de tipos em tempo real |  [Sim](../articles/machine-learning/service/how-to-deploy-inferencing-gpus.md)  | [Sim](../articles/machine-learning/service/how-to-deploy-fpga-web-service.md)   |Ideal para implementações de produção de grande escala. Fornece o dimensionamento automático e tempos de resposta rápidos.  AKS é a única opção disponível para a interface visual. |
| [Azure Container Instances (ACI)](../articles/machine-learning/service/how-to-deploy-and-where.md#aci) | Teste ou desenvolvimento | &nbsp;  | &nbsp; | Bom, de pequena escala, com base na CPU cargas de trabalho requerem < 48 GB de RAM |
| [Computação do Azure Machine Learning](../articles/machine-learning/service/how-to-run-batch-predictions.md) | (Pré-visualização) Inferência de tipos do batch | sim | &nbsp;  | Execute a computação sem servidor de classificação de batch. Suporta VMs normais e de baixa prioridade. |
| [Azure IoT Edge](../articles/machine-learning/service/how-to-deploy-and-where.md#iotedge) | (Pré-visualização) Módulo de IoT |  &nbsp; | &nbsp; | Implementar e servir os modelos de ML em dispositivos IoT. |
| [Edge de caixa de dados do Azure](../articles/databox-online/data-box-edge-overview.md)   | via IoT Edge |  &nbsp; | sim | Implementar e servir os modelos de ML em dispositivos IoT. |