---
title: Automatizar tarefas de gestão em SQL VMs (Classic) Microsoft Docs
description: Este tópico descreve como gerir a extensão do agente do SQL Server, que automatiza tarefas específicas de administração do SQL Server. Estes incluem cópia de segurança automatizada, patching automatizado e integração de cofre de chaves Azure. Este tópico utiliza o modo de implementação clássico.
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
ms.openlocfilehash: bd913b597e52f81c19b9c6bb20e83be23e5b35bd
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86134705"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-with-the-sql-server-agent-extension-classic"></a>Automatizar tarefas de gestão em Máquinas Virtuais Azure com a Extensão do Agente do Servidor SQL (Clássico)
> [!div class="op_single_selector"]
> * [Resource Manager](../../../azure-sql/virtual-machines/windows/sql-server-iaas-agent-extension-automate-management.md)
> * [Clássico](virtual-machines-windows-classic-sql-server-agent-extension.md)
> 
>
 
A Extensão do Agente IAAS do SQL Server (SQLIaaSAgent) funciona em máquinas virtuais Azure para automatizar tarefas de administração. Este tópico fornece uma visão geral dos serviços suportados pela extensão, bem como instruções de instalação, estado e remoção.

> [!IMPORTANT] 
> A Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Gestor de Recursos e Classic.](../../../azure-resource-manager/management/deployment-models.md) Este artigo abrange utilizando o modelo de implantação Classic. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager. Para ver a versão do Gestor de Recursos deste artigo, consulte a Extensão do [Agente do Servidor SQL para O Gestor de Recursos VMs do SQL Server](../../../azure-sql/virtual-machines/windows/sql-server-iaas-agent-extension-automate-management.md).

## <a name="supported-services"></a>Serviços suportados
A extensão do agente do servidor SQL IaaS suporta as seguintes tarefas de administração:

| Recurso de administração | Descrição |
| --- | --- |
| **Cópia de Segurança Automatizada do SQL** |Automatiza o agendamento de backups para todas as bases de dados para a instância padrão do SQL Server no VM. Para obter mais informações, consulte [a cópia de segurança automatizada para O Servidor SQL em Máquinas Virtuais Azure (Clássico)](../classic/sql-automated-backup.md). |
| **Aplicação de Patches Automatizada do SQL** |Configura uma janela de manutenção durante a qual podem ocorrer atualizações importantes do Windows para o seu VM, para que possa evitar atualizações durante os tempos de pico da sua carga de trabalho. Para obter mais informações, consulte [patching automatizado para SQL Server em Azure Virtual Machines (Classic)](../classic/sql-automated-patching.md). |
| **Integração do Cofre de Chaves do Azure** |Permite-lhe instalar e configurar automaticamente o Cofre da Chave Azure no seu SQL Server VM. Para obter mais informações, consulte a Integração do [Cofre de Chave Configure Para O Servidor SQL em VMs Azure (Clássico)](../classic/ps-sql-keyvault.md). |

## <a name="prerequisites"></a>Pré-requisitos
Requisitos para utilizar a extensão do agente IAAS do sql server no seu VM:

### <a name="operating-system"></a>Sistema operativo:
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

### <a name="sql-server-versions"></a>Versões SQL Server:
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

### <a name="azure-powershell"></a>Azure PowerShell:
[Descarregue e configuure os mais recentes comandos Azure PowerShell](/powershell/azure/overview).

Inicie o Windows PowerShell e conecte-o à sua subscrição Azure com o comando **Add-AzureAccount.**

```azurepowershell
Add-AzureAccount
```

Se tiver várias subscrições, utilize **a Subscrição Select-AzureSubscription** para selecionar a subscrição que contém o seu VM clássico alvo.

```azurepowershell
Select-AzureSubscription -SubscriptionName <subscriptionname>
```

Neste momento, você pode obter uma lista das máquinas virtuais clássicas e seus nomes de serviço associados com o comando **Get-AzureVM.**

```azurepowershell
Get-AzureVM
```

## <a name="installation"></a>Instalação
Para os VMs clássicos, deve utilizar o PowerShell para instalar a Extensão do Agente IAAS do Servidor SQL e configurar os seus serviços associados. Utilize o **set-AzureVMSqlServerExtension** PowerShell cmdlet para instalar a extensão. Por exemplo, o seguinte comando instala a extensão num VM do Windows Server (clássico) e dá-lhe o nome de "SQLIaaSExtension".

```azurepowershell
Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -ReferenceName "SQLIaasExtension" -Version "1.2" | Update-AzureVM
```

Se atualizar para a versão mais recente da Extensão do Agente IAAS SQL, deve reiniciar a sua máquina virtual depois de atualizar a extensão.

> [!NOTE]
> As máquinas virtuais clássicas não têm opção de instalar e configurar a Extensão do Agente IAAS SQL através do portal.

> [!NOTE]
> A extensão do agente do SQL Server IaaS só é suportada em imagens da [galeria SQL Server VM](../../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md#get-started-with-sql-server-vms) (pay-as-you-go ou bring-your-own-license). Não é suportado se instalar manualmente o SQL Server numa máquina virtual do Windows Server apenas com OS ou se implementar um VHD VM VHD do Servidor SQL personalizado. Nestes casos, pode ser possível instalar e gerir a extensão manualmente utilizando o PowerShell, mas é fortemente recomendado instalar uma imagem de galeria SQL Server VM e depois personalizá-la.

## <a name="status"></a>Estado
Uma forma de verificar se a extensão está instalada é visualizar o estado do agente no Portal Azure. Selecione uma máquina virtual listada na lâmina da máquina virtual e, em seguida, clique em **Extensões**. Você deve ver a extensão **SQLIaaSAgent** listada.

![Extensão do agente iaas do servidor SQL no Portal Azure](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-portal.png)

Também pode utilizar o **cmdlet Get-AzureVMSqlServerExtension** Azure Powershell.

```azurepowershell
Get-AzureVM –ServiceName "service" –Name "vmname" | Get-AzureVMSqlServerExtension
```

## <a name="removal"></a>Remoção
No Portal Azure, é possível desinstalar a extensão clicando na **elipse** na lâmina extensões das propriedades da sua máquina virtual. Em seguida, clique **em Desinstalar**.

![Desinstalar a extensão do agente iaas do servidor SQL no Portal Azure](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-uninstall.png)

Também pode utilizar o **cmdlet Remove-AzureVMSqlServerExtension** Powershell.

```azurepowershell
Get-AzureVM –ServiceName "service" –Name "vmname" | Remove-AzureVMSqlServerExtension | Update-AzureVM
```

## <a name="next-steps"></a>Passos Seguintes
Comece a utilizar um dos serviços suportados pela extensão. Para mais detalhes, consulte os tópicos referenciados na secção de [serviços apoiados](#supported-services) deste artigo.

Para obter mais informações sobre a execução do SQL Server em Azure Virtual Machines, consulte [o SQL Server na visão geral das Máquinas Virtuais Azure](../../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md).

