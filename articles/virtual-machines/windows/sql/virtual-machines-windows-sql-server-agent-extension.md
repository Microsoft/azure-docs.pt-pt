---
title: Automatizar tarefas de gerenciamento em máquinas virtuais do Azure usando a extensão SQL Server IaaS Agent | Microsoft Docs
description: Este artigo descreve como gerenciar a extensão do agente IaaS SQL Server, que automatiza tarefas específicas de administração de SQL Server. Isso inclui backup automatizado, aplicação de patch automatizada e integração de Azure Key Vault.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: jroth
editor: ''
tags: azure-resource-manager
ms.assetid: effe4e2f-35b5-490a-b5ef-b06746083da4
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/24/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: f4dd529481a6216e43d35c76ecee734543d487f3
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100484"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-by-using-the-sql-server-iaas-agent-extension"></a>Automatizar tarefas de gerenciamento em máquinas virtuais do Azure usando a extensão SQL Server IaaS Agent
> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-sql-server-agent-extension.md)
> * [Clássico](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)

A Extensão Agende IaaS do SQL Server (SqlIaasExtension) é executada nas máquinas virtuais do Azure para automatizar as tarefas de administração. Este artigo fornece uma visão geral dos serviços aos quais a extensão dá suporte. Este artigo também fornece instruções para instalação, status e remoção da extensão.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Para exibir a versão clássica deste artigo, consulte [SQL Server extensão do agente IaaS para VMs SQL Server (clássico)](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md).

Há três modos de gerenciamento para a extensão de IaaS SQL Server: 

- O modo **completo** fornece todas as funcionalidades, mas requer uma reinicialização das permissões de SQL Server e administrador do sistema. Essa é a opção que é instalada por padrão. Use-o para gerenciar uma VM SQL Server com uma única instância. 

- **Leve** não requer a reinicialização de SQL Server, mas dá suporte apenas à alteração do tipo de licença e da edição do SQL Server. Use essa opção para SQL Server VMs com várias instâncias ou para participar de uma FCI (instância de cluster de failover). 

