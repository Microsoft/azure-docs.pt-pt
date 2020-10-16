---
title: Automatizar tarefas de gestão com a Extensão do Agente IaaS
description: Este artigo descreve como gerir a extensão do agente iaaS do SQL Server, que automatiza tarefas específicas de administração do SQL Server. Estes incluem backup automatizado, patching automatizado e integração Azure Key Vault.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
editor: ''
tags: azure-resource-manager
ms.assetid: effe4e2f-35b5-490a-b5ef-b06746083da4
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/30/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: df3e2ae2737aa8bfedd0e8d7daa9d42771f1937b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91570349"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-by-using-the-sql-server-iaas-agent-extension"></a>Automatizar tarefas de gestão em máquinas virtuais Azure utilizando a extensão do agente iaaS do servidor SQL
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]


A Extensão Agende IaaS do SQL Server (SqlIaasExtension) é executada nas máquinas virtuais do Azure para automatizar as tarefas de administração. Este artigo fornece uma visão geral dos serviços que a extensão suporta. Este artigo também fornece instruções para a instalação, estado e remoção da extensão.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Para ver a versão clássica deste artigo, consulte a extensão do [agente do SQL Server IaaS para VMs do servidor SQL (clássico)](../../../virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md).


## <a name="supported-services"></a>Serviços suportados
A extensão do agente do servidor SQL IaaS suporta as seguintes tarefas de administração:

| Recurso de administração | Descrição |
| --- | --- |
| **Backup automatizado do SQL Server** |Automatiza o agendamento de backups para todas as bases de dados para a instância predefinida ou para uma instância nomeada [corretamente instalada](frequently-asked-questions-faq.md#administration) do SQL Server no VM. Para obter mais informações, consulte [a cópia de segurança automatizada para o SQL Server em máquinas virtuais Azure (Gestor de Recursos)](automated-backup-sql-2014.md). |
| **Patching automatizado do SQL Server** |Configura uma janela de manutenção durante a qual podem ocorrer atualizações importantes do Windows para o seu VM, para que possa evitar atualizações durante os tempos de pico da sua carga de trabalho. Para obter mais informações, consulte [patching automatizado para O Servidor SQL em máquinas virtuais Azure (Gestor de Recursos)](automated-patching.md). |
| **Integração do Cofre chave Azure** |Permite-lhe instalar e configurar automaticamente o Cofre da Chave Azure no seu SQL Server VM. Para obter mais informações, consulte [a integração do Cofre de Chave Configure para O Servidor SQL em Máquinas Virtuais Azure (Gestor de Recursos)](azure-key-vault-integration-configure.md). |

Depois de a extensão do agente do Sql Server Iaas ser instalada e em funcionamento, disponibiliza as funcionalidades da administração:

* No painel SQL Server da máquina virtual no portal Azure e através do Azure PowerShell para imagens do SQL Server no Azure Marketplace.
* Através do Azure PowerShell para instalações manuais da extensão. 

## <a name="prerequisites"></a>Pré-requisitos
Aqui estão os requisitos para utilizar a extensão do agente IAAS do SQL Server no seu VM:

**Sistema operativo:**

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016
* Windows Server 2019 

**Versão SQL Server**:

* SQL Server 2008 
* SQL Server 2008 R2
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016
* SQL Server 2017
* SQL Server 2019

**Azure PowerShell:**

* [Descarregue e configuure os mais recentes comandos Azure PowerShell](/powershell/azure/)

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]


##  <a name="installation"></a>Instalação
A extensão IAAS do Servidor SQL é instalada quando regista o seu SQL Server VM com o fornecedor de [recursos SQL Server VM](sql-vm-resource-provider-register.md). Se necessário, pode instalar manualmente o agente SQL Server IaaS utilizando o comando PowerShell abaixo: 

  ```powershell-interactive
    Set-AzVMSqlServerExtension -VMName "sql2017" `
    -ResourceGroupName "LabsqlIAASagent" -Name "SQLIaasExtension" `
    -Version "2.0" -Location "Central US";  
  ```

> [!WARNING]
> A instalação da extensão reinicia o serviço SQL Server. 


### <a name="install-on-a-vm-with-a-single-named-sql-server-instance"></a>Instale num VM com uma única instância chamada SQL Server
A extensão IAAS do Servidor SQL funcionará com uma instância nomeada no SQL Server se a instância padrão for desinstalada e a extensão IaaS for reinstalada.

Para utilizar uma instância nomeada do SQL Server, siga estes passos:
   1. Implementar um SQL Server VM do Azure Marketplace. 
   1. Desinstalar a extensão IaaS do [portal Azure](https://portal.azure.com).
   1. Desinstale completamente o SQL Server dentro do SQL Server VM.
   1. Instale o SQL Server com uma instância nomeada dentro do SQL Server VM. 
   1. Instale a extensão IaaS a partir do portal Azure.  


## <a name="get-the-status-of-the-sql-server-iaas-extension"></a>Obtenha o estado da extensão IAAS do Servidor SQL
Uma forma de verificar se a extensão está instalada é visualizar o estado do agente no portal Azure. Selecione **todas as definições** na janela da máquina virtual e, em seguida, selecione **Extensões**. Deve ver a extensão **sqlIaasExtension** listada.

![Estado da extensão do agente iaas do servidor SQL no portal Azure](./media/sql-server-iaas-agent-extension-automate-management/azure-rm-sql-server-iaas-agent-portal.png)

Também pode utilizar o **cmdlet Get-AzVMSqlServerExtension** Azure PowerShell:

   ```powershell-interactive
   Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
   ```

O comando anterior confirma que o agente está instalado e fornece informações gerais sobre o estado. Pode obter informações específicas sobre a cópia de segurança e remendos automatizados utilizando os seguintes comandos:

   ```powershell-interactive
    $sqlext = Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings
   ```

## <a name="removal"></a>Remoção
No portal Azure, é possível desinstalar a extensão selecionando a **elipse** na janela extensões das propriedades da sua máquina virtual. Em seguida, selecione **Eliminar**.

![Desinstalar a extensão do agente iaas do servidor SQL no portal Azure](./media/sql-server-iaas-agent-extension-automate-management/azure-rm-sql-server-iaas-agent-uninstall.png)

Também pode utilizar o **cmdlet Remove-AzVMSqlServerExtension** PowerShell:

   ```powershell-interactive
    Remove-AzVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SqlIaasExtension"
   ```

## <a name="next-steps"></a>Passos seguintes
Comece a usar um dos serviços que a extensão suporta. Para mais informações, consulte os artigos referenciados na secção de [serviços apoiados](#supported-services) deste artigo.

Para obter mais informações sobre a execução do SQL Server em Máquinas Virtuais Azure, consulte o [Servidor SQL em Máquinas Virtuais Azure?](sql-server-on-azure-vm-iaas-what-is-overview.md)
