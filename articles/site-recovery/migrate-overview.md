---
title: Servidores migratórios e VMs para Azure com recuperação do site Azure
description: Descreve como migrar no local e VMs Azure IaaS para Azure usando o serviço de Recuperação do Local Azure.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: raynew
ms.openlocfilehash: f42942d0e112aad147f78d62b5f5b43587eca9b3
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86135612"
---
# <a name="about-migration"></a>Acerca da migração

Leia este artigo para uma visão geral rápida de como o serviço [de recuperação do local de Azure](site-recovery-overview.md) ajuda a migrar máquinas. 

> [!TIP]
> Deve agora utilizar o serviço Azure Migrate para migrar VMs e servidores para Azure, em vez do serviço de Recuperação do Local Azure. [Saiba mais](../migrate/migrate-services-overview.md).


Aqui está o que pode migrar usando a Recuperação do Local:

- **Migrar de instalações para Azure**: Migrar no local Hiper-V VMs, VMware vMs e servidores físicos para Azure. Após a migração, as cargas de trabalho em execução nas máquinas no local vão estar em execução em VMs do Azure. 
- **Migrar no Azure**: migre VMs do Azure entre regiões do Azure. 
- **Migrar AWS**: migre instâncias do Windows AWS para VMs IaaS do Azure. 

> [!NOTE]
> Pode agora migrar do local para Azure utilizando o serviço Azure Migrate. [Saiba mais](../migrate/migrate-services-overview.md).

## <a name="what-do-we-mean-by-migration"></a>O que queremos dizer com migração?

Além de utilizar a Recuperação do Local para a recuperação de desastres no local e VMs Azure, pode utilizar o serviço de Recuperação do Local para os migrar. Qual a diferença?

- Para a recuperação de desastres, replicas máquinas regularmente para o Azure. Quando ocorre uma paragem, falha as máquinas do local primário para o local secundário do Azure e acede-las a partir daí. Quando o site principal estiver novamente disponível, recua do Azure.
- Para a migração, você replica máquinas no local para Azure, ou VMs Azure para uma região secundária. Em seguida, você falha o VM do local primário para o secundário, e completa o processo de migração. Não há nenhuma reativação pós-falha envolvida.  


## <a name="migration-scenarios"></a>Cenários de migração

**Cenário** | **Detalhes**
--- | ---
**Migrar do local para Azure** | Pode migrar no local VMware VMs, VMs hiper-V e servidores físicos para Azure. Para isso, você completa quase os mesmos passos que faria para a recuperação total de desastres. Simplesmente não se falham nas máquinas de Azure para o local.
**Migrar entre regiões de Azure** | Você pode migrar VMs Azure de uma região de Azure para outra. Após a conclusão da migração, pode configurar a recuperação de desastres para os VMs Azure agora na região secundária para a qual emigrou.
**Migrar o AWS para o Azure** | Pode migrar instâncias do AWS para VMs do Azure. A Recuperação do Site trata os casos AWS como servidores físicos para fins de migração. 

## <a name="next-steps"></a>Próximos passos

- [Migrar máquinas no local para o Azure](migrate-tutorial-on-premises-azure.md)
- [Migrar VMs de uma região do Azure para outra](azure-to-azure-tutorial-migrate.md)
- [Migrar o AWS para o Azure](migrate-tutorial-aws-azure.md)
