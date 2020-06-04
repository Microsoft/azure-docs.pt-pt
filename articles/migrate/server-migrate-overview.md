---
title: Selecione uma opção de migração VMware com Azure Migrate Server Migration / Microsoft Docs
description: Fornece uma visão geral das opções para migrar VMware VMs para Azure com Azure Migrate Server Migration
ms.topic: conceptual
ms.date: 06/03/2020
ms.openlocfilehash: 196b80f600d9895e9fcb6471bfb5df5fb5bbe8d1
ms.sourcegitcommit: 58ff2addf1ffa32d529ee9661bbef8fbae3cddec
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/03/2020
ms.locfileid: "84324509"
---
# <a name="select-a-vmware-migration-option"></a>Selecione uma opção de migração VMware

Pode migrar VMware VMs para Azure utilizando a ferramenta de migração do servidor Azure Migrate. Esta ferramenta oferece um par de opções para a migração VMware VM:

- Migração usando replicação sem agente. Migrar VMs sem precisar de instalar nada neles.
- Migração com um agente para replicação. Instale um agente no VM para replicação.




## <a name="compare-migration-methods"></a>Comparar métodos de migração

Utilize estas comparações selecionadas para ajudá-lo a decidir qual o método a utilizar. Também pode rever os requisitos completos de suporte para migração [sem agentes](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) e [baseada em agentes.](migrate-support-matrix-vmware-migration.md#agent-based-vmware-servers)

**Definição** | **Sem agente** | **Baseada em agente**
--- | --- | ---
**Permissões Azure** | Precisa de permissões para criar um projeto Azure Migrate e para registar aplicações AD AZure criadas quando implementa o aparelho Azure Migrate. | Precisa de permissões do Contribuinte na assinatura Azure. 
**Replicação simultânea** | Um máximo de 100 VMs pode ser replicado simultaneamente a partir de um servidor vCenter.<br/> Se tiver mais de 50 VMs para migração, crie vários lotes de VMs.<br/> Replicar mais num único momento terá impacto no desempenho. | ND
**Implantação do aparelho** | O [aparelho Azure Migrate](migrate-appliance.md) está implantado no local. | O [aparelho de replicação Azure Migrate](migrate-replication-appliance.md) está implantado no local.
**Compatibilidade de Recuperação de Sítio** | É compatível. | Não é possível replicar com a migração do servidor Azure Migrate se tiver configurado a replicação para uma máquina que utiliza a Recuperação do Site.
**Disco-alvo** | Managed disks | Managed disks
**Limites de disco** | Disco de os: 2 TB<br/><br/> Disco de dados: 4 TB<br/><br/> Discos máximos: 60 | Disco de os: 2 TB<br/><br/> Disco de dados: 8 TB<br/><br/> Discos máximos: 63
**Discos passthrough** | Não suportado | Suportado
**Bota UEFI** | Não suportado | O VM migrado em Azure será automaticamente convertido para um VM de arranque BIOS.<br/><br/> O disco de so deve ter até quatro divisórias, e os volumes devem ser formatados com NTFS.


## <a name="deployment-steps-comparison"></a>Comparação de etapas de implantação

Depois de rever as limitações, compreender os passos envolvidos na implementação de cada solução pode ajudá-lo a decidir qual a opção a escolher.

**Tarefa** | **Detalhes** |**Sem agente** | **Baseada em agente**
--- | --- | --- | ---
**Avaliação** | Avaliar servidores antes da migração.  A avaliação é opcional. Sugerimos que avalie as máquinas antes de as migrar, mas não é preciso. <br/><br/> Para avaliação, a Azure Migrate cria um aparelho leve para descobrir e avaliar os VM. | Se executar uma migração sem agente após avaliação, o mesmo aparelho Azure Migrate criado para avaliação é utilizado para migração sem agentes.  |  Se executar uma migração baseada em agente após avaliação, o aparelho configurado para avaliação não é utilizado durante a migração baseada em agentes. Pode deixar o aparelho no lugar ou removê-lo se não quiser fazer mais descobertas e avaliações.
**Preparar servidores VMware e VMs para migração** | Configure uma série de configurações em servidores VMware e VMs. | Necessário | Necessário
**Adicione a ferramenta de migração do servidor** | Adicione a ferramenta de migração do servidor Azure Migrate no projeto Azure Migrate. | Necessário | Necessário
**Implementar o aparelho Azure Migrate** | Instale um aparelho leve num VMware VMware para a descoberta e avaliação de VM. | Necessário | Não é necessário.
**Instalar o serviço de Mobilidade em VMs** | Instale o serviço de Mobilidade em cada VM que pretende replicar | Não é necessária | Necessário
**Implementar o aparelho de replicação da migração do servidor Azure Migrate** | Configurar um aparelho num VMware VM para descobrir VMs, e fazer uma ponte entre o serviço de Mobilidade em execução em VMs e Azure Migrate Server Migration | Não é necessária | Necessário
**VMs replicados**. Ativar a replicação de VM. | Configurar definições de replicação e selecionar VMs para replicar | Necessário | Necessário
**Executar uma migração de teste** | Faça um teste de migração para garantir que tudo está funcionando como esperado. | Necessário | Necessário
**Executar uma migração completa** | Migrar os VMs. | Necessário | Necessário




## <a name="next-steps"></a>Próximos passos

[Migrar VMware VMs](tutorial-migrate-vmware.md) com migração sem agentes.



