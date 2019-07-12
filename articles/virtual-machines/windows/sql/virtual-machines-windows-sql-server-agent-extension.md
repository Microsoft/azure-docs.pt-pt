---
title: Automatizar tarefas de gestão em máquinas virtuais do Azure com a extensão do agente IaaS do SQL Server | Documentos da Microsoft
description: Este artigo descreve como gerir a extensão de agente do SQL Server, que automatiza tarefas de administração do SQL Server específicas. Estes incluem a cópia de segurança automatizada, a aplicação de patches automatizada e integração do Azure Key Vault.
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
ms.date: 06/24/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 41023103dc30d16f599e847f9d324bc7bb4be11c
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798058"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-with-the-sql-server-iaas-agent-extension"></a>Automatizar tarefas de gestão em máquinas virtuais do Azure com a extensão do agente IaaS do SQL Server
> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-sql-server-agent-extension.md)
> * [Clássico](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)

A Extensão Agende IaaS do SQL Server (SqlIaasExtension) é executada nas máquinas virtuais do Azure para automatizar as tarefas de administração. Este artigo fornece uma descrição geral e os serviços suportados pela extensão, bem como instruções para instalação, o estado e remoção.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Para ver a versão clássica deste artigo, consulte [extensão de agente do SQL Server para VMs clássicas do SQL Server](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md).

Existem três modos de capacidade de gerenciamento SQL para a extensão SQL IaaS: **Completa**, **Lightweight**, e **NoAgent**. 

- **Total** modo fornece todas as funcionalidades, mas requer um reinício das permissões do SQL Server e SA. Esta é a opção que é instalada por predefinição e deve ser utilizada para gerir uma VM do SQL Server com uma única instância. 

- **Lightweight** não requer o reinício do SQL Server, mas só suporta a alterar o tipo de licença e a edição do SQL Server. Esta opção deve ser utilizado para VMs do SQL Server com várias instâncias ou participantes numa instância de cluster de ativação pós-falha (FCI). 

