---
title: Sobre a migração de máquinas no local e o Azure VMs do Azure Site Recovery | Documentos da Microsoft
description: Este artigo descreve como migrar VMs IaaS do Azure e no local para o Azure com o serviço Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: raynew
ms.openlocfilehash: 5b7858793ec75d20bb367a6cf49b59fa9906ec9b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66396474"
---
# <a name="about-migration"></a>Acerca da migração

Leia este artigo para uma descrição geral de como o [do Azure Site Recovery](site-recovery-overview.md) serviço ajuda-o para migrar máquinas. 

Eis o que pode migrar com o Site Recovery:

- **Migrar do local para o Azure**: Migre VMs de Hyper-V no local, VMware VMs e servidores físicos para o Azure. Após a migração, as cargas de trabalho em execução nas máquinas no local vão estar em execução em VMs do Azure. 
- **Migrar no Azure**: Migre VMs do Azure entre regiões do Azure. 
- **Migrar o AWS**: Migre instâncias do Windows AWS para VMs IaaS do Azure. 


## <a name="what-do-we-mean-by-migration"></a>O que queremos dizer com migração?

Além de utilizar o Site Recovery para recuperação após desastre de VMs do Azure e no local, pode utilizar o serviço Site Recovery para migrá-los. O que é a diferença?

- Para a recuperação após desastre, replicar máquinas em intervalos regulares para o Azure. Quando ocorre uma falha, pós-falha as máquinas do site primário para o site secundário do Azure e aceder aos mesmos a partir daí. Quando o site primário estiver novamente disponível, efetuar a ativação pós-falha do Azure.
- Para a migração, replicar máquinas no local para o Azure ou VMs do Azure para uma região secundária. Em seguida, falhar a VM através do site primário para o secundário e concluir o processo de migração. Não há nenhuma reativação pós-falha envolvida.  


## <a name="migration-scenarios"></a>Cenários de migração

**Cenário** | **Detalhes**
--- | ---
**Migrar do local para o Azure** | Pode migrar VMs de VMware no local, VMs Hyper-V e servidores físicos para o Azure. Para fazer isso, conclua quase os mesmos passos que faria para recuperação após desastre. Simplesmente não falhar máquinas pós-falha do Azure para o site no local.
**Migrar entre regiões do Azure** | Pode migrar as VMs do Azure de uma região do Azure para outra. Após a migração estar concluída, pode configurar a recuperação de desastres para as VMs do Azure agora na região secundária para a qual migrou.
**Migrar o AWS para o Azure** | Pode migrar instâncias do AWS para VMs do Azure. Site Recovery trata instâncias do AWS como servidores físicos para fins de migração. 

## <a name="next-steps"></a>Passos Seguintes

- [Migrar máquinas no local para o Azure](migrate-tutorial-on-premises-azure.md)
- [Migrar VMs de uma região do Azure para outra](azure-to-azure-tutorial-migrate.md)
- [Migrar o AWS para o Azure](migrate-tutorial-aws-azure.md)
