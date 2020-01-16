---
title: Automatizar tarefas de gerenciamento em VMs do SQL (clássicas) | Microsoft Docs
description: Este tópico descreve como gerenciar a extensão do agente de SQL Server, que automatiza tarefas específicas de administração de SQL Server. Isso inclui backup automatizado, aplicação de patch automatizada e integração de Azure Key Vault. Este tópico usa o modo de implantação clássico.
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
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75982274"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-with-the-sql-server-agent-extension-classic"></a>Automatizar tarefas de gerenciamento em máquinas virtuais do Azure com a extensão SQL Server Agent (clássica)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-server-agent-extension.md)
> * [Clássico](../classic/sql-server-agent-extension.md)
> 
>
 
O SQL Server SQLIaaSAgent (extensão do agente IaaS) é executado em máquinas virtuais do Azure para automatizar tarefas de administração. Este tópico fornece uma visão geral dos serviços com suporte na extensão, bem como instruções de instalação, status e remoção.

> [!IMPORTANT] 
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Gerenciador de recursos e clássico](../../../azure-resource-manager/management/deployment-models.md). Este artigo aborda o uso do modelo de implantação clássico. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager. Para exibir a versão do Resource Manager deste artigo, consulte [extensão de SQL Server Agent para o Gerenciador de recursos de VMs SQL Server](../sql/virtual-machines-windows-sql-server-agent-extension.md).

## <a name="supported-services"></a>Serviços suportados
A extensão do agente IaaS SQL Server dá suporte às seguintes tarefas de administração:

| Recurso de administração | Descrição |
| --- | --- |
| **Backup automatizado do SQL** |Automatiza o agendamento de backups para todos os bancos de dados para a instância padrão do SQL Server na VM. Para obter mais informações, consulte [backup automatizado para SQL Server em máquinas virtuais do Azure (clássico)](../classic/sql-automated-backup.md). |
| **Aplicação de patch automatizada do SQL** |Configura uma janela de manutenção durante a qual as atualizações importantes do Windows para sua VM podem ocorrer, para que você possa evitar atualizações durante horários de pico para sua carga de trabalho. Para obter mais informações, consulte [aplicação de patch automatizada para SQL Server em máquinas virtuais do Azure (clássico)](../classic/sql-automated-patching.md). |
| **Integração do Cofre de Chaves do Azure** |Permite que você instale e configure automaticamente Azure Key Vault em sua VM SQL Server. Para obter mais informações, consulte [Configurar a integração de Azure Key Vault para SQL Server em VMs do Azure (clássico)](../classic/ps-sql-keyvault.md). |

## <a name="prerequisites"></a>Pré-requisitos
Requisitos para usar a extensão do agente IaaS SQL Server em sua VM:

### <a name="operating-system"></a>Sistema Operativo:
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

### <a name="sql-server-versions"></a>SQL Server versões:
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

### <a name="azure-powershell"></a>Azure PowerShell:
[Baixe e configure os comandos de Azure PowerShell mais recentes](/powershell/azure/overview).

Inicie o Windows PowerShell e conecte-o à sua assinatura do Azure com o comando **Add-AzureAccount** .

    Add-AzureAccount

Se você tiver várias assinaturas, use **Select-AzureSubscription** para selecionar a assinatura que contém a VM clássica de destino.

    Select-AzureSubscription -SubscriptionName <subscriptionname>

Neste ponto, você pode obter uma lista das máquinas virtuais clássicas e seus nomes de serviço associados com o comando **Get-AzureVM** .

    Get-AzureVM

## <a name="installation"></a>Instalação
Para VMs clássicas, você deve usar o PowerShell para instalar o SQL Server extensão do agente IaaS e configurar seus serviços associados. Use o cmdlet do PowerShell **set-AzureVMSqlServerExtension** para instalar a extensão. Por exemplo, o comando a seguir instala a extensão em uma VM do Windows Server (clássica) e a nomeia como "SQLIaaSExtension".

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -ReferenceName "SQLIaasExtension" -Version "1.2" | Update-AzureVM

Se você atualizar para a versão mais recente da extensão do SQL IaaS Agent, deverá reiniciar a máquina virtual depois de atualizar a extensão.

> [!NOTE]
> As máquinas virtuais clássicas não têm uma opção para instalar e configurar a extensão do SQL IaaS Agent por meio do Portal.

> [!NOTE]
> A extensão do agente IaaS SQL Server só tem suporte em [imagens da Galeria de VM SQL Server](../sql/virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms) (pré-pago ou traga sua própria licença). Não haverá suporte se você instalar manualmente SQL Server em uma máquina virtual do Windows Server somente so ou se implantar um VHD de VM de SQL Server personalizado. Nesses casos, pode ser possível instalar e gerenciar a extensão manualmente usando o PowerShell, mas é altamente recomendável instalar uma imagem da Galeria de VM SQL Server e, em seguida, personalizá-la.

## <a name="status"></a>Estado
Uma maneira de verificar se a extensão está instalada é exibir o status do agente no portal do Azure. Selecione uma máquina virtual listada na folha máquina virtual e, em seguida, clique em **extensões**. Você deve ver a extensão **SQLIaaSAgent** listada.

![SQL Server extensão do agente IaaS no portal do Azure](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-portal.png)

Você também pode usar o cmdlet **Get-AzureVMSqlServerExtension** do Azure PowerShell.

    Get-AzureVM –ServiceName "service" –Name "vmname" | Get-AzureVMSqlServerExtension

## <a name="removal"></a>Removidos
No portal do Azure, você pode desinstalar a extensão clicando nas reticências na folha **extensões** de suas propriedades de máquina virtual. Em seguida, clique em **desinstalar**.

![Desinstalar a extensão do agente IaaS SQL Server no portal do Azure](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-uninstall.png)

Você também pode usar o cmdlet do PowerShell **Remove-AzureVMSqlServerExtension** .

    Get-AzureVM –ServiceName "service" –Name "vmname" | Remove-AzureVMSqlServerExtension | Update-AzureVM

## <a name="next-steps"></a>Próximos Passos
Comece a usar um dos serviços com suporte pela extensão. Para obter mais detalhes, consulte os tópicos referenciados na seção [serviços com suporte](#supported-services) deste artigo.

Para obter mais informações sobre como executar SQL Server em máquinas virtuais do Azure, consulte a [visão geral de SQL Server em máquinas virtuais do Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

