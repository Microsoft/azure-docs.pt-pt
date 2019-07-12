---
title: Selecione uma opção de migração do VMware com o Azure Migrate Server Migration | Documentos da Microsoft
description: Fornece uma descrição geral das opções para migrar VMs de VMware para o Azure com a migração do servidor de migrar do Azure
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: raynew
ms.openlocfilehash: f8bfbe26dc4c6ddbcf622f91938ba060de00b2ec
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811573"
---
# <a name="select-a-vmware-migration-option"></a>Selecione uma opção de migração do VMware

É possível migrar VMs de VMware para o Azure com a ferramenta de migração do servidor de migrar do Azure. Essa ferramenta oferece duas opções para a migração de VM de VMware:

- Migração usando a replicação sem agente. Migre VMs sem precisar instalar nada nos mesmos.
- Migração com um agente de replicação. Instale um agente na VM para replicação.


Embora os replicação sem agente seja mais fácil de uma perspectiva de implantação, atualmente tem várias limitações.

## <a name="agentless-migration-limitations"></a>Limitações de migração sem agente

Limitações são os seguintes:

- **Replicação simultânea**: Um máximo de 50 VMs pode ser replicado em simultâneo a partir de um vCenter Server.<br/> Se tiver mais de 50 VMs para a migração, crie vários lotes de VMs.<br/> Replicar mais de uma única vez irá afetar o desempenho.
- **Discos de VM**: Uma VM que pretende migrar tem de ter discos de 60 ou menos.
- **Sistemas operacionais VM**: Em geral, o Azure Migrate pode migrar qualquer Windows Server ou o sistema operativo Linux, mas podem ser necessárias alterações nas VMs, para que eles podem ser executados no Azure. O Azure Migrate torna as alterações automaticamente para estes sistemas operativos:
    - Red Hat Enterprise Linux 6.5+, 7.0+
    - CentOS 6.5+, 7.0+
    - SUSE Linux Enterprise Server 12 SP1+
    - Ubuntu 14.04LTS, 16.04LTS, 18.04LTS
    - Debian 7,8
    - Para outros sistemas operacionais, precisa fazer ajustes manualmente antes da migração. O [migrar tutorial](tutorial-migrate-vmware.md) explica como fazer isso.
- **Arranque de Linux**: Se for /boot numa partição dedicada, devem residir no disco do SO e não serão distribuído por vários discos.<br/> Se /boot faz parte da partição de raiz (/), em seguida, a partição '/' deve ser no disco do SO e não abrangem outros discos.
- **Arranque UEFI**: As VMs com arranque UEFI não são suportadas para migração.
- **(BitLocker, cryptfs) de discos/volumes encriptados**: As VMs com discos/volumes encriptados não são suportadas para migração.
- **Discos RDM/passthrough**: Se as VMs têm discos RDM ou pass-through, estes discos não ser replicados para o Azure
- **NFS**: Volumes NFS montados como volumes nas VMs não ser replicadas.
- **Armazenamento de destino**: Só é possível migrar VMs de VMware para VMs do Azure com discos geridos (Standard HDD, Premium SSD).



## <a name="deployment-steps-comparison"></a>Comparação de passos de implementação

Depois de rever as limitações, compreender as etapas envolvidas na implantação de cada solução pode ajudar a decidir qual a opção para escolher.

**Tarefa** | **Detalhes** |**Sem agente** | **Com base em agente**
--- | --- | --- | ---
**Preparar servidores VMware e VMs para a migração** | Configure várias definições nos servidores de VMware e VMs. | Necessário | Necessário
**Adicionar a ferramenta de migração do servidor** | Adicione a ferramenta de migração do servidor de migrar do Azure no projeto do Azure Migrate. | Necessário | Necessário
**Implementar a aplicação do Azure Migrate** | Configure um dispositivo de simples numa VM do VMware para a VM deteção e avaliação. | Necessário | Não é necessário.
**Instalar o serviço de mobilidade em VMs** | Instalar o serviço de mobilidade em cada VM que pretende replicar | Não necessário | Necessário
**Implementar a aplicação de replicação de migração do servidor de migrar do Azure** | Configurar uma aplicação numa VM do VMware para detetar VMs e preencher entre o serviço de mobilidade em execução em VMs e migração do servidor de migrar do Azure | Não necessário | Necessário
**Replicar VMs**. Ative a replicação de VM. | Configurar definições de replicação e selecione as VMs para replicar | Necessário | Necessário
**Executar uma migração de teste** | Execute uma migração de teste para se certificar de que está tudo a funcionar conforme esperado. | Necessário | Necessário
**Executar uma migração completa** | Migre as VMs. | Necessário | Necessário




## <a name="next-steps"></a>Passos Seguintes

[Migrar VMs do VMware](tutorial-migrate-vmware.md) utilizando a migração sem agente.



