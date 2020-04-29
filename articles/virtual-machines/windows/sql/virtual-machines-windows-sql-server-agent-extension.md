---
title: Automatizar tarefas de gestão com a extensão do agente IaaS
description: Este artigo descreve como gerir a extensão do agente SQL Server IaaS, que automatiza tarefas específicas de administração do Servidor SQL. Estes incluem backup automatizado, patching automatizado e integração do Cofre chave Azure.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77030783"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-by-using-the-sql-server-iaas-agent-extension"></a>Automatizar tarefas de gestão em máquinas virtuais Azure utilizando a extensão do agente SQL Server IaaS
> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-sql-server-agent-extension.md)
> * [Clássica](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)

A Extensão Agende IaaS do SQL Server (SqlIaasExtension) é executada nas máquinas virtuais do Azure para automatizar as tarefas de administração. Este artigo fornece uma visão geral dos serviços que a extensão suporta. Este artigo também fornece instruções para a instalação, estado e remoção da extensão.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Para ver a versão clássica deste artigo, consulte a extensão do [agente SQL Server IaaS para VMs sQL Server (clássico)](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md).


## <a name="supported-services"></a>Serviços suportados
A extensão do agente SQL Server IaaS suporta as seguintes tarefas de administração:

| Característica de administração | Descrição |
| --- | --- |
| **Backup automatizado do SQL Server** |Automatiza o agendamento de backups para todas as bases de dados para a instância predefinida ou para uma instância de nome [sql corretamente instalada](virtual-machines-windows-sql-server-iaas-faq.md#administration) no VM. Para mais informações, consulte [backup automatizado para O Servidor SQL em máquinas virtuais Azure (Gestor de Recursos)](virtual-machines-windows-sql-automated-backup.md). |
| **Patching automatizado do Servidor SQL** |Configura uma janela de manutenção durante a qual podem ser efetuadas importantes atualizações do Windows para o seu VM, para que possa evitar atualizações durante os períodos de pico da sua carga de trabalho. Para mais informações, consulte [patching automatizado para O Servidor SQL em máquinas virtuais Azure (Gestor de Recursos)](virtual-machines-windows-sql-automated-patching.md). |
| **Integração do Cofre de Chaves do Azure** |Permite-lhe instalar e configurar automaticamente o Cofre de Chaves Azure no seu VM do Servidor SQL. Para mais informações, consulte a integração do [Cofre chave Configure Azure para o Servidor SQL em Máquinas Virtuais Azure (Gestor de Recursos)](virtual-machines-windows-ps-sql-keyvault.md). |

Após a instalação e funcionamento da extensão do agente SQL Server Iaas, disponibiliza as funcionalidades da administração:

* No painel SQL Server da máquina virtual no portal Azure e através do Azure PowerShell para imagens do SQL Server no Azure Marketplace.
* Através da Azure PowerShell para instalações manuais da extensão. 

## <a name="prerequisites"></a>Pré-requisitos
Aqui estão os requisitos para utilizar a extensão do agente SQL Server IaaS no seu VM:

**Sistema operativo:**

* Windows Server 2008 R2
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
A extensão SQL Server IaaS é instalada quando regista o seu VM do Servidor SQL com o fornecedor de [recursos SQL VM](virtual-machines-windows-sql-register-with-resource-provider.md). Se necessário, pode instalar manualmente o agente SQL Server IaaS utilizando o comando abaixo da PowerShell: 

  ```powershell-interactive
    Set-AzVMSqlServerExtension -VMName "sql2017" `
    -ResourceGroupName "LabsqlIAASagent" -Name "SQLIaasExtension" `
    -Version "2.0" -Location "Central US";  
  ```

> [!NOTE]
> A instalação da extensão reinicia o serviço SQL Server. 


### <a name="install-on-a-vm-with-a-single-named-sql-server-instance"></a>Instale num VM com uma única instância de Servidor SQL
A extensão SQL Server IaaS funcionará com uma instância nomeada no Servidor SQL se a instância padrão não for instalada e a extensão IaaS for reinstalada.

Para utilizar uma instância nomeada do Servidor SQL, siga estes passos:
   1. Implemente um VM de servidor SQL a partir do Azure Marketplace. 
   1. Desinstale a extensão IaaS do [portal Azure](https://portal.azure.com).
   1. Desinstale completamente o Servidor SQL dentro do VM do Servidor SQL.
   1. Instale o Servidor SQL com uma instância nomeada dentro do VM do Servidor SQL. 
   1. Instale a extensão IaaS do portal Azure.  


## <a name="get-the-status-of-the-sql-server-iaas-extension"></a>Obtenha o estado da extensão SQL Server IaaS
Uma forma de verificar se a extensão está instalada é ver o estado do agente no portal Azure. **Selecione todas as definições** na janela da máquina virtual e, em seguida, selecione **Extensões**. Deve ver a extensão **sqliaasExtension** listada.

![Estado da extensão do agente SQL Server IaaS no portal Azure](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-portal.png)

Também pode utilizar o cmdlet **Get-AzVMSqlServerExtension** Azure PowerShell:

   ```powershell-interactive
   Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
   ```

O comando anterior confirma que o agente está instalado e fornece informações gerais sobre o estado. Pode obter informações específicas sobre backup automatizado e patching utilizando os seguintes comandos:

   ```powershell-interactive
    $sqlext = Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings
   ```

## <a name="removal"></a>Remoção
No portal Azure, pode desinstalar a extensão selecionando a elipse na janela **Extensões** das suas propriedades de máquina virtual. Em seguida, selecione **Eliminar**.

![Desinstalação da extensão do agente IaaS do Servidor SQL no portal Azure](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-uninstall.png)

Também pode utilizar o cmdlet de powerShell de **extensão remove-AzVMSqlServer:**

   ```powershell-interactive
    Remove-AzVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SqlIaasExtension"
   ```

## <a name="next-steps"></a>Passos seguintes
Comece a utilizar um dos serviços que a extensão suporta. Para mais informações, consulte os artigos referenciados na secção de [serviços suportados](#supported-services) deste artigo.

Para obter mais informações sobre a execução do Servidor SQL em Máquinas Virtuais Azure, consulte o [Servidor SQL em Máquinas Virtuais Azure?](virtual-machines-windows-sql-server-iaas-overview.md)