- **Noagent** é dedicado para SQL Server 2008 e SQL Server 2008 R2 instalado no Windows Server 2008. Para obter informações sobre como usar esse modo para sua imagem do Windows Server 2008, consulte [Windows server 2008 Registration](virtual-machines-windows-sql-register-with-resource-provider.md#register-sql-server-2008-or-2008-r2-on-windows-server-2008-vms). 

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
* Windows Server de 2019 

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


## <a name="change-management-modes"></a>Modos de gerenciamento de alterações

Você pode exibir o modo atual de seu SQL Server agente IaaS usando o PowerShell: 

  ```powershell-interactive
     #Get the SqlVirtualMachine
     $sqlvm = Get-AzResource -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName  -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines
     $sqlvm.Properties.sqlManagement
  ```

SQL Server VMs que têm a extensão de IaaS *leve* instalada podem atualizar o modo para _completo_ usando o portal do Azure. SQL Server VMs no modo _no-Agent_ podem ser atualizadas para _Full_ depois que o sistema operacional for atualizado para o Windows 2008 R2 e superior. Não é possível fazer o downgrade – para isso, você precisará desinstalar completamente a extensão IaaS do SQL e instalá-la novamente. 

Para atualizar o modo do agente para completo: 


# <a name="azure-portaltabazure-portal"></a>[Azure portal](#tab/azure-portal)

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Vá para o recurso de [máquinas virtuais do SQL](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) . 
1. Selecione sua SQL Server máquina virtual e selecione **visão geral**. 
1. Para SQL Server VMs com o modo noagent ou Lightweight IaaS, selecione o **único tipo de licença e atualizações de edição estão disponíveis com a mensagem de extensão IaaS do SQL** .

   ![Seleções para alterar o modo do portal](media/virtual-machines-windows-sql-server-agent-extension/change-sql-iaas-mode-portal.png)

1. Selecione a caixa de seleção **eu concordo em reiniciar o serviço de SQL Server na máquina virtual** e, em seguida, selecione **confirmar** para atualizar o modo IaaS para completo. 

    ![Caixa de seleção para concordar em reiniciar o serviço de SQL Server na máquina virtual](media/virtual-machines-windows-sql-server-agent-extension/enable-full-mode-iaas.png)

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

Execute o seguinte trecho de código AZ CLI:

  ```azurecli-interactive
  # Update to full mode

  az sql vm update --name <vm_name> --resource-group <resource_group_name> --sql-mgmt-type full  
  ```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

Execute o seguinte trecho de código do PowerShell:

  ```powershell-interactive
  # Update to full mode

  $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
  $SqlVm.Properties.sqlManagement="Full"
  $SqlVm | Set-AzResource -Force
  ```

---


##  <a name="installation"></a>Instalação
A extensão de IaaS SQL Server é instalada quando você registra sua VM de SQL Server com o [provedor de recursos de VM do SQL](virtual-machines-windows-sql-register-with-resource-provider.md). Se necessário, você pode instalar o agente IaaS SQL Server manualmente usando o modo completo ou leve. 

O SQL Server extensão do agente IaaS no modo completo é instalado automaticamente quando você provisiona uma das imagens SQL Server máquina virtual do Azure Marketplace usando o portal do Azure. 

### <a name="install-in-full-mode"></a>Instalar em modo completo
O modo completo para a extensão SQL Server IaaS oferece capacidade de gerenciamento total para uma única instância na VM SQL Server. Se houver uma instância padrão, a extensão funcionará com a instância padrão e não dará suporte ao gerenciamento de outras instâncias. Se não houver nenhuma instância padrão, mas apenas uma instância nomeada, ela gerenciará a instância nomeada. Se não houver nenhuma instância padrão e houver várias instâncias nomeadas, a extensão não será instalada. 

Instale o agente IaaS SQL Server com o modo completo usando o PowerShell:

  ```powershell-interactive
     # Get the existing compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     # Install 'Full' SQL Server IaaS agent extension
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;sqlServerLicenseType='AHUB';sqlManagement='Full'}  
  
  ```

| Parâmetro | Valores aceitáveis                        |
| :------------------| :-------------------------------|
| **sqlServerLicenseType** | `AHUB` ou `PAYG`     |
| &nbsp;             | &nbsp;                          |


> [!NOTE]
> Se a extensão ainda não estiver instalada, a instalação da extensão completa reiniciará o serviço de SQL Server. Para evitar a reinicialização do serviço de SQL Server, instale o modo leve com capacidade de gerenciamento limitada em vez disso.
> 
> Atualizar a extensão de IaaS SQL Server não reinicia o serviço SQL Server. 

### <a name="install-on-a-vm-with-a-single-named-sql-server-instance"></a>Instalar em uma VM com uma única instância de SQL Server nomeada
A extensão SQL Server IaaS funcionará com uma instância nomeada no SQL Server se a instância padrão for desinstalada e a extensão IaaS for reinstalada.

Para usar uma instância nomeada do SQL Server:
   1. Implante uma VM SQL Server do Azure Marketplace. 
   1. Desinstale a extensão IaaS da [portal do Azure](https://portal.azure.com).
   1. Desinstale SQL Server completamente dentro da VM SQL Server.
   1. Instale SQL Server com uma instância nomeada dentro da VM SQL Server. 
   1. Instale a extensão IaaS do portal do Azure.  


### <a name="install-in-lightweight-mode"></a>Instalar no modo leve
O modo leve não reiniciará o serviço SQL Server, mas ele oferece funcionalidade limitada. 

Instale o agente IaaS SQL Server com o modo leve usando o PowerShell:


  ```powershell-interactive
     /#Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     #Register the SQL Server VM with the 'Lightweight' SQL IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;sqlServerLicenseType='AHUB';sqlManagement='LightWeight'}  
  
  ```

| Parâmetro | Valores aceitáveis                        |
| :------------------| :-------------------------------|
| **sqlServerLicenseType** | `AHUB` ou `PAYG`     |
| &nbsp;             | &nbsp;                          |


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

## <a name="removal"></a>Remoção
No portal do Azure, você pode desinstalar a extensão selecionando as reticências na janela **extensões** de suas propriedades de máquina virtual. Em seguida, selecione **Eliminar**.

![Desinstalando a extensão do agente IaaS SQL Server no portal do Azure](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-uninstall.png)

Você também pode usar o cmdlet do PowerShell **Remove-AzVMSqlServerExtension** :

   ```powershell-interactive
    Remove-AzVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SqlIaasExtension"
   ```

## <a name="next-steps"></a>Passos Seguintes
Comece a usar um dos serviços que a extensão dá suporte. Para obter mais informações, consulte os artigos mencionados na seção [serviços com suporte](#supported-services) deste artigo.

Para obter mais informações sobre como executar SQL Server em máquinas virtuais do Azure, consulte o [que é SQL Server nas máquinas virtuais do Azure?](virtual-machines-windows-sql-server-iaas-overview.md).