- **NoAgent** encontra dedicado para o SQL Server 2008 e SQL Server 2008 R2 instalado no Windows Server 2008. Para obter informações sobre como usar `NoAgent` modo para que a imagem do Windows Server 2008, consulte [registo do Windows Server 2008](virtual-machines-windows-sql-register-with-resource-provider.md#register-sql-server-2008r2-on-windows-server-2008-vms). 

## <a name="supported-services"></a>Serviços suportados
A extensão do agente IaaS do SQL Server suporta as seguintes tarefas de administração:

| Funcionalidade de administração | Descrição |
| --- | --- |
| **Cópia de segurança automatizada do SQL** |Automatiza o agendamento de cópias de segurança para todas as bases de dados por qualquer instância predefinida ou uma [corretamente instalados](virtual-machines-windows-sql-server-iaas-faq.md#administration) com o nome a instância do SQL Server na VM. Para obter mais informações, consulte [cópia de segurança automatizada para SQL Server em máquinas virtuais do Azure (Resource Manager)](virtual-machines-windows-sql-automated-backup.md). |
| **Aplicação de patches automatizada de SQL** |Configura uma janela de manutenção durante o qual as atualizações importantes do Windows para a VM podem ser feita, para que pode evitar atualizações durante as horas de pico para a sua carga de trabalho. Para obter mais informações, consulte [patching automatizado para SQL Server em máquinas virtuais do Azure (Resource Manager)](virtual-machines-windows-sql-automated-patching.md). |
| **Integração do Cofre de Chaves do Azure** |Permite-lhe automaticamente instalado e configurado o Azure Key Vault na sua VM do SQL Server. Para obter mais informações, consulte [configurar o Azure integração Key Vault para o SQL Server em VMs do Azure (Resource Manager)](virtual-machines-windows-ps-sql-keyvault.md). |

Depois de instalado e em execução, a extensão do agente IaaS do SQL Server disponibiliza estas funcionalidades de administração no painel do SQL Server da máquina virtual no portal do Azure e através do PowerShell do Azure para imagens do marketplace do SQL Server e através do Azure PowerShell para instalações manuais da extensão. 

## <a name="prerequisites"></a>Pré-requisitos
Requisitos para utilizar a extensão do agente IaaS do SQL Server na sua VM:

**Sistema operativo**:

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016
* Windows Server de 2019 

**Versões do SQL Server**:

* SQL Server 2008 
* SQL Server 2008 R2
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016
* SQL Server 2017

**Azure PowerShell**:

* [Transferir e configurar os comandos do Azure PowerShell mais recente](/powershell/azure/overview)

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]


## <a name="change-management-modes"></a>Alterar os modos de gestão

Pode ver o modo atual do seu agente IaaS do SQL com o PowerShell: 

  ```powershell-interactive
     //Get the SqlVirtualMachine
     $sqlvm = Get-AzResource -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName  -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines
     $sqlvm.Properties.sqlManagement
  ```

Para VMs do SQL Server que têm o *NoAgent* ou *leve* extensão de IaaS instalada, pode atualizar o modo como *completa* no portal do Azure. Não é possível fazer o downgrade - fazê-lo, precisará completamente desinstalar a extensão SQL IaaS e instale-o novamente. 

Para o modo de atualização de agente para *completo*, efetue o seguinte procedimento: 

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
1. Navegue até à sua [máquinas virtuais do SQL](virtual-machines-windows-sql-manage-portal.md#access-sql-virtual-machine-resource) recursos. 
1. Selecione a sua máquina virtual do SQL Server e selecione **descrição geral**. 
1. Para VMs de SQL com o *NoAgent* ou *leve* modos de IaaS, selecionar a mensagem para **apenas atualizações de tipo e a edição de licença estão disponíveis com a extensão SQL IaaS**.

    ![Iniciar modo de alteração do portal](media/virtual-machines-windows-sql-server-agent-extension/change-sql-iaas-mode-portal.png)

1. Aceita **reiniciar o serviço SQL Server** ao selecionar a caixa de verificação e, em seguida, selecione **confirmar** para atualizar o seu modo de IaaS para "total". 

    ![Habilitar o gerenciamento completo para a extensão de IaaS](media/virtual-machines-windows-sql-server-agent-extension/enable-full-mode-iaas.png)

##  <a name="installation"></a>Instalação
A extensão SQL IaaS é instalada quando registra sua VM do SQL Server com o [fornecedor de recursos de VM do SQL](virtual-machines-windows-sql-register-with-resource-provider.md#register-with-sql-vm-resource-provider). No entanto, se necessário, o agente IaaS do SQL também pode ser instalado manualmente usando *completo* ou *leve* instalação em modo. 

O *completo* extensão do agente de IaaS do SQL Server é instalado automaticamente quando Aprovisiona uma das imagens de Galeria de máquinas virtuais do SQL Server no portal do Azure. 

### <a name="full-mode-installation"></a>Instalação em modo completo
O *completo* extensão SQL IaaS oferece a capacidade de gerenciamento completa para uma única instância na VM do SQL Server. Se houver uma instância predefinida, em seguida, a extensão funcionará com a instância predefinida e que não oferecerá suporte a gestão das outras instâncias. Se não existir nenhuma instância predefinida, mas apenas uma instância nomeada, em seguida, irá gerenciar a instância nomeada. Se não existe nenhuma instância predefinida, e existem várias instâncias com nome, a extensão irá falhar instalar. 

Instalar o *completo* modo do IaaS do SQL irá reiniciar o serviço SQL Server. Para evitar o reinício do serviço do SQL Server, instale o *leve* limitada de modo com capacidade de gerenciamento em vez disso. 

Instalar o agente IaaS do SQL com *completo* modo através do PowerShell:

  ```powershell-interactive
     // Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     // Register SQL VM with 'Full' SQL IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;sqlServerLicenseType='AHUB';sqlManagement='Full'}  
  
  ```

| Parâmetro | Valores aceitáveis                        |
| :------------------| :-------------------------------|
| **sqlServerLicenseType** | `'AHUB'`, ou `'PAYG'`     |
| &nbsp;             | &nbsp;                          |


> [!WARNING]
> - Se a extensão já não estiver instalada, instalar o **completo** extensão reinicia o serviço do SQL Server. Uso **leve** modo para evitar o reinício do serviço do SQL Server. 
> - A atualizar a extensão SQL IaaS não reinicia o serviço SQL Server. 

#### <a name="install-on-a-vm-with-a-single-named-sql-server-instance"></a>Instalar numa VM com uma única instância nomeada do SQL Server
A extensão SQL IaaS irá funcionar com uma instância nomeada num SQL Server se a instância predefinida é desinstalada e é reinstalada a extensão de IaaS.

Para utilizar uma instância nomeada do SQL Server, efetue o seguinte:
   1. Implemente uma VM do SQL Server do marketplace. 
   1. Desinstale a extensão de IaaS de dentro do [portal do Azure](https://portal.azure.com).
   1. Desinstale o SQL Server inteiramente no interior da VM do SQL Server.
   1. Instale o SQL Server com uma instância nomeada numa VM do SQL Server. 
   1. Instale a extensão de IaaS a partir do portal do Azure.  


### <a name="install-in-lightweight-mode"></a>Instalar no modo simples
Modo simples não irá reiniciar o serviço do SQL Server, mas oferece funcionalidade limitada. 

Instalar o agente IaaS do SQL com *leve* modo através do PowerShell:


  ```powershell-interactive
     // Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     // Register SQL VM with 'Lightweight' SQL IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;sqlServerLicenseType='AHUB';sqlManagement='LightWeight'}  
  
  ```

| Parâmetro | Valores aceitáveis                        |
| :------------------| :-------------------------------|
| **sqlServerLicenseType** | `'AHUB'`, ou `'PAYG'`     |
| &nbsp;             | &nbsp;                          |


## <a name="get-status-of-sql-iaas-extension"></a>Obter o estado da extensão do IaaS do SQL
É uma forma de verificar se a extensão está instalada ver o estado do agente no portal do Azure. Selecione **todas as definições** na janela de máquina virtual e, em seguida, clique em **extensões**. Deverá ver o **SqlIaasExtension** extensão listado.

![Extensão do agente de IaaS do SQL Server no portal do Azure](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-portal.png)

Também pode utilizar o **Get-AzVMSqlServerExtension** cmdlet do PowerShell do Azure.

   ```powershell-interactive
   Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
   ```

O comando anterior confirma que o agente está instalado e fornece informações de estado geral. Também pode obter informações de estado específicos sobre a cópia de segurança automatizada e a aplicação de patches com os seguintes comandos.

   ```powershell-interactive
    $sqlext = Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings
   ```

## <a name="removal"></a>Remoção
No portal do Azure, pode desinstalar a extensão ao clicar nas reticências no **extensões** janela das suas propriedades de máquina virtual. Em seguida, clique em **Eliminar**.

![Desinstale a extensão de agente IaaS do SQL Server no portal do Azure](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-uninstall.png)

Também pode utilizar o **Remove-AzVMSqlServerExtension** cmdlet do PowerShell.

   ```powershell-interactive
    Remove-AzVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SqlIaasExtension"
   ```

## <a name="next-steps"></a>Passos seguintes
Começar a utilizar um dos serviços suportados pela extensão. Para obter mais informações, consulte os artigos referenciados no [serviços suportados](#supported-services) seção deste artigo.

Para obter mais informações sobre a execução do SQL Server em máquinas de virtuais do Azure, consulte [SQL Server em Descrição geral de máquinas virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md).

