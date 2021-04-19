---
title: Selecione uma opção de migração VMware com migração de servidores Azure Migrate
description: Fornece uma visão geral das opções para migrar VMware VMs para Azure com Azure Migrate Server Migration
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: 22a0629d50ee8181ffcbfe7dad32ab76fb3e68fd
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714169"
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
**Replicação** | Um máximo de 500 VMs pode ser replicado simultaneamente a partir de um servidor vCenter. No portal, pode selecionar até 10 máquinas ao mesmo tempo para replicação. Para replicar mais máquinas, adicione em lotes de 10.| A capacidade de replicação aumenta escalando o aparelho de replicação.
**Implantação do aparelho** | O [aparelho Azure Migrate](migrate-appliance.md) está implantado no local. | O [aparelho de replicação Azure Migrate](migrate-replication-appliance.md) está implantado no local.
**Compatibilidade de Recuperação de Sítio** | É compatível. | Não é possível replicar com a migração do servidor Azure Migrate se tiver configurado a replicação para uma máquina que utiliza a Recuperação do Site.
**Disco-alvo** | Managed disks | Managed disks
**Limites de disco** | Disco de os: 2 TB<br/><br/> Disco de dados: 32 TB<br/><br/> Discos máximos: 60 | Disco de os: 2 TB<br/><br/> Disco de dados: 32 TB<br/><br/> Discos máximos: 63
**Discos passthrough** | Não suportado | Suportado
**Bota UEFI** | Suportado. | Suportado. 
**Conetividade** | Internet pública <br/> ExpressRoute com olhando a Microsoft <br/> <br/> [Aprenda](./replicate-using-expressroute.md) a usar pontos finais privados para replicação sobre um espreitamento privado ExpressRoute ou uma ligação S2S VPN. |Internet pública <br/> ExpressRoute com espreitamento privado <br/> ExpressRoute com olhando a Microsoft <br/> VPN site a site

## <a name="compare-deployment-steps"></a>Compare etapas de implementação

Depois de rever as limitações, compreender os passos envolvidos na implementação de cada solução pode ajudá-lo a decidir qual a opção a escolher.

**Tarefa** | **Detalhes** |**Sem agente** | **Baseada em agente**
--- | --- | --- | ---
**Implementar a aplicação do Azure Migrate** | Um aparelho leve que funciona com um VMware VM.<br/><br/> O aparelho é utilizado para descobrir e avaliar máquinas e migrar máquinas utilizando uma migração sem agentes. | Obrigatório.<br/><br/> Se já montou o aparelho para avaliação, pode utilizar o mesmo aparelho para migração sem agentes. | Não necessárias.<br/><br/> Se tiver configurado um aparelho para avaliação, pode deixá-lo no lugar ou removê-lo se tiver terminado a avaliação.
**Utilize a ferramenta de avaliação do servidor** | Avaliar máquinas com a ferramenta Azure Migrate:Server Assessment. | A avaliação é opcional. | A avaliação é opcional.
**Utilize a ferramenta de migração do servidor** | Adicione a ferramenta de migração do servidor Azure Migrate no projeto Azure Migrate. | Necessário | Necessário
**Preparar VMware para migração** | Configurar as definições nos servidores VMware e VMs. | Necessário | Necessário
**Instalar o serviço de Mobilidade em VMs** | O serviço de mobilidade funciona em cada VM que pretende replicar | não é necessário | Necessário
**Implementar o aparelho de replicação** | O [aparelho de replicação](migrate-replication-appliance.md) é utilizado para migração baseada em agentes. Liga-se entre o serviço de Mobilidade em execução em VMs e a Migração de Servidores. | não é necessário | Necessário
**VMs replicados**. Ativar a replicação de VM. | Configurar definições de replicação e selecionar VMs para replicar | Necessário | Necessário
**Executar uma migração de teste** | Faça um teste de migração para garantir que tudo está funcionando como esperado. | Necessário | Necessário
**Executar uma migração completa** | Migrar os VMs. | Necessário | Necessário



## <a name="next-steps"></a>Passos seguintes

[Migrar VMware VMs](tutorial-migrate-vmware.md) com migração sem agentes.



