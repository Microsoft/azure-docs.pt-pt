---
title: Modelos do Azure Resource Manager
description: Modelos do Gestor de Recursos Azure para a utilização de funcionalidades de recuperação do site Azure.
services: site-recovery
author: rishjai
manager: gaggupta
ms.service: site-recovery
ms.topic: article
ms.date: 02/04/2021
ms.author: rishjai
ms.openlocfilehash: efd5c1ab3e23348373be76c79d1b9689bc5f4941
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101720533"
---
# <a name="azure-resource-manager-templates-for-azure-site-recovery"></a>Modelos de gestor de recursos Azure para recuperação do site Azure

A tabela seguinte inclui links para modelos do Gestor de Recursos Azure para a utilização de funcionalidades de Recuperação do Site Azure.

| Modelo | Description |
|---|---|
|**Azure para o Azure** | |
| [Criar um cofre dos Serviços de Recuperação](./quickstart-create-vault-template.md)| Crie um cofre dos Serviços de Recuperação. O cofre pode ser utilizado no Azure Backup e no Azure Site Recovery. |
| [Ativar a replicação de VMs Azure](https://aka.ms/asr-arm-enable-replication) | Ativar a replicação dos VMs Azure utilizando o cofre existente e as definições de alvo personalizadas.|
| [Trigger Failover e Reprotect](https://aka.ms/asr-arm-failover-reprotect) | Desencadeie uma operação Failover e Reprotect para um conjunto de VMs Azure. |
| [Executar um fluxo dr final para VMs Azure](https://aka.ms/asr-arm-e2e-flow) | Inicie um fluxo completo de recuperação de desastres (Ativar a replicação + failover e Reprotect + Failback e Reprotect) para VMs Azure, também chamado de fluxo de 540°.|
|   |   |