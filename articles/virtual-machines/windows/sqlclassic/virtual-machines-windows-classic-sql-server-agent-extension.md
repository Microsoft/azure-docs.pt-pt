---
title: Automatizar tarefas de gestão em VMs do SQL (clássico) | Documentos da Microsoft
description: Este tópico descreve como gerir a extensão de agente do SQL Server, que automatiza tarefas de administração do SQL Server específicas. Estes incluem a cópia de segurança automatizada, a aplicação de patches automatizada e integração do Azure Key Vault. Este tópico utiliza o modo de implementação clássica.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: a9bda2e7-cdba-427c-bc30-77cde4376f3a
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/12/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 2b719185aabd39cd70b9cb890a9599aa06ca4ff4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60334850"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-with-the-sql-server-agent-extension-classic"></a>Automatizar tarefas de gestão em máquinas virtuais do Azure com a extensão de agente do SQL Server (clássico)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-server-agent-extension.md)
> * [Clássico](../classic/sql-server-agent-extension.md)
> 
>
 
Extensão do agente IaaS do SQL Server (SQLIaaSAgent) é executado em máquinas virtuais do Azure para automatizar tarefas de administração. Este tópico fornece uma descrição geral dos serviços suportados pela extensão, bem como instruções para instalação, o estado e remoção.

> [!IMPORTANT] 
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Resource Manager e clássica](../../../azure-resource-manager/resource-manager-deployment-model.md). Este artigo explica como utilizar o modelo de implementação clássica. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager. Para ver a versão do Resource Manager deste artigo, consulte [extensão de agente do SQL Server para o SQL Server VMs do Resource Manager](../sql/virtual-machines-windows-sql-server-agent-extension.md).

## <a name="supported-services"></a>Serviços suportados
A extensão do agente IaaS do SQL Server suporta as seguintes tarefas de administração:

| Funcionalidade de administração | Descrição |
| --- | --- |
| **Cópia de segurança automatizada do SQL** |Automatiza o agendamento de cópias de segurança para todas as bases de dados para a instância predefinida do SQL Server na VM. Para obter mais informações, consulte [cópia de segurança automatizada para SQL Server em máquinas virtuais do Azure (clássico)](../classic/sql-automated-backup.md). |
| **Aplicação de patches automatizada de SQL** |Configura uma janela de manutenção durante o qual as atualizações importantes do Windows para a VM podem ser feita, para que pode evitar atualizações durante as horas de pico para a sua carga de trabalho. Para obter mais informações, consulte [patching automatizado para SQL Server em máquinas virtuais do Azure (clássico)](../classic/sql-automated-patching.md). |
| **Integração do Cofre de Chaves do Azure** |Permite-lhe automaticamente instalado e configurado o Azure Key Vault na sua VM do SQL Server. Para obter mais informações, consulte [configurar o Azure integração Key Vault para o SQL Server em VMs do Azure (clássico)](../classic/ps-sql-keyvault.md). |

## <a name="prerequisites"></a>Pré-requisitos
Requisitos para utilizar a extensão do agente IaaS do SQL Server na sua VM:

### <a name="operating-system"></a>Sistema operativo:
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

### <a name="sql-server-versions"></a>Versões do SQL Server:
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

### <a name="azure-powershell"></a>Azure PowerShell:
[Transferir e configurar os comandos do Azure PowerShell mais recente](/powershell/azure/overview).

Inicie o Windows PowerShell e ligá-la à sua subscrição do Azure com o **Add-AzureAccount** comando.

    Add-AzureAccount

Se tiver várias subscrições, utilize **Select-AzureSubscription** para selecionar a subscrição que contém o destino de VM clássica.

    Select-AzureSubscription -SubscriptionName <subscriptionname>

Neste ponto, pode obter uma lista de máquinas virtuais clássicas e seus nomes de serviço associado com o **Get-AzureVM** comando.

    Get-AzureVM

## <a name="installation"></a>Instalação
Para VMs clássicas, tem de utilizar o PowerShell para instalar a extensão do agente IaaS do SQL Server e configurar aos serviços associados. Utilize o **Set-AzureVMSqlServerExtension** cmdlet do PowerShell para instalar a extensão. Por exemplo, o seguinte comando instala a extensão numa VM do Windows Server (clássico) e o nomeia como "SQLIaaSExtension".

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -ReferenceName "SQLIaasExtension" -Version "1.2" | Update-AzureVM

Se atualizar para a versão mais recente da extensão de agente IaaS do SQL, tem de reiniciar a máquina virtual depois de atualizar a extensão.

> [!NOTE]
> Máquinas virtuais clássicas não tem uma opção para instalar e configurar a extensão de agente do SQL IaaS através do portal.

> [!NOTE]
> A extensão do agente IaaS do SQL Server só é suportada no [imagens da Galeria VM do SQL Server](../sql/virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms) (pay as you go ou bring-your-own-license). Não é suportada se instalar manualmente o SQL Server numa máquina virtual apenas de sistema operacional Windows Server ou se implementar uma VM VHD personalizado do SQL Server. Nestes casos, poderá ser possível instalar e gerir a extensão manualmente com o PowerShell, mas é vivamente recomendado em vez disso, instale uma imagem de galeria VM do SQL Server e, em seguida, personalizá-lo.

## <a name="status"></a>Estado
É uma forma de verificar se a extensão está instalada ver o estado do agente no Portal do Azure. Selecione uma máquina virtual listada no painel da máquina virtual e, em seguida, clique em **extensões**. Deverá ver o **SQLIaaSAgent** extensão listado.

![Extensão do agente IaaS do SQL Server no Portal do Azure](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-portal.png)

Também pode utilizar o **Get-AzureVMSqlServerExtension** cmdlet do Powershell do Azure.

    Get-AzureVM –ServiceName "service" –Name "vmname" | Get-AzureVMSqlServerExtension

## <a name="removal"></a>Remoção
No Portal do Azure, pode desinstalar a extensão ao clicar nas reticências no **extensões** painel das suas propriedades de máquina virtual. Em seguida, clique em **desinstalação**.

![Desinstale a extensão de agente IaaS do SQL Server no Portal do Azure](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-uninstall.png)

Também pode utilizar o **Remove-AzureVMSqlServerExtension** cmdlet do Powershell.

    Get-AzureVM –ServiceName "service" –Name "vmname" | Remove-AzureVMSqlServerExtension | Update-AzureVM

## <a name="next-steps"></a>Próximos Passos
Começar a utilizar um dos serviços suportados pela extensão. Para obter mais detalhes, consulte os tópicos referenciados no [serviços suportados](#supported-services) seção deste artigo.

Para obter mais informações sobre a execução do SQL Server em máquinas de virtuais do Azure, consulte [SQL Server em Descrição geral de máquinas virtuais do Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

