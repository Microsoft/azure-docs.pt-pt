---
title: Alterações de documentação para O Servidor SQL em Máquinas Virtuais Azure Microsoft Docs
description: Conheça as novas funcionalidades e melhorias para o SQL Server em Azure Virtual Machines.
services: virtual-machines-windows
author: MashaMSFT
ms.author: mathoma
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/06/2020
ms.openlocfilehash: 5a09a30bafa5bc9ef052b14cad29e000bbd09a48
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87287375"
---
# <a name="documentation-changes-for-sql-server-on-azure-virtual-machines"></a>Alterações de documentação para o servidor SQL em máquinas virtuais Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

O Azure permite-lhe implantar uma máquina virtual (VM) com uma imagem do SQL Server incorporada. Este artigo resume as alterações de documentação associadas a novas funcionalidades e melhorias nas recentes versões do [SQL Server em Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/sql-server/). 

## <a name="july-2020"></a>Julho de 2020


| Alterações | Detalhes |
| --- | --- |
| **Migrar log para ultra disco** | Saiba como pode [migrar o seu ficheiro de registo para um disco ultra](storage-migrate-to-ultradisk.md) para alavancar o alto desempenho e a baixa latência. | 
| **Criar AG usando PowerShell** | Agora é possível simplificar a criação de um grupo de disponibilidade usando [o PowerShell,](availability-group-az-commandline-configure.md) bem como o Azure CLI. | 


## <a name="june-2020"></a>Junho de 2020

| Alterações | Detalhes |
| --- | --- |
| **Nome da rede distribuída (DNN)** | O SQL Server 2019 no Windows Server 2016+ está agora a pré-visualizar o suporte para encaminhar o tráfego para a sua instância de cluster failover (FCI) utilizando um nome de [rede distribuído](hadr-distributed-network-name-dnn-configure.md) em vez de utilizar o Azure Load Balancer. Este suporte simplifica e simplifica a ligação à sua solução de alta disponibilidade (HA) em Azure. | 
| **FCI com discos partilhados do Azure** | Agora é possível implementar o seu caso de [cluster failover (FCI)](failover-cluster-instance-overview.md) usando [discos partilhados Azure](failover-cluster-instance-azure-shared-disks-manually-configure.md). |
| **Docs da FCI reorganizados** | A documentação em torno de [instâncias de cluster failover com SQL Server em Azure VMs](failover-cluster-instance-overview.md) foi reescrita e reorganizada para maior clareza. Separámos alguns dos conteúdos de configuração, como as [melhores práticas](hadr-cluster-best-practices.md)de configuração do cluster, como preparar uma [máquina virtual para um SQL Server FCI,](failover-cluster-instance-prepare-vm.md)e como configurar o [Azure Load Balancer](hadr-vnn-azure-load-balancer-configure.md). | 
| &nbsp; | &nbsp; |


## <a name="may-2020"></a>Maio de 2020 

| Alterações | Detalhes |
| --- | --- |
| **Família Azure SQL** | O SQL Server em Azure Virtual Machines é agora parte da [família de produtos Azure SQL](../../azure-sql-iaas-vs-paas-what-is-overview.md). Confira o nosso [novo visual!](../index.yml) Nada mudou no produto, mas a documentação visa facilitar a decisão do produto Azure SQL. | 


## <a name="january-2020"></a>Janeiro de 2020

