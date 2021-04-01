---
title: Depreciação do recurso de encriptação de dados de recuperação do site Azure | Microsoft Docs
description: Detalhes regarig Azure Site Recovery data encryption feature
services: site-recovery
author: Sharmistha-Rai
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/15/2019
ms.author: sharrai
ms.openlocfilehash: 5860928d71c0e7431190908d5df5d7496e8ffb17
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "89426321"
---
# <a name="deprecation-of-site-recovery-data-encryption-feature"></a>Depreciação da funcionalidade de encriptação de dados de recuperação de sítios

Este documento descreve os detalhes da depreciação e as medidas de reparação que precisa de tomar se estiver a utilizar a funcionalidade de encriptação de dados de Recuperação do Site, enquanto configura a recuperação de desastres de máquinas virtuais Hiper-V para Azure. 

## <a name="deprecation-information"></a>Informação sobre depreciação


A funcionalidade de encriptação de dados de recuperação do site estava disponível para os clientes que protegem os hiper-V vms para garantir que os dados replicados estavam protegidos contra ameaças de segurança. esta funcionalidade será depreciada até **30 de abril de 2022**. Está a ser substituído pela funcionalidade [de Encriptação mais](https://azure.microsoft.com/blog/azure-site-recovery-encryption-at-rest/) avançada em Repouso, que utiliza [encriptação do serviço de armazenamento](../storage/common/storage-service-encryption.md) (SSE). Com a SSE, os dados são encriptados antes de persistirem no armazenamento e desencriptados na recuperação, e, após a falha para o Azure, os seus VMs serão executados a partir das contas de armazenamento encriptadas, permitindo um melhor objetivo de tempo de recuperação (RTO).

Por favor, note que se for um cliente existente usando esta funcionalidade, teria recebido comunicações com os detalhes de depreciação e medidas de reparação. 


## <a name="what-are-the-implications"></a>Quais são as implicações?

Depois de **30 de abril de 2022**, quaisquer VMs que ainda utilizem a funcionalidade de encriptação reformada não serão autorizados a executar failover. 

## <a name="required-action"></a>Ação necessária
Para continuar as operações de failover bem sucedidas e as replicações seguem os passos abaixo mencionados:

Siga estes passos para cada VM: 
1.  [Desativar a replicação.](./site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario)
2.  [Criar uma nova política de replicação.](./hyper-v-azure-tutorial.md#set-up-a-replication-policy)
3.  [Ativar a replicação](./hyper-v-vmm-azure-tutorial.md#enable-replication) e selecionar uma conta de armazenamento com SSE ativada.

Depois de completar a replicação inicial para contas de armazenamento com SSE ativada, os seus VMs estarão a usar encriptação em repouso com recuperação do site Azure.


## <a name="next-steps"></a>Passos seguintes
Planeie executar os passos de reparação, e executá-los o mais cedo possível. No caso de ter dúvidas sobre esta depreciação, por favor contacte o Microsoft Support. Para ler mais sobre o cenário Hyper-V para Azure, consulte [aqui](hyper-v-vmm-architecture.md).
