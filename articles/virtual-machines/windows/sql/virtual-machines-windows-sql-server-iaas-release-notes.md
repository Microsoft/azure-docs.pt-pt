---
title: Alterações de documentação para SQL Server em máquinas virtuais do Azure | Microsoft Docs
description: Saiba mais sobre os novos recursos e aprimoramentos para SQL Server em uma VM do Azure
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
ms.date: 08/01/2019
ms.openlocfilehash: c122baa21c5d94b57f29cb0530f0a2655faa87d0
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74790374"
---
# <a name="documentation-changes-for-sql-server-on-azure-virtual-machines"></a>Alterações de documentação para SQL Server em máquinas virtuais do Azure

O Azure permite que você implante uma máquina virtual (VM) com uma imagem de SQL Server interna. Este artigo resume as alterações de documentação associadas a novos recursos e aprimoramentos nas versões recentes do [SQL Server em máquinas virtuais do Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/). 


## <a name="october-2019"></a>Outubro de 2019

| For | Detalhes |
| --- | --- |
| **Registro do provedor de recursos em massa** | Agora você pode [registrar em massa](virtual-machines-windows-sql-bulk-register-with-resource-provider.md) as máquinas virtuais do SQL com o provedor de recursos. | 
| **Configuração de armazenamento otimizado para desempenho** | Agora você pode [personalizar totalmente sua configuração de armazenamento](virtual-machines-windows-sql-server-storage-configuration.md#new-vms) ao criar uma nova VM SQL Server. |
| **Compartilhamento de arquivos premium para FCI** | Agora você pode criar uma instância de cluster de failover usando um [compartilhamento de arquivos Premium](virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share.md) em vez do método original de [espaços de armazenamento diretos](virtual-machines-windows-portal-sql-create-failover-cluster.md). 
| &nbsp; | &nbsp; |

## <a name="august-2019"></a>Agosto de 2019

| For | Detalhes |
| --- | --- |
| **Host dedicado do Azure** | Você pode executar a VM SQL Server em um [host dedicado do Azure](virtual-machines-windows-sql-dedicated-host.md). |
| &nbsp; | &nbsp; |


## <a name="july-2019"></a>Julho de 2019


| For | Detalhes |
| --- | --- |
| **Mover a VM do SQL para uma região diferente** | Use Azure Site Recovery para [migrar sua VM SQL Server de uma região para outra](virtual-machines-windows-sql-move-different-region.md). |
| &nbsp; | &nbsp; |

## <a name="june-2019"></a>Junho de 2019


| For | Detalhes |
| --- | --- |
| **Novos modos de instalação do SQL IaaS** | Agora é possível instalar a extensão SQL Server IaaS no [modo leve](virtual-machines-windows-sql-server-agent-extension.md) para evitar a reinicialização do serviço SQL Server.  |
| **Modificação da edição do SQL Server** | Agora você pode alterar a [propriedade de edição](virtual-machines-windows-sql-change-edition.md) para sua VM SQL Server. |
| **Alterações no provedor de recursos de VM do SQL** | Você pode [registrar sua vm SQL Server com o provedor de recursos de VM do SQL](virtual-machines-windows-sql-register-with-resource-provider.md) usando os novos modos SQL IaaS. Esse recurso inclui imagens [do Windows Server 2008](virtual-machines-windows-sql-register-with-resource-provider.md#management-modes) .|
| **Imagens traga sua própria licença usando Benefício Híbrido do Azure** | As imagens traga sua própria licença implantadas no Azure Marketplace agora podem mudar seu [tipo de licença para](virtual-machines-windows-sql-ahb.md#remarks)pré-pago.| 
| &nbsp; | &nbsp; |


## <a name="may-2019"></a>Maio de 2019

| For | Detalhes |
| --- | --- |
| **Novo SQL Server gerenciamento de VM no portal do Azure** | Agora há uma maneira de gerenciar sua VM SQL Server no portal do Azure. Para obter mais informações, consulte [gerenciar VMs SQL Server no portal do Azure](virtual-machines-windows-sql-manage-portal.md).  | 
| &nbsp; | &nbsp; |



## <a name="april-2019"></a>Abril de 2019

| For | Detalhes |
| --- | --- |
| **Suporte estendido para o SQL Server 2008/2008 R2** | [Estenda o suporte](virtual-machines-windows-sql-server-2008-eos-extend-support.md) para SQL Server 2008 e SQL Server 2008 R2 migrando *o as for* para uma VM do Azure. | 
| &nbsp; | &nbsp; |


## <a name="march-2019"></a>Março de 2019

| For | Detalhes |
| --- | --- |
| **Suporte a imagens personalizadas** | Agora você pode instalar a [extensão SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md#installation) para imagens de sistema operacional personalizadas e SQL, que oferece a funcionalidade limitada de [licenciamento flexível](virtual-machines-windows-sql-ahb.md). Quando você estiver registrando sua imagem personalizada com o provedor de recursos SQL, especifique o tipo de licença como "AHUB". Caso contrário, o registro falhará. | 
| **Suporte à instância nomeada** | Agora você pode usar a [extensão SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md#installation) com uma instância nomeada, se a instância padrão tiver sido desinstalada corretamente. | 
| **Aprimoramento do portal** | A experiência de portal do Azure para implantar uma VM SQL Server foi renovada para melhorar a usabilidade. Para obter mais informações, consulte o breve [início rápido](quickstart-sql-vm-create-portal.md) e o [Guia de instruções](virtual-machines-windows-portal-sql-server-provision.md) mais abrangentes para implantar uma VM SQL Server.|
| &nbsp; | &nbsp; |


## <a name="february-2019"></a>Fevereiro de 2019

| For | Detalhes |
| --- | --- |
| **Aprimoramento do portal** | Agora é possível alterar o modelo de licenciamento de uma VM SQL Server de pré-pago para o traga sua própria licença usando o [portal do Azure](virtual-machines-windows-sql-ahb.md#change-the-license-for-vms-already-registered-with-the-resource-provider).|
|**Simplificação da implantação do grupo de disponibilidade com a CLI da VM SQL Server do Azure** | Agora é mais fácil do que nunca implantar um grupo de disponibilidade em uma VM SQL Server no Azure. Você pode usar o [CLI do Azure](/cli/azure/sql/vm?view=azure-cli-2018-03-01-hybrid) para criar o cluster de failover do Windows, o balanceador de carga interno e os ouvintes do grupo de disponibilidade todos na linha de comando. Para obter mais informações, consulte [usar a CLI da VM SQL Server do Azure para configurar um grupo de disponibilidade Always on para SQL Server em uma VM do Azure](virtual-machines-windows-sql-availability-group-cli.md). | 
| &nbsp; | &nbsp; |

## <a name="2018"></a>2018


### <a name="december-2018"></a>Dezembro de 2018

| For | Detalhes |
| --- | --- |
| **Novo provedor de recursos para um cluster SQL Server** | Um novo provedor de recursos (Microsoft. SqlVirtualMachine/SqlVirtualMachineGroups) define os metadados do cluster de failover do Windows. Ingressar em uma VM SQL Server para o *SqlVirtualMachineGroups* Inicializa o serviço WSFC (cluster de failover do Windows Server) e une a VM ao cluster.  |
|**Configuração automatizada de uma implantação de grupo de disponibilidade com modelos de início rápido do Azure** |Agora é possível criar o cluster de failover do Windows, ingressar SQL Server VMs nele, criar o ouvinte e configurar o balanceador de carga interno com dois modelos de início rápido do Azure. Para obter mais informações, consulte [usar modelos de início rápido do Azure para configurar um grupo de disponibilidade Always on para SQL Server em uma VM do Azure](virtual-machines-windows-sql-availability-group-quickstart-template.md). | 
| **Registro automático para o provedor de recursos de VM do SQL** | SQL Server VMs implantadas após este mês são automaticamente registradas com o novo provedor de recursos de SQL Server. SQL Server VMs implantadas antes deste mês ainda precisam ser registradas manualmente. Para obter mais informações, consulte [registrar um SQL Server máquina virtual no Azure com o provedor de recursos da VM do SQL](virtual-machines-windows-sql-register-with-resource-provider.md).|
| &nbsp; | &nbsp; |


### <a name="november-2018"></a>Novembro de 2018

| For | Detalhes |
| --- | --- |
| **Novo provedor de recursos de VM do SQL** |  Um novo provedor de recursos (Microsoft. SqlVirtualMachine) fornece melhor gerenciamento de suas VMs SQL Server. Para obter mais informações sobre como registrar suas VMs, consulte [registrar um SQL Server máquina virtual no Azure com o provedor de recursos da VM do SQL](virtual-machines-windows-sql-register-with-resource-provider.md). |
|**Alternar modelo de licenciamento** | Agora você pode alternar entre os modelos de pagamento por uso e traga sua própria licença para sua VM SQL Server usando o CLI do Azure ou o PowerShell. Para obter mais informações, consulte [como alterar o modelo de licenciamento para uma máquina virtual SQL Server no Azure](virtual-machines-windows-sql-ahb.md). | 
| &nbsp; | &nbsp; |


## <a name="additional-resources"></a>Recursos adicionais

**VMs do Windows**:

* [Visão geral de SQL Server em uma VM do Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [Provisionar uma VM SQL Server Windows](virtual-machines-windows-portal-sql-server-provision.md)
* [Migrando um banco de dados para SQL Server em uma VM do Azure](virtual-machines-windows-migrate-sql.md)
* [Alta disponibilidade e recuperação de desastre para SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-high-availability-dr.md)
* [Práticas recomendadas de desempenho para SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-performance.md)
* [Padrões de aplicativo e estratégias de desenvolvimento para SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**VMs do Linux**:

* [Visão geral de SQL Server em uma VM Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [Provisionar uma máquina virtual do SQL Server Linux](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [Perguntas frequentes (Linux)](../../linux/sql/sql-server-linux-faq.md)
* [Documentação do SQL Server no Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
