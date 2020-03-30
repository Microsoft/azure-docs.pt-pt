---
title: Selecione uma opção de migração VMware com migração do servidor migratório de migração do Azure Migrate [ Microsoft Docs
description: Fornece uma visão geral das opções para vMware migratório vMs para Azure com migração de servidores migratórios Azure
ms.topic: conceptual
ms.date: 07/09/2019
ms.openlocfilehash: 52e7103ea3ebcd83369a866cc3f75b0bf0e889a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76028724"
---
# <a name="select-a-vmware-migration-option"></a>Selecione uma opção de migração VMware

Pode migrar VMware VMs para Azure utilizando a ferramenta migração do servidor de migração do servidor migratório Azure Migrate. Esta ferramenta oferece um par de opções para a migração VMware VM:

- Migração usando replicação sem agente. Migrar VMs sem precisar de instalar nada neles.
- Migração com um agente para replicação. Instale um agente no VM para replicação.




## <a name="compare-migration-methods"></a>Comparar métodos de migração

Utilize estas comparações selecionadas para ajudá-lo a decidir qual o método a utilizar. Também pode rever os requisitos completos de apoio para a migração [sem agente](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) e [baseada em agentes.](migrate-support-matrix-vmware-migration.md#agent-based-vmware-servers)

**Definição** | **Sem agente** | **Baseado em agente**
--- | --- | ---
**Permissões azure** | É necessário permissão para criar um projeto Azure Migrate e para registar aplicações Azure AD criadas quando implementar o aparelho Azure Migrate. | Precisa de permissões do Colaborador na subscrição do Azure. 
**Replicação simultânea** | Um máximo de 100 VMs pode ser replicado simultaneamente a partir de um servidor vCenter.<br/> Se tiver mais de 50 VMs para migração, crie vários lotes de VMs.<br/> Replicar mais numa só vez terá impacto no desempenho. | ND
**Implantação de aparelhos** | O [aparelho Azure Migrate](migrate-appliance.md) é implantado no local. | O aparelho de [replicação de migração Azure](migrate-replication-appliance.md) é implantado no local.
**Compatibilidade de recuperação do site** | É compatível. | Não é possível replicar com a Migração do Servidor Migratório Migratório De Migração de Migração de Migração de Migração de Migração de Migração de Migrações Azure se tiver configurado a replicação para uma máquina que utilize a Recuperação do Site.
**Disco-alvo** | Managed disks | Managed disks
**Limites de disco** | Disco osso: 2 TB<br/><br/> Disco de dados: 4 TB<br/><br/> Discos máximos: 60 | Disco osso: 2 TB<br/><br/> Disco de dados: 8 TB<br/><br/> Discos máximos: 63
**Discos de passagem** | Não suportado | Suportado
**Bota UEFI** | Não suportado | O VM migrado em Azure será automaticamente convertido para um VM de arranque BIOS.<br/><br/> O disco osso deve ter até quatro divisórias, e os volumes devem ser formatados com NTFS.


## <a name="deployment-steps-comparison"></a>Comparação de etapas de implantação

Depois de rever as limitações, compreender os passos envolvidos na implementação de cada solução pode ajudá-lo a decidir qual a opção a escolher.

**Tarefa** | **Detalhes** |**Sem agente** | **Baseado em agente**
--- | --- | --- | ---
**Avaliação** | Avaliar servidores antes da migração.  A avaliação é opcional. Sugerimos que avalie as máquinas antes de as migrar, mas não tem de o fazer. <br/><br/> Para avaliação, a Azure Migrate cria um aparelho leve para descobrir e avaliar VMs. | Se realizar uma migração sem agente após avaliação, o mesmo aparelho Azure Migrate criado para avaliação é utilizado para migração sem agente.  |  Se executar uma migração baseada em agente após avaliação, o aparelho configurado para avaliação não é utilizado durante a migração sem agente. Pode deixar o aparelho no lugar ou removê-lo se não quiser fazer mais descobertas e avaliações.
**Prepare servidores VMware e VMs para migração** | Configure uma série de definições em servidores VMware e VMs. | Necessário | Necessário
**Adicione a ferramenta de migração do servidor** | Adicione a ferramenta de migração do servidor migratório Azure no projeto Azure Migrate. | Necessário | Necessário
**Implante o aparelho Azure Migrate** | Instale um aparelho leve num VMware VM para a descoberta e avaliação vm. | Necessário | Não é necessário.
**Instalar o serviço de Mobilidade em VMs** | Instale o serviço de Mobilidade em cada VM que pretende replicar | Não é necessária | Necessário
**Implementar o aparelho de replicação de migração do servidor migratório Azure** | Instale um aparelho num VMware VM para descobrir VMs, e ponte entre o serviço de Mobilidade em funcionamento em VMs e Migração de Servidores Migratórios Azure Migrate | Não é necessária | Necessário
**Replicar VMs.** Ativar a replicação vm. | Configure as definições de replicação e selecione VMs para replicar | Necessário | Necessário
**Executar uma migração de teste** | Faça uma migração de teste para ter certeza de que está tudo funcionando como esperado. | Necessário | Necessário
**Executar uma migração completa** | Migrar os VMs. | Necessário | Necessário




## <a name="next-steps"></a>Passos seguintes

[VMs migratórios](tutorial-migrate-vmware.md) com migração sem agente.



