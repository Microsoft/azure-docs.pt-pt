---
title: Servidores migratórios e VMs para Azure com recuperação do site Azure
description: Descreve como migrar no local e VMs Azure IaaS para Azure usando o serviço de recuperação de locais Azure.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: raynew
ms.openlocfilehash: a7107eae5c798deb78d4d35eccdf4adcf5273335
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80388904"
---
# <a name="about-migration"></a>Acerca da migração

Leia este artigo para uma visão geral rápida de como o serviço de recuperação de [sites Azure](site-recovery-overview.md) o ajuda a migrar máquinas. 

> [!TIP]
> Deverá agora utilizar o serviço Azure Migrate para migrar VMs e servidores para o Azure, em vez do serviço de Recuperação do Site Azure. [Saiba mais](../migrate/migrate-services-overview.md).


Aqui está o que você pode migrar usando a Recuperação do Site:

- **Migrar de instalações para Azure**: Migrar no local Hiper-V VMs, VMs VMware e servidores físicos para Azure. Após a migração, as cargas de trabalho em execução nas máquinas no local vão estar em execução em VMs do Azure. 
- **Migrar no Azure**: migre VMs do Azure entre regiões do Azure. 
- **Migrar AWS**: migre instâncias do Windows AWS para VMs IaaS do Azure. 

> [!NOTE]
> Agora pode migrar de instalações para Azure usando o serviço Azure Migrate. [Saiba mais](../migrate/migrate-overview.md).

## <a name="what-do-we-mean-by-migration"></a>O que queremos dizer com migração?

Além de utilizar a Recuperação do Site para a recuperação de desastres nas instalações e vMs azure, pode utilizar o serviço de Recuperação do Site para os migrar. Qual a diferença?

- Para a recuperação de desastres, você replica máquinas regularmente para Azure. Quando ocorre uma paragem, falha-se as máquinas do local primário para o local secundário do Azure e acede-as a partir daí. Quando o site principal estiver disponível novamente, você ressatique de Azure.
- Para a migração, você replica máquinas no local para Azure, ou VMs Azure para uma região secundária. Depois falha-se o VM do local primário para o secundário e completa-se o processo de migração. Não há nenhuma reativação pós-falha envolvida.  


## <a name="migration-scenarios"></a> Cenários de migração

**Cenário** | **Detalhes**
--- | ---
**Migrar de instalações para Azure** | Pode migrar vMs no local, VMs hiper-V e servidores físicos para o Azure. Para isso, completa-se quase os mesmos passos que faria para a recuperação total de desastres. Simplesmente não falha saquede máquinas de Azure para o local.
**Migrar entre as regiões de Azure** | Pode migrar VMs Azure de uma região de Azure para outra. Após a migração estar completa, pode configurar a recuperação de desastres para os VMs Azure agora na região secundária para onde emigrou.
**Migrar o AWS para o Azure** | Pode migrar instâncias do AWS para VMs do Azure. A Recuperação do Site trata os casos de AWS como servidores físicos para efeitos de migração. 

## <a name="next-steps"></a>Passos seguintes

- [Migrar máquinas no local para o Azure](migrate-tutorial-on-premises-azure.md)
- [Migrar VMs de uma região do Azure para outra](azure-to-azure-tutorial-migrate.md)
- [Migrar o AWS para o Azure](migrate-tutorial-aws-azure.md)
