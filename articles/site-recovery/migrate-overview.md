---
title: Sobre a migração de máquinas locais e VMs do Azure Azure Site Recovery
description: Este artigo descreve como migrar VMs de IaaS locais e do Azure para o Azure usando o serviço Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: raynew
ms.openlocfilehash: c043950de9565f96d52c848f96efac80385f2321
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/09/2019
ms.locfileid: "70814485"
---
# <a name="about-migration"></a>Acerca da migração

Leia este artigo para obter uma visão geral rápida de como o serviço de [Azure site Recovery](site-recovery-overview.md) ajuda a migrar computadores. 

Veja o que você pode migrar usando Site Recovery:

- **Migrar do local para o Azure**: Migre VMs do Hyper-V locais, VMs VMware e servidores físicos para o Azure. Após a migração, as cargas de trabalho em execução nas máquinas no local vão estar em execução em VMs do Azure. 
- **Migrar no Azure**: Migre VMs do Azure entre regiões do Azure. 
- **Migrar AWS**: Migre instâncias do Windows AWS para VMs IaaS do Azure. 

> [!NOTE]
> Agora você pode migrar do local para o Azure usando o serviço migrações para Azure. [Saiba mais](../migrate/migrate-overview.md).

## <a name="what-do-we-mean-by-migration"></a>O que queremos dizer com migração?

Além de usar Site Recovery para recuperação de desastres de VMs locais e do Azure, você pode usar o serviço de Site Recovery para migrá-los. Qual é a diferença?

- Para a recuperação de desastres, você replica os computadores regularmente para o Azure. Quando ocorre uma interrupção, você realiza o failover dos computadores do site primário para o site secundário do Azure e os acessa a partir daí. Quando o site primário estiver disponível novamente, você realiza o failback do Azure.
- Para a migração, você Replica máquinas locais para o Azure ou VMs do Azure para uma região secundária. Em seguida, faça o failover da VM do site primário para o secundário e conclua o processo de migração. Não há nenhuma reativação pós-falha envolvida.  


## <a name="migration-scenarios"></a>Cenários de migração

**Cenário** | **Detalhes**
--- | ---
**Migrar do local para o Azure** | Você pode migrar VMs VMware locais, VMs Hyper-V e servidores físicos para o Azure. Para fazer isso, você completa quase as mesmas etapas que faria para a recuperação completa de desastres. Você simplesmente não faz failback de computadores do Azure para o site local.
**Migrar entre regiões do Azure** | Você pode migrar VMs do Azure de uma região do Azure para outra. Depois que a migração for concluída, você poderá configurar a recuperação de desastre para as VMs do Azure agora na região secundária para a qual você migrou.
**Migrar o AWS para o Azure** | Pode migrar instâncias do AWS para VMs do Azure. Site Recovery trata as instâncias de AWS como servidores físicos para fins de migração. 

## <a name="next-steps"></a>Passos seguintes

- [Migrar máquinas no local para o Azure](migrate-tutorial-on-premises-azure.md)
- [Migrar VMs de uma região do Azure para outra](azure-to-azure-tutorial-migrate.md)
- [Migrar o AWS para o Azure](migrate-tutorial-aws-azure.md)
