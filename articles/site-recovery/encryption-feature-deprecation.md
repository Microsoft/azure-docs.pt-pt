---
title: Deprecation da funcionalidade de encriptação de dados de recuperação de sites do Azure [ Microsoft Docs
description: Detalhes regarig Azure Site Recovery data encryption
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/15/2019
ms.author: rajanaki
ms.openlocfilehash: 5e74466891a5926d8ae8feb3c1c48348ecf3cfe6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74134999"
---
# <a name="deprecation-of-site-recovery-data-encryption-feature"></a>Deprecation da funcionalidade de encriptação de dados de recuperação do site

Este documento descreve os detalhes de depreciação e a ação de reparação que precisa de tomar se estiver a utilizar a funcionalidade de encriptação de dados de recuperação do site enquanto configura a recuperação de desastres de máquinas virtuais Hyper-V para O Azure. 

## <a name="deprecation-information"></a>Informação de depreciação


A funcionalidade de encriptação de dados de recuperação do site estava disponível para os clientes que protegem os vms Hyper-V para garantir que os dados replicados estavam protegidos contra ameaças à segurança. esta funcionalidade será depreciada até 30 de dezembro de **2019.** Está a ser substituído pela funcionalidade de encriptação mais avançada [em Rest,](https://azure.microsoft.com/blog/azure-site-recovery-encryption-at-rest/) que utiliza [encriptação](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) do serviço de armazenamento (SSE). Com a SSE, os dados são encriptados antes de persistirem no armazenamento e desencriptados na recuperação, e, após a falha com o Azure, os seus VMs serão executados a partir das contas de armazenamento encriptadas, permitindo um melhor objetivo de tempo de recuperação (RTO).

Por favor, note que se for um cliente existente usando esta funcionalidade, teria recebido comunicações com os detalhes de depreciação e medidas de reparação. 


## <a name="what-are-the-implications"></a>Quais são as implicações?

Depois de 30 de dezembro de **2019**, quaisquer VMs que ainda utilizem a funcionalidade de encriptação aposentada não serão autorizados a realizar falhas. 

## <a name="required-action"></a>Ação necessária
Para continuar com as operações de failover bem sucedidas, e as replicações sigam os passos mencionados abaixo:

Siga estes passos para cada VM: 
1.  [Desativar a replicação.](https://docs.microsoft.com/azure/site-recovery/site-recovery-manage-registration-and-protection#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario)
2.  Criar uma nova política de [replicação.](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-tutorial#set-up-a-replication-policy)
3.  [Ative a replicação](https://docs.microsoft.com/azure/site-recovery/hyper-v-vmm-azure-tutorial#enable-replication) e selecione uma conta de armazenamento com SSE ativada.

Depois de concluir a replicação inicial para contas de armazenamento com SSE ativada, os seus VMs estarão a utilizar encriptação em Repouso com a Recuperação do Site Azure.


## <a name="next-steps"></a>Passos seguintes
Planeie executar os passos de reparação e executá-los o mais cedo possível. Caso tenha alguma dúvida sobre esta depreciação, contacte o Microsoft Support. Para ler mais sobre o cenário Hyper-V para Azure, consulte [aqui](hyper-v-vmm-architecture.md).

