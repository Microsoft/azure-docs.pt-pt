---
title: Automatizar tarefas de gerenciamento com a extensão do agente IaaS
description: Este artigo descreve como gerenciar a extensão do agente IaaS SQL Server, que automatiza tarefas específicas de administração de SQL Server. Isso inclui backup automatizado, aplicação de patch automatizada e integração de Azure Key Vault.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: jroth
editor: ''
tags: azure-resource-manager
ms.assetid: effe4e2f-35b5-490a-b5ef-b06746083da4
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/30/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 9aae386e21df6711fc4984a7abfd34f418399f76
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034204"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-by-using-the-sql-server-iaas-agent-extension"></a>Automatizar tarefas de gerenciamento em máquinas virtuais do Azure usando a extensão SQL Server IaaS Agent
> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-sql-server-agent-extension.md)
> * [Clássico](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)

A Extensão Agende IaaS do SQL Server (SqlIaasExtension) é executada nas máquinas virtuais do Azure para automatizar as tarefas de administração. Este artigo fornece uma visão geral dos serviços aos quais a extensão dá suporte. Este artigo também fornece instruções para instalação, status e remoção da extensão.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Para exibir a versão clássica deste artigo, consulte [SQL Server extensão do agente IaaS para VMs SQL Server (clássico)](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md).


## <a name="supported-services"></a>Serviços suportados
A extensão do agente IaaS SQL Server dá suporte às seguintes tarefas de administração:

| Recurso de administração | Descrição |
| --- | --- |
| **SQL Server backup automatizado** |Automatiza o agendamento de backups para todos os bancos de dados para a instância padrão ou uma instância nomeada do SQL Server [instalada corretamente](virtual-machines-windows-sql-server-iaas-faq.md#administration) na VM. Para obter mais informações, consulte [backup automatizado para SQL Server em máquinas virtuais do Azure (Resource Manager)](virtual-machines-windows-sql-automated-backup.md). |
| **SQL Server aplicação de patch automatizada** |Configura uma janela de manutenção durante a qual as atualizações importantes do Windows para sua VM podem ocorrer, para que você possa evitar atualizações durante horários de pico para sua carga de trabalho. Para obter mais informações, consulte [aplicação de patch automatizada para SQL Server em máquinas virtuais do Azure (Resource Manager)](virtual-machines-windows-sql-automated-patching.md). |
| **Integração do Azure Key Vault** |Permite que você instale e configure automaticamente Azure Key Vault em sua VM SQL Server. Para obter mais informações, consulte [Configurar a integração de Azure Key Vault para SQL Server em máquinas virtuais do Azure (Gerenciador de recursos)](virtual-machines-windows-ps-sql-keyvault.md). |

Depois que a extensão do agente IaaS SQL Server estiver instalada e em execução, ela disponibilizará os recursos de administração:

* No painel de SQL Server da máquina virtual na portal do Azure e por meio de Azure PowerShell para SQL Server imagens no Azure Marketplace.
* Por meio de Azure PowerShell para instalações manuais da extensão. 

## <a name="prerequisites"></a>Pré-requisitos
Aqui estão os requisitos para usar a extensão do agente IaaS SQL Server em sua VM:

**Sistema operacional**:

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016
* Windows Server 2019 

**Versão do SQL Server**:

* SQL Server 2008 
* SQL Server 2008 R2
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016
* SQL Server 2017

**Azure PowerShell**:

* [Baixar e configurar os comandos de Azure PowerShell mais recentes](/powershell/azure/overview)

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]


##  <a name="installation"></a>Instalação
A extensão de IaaS SQL Server é instalada quando você registra sua VM de SQL Server com o [provedor de recursos de VM do SQL](virtual-machines-windows-sql-register-with-resource-provider.md). Se necessário, você pode instalar o agente IaaS SQL Server manualmente usando o comando do PowerShell abaixo: 

  ```powershell-interactive
    Set-AzVMExtension -ResourceGroupName "<ResourceGroupName>" `
    -Location "<VMLocation>" -VMName "<VMName>" `
    -Name "SqlIaasExtension" -Publisher "Microsoft.SqlServer.Management" `
    -ExtensionType "SqlIaaSAgent" -TypeHandlerVersion "2.0";  
  ```

> [!NOTE]
> A instalação da extensão reinicia o serviço SQL Server. 


### <a name="install-on-a-vm-with-a-single-named-sql-server-instance"></a>Instalar em uma VM com uma única instância de SQL Server nomeada
A extensão SQL Server IaaS funcionará com uma instância nomeada no SQL Server se a instância padrão for desinstalada e a extensão IaaS for reinstalada.

Para usar uma instância nomeada do SQL Server, siga estas etapas:
   1. Implante uma VM SQL Server do Azure Marketplace. 
   1. Desinstale a extensão IaaS da [portal do Azure](https://portal.azure.com).
   1. Desinstale SQL Server completamente dentro da VM SQL Server.
   1. Instale SQL Server com uma instância nomeada dentro da VM SQL Server. 
   1. Instale a extensão IaaS do portal do Azure.  


## <a name="get-the-status-of-the-sql-server-iaas-extension"></a>Obter o status da extensão de IaaS SQL Server
Uma maneira de verificar se a extensão está instalada é exibir o status do agente no portal do Azure. Selecione **todas as configurações** na janela da máquina virtual e, em seguida, selecione **extensões**. Você deve ver a extensão **SqlIaasExtension** listada.

![Status da extensão do agente IaaS SQL Server no portal do Azure](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-portal.png)

Você também pode usar o cmdlet **Get-AzVMSqlServerExtension** Azure PowerShell:

   ```powershell-interactive
   Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
   ```

O comando anterior confirma que o agente está instalado e fornece informações gerais de status. Você pode obter informações de status específicas sobre backup e aplicação de patch automatizados usando os seguintes comandos:

   ```powershell-interactive
    $sqlext = Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings
   ```

## <a name="removal"></a>Removidos
No portal do Azure, você pode desinstalar a extensão selecionando as reticências na janela **extensões** de suas propriedades de máquina virtual. Em seguida, selecione **Eliminar**.

![Desinstalando a extensão do agente IaaS SQL Server no portal do Azure](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-uninstall.png)

Você também pode usar o cmdlet do PowerShell **Remove-AzVMSqlServerExtension** :

   ```powershell-interactive
    Remove-AzVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SqlIaasExtension"
   ```

## <a name="next-steps"></a>Passos seguintes
Comece a usar um dos serviços que a extensão dá suporte. Para obter mais informações, consulte os artigos mencionados na seção [serviços com suporte](#supported-services) deste artigo.

Para obter mais informações sobre como executar SQL Server em máquinas virtuais do Azure, consulte o [que é SQL Server nas máquinas virtuais do Azure?](virtual-machines-windows-sql-server-iaas-overview.md).
