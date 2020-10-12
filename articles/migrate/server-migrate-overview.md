---
title: Selecione uma opção de migração VMware com Azure Migrate Server Migration / Microsoft Docs
description: Fornece uma visão geral das opções para migrar VMware VMs para Azure com Azure Migrate Server Migration
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: e62b9cea80f1ed7f672135b93e52ba606a717a6c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88950226"
---
# <a name="select-a-vmware-migration-option"></a>Selecione uma opção de migração VMware

Pode migrar VMware VMs para Azure utilizando a ferramenta de migração do servidor Azure Migrate. Esta ferramenta oferece um par de opções para a migração VMware VM:

- Migração usando replicação sem agente. Migrar VMs sem precisar de instalar nada neles.
- Migração com um agente para replicação. Instale um agente no VM para replicação.


## <a name="compare-migration-methods"></a>Comparar métodos de migração

Utilize estas comparações selecionadas para ajudá-lo a decidir qual o método a utilizar. Também pode rever os requisitos completos de suporte para migração [sem agentes](migrate-support-matrix-vmware-migration.md#agentless-migration) e [baseada em agentes.](migrate-support-matrix-vmware-migration.md#agent-based-migration)

**Definição** | **Sem agente** | **Baseada em agente**
--- | --- | ---
**Permissões Azure** | Precisa de permissões para criar um projeto Azure Migrate e para registar aplicações AD AZure criadas quando implementa o aparelho Azure Migrate. | Precisa de permissões do Contribuinte na assinatura Azure. 
**Replicação** | Um máximo de 300 VMs pode ser replicado simultaneamente a partir de um servidor vCenter.<br/> Se tiver mais de 50 VMs para migração, crie vários lotes de VMs.<br/> Replicar mais num único momento terá impacto no desempenho.<br/><br/> No portal, pode selecionar até 10 máquinas ao mesmo tempo para replicação. Para replicar mais máquinas, adicione em lotes de 10.| A capacidade de replicação aumenta escalando o aparelho de replicação.
**Implantação do aparelho** | O [aparelho Azure Migrate](migrate-appliance.md) está implantado no local. | O [aparelho de replicação Azure Migrate](migrate-replication-appliance.md) está implantado no local.
**Compatibilidade de Recuperação de Sítio** | É compatível. | Não é possível replicar com a migração do servidor Azure Migrate se tiver configurado a replicação para uma máquina que utiliza a Recuperação do Site.
**Disco-alvo** | Managed disks | Managed disks
**Limites de disco** | Disco de os: 2 TB<br/><br/> Disco de dados: 8 TB<br/><br/> Discos máximos: 60 | Disco de os: 2 TB<br/><br/> Disco de dados: 8 TB<br/><br/> Discos máximos: 63
**Discos passthrough** | Não suportado | Suportado
**Bota UEFI** | Não suportado | O VM migrado em Azure será automaticamente convertido para um VM de arranque BIOS.<br/><br/> O disco de so deve ter até quatro divisórias, e os volumes devem ser formatados com NTFS.

## <a name="compare-deployment-steps"></a>Compare etapas de implementação

Depois de rever as limitações, compreender os passos envolvidos na implementação de cada solução pode ajudá-lo a decidir qual a opção a escolher.

**Tarefa** | **Detalhes** |**Sem agente** | **Baseada em agente**
--- | --- | --- | ---
**Implementar o aparelho Azure Migrate** | Um aparelho leve que funciona com um VMware VM.<br/><br/> O aparelho é utilizado para descobrir e avaliar máquinas e migrar máquinas utilizando uma migração sem agentes. | Obrigatório.<br/><br/> Se já montou o aparelho para avaliação, pode utilizar o mesmo aparelho para migração sem agentes. | Não é necessário.<br/><br/> Se tiver configurado um aparelho para avaliação, pode deixá-lo no lugar ou removê-lo se tiver terminado a avaliação.
**Utilize a ferramenta de avaliação do servidor** | Avaliar máquinas com a ferramenta Azure Migrate:Server Assessment. | Podes avaliar as máquinas antes de as migrares, mas não tens de o fazer. | A avaliação é opcional | A avaliação é opcional.
**Utilize a ferramenta de migração do servidor** | Adicione a ferramenta de migração do servidor Azure Migrate no projeto Azure Migrate. | Necessário | Necessário
**Preparar VMware para migração** | Configurar as definições nos servidores VMware e VMs. | Necessário | Necessário
**Instalar o serviço de Mobilidade em VMs** | O serviço de mobilidade funciona em cada VM que pretende replicar | não é necessário | Necessário
**Implementar o aparelho de replicação** | O [aparelho de replicação](migrate-replication-appliance.md) é utilizado para migração baseada em agentes. Liga-se entre o serviço de Mobilidade em execução em VMs e a Migração de Servidores. | não é necessário | Necessário
**VMs replicados**. Ativar a replicação de VM. | Configurar definições de replicação e selecionar VMs para replicar | Necessário | Necessário
**Executar uma migração de teste** | Faça um teste de migração para garantir que tudo está funcionando como esperado. | Necessário | Necessário
**Executar uma migração completa** | Migrar os VMs. | Necessário | Necessário



## <a name="next-steps"></a>Passos seguintes

[Migrar VMware VMs](tutorial-migrate-vmware.md) com migração sem agentes.