| Alterações | Detalhes |
| --- | --- |
| **Apoio do Governo de Azure** | Agora é possível registar máquinas virtuais SQL Server com o fornecedor de recursos SQL VM para máquinas virtuais hospedadas na nuvem do [Governo Azure.](https://azure.microsoft.com/global-infrastructure/government/) | 
| &nbsp; | &nbsp; |

## <a name="2019"></a>2019

|Alterações | Detalhes |
 --- | --- |
| **Réplica GRATUITA DR em Azure** | Pode hospedar uma [instância passiva gratuita](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure) para a recuperação de desastres em Azure para a sua instância do SQL Server no local se tiver [Garantia de Software](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot:primaryr3). | 
| **Registo de fornecedores de recursos a granel** | Agora pode [registar a granel](sql-vm-resource-provider-bulk-register.md) máquinas virtuais SQL Server com o fornecedor de recursos. | 
|**Configuração de armazenamento otimizada pelo desempenho** | Agora pode [personalizar totalmente a sua configuração de armazenamento](storage-configuration.md#new-vms) ao criar um novo SQL Server VM. |
|**Participação de ficheiros premium para a FCI** | Pode agora criar uma instância de cluster de failover utilizando uma partilha de [ficheiros Premium](failover-cluster-instance-premium-file-share-manually-configure.md) em vez do método original de [Storage Spaces Direct](failover-cluster-instance-storage-spaces-direct-manually-configure.md). 
| **Azure Dedicated Host** | Pode executar o seu VM do servidor SQL no [Azure Dedicated Host](dedicated-host.md). | 
| **Migração VM do Servidor SQL para uma região diferente** | Utilize a Recuperação do Site Azure para [migrar o seu SQL Server VM de uma região para outra](move-sql-vm-different-region.md). |
|  **Novos modos de instalação SQL IaaS** | Agora é possível instalar a extensão SQL Server IaaS em [modo leve](sql-server-iaas-agent-extension-automate-management.md) para evitar reiniciar o serviço SQL Server.  |
| **Modificação da edição do SQL Server** | Agora pode alterar a propriedade de [edição](change-sql-server-edition.md) para o seu SQL Server VM. |
| **Alterações ao fornecedor de recursos SQL VM** | Pode [registar o seu SQL Server VM com o fornecedor de recursos SQL VM](sql-vm-resource-provider-register.md) utilizando os novos modos SQL IaaS. Esta capacidade inclui imagens [do Windows Server 2008.](sql-vm-resource-provider-register.md#management-modes)|
| **Imagens de trazer a sua própria licença usando O Benefício Híbrido Azure** | As imagens de "Bring-your-your-own-license" implementadas a partir do Azure Marketplace podem agora mudar o seu [tipo de licença para pay-as-you-go](licensing-model-azure-hybrid-benefit-ahb-change.md#remarks).| 
| **Nova gestão de VM do Servidor SQL no portal Azure** | Existe agora uma forma de gerir o seu SQL Server VM no portal Azure. Para obter mais informações, consulte [Gerir VMs do Servidor SQL no portal Azure](manage-sql-vm-portal.md).  | 
| **Suporte alargado para SQL Server 2008 e 2008 R2** | [Estenda o suporte](sql-server-2008-extend-end-of-support.md) para o SQL Server 2008 e SQL Server 2008 R2 migrando *como é* para um VM Azure. | 
| **Suporte personalizado de imagem** | Agora pode instalar a [extensão SQL Server IaaS](sql-server-iaas-agent-extension-automate-management.md#installation) para imagens personalizadas de OS e SQL Server, que oferece a funcionalidade limitada de [licenciamento flexível](licensing-model-azure-hybrid-benefit-ahb-change.md). Quando registar a sua imagem personalizada com o fornecedor de recursos SQL VM, especifique o tipo de licença como "AHUB". Caso contrário, o registo falhará. | 
| **Apoio a instâncias nomeadas** | Pode agora utilizar a [extensão IAAS do SQL Server](sql-server-iaas-agent-extension-automate-management.md#installation) com uma instância nomeada, se a instância padrão tiver sido desinstalada corretamente. | 
| **Melhoramento do portal** | A experiência do portal Azure para a implementação de um SQL Server VM foi renovada para melhorar a usabilidade. Para obter mais informações, consulte o breve [quickstart](sql-vm-create-portal-quickstart.md) e o guia mais completo [para](create-sql-vm-portal.md) implementar um SQL Server VM.|
| **Melhoria do portal** | Agora é possível alterar o modelo de licenciamento para um SQL Server VM de pay-as-you-go para trazer a sua própria licença usando o [portal Azure](licensing-model-azure-hybrid-benefit-ahb-change.md#vms-already-registered-with-the-resource-provider).|
| **Simplificação da implementação do grupo de disponibilidade para um SQL Server VM através do CLI Azure** | É agora mais fácil do que nunca implementar um grupo de disponibilidade para um SQL Server VM em Azure. Pode utilizar o [CLI do Azure](/cli/azure/sql/vm?view=azure-cli-2018-03-01-hybrid) para criar o cluster de failover do Windows, o equilibrador de carga interno e os ouvintes do grupo de disponibilidade, todos a partir da linha de comando. Para obter mais informações, consulte [o Azure CLI para configurar um grupo de disponibilidade Always On para O Servidor SQL num VM Azure](availability-group-az-cli-configure.md). | 
| &nbsp; | &nbsp; |

## <a name="2018"></a>2018 

 Alterações | Detalhes |
| --- | --- |
|  **Novo fornecedor de recursos para um cluster SQL Server** | Um novo fornecedor de recursos (Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups) define os metadados do cluster de failover do Windows. A junção de um SQL Server VM ao *SqlVirtualMachineGroups,* o serviço Desagravado Do Windows Server Failover Cluster (WSFC) e junta-se ao VM ao cluster.  |
| **Configuração automatizada de uma implementação de grupo de disponibilidade com modelos de arranque rápido Azure** |Agora é possível criar o cluster de failover do Windows, juntar VMs sql server ao mesmo, criar o ouvinte e configurar o equilibrador de carga interno utilizando dois modelos de arranque rápido Azure. Para obter mais informações, consulte [use os modelos de arranque rápido do Azure para configurar um grupo de disponibilidade Always On para O Servidor SQL num VM Azure](availability-group-quickstart-template-configure.md). | 
| **Registo automático ao fornecedor de recursos SQL VM** | Os VMs do SQL Server implantados após este mês são automaticamente registados no novo fornecedor de recursos SQL VM. Os VMs do Servidor SQL implantados antes deste mês ainda precisam de ser registados manualmente. Para obter mais informações, consulte [registar uma máquina virtual SQL Server em Azure com o fornecedor de recursos SQL VM](sql-vm-resource-provider-register.md).|
|**Novo fornecedor de recursos SQL VM** |  Um novo fornecedor de recursos (Microsoft.SqlVirtualMachine) proporciona uma melhor gestão dos seus VMs do seu SQL Server VMs. Para obter mais informações sobre o registo dos seus VMs, consulte [registar uma máquina virtual SQL Server em Azure com o fornecedor de recursos SQL VM](sql-vm-resource-provider-register.md). |
|**Modelo de licenciamento da Switch** | Agora pode alternar entre os modelos pay-per-use e bring-your-own-license para o seu SQL Server VM utilizando o Azure CLI ou o PowerShell. Para obter mais informações, consulte [como alterar o modelo de licenciamento de uma máquina virtual SQL Server em Azure](licensing-model-azure-hybrid-benefit-ahb-change.md). | 
| &nbsp; | &nbsp; |

## <a name="additional-resources"></a>Recursos adicionais

**VMs do Windows:**

* [Visão geral do SQL Server num VM do Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Provisão SQL Server em um VM Windows](create-sql-vm-portal.md)
* [Migrar uma base de dados para o SQL Server numa VM do Azure](migrate-to-vm-from-sql-server.md)
* [Alta disponibilidade e recuperação de desastres para o SQL Server em Azure Virtual Machines](business-continuity-high-availability-disaster-recovery-hadr-overview.md)
* [Melhores práticas de desempenho para SQL Server em Azure Virtual Machines](performance-guidelines-best-practices.md)
* [Padrões de aplicação e estratégias de desenvolvimento para O SQL Server em Máquinas Virtuais Azure](application-patterns-development-strategies.md)

**Linux VMs:**

* [Visão geral do SQL Server num Linux VM](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)
* [Provisão SQL Server em uma máquina virtual Linux](../linux/sql-vm-create-portal-quickstart.md)
* [FAQ (Linux)](../linux/frequently-asked-questions-faq.md)
* [SqL Server na documentação do Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
