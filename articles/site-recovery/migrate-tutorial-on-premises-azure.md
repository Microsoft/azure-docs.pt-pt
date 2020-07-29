---
title: Migrar máquinas no local com recuperação do local de Azure
description: Este artigo descreve como migrar as máquinas no local para o Azure, com o Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/27/2020
ms.author: raynew
ms.openlocfilehash: 3c421845d4e15ef13ce98d0de111270159f564fe
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87285361"
---
# <a name="migrate-on-premises-machines-to-azure"></a>Migrar máquinas no local para o Azure

Este artigo descreve opções para migrar máquinas no local para Azure. 

## <a name="migrate-with-azure-migrate"></a>Migrar com Azure Migrar

Recomendamos que emigure as máquinas para Azure utilizando o serviço [Azure Migrate.](../migrate/migrate-services-overview.md) A Azure Migrate fornece um centro centralizado para avaliação e migração de máquinas no local para Azure, utilizando Azure Migrate, outros serviços Azure e ferramentas de terceiros.

Siga estes links para migrar com Azure Migrate:

- [Saiba mais sobre](../migrate/server-migrate-overview.md) as opções de migração para VMware VMs e, em seguida, migrar VMs para Azure com migração [sem agente](../migrate/tutorial-migrate-vmware.md) ou baseada [em agentes.](../migrate/tutorial-migrate-vmware-agent.md)
- [Migrar os Hiper-V VMs](../migrate/tutorial-migrate-hyper-v.md) para Azure.
- Migrar [servidores físicos ou outros VMs,](../migrate/tutorial-migrate-physical-virtual-machines.md)incluindo [instâncias AWS](../migrate/tutorial-migrate-aws-virtual-machines.md) para Azure.

## <a name="migrate-with-site-recovery"></a>Migrar com recuperação do local
A recuperação do local deve ser usada apenas para a recuperação de desastres, e não para a migração.

Se já está a usar a Recuperação do Sítio Azure e pretende continuar a usá-lo para migração, siga os mesmos passos que usa para a recuperação de desastres.

- VMware VMs: [Prepare o Azure](tutorial-prepare-azure.md) e [o VMware,](vmware-azure-tutorial-prepare-on-premises.md)comece [a replicar máquinas,](vmware-azure-tutorial.md) [verifique](tutorial-dr-drill-azure.md) se está tudo a funcionar e faça [uma falha](vmware-azure-tutorial-failover-failback.md).
- Hiper-V VMs: [Prepare o Azure](tutorial-prepare-azure-for-hyperv.md) e [o Hyper-V,](hyper-v-prepare-on-premises-tutorial.md)comece [a replicar máquinas,](hyper-v-azure-tutorial.md) [verifique](tutorial-dr-drill-azure.md) se está tudo a funcionar e faça [uma falha.](hyper-v-azure-failover-failback-tutorial.md)
- Servidores físicos: [Siga a passagem](physical-azure-disaster-recovery.md) para preparar o Azure, prepare máquinas para a recuperação de desastres e confende a replicação.

> [!NOTE]
> Quando se corre um fracasso para a recuperação de desastres, como último passo, comete-se o fracasso. Quando se migram para as máquinas no local, a opção **"Comprometer"** não é relevante. Em vez disso, selecione a opção **Migração Completa.** 

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Reveja questões comuns](../migrate/resources-faq.md) sobre Azure Migrate.

  
