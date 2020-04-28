---
title: Automatizar tarefas de gestão em VMs SQL (Clássico) / Microsoft Docs
description: Este tópico descreve como gerir a extensão do agente Do Servidor SQL, que automatiza tarefas específicas de administração do Servidor SQL. Estes incluem backup automatizado, patching automatizado e integração do cofre de chaves Azure. Este tópico utiliza o modo de implantação clássico.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: a9bda2e7-cdba-427c-bc30-77cde4376f3a
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/12/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: b76ade40db1e85abc0fb42af2e6f4ab88cb092c4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75982274"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-with-the-sql-server-agent-extension-classic"></a>Automatizar tarefas de gestão em Máquinas Virtuais Azure com a extensão do agente do servidor SQL (Clássico)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-server-agent-extension.md)
> * [Clássica](../classic/sql-server-agent-extension.md)
> 
>
 
A extensão do agente SQL Server IaaS (SQLIaSAgent) funciona em máquinas virtuais Azure para automatizar tarefas de administração. Este tópico fornece uma visão geral dos serviços suportados pela extensão, bem como instruções de instalação, estado e remoção.

> [!IMPORTANT] 
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Gestor de Recursos e Clássico.](../../../azure-resource-manager/management/deployment-models.md) Este artigo cobre a utilização do modelo de implantação Classic. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager. Para ver a versão do Gestor de Recursos deste artigo, consulte a extensão do agente do [servidor SQL para o Gestor de Recursos VMs do Servidor SQL](../sql/virtual-machines-windows-sql-server-agent-extension.md).

## <a name="supported-services"></a>Serviços suportados
A extensão do agente SQL Server IaaS suporta as seguintes tarefas de administração:

| Característica de administração | Descrição |
| --- | --- |
| **Cópia de Segurança Automatizada do SQL** |Automatiza o agendamento de backups para todas as bases de dados para a instância padrão do Servidor SQL no VM. Para mais informações, consulte [backup automatizado para O Servidor SQL em Máquinas Virtuais Azure (Clássico)](../classic/sql-automated-backup.md). |
| **Aplicação de Patches Automatizada do SQL** |Configura uma janela de manutenção durante a qual podem ser efetuadas importantes atualizações do Windows para o seu VM, para que possa evitar atualizações durante os períodos de pico da sua carga de trabalho. Para mais informações, consulte [patching automatizado para O Servidor SQL em Máquinas Virtuais Azure (Clássico)](../classic/sql-automated-patching.md). |
| **Integração do cofre de chaves Azure** |Permite-lhe instalar e configurar automaticamente o Cofre de Chaves Azure no seu VM do Servidor SQL. Para mais informações, consulte [configure Azure Key Vault Integration for SQL Server on Azure VMs (Classic)](../classic/ps-sql-keyvault.md). |

## <a name="prerequisites"></a>Pré-requisitos
Requisitos para utilizar a extensão do agente SQL Server IaaS no seu VM:

### <a name="operating-system"></a>Sistema operativo:
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

### <a name="sql-server-versions"></a>Versões SQL Server:
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

### <a name="azure-powershell"></a>Azure PowerShell:
[Descarregue e configure os mais recentes comandos Azure PowerShell](/powershell/azure/overview).

Inicie o Windows PowerShell e conecte-o à subscrição do Azure com o comando **Add-AzureAccount.**

    Add-AzureAccount

Se tiver várias subscrições, utilize **select-AzureSubscription** para selecionar a subscrição que contém o seu VM clássico alvo.

    Select-AzureSubscription -SubscriptionName <subscriptionname>

Neste ponto, você pode obter uma lista das máquinas virtuais clássicas e seus nomes de serviço associados com o comando **Get-AzureVM.**

    Get-AzureVM

## <a name="installation"></a>Instalação
Para VMs clássicos, você deve usar powerShell para instalar a extensão do agente SQL Server IaaS e configurar os seus serviços associados. Utilize o cmdlet de powerShell de **extensão set-AzureVMSqlServer** para instalar a extensão. Por exemplo, o seguinte comando instala a extensão num VM do Servidor do Windows (clássico) e dá-lhe o nome de "Extensão SQLIaS".

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -ReferenceName "SQLIaasExtension" -Version "1.2" | Update-AzureVM

Se atualizar para a versão mais recente da extensão do agente SQL IaaS, deve reiniciar a sua máquina virtual depois de atualizar a extensão.

> [!NOTE]
> As máquinas virtuais clássicas não têm opção de instalar e configurar a extensão do agente SQL IaaS através do portal.

> [!NOTE]
> A extensão do agente SQL Server IaaS só é suportada em imagens de [galeria SQL Server VM](../sql/virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms) (pay-as-you-go ou bring-your-own-license). Não é suportado se instalar manualmente o SQL Server numa máquina virtual do Windows Server apenas para OS ou se implementar um VHD VM D SQL personalizado. Nestes casos, pode ser possível instalar e gerir a extensão manualmente utilizando o PowerShell, mas é fortemente recomendado instalar uma imagem de galeria SQL Server VM e depois personalizá-la.

## <a name="status"></a>Estado
Uma forma de verificar se a extensão está instalada é ver o estado do agente no Portal Azure. Selecione uma máquina virtual listada na lâmina virtual e, em seguida, clique em **Extensões**. Deve ver a extensão **SQLIaSAgent** listada.

![Extensão do agente IaaS do Servidor SQL no Portal Azure](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-portal.png)

Também pode utilizar o **cmdlet Get-AzureVMSqlServerExtension** Azure Powershell.

    Get-AzureVM –ServiceName "service" –Name "vmname" | Get-AzureVMSqlServerExtension

## <a name="removal"></a>Remoção
No Portal Azure, pode desinstalar a extensão clicando na elipse na lâmina de **extensões** das suas propriedades de máquina virtual. Em seguida, clique em **Desinstalar**.

![Desinstale a extensão do agente IaaS do Servidor SQL no Portal Azure](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-uninstall.png)

Também pode utilizar o cmdlet de powershell **de extensão remove-AzureVMSqlServer.**

    Get-AzureVM –ServiceName "service" –Name "vmname" | Remove-AzureVMSqlServerExtension | Update-AzureVM

## <a name="next-steps"></a>Passos Seguintes
Comece a utilizar um dos serviços suportados pela extensão. Para mais detalhes, consulte os tópicos referenciados na secção de [serviços suportados](#supported-services) deste artigo.

Para obter mais informações sobre a execução do Servidor SQL em Máquinas Virtuais Azure, consulte [o Servidor SQL na visão geral das Máquinas Virtuais Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

