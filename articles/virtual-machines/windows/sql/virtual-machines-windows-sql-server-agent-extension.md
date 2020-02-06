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
ms.openlocfilehash: 3d16c1950cbae0bcc7dd858e5520eb8bfc6e496d
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77030783"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-by-using-the-sql-server-iaas-agent-extension"></a>Automatizar tarefas de gerenciamento em máquinas virtuais do Azure usando a extensão SQL Server IaaS Agent
> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-sql-server-agent-extension.md)
> * [Clássico](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)

A Extensão Agende IaaS do SQL Server (SqlIaasExtension) é executada nas máquinas virtuais do Azure para automatizar as tarefas de administração. Este artigo fornece uma visão geral dos serviços aos quais a extensão dá suporte. Este artigo também fornece instruções para instalação, status e remoção da extensão.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Para ver a versão clássica deste artigo, consulte a extensão do [agente SQL Server IaaS para VMs sQL Server (clássico)](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md).


## <a name="supported-services"></a>Serviços suportados
A extensão do agente IaaS SQL Server dá suporte às seguintes tarefas de administração:

| Recurso de administração | Descrição |
| --- | --- |
| **Backup automatizado do SQL Server** |Automatiza o agendamento de backups para todas as bases de dados para a instância predefinida ou para uma instância de nome [sql corretamente instalada](virtual-machines-windows-sql-server-iaas-faq.md#administration) no VM. Para mais informações, consulte [backup automatizado para O Servidor SQL em máquinas virtuais Azure (Gestor de Recursos)](virtual-machines-windows-sql-automated-backup.md). |
| **Patching automatizado do Servidor SQL** |Configura uma janela de manutenção durante a qual as atualizações importantes do Windows para sua VM podem ocorrer, para que você possa evitar atualizações durante horários de pico para sua carga de trabalho. Para mais informações, consulte [patching automatizado para O Servidor SQL em máquinas virtuais Azure (Gestor de Recursos)](virtual-machines-windows-sql-automated-patching.md). |
| **Integração do cofre de chaves Azure** |Permite que você instale e configure automaticamente Azure Key Vault em sua VM SQL Server. Para mais informações, consulte a integração do [Cofre chave Configure Azure para o Servidor SQL em Máquinas Virtuais Azure (Gestor de Recursos)](virtual-machines-windows-ps-sql-keyvault.md). |

Depois que a extensão do agente IaaS SQL Server estiver instalada e em execução, ela disponibilizará os recursos de administração:

* No painel de SQL Server da máquina virtual na portal do Azure e por meio de Azure PowerShell para SQL Server imagens no Azure Marketplace.
* Por meio de Azure PowerShell para instalações manuais da extensão. 

## <a name="prerequisites"></a>Pré-requisitos
Aqui estão os requisitos para usar a extensão do agente IaaS SQL Server em sua VM:

**Sistema operativo:**

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016
* Windows Server 2019 

**Versão SQL Server**:

* SQL Server 2008 
* SQL Server 2008 R2
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016
* SQL Server 2017
* SQL Server 2019

**Azure PowerShell:**

* [Descarregue e configure os mais recentes comandos Azure PowerShell](/powershell/azure/overview)

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]


##  <a name="installation"></a>Instalação
A extensão SQL Server IaaS é instalada quando regista o seu VM do Servidor SQL com o fornecedor de [recursos SQL VM](virtual-machines-windows-sql-register-with-resource-provider.md). Se necessário, você pode instalar o agente IaaS SQL Server manualmente usando o comando do PowerShell abaixo: 

  ```powershell-interactive
    Set-AzVMSqlServerExtension -VMName "sql2017" `
    -ResourceGroupName "LabsqlIAASagent" -Name "SQLIaasExtension" `
    -Version "2.0" -Location "Central US";  
  ```

> [!NOTE]
> A instalação da extensão reinicia o serviço SQL Server. 


### <a name="install-on-a-vm-with-a-single-named-sql-server-instance"></a>Instalar em uma VM com uma única instância de SQL Server nomeada
A extensão SQL Server IaaS funcionará com uma instância nomeada no SQL Server se a instância padrão for desinstalada e a extensão IaaS for reinstalada.

Para usar uma instância nomeada do SQL Server, siga estas etapas:
   1. Implante uma VM SQL Server do Azure Marketplace. 
   1. Desinstale a extensão IaaS do [portal Azure](https://portal.azure.com).
   1. Desinstale SQL Server completamente dentro da VM SQL Server.
   1. Instale SQL Server com uma instância nomeada dentro da VM SQL Server. 
   1. Instale a extensão IaaS do portal do Azure.  


## <a name="get-the-status-of-the-sql-server-iaas-extension"></a>Obter o status da extensão de IaaS SQL Server
Uma maneira de verificar se a extensão está instalada é exibir o status do agente no portal do Azure. **Selecione todas as definições** na janela da máquina virtual e, em seguida, selecione **Extensões**. Deve ver a extensão **sqliaasExtension** listada.

![Status da extensão do agente IaaS SQL Server no portal do Azure](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-portal.png)

Também pode utilizar o cmdlet **Get-AzVMSqlServerExtension** Azure PowerShell:

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
No portal Azure, pode desinstalar a extensão selecionando a elipse na janela **Extensões** das suas propriedades de máquina virtual. Em seguida, selecione **Eliminar**.

![Desinstalando a extensão do agente IaaS SQL Server no portal do Azure](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-uninstall.png)

Também pode utilizar o cmdlet de powerShell de **extensão remove-AzVMSqlServer:**

   ```powershell-interactive
    Remove-AzVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SqlIaasExtension"
   ```

## <a name="next-steps"></a>Passos seguintes
Comece a usar um dos serviços que a extensão dá suporte. Para mais informações, consulte os artigos referenciados na secção de [serviços suportados](#supported-services) deste artigo.

Para obter mais informações sobre a execução do Servidor SQL em Máquinas Virtuais Azure, consulte o [Servidor SQL em Máquinas Virtuais Azure?](virtual-machines-windows-sql-server-iaas-overview.md)
