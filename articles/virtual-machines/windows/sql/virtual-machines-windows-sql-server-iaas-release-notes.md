---
title: Alterações na documentação para o Servidor SQL em Máquinas Virtuais Azure. Microsoft Docs
description: Conheça as novas funcionalidades e melhorias para o SQL Server num VM Azure
services: virtual-machines-windows
author: MashaMSFT
ms.author: mathoma
manager: craigg
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/06/2020
ms.openlocfilehash: 27682863a96e2f190a0dafe6e4d783029e987453
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77201650"
---
# <a name="documentation-changes-for-sql-server-on-azure-virtual-machines"></a>Alterações na documentação para O Servidor SQL em Máquinas Virtuais Azure

O Azure permite-lhe implementar uma máquina virtual (VM) com uma imagem do SQL Server incorporada. Este artigo resume as alterações de documentação associadas a novas funcionalidades e melhorias nos recentes lançamentos do [SQL Server em Máquinas Virtuais Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/). 


## <a name="january-2020"></a>Janeiro de 2020

| Alterações | Detalhes |
| --- | --- |
| **Apoio do Governo de Azure** | Agora é possível registar máquinas virtuais SQL Server com o fornecedor de recursos SQL VM para máquinas virtuais alojadas na nuvem [do Governo Azure.](https://azure.microsoft.com/global-infrastructure/government/) | 
| &nbsp; | &nbsp; |

## <a name="2019"></a>2019

|Alterações | Detalhes |
 --- | --- |
| **Réplica DR grátis em Azure** | Pode acolher uma [instância passiva gratuita](virtual-machines-windows-sql-high-availability-dr.md#free-dr-replica-in-azure) para a recuperação de desastres em Azure para a sua instância de SQL Server no local se tiver garantia de [software](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot:primaryr3). | 
| **Registo do fornecedor de recursos a granel** | Agora pode registar em [massa](virtual-machines-windows-sql-bulk-register-with-resource-provider.md) máquinas virtuais SQL com o fornecedor de recursos. | 
|**Configuração de armazenamento otimizado de desempenho** | Agora pode [personalizar totalmente a sua configuração](virtual-machines-windows-sql-server-storage-configuration.md#new-vms) de armazenamento ao criar um novo VM do Servidor SQL. |
|**Partilha de ficheiropremium para fci** | Agora pode criar uma instância de cluster failover usando uma Partilha de [Ficheiros Premium](virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share.md) em vez do método original de Espaços de [Armazenamento Direto](virtual-machines-windows-portal-sql-create-failover-cluster.md). 
| **Anfitrião dedicado azure** | Pode executar o seu VM de Servidor SQL num [Anfitrião Dedicado Azure.](virtual-machines-windows-sql-dedicated-host.md) | 
| **Mover SQL VM para diferentes regiões** | Utilize a Recuperação do Site Azure para [migrar o seu VM do Servidor SQL de uma região para outra](virtual-machines-windows-sql-move-different-region.md). |
|  **Novos modos de instalação SQL IaaS** | Agora é possível instalar a extensão SQL Server IaaS em [modo leve](virtual-machines-windows-sql-server-agent-extension.md) para evitar reiniciar o serviço SQL Server.  |
| **Modificação da edição do SQL Server** | Agora pode alterar a [propriedade da edição](virtual-machines-windows-sql-change-edition.md) para o seu VM SQL Server. |
| **Alterações ao fornecedor de recursos SQL VM** | Pode [registar o seu VM Do Servidor SQL com o fornecedor de recursos SQL VM](virtual-machines-windows-sql-register-with-resource-provider.md) utilizando os novos modos SQL IaaS. Esta capacidade inclui imagens [do Windows Server 2008.](virtual-machines-windows-sql-register-with-resource-provider.md#management-modes)|
| **Traga imagens de sua própria licença usando O Benefício Híbrido Azure** | As imagens de bring-your-your-license implantadas do Azure Marketplace podem agora mudar o seu tipo de [licença para pay-as-you-go](virtual-machines-windows-sql-ahb.md#remarks).| 
| **Nova gestão VM do Servidor SQL no portal Azure** | Há agora uma maneira de gerir o seu VM SQL Server no portal Azure. Para mais informações, consulte [Gerir VMs do Servidor SQL no portal Azure](virtual-machines-windows-sql-manage-portal.md).  | 
| **Suporte alargado para SQL Server 2008/2008 R2** | [Alargar](virtual-machines-windows-sql-server-2008-eos-extend-support.md) o suporte para o SQL Server 2008 e o SQL Server 2008 R2 migrando *como é* para um VM Azure. | 
| **Suporte de imagem personalizado** | Agora pode instalar a [extensão SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md#installation) para imagens personalizadas de OS e SQL, que oferece a funcionalidade limitada de [licenciamento flexível.](virtual-machines-windows-sql-ahb.md) Quando estiver a registar a sua imagem personalizada junto do fornecedor de recursos SQL, especifique o tipo de licença como "AHUB". Caso contrário, o registo falhará. | 
| **Apoio à instância nomeada** | Pode agora utilizar a [extensão SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md#installation) com uma instância nomeada, se a instância predefinida tiver sido desinstalada corretamente. | 
| **Melhoria do portal** | A experiência do portal Azure para a implementação de um VM SQL Server foi renovada para melhorar a usabilidade. Para mais informações, consulte o breve [quickstart](quickstart-sql-vm-create-portal.md) e o [guia](virtual-machines-windows-portal-sql-server-provision.md) mais minucioso para implementar um VM do Servidor SQL.|
| **Melhoria do portal** | Agora é possível alterar o modelo de licenciamento para um VM SQL Server de pay-as-you-go para trazer a sua própria licença usando o [portal Azure](virtual-machines-windows-sql-ahb.md#vms-already-registered-with-the-resource-provider).|
| **Simplificação da implementação do grupo de disponibilidade com o Azure SQL Server VM CLI** | É agora mais fácil do que nunca implementar um grupo de disponibilidade para um SQL Server VM em Azure. Pode utilizar o [Azure CLI](/cli/azure/sql/vm?view=azure-cli-2018-03-01-hybrid) para criar o cluster de failover do Windows, o equilibrador de carga interna e os ouvintes do grupo de disponibilidade, todos a partir da linha de comando. Para mais informações, consulte [Utilize o Azure SQL Server VM CLI para configurar um grupo de disponibilidade Always On para O Servidor SQL num VM Azure](virtual-machines-windows-sql-availability-group-cli.md). | 
| &nbsp; | &nbsp; |

## <a name="2018"></a>2018 

 Alterações | Detalhes |
| --- | --- |
|  **Novo fornecedor de recursos para um cluster SQL Server** | Um novo fornecedor de recursos (Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups) define os metadados do cluster failover do Windows. Juntar um VM de servidor SQL ao *SqlVirtualMachineGroups* é o serviço de Failover Cluster (WSFC) do Windows Server e junta o VM ao cluster.  |
| **Configuração automatizada de uma implementação de grupo de disponibilidade com modelos de quickstart Azure** |Agora é possível criar o cluster failover do Windows, juntar-se a VMs do Servidor SQL, criar o ouvinte e configurar o equilibrador de carga interna com dois modelos de quickstart Azure. Para mais informações, consulte [os modelos de quickstart do Use Azure para configurar um grupo de disponibilidade Always On para O Servidor SQL num VM Azure](virtual-machines-windows-sql-availability-group-quickstart-template.md). | 
| **Registo automático ao prestador de recursos SQL VM** | Os VMs do Servidor SQL implantados após este mês são automaticamente registados com o novo fornecedor de recursos do Servidor SQL. Os VMs do Servidor SQL implantados antes deste mês ainda precisam de ser registados manualmente. Para mais informações, consulte [O Registo de uma máquina virtual SQL Server em Azure com o fornecedor de recursos SQL VM](virtual-machines-windows-sql-register-with-resource-provider.md).|
|**Novo fornecedor de recursos SQL VM** |  Um novo fornecedor de recursos (Microsoft.SqlVirtualMachine) fornece uma melhor gestão dos seus VMs do Servidor SQL. Para obter mais informações sobre o registo dos seus VMs, consulte [O Registo de uma máquina virtual SQL Server em Azure com o fornecedor de recursos SQL VM](virtual-machines-windows-sql-register-with-resource-provider.md). |
|**Modelo de licenciamento de switch** | Agora pode alternar entre os modelos pay-per-use e bring-your-your-own-license para o seu SQL Server VM utilizando o Azure CLI ou PowerShell. Para mais informações, consulte [Como alterar o modelo de licenciamento para uma máquina virtual SQL Server em Azure](virtual-machines-windows-sql-ahb.md). | 
| &nbsp; | &nbsp; |

## <a name="additional-resources"></a>Recursos adicionais

**VMs do Windows:**

* [Visão geral do Servidor SQL num VM do Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [Provision a SQL Server Windows VM](virtual-machines-windows-portal-sql-server-provision.md)
* [Migrar uma base de dados para o SQL Server num VM Azure](virtual-machines-windows-migrate-sql.md)
* [Alta disponibilidade e recuperação de desastres para o Servidor SQL em Máquinas Virtuais Azure](virtual-machines-windows-sql-high-availability-dr.md)
* [Boas práticas de desempenho para O Servidor SQL em Máquinas Virtuais Azure](virtual-machines-windows-sql-performance.md)
* [Padrões de aplicação e estratégias de desenvolvimento para O Servidor SQL em Máquinas Virtuais Azure](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**VMs Linux:**

* [Visão geral do Servidor SQL em um VM Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [Provision uma máquina virtual SQL Server Linux](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [FAQ (Linux)](../../linux/sql/sql-server-linux-faq.md)
* [Servidor SQL na documentação linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
