---
title: Selecione uma opção de migração VMware com migração de servidor migrações para Azure | Microsoft Docs
description: Fornece uma visão geral das opções para migrar VMs VMware para o Azure com migração de servidor de migrações para Azure
ms.topic: conceptual
ms.date: 07/09/2019
ms.openlocfilehash: 52e7103ea3ebcd83369a866cc3f75b0bf0e889a2
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028724"
---
# <a name="select-a-vmware-migration-option"></a>Selecione uma opção de migração do VMware

Você pode migrar VMs do VMware para o Azure usando a ferramenta de migração de servidor de migrações para Azure. Essa ferramenta oferece algumas opções para a migração de VM do VMware:

- Migração usando a replicação sem agente. Migre VMs sem a necessidade de instalar nada nelas.
- Migração com um agente para replicação. Instale um agente na VM para replicação.




## <a name="compare-migration-methods"></a>Comparar métodos de migração

Use essas comparações selecionadas para ajudá-lo a decidir qual método usar. Você também pode examinar os requisitos de suporte completo para migração [baseada em](migrate-support-matrix-vmware-migration.md#agent-based-vmware-servers) agente e sem [agentes](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) .

**Definição** | **Sem agente** | **Baseado em agente**
--- | --- | ---
**Permissões do Azure** | Você precisa de permissões para criar um projeto de migrações para Azure e registrar os aplicativos do Azure AD criados ao implantar o dispositivo de migrações para Azure. | Você precisa de permissões de colaborador na assinatura do Azure. 
**Replicação simultânea** | Um máximo de 100 VMs pode ser replicado simultaneamente de um vCenter Server.<br/> Se você tiver mais de 50 VMs para migração, crie vários lotes de VMs.<br/> Replicar mais em uma única vez afetará o desempenho. | N/D
**Implantação de dispositivo** | O [dispositivo migrações para Azure](migrate-appliance.md) é implantado localmente. | O [dispositivo de replicação de migrações para Azure](migrate-replication-appliance.md) é implantado localmente.
**Compatibilidade Site Recovery** | Compatível. | Você não poderá replicar com a migração de servidor de migrações para Azure se tiver configurado a replicação para um computador usando Site Recovery.
**Disco de destino** | Managed Disks | Managed Disks
**Limites de disco** | Disco do sistema operacional: 2 TB<br/><br/> Disco de dados: 4 TB<br/><br/> Máximo de discos: 60 | Disco do sistema operacional: 2 TB<br/><br/> Disco de dados: 8 TB<br/><br/> Máximo de discos: 63
**Discos de passagem** | Não suportado | Suportadas
**Inicialização UEFI** | Não suportado | A VM migrada no Azure será convertida automaticamente em uma VM de inicialização do BIOS.<br/><br/> O disco do sistema operacional deve ter até quatro partições, e os volumes devem ser formatados com NTFS.


## <a name="deployment-steps-comparison"></a>Comparação de etapas de implantação

Depois de revisar as limitações, entender as etapas envolvidas na implantação de cada solução pode ajudá-lo a decidir qual opção escolher.

**Tarefa** | **Detalhes** |**Sem agente** | **Baseado em agente**
--- | --- | --- | ---
**Avaliação** | Avalie servidores antes da migração.  A avaliação é opcional. Sugerimos que você avalie as máquinas antes de migrá-las, mas não é necessário. <br/><br/> Para avaliação, as migrações para Azure configuram um dispositivo leve para descobrir e avaliar as VMs. | Se você executar uma migração sem agente após a avaliação, o mesmo dispositivo de migração do Azure configurado para avaliação será usado para a migração sem agente.  |  Se você executar uma migração baseada em agente após a avaliação, a configuração do dispositivo para avaliação não será usada durante a migração sem agente. Você pode deixar o dispositivo em vigor ou removê-lo se não quiser fazer mais descoberta e avaliação.
**Preparar servidores VMware e VMs para migração** | Defina várias configurações em servidores VMware e VMs. | Obrigatório | Obrigatório
**Adicionar a ferramenta de migração de servidor** | Adicione a ferramenta de migração de servidor de migrações para Azure no projeto de migrações para Azure. | Obrigatório | Obrigatório
**Implantar o dispositivo de migrações para Azure** | Configure um dispositivo leve em uma VM VMware para avaliação e descoberta de VM. | Obrigatório | Não é necessário.
**Instalar o serviço de mobilidade em VMs** | Instalar o serviço de mobilidade em cada VM que você deseja replicar | Não necessário | Obrigatório
**Implantar o dispositivo de replicação de migração de servidor de migrações para Azure** | Configurar um dispositivo em uma VM VMware para descobrir VMs e fazer a ponte entre o serviço de mobilidade em execução em VMs e migração de servidor de migrações para Azure | Não necessário | Obrigatório
**Replicar VMs**. Habilite a replicação da VM. | Definir as configurações de replicação e selecionar as VMs a serem replicadas | Obrigatório | Obrigatório
**Executar uma migração de teste** | Execute uma migração de teste para verificar se tudo está funcionando conforme o esperado. | Obrigatório | Obrigatório
**Executar uma migração completa** | Migre as VMs. | Obrigatório | Obrigatório




## <a name="next-steps"></a>Passos seguintes

[Migre VMs VMware](tutorial-migrate-vmware.md) com migração sem agente.



