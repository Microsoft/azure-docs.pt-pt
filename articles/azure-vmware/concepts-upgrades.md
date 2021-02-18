---
title: Conceitos - Atualizações e atualizações em nuvem privadas
description: Conheça os principais processos e funcionalidades de upgrade na Azure VMware Solution.
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: d93453cbf6ad744844a04cd298cc18ad181cc0b0
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100634997"
---
# <a name="azure-vmware-solution-private-cloud-updates-and-upgrades"></a>Atualizações e atualizações privadas de nuvem Azure VMware

Um dos benefícios das nuvens privadas Azure VMware Solution é que a plataforma é mantida para si. A manutenção inclui atualizações automatizadas para um pacote de software validado VMware para ajudar a garantir que está a usar a versão mais recente do software de nuvem privada Azure VMware Solution.

Especificamente, uma nuvem privada Azure VMware Solution inclui:

- Nódoas de servidor de metal nu dedicada aprovisionadas com hipervisor VMware ESXi 
- vCenter servidor para gerir ESXi e vSAN 
- Software VMware NSX-T definido em rede para vSphere carga de trabalho VMs  
- Loja de dados VMware vSAN para vSphere carga de trabalho VMs  
- VMware HCX para mobilidade de carga de trabalho  

Uma nuvem privada Azure VMware Solution também inclui recursos na base Azure necessária para a conectividade e para operar a nuvem privada. A Azure VMware Solution monitoriza continuamente a saúde tanto da base como dos componentes VMware. Quando a Azure VMware Solution deteta uma falha, toma medidas para reparar os componentes falhados. 

## <a name="what-components-get-updated"></a>Que componentes são atualizados?   

A Azure VMware Solution atualiza os seguintes componentes VMware: 

- vCenter Server e ESXi em execução nos nos acenos do servidor de metal nu 
- vSAN 
- NSX-T 

A Azure VMware Solution também atualiza o software na base, como controladores, software nos interruptores de rede e firmware nos nós de metal nu. 

## <a name="types-of-updates"></a>Tipos de atualizações

A Azure VMware Solution aplica os seguintes tipos de atualizações aos componentes VMware:

- Patches: Patches de segurança e correções de erros lançados pela VMware. 
- Atualizações: Atualizações de versão menores de um ou mais componentes VMware. 
- Upgrades: Principais atualizações de versão de um ou mais componentes VMware.

Será notificado antes e depois de os patches serem aplicados às suas nuvens privadas. Também trabalharemos consigo para agendar uma janela de manutenção antes de aplicar atualizações ou atualizações na sua nuvem privada. 

## <a name="vmware-appliance-backup"></a>Backup do aparelho VMware 

A Azure VMware Solution também requer uma cópia de segurança de configuração dos seguintes componentes VMware:

- vCenter Server 
- Gerente da NSX-T 

Em momentos de falha, a Azure VMware Solution pode restaurar estes componentes a partir da cópia de segurança de configuração. 

Para obter mais informações sobre as versões de software VMware, consulte o [artigo de conceito de nuvens e clusters privados](concepts-private-clouds-clusters.md) e o [FAQ](faq.yml).

## <a name="next-steps"></a>Passos seguintes

Agora que cobriu os principais processos e funcionalidades de upgrade na Azure VMware Solution, talvez queira saber mais sobre:

- [Como criar uma nuvem privada.](tutorial-create-private-cloud.md)
- [Como ativar o recurso Azure VMware Solution](enable-azure-vmware-solution.md).

<!-- LINKS - external -->

<!-- LINKS - internal -->
