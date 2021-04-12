---
title: Registe-se com extensão do agente sql iaas
description: Registe a sua máquina virtual Azure SQL Server com a extensão SQL IaaS Agent para permitir funcionalidades para máquinas virtuais SQL Server implantadas fora do Mercado Azure, bem como conformidade e melhor gestão.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: management
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/07/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: devx-track-azurecli, devx-track-azurepowershell, contperf-fy21q2
ms.openlocfilehash: 3cea15114e125951a8fbec73f965b272a4f8053d
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2021
ms.locfileid: "106284163"
---
# <a name="register-sql-server-vm-with-sql-iaas-agent-extension"></a>Registar SQL Server VM com extensão do agente SQL IaaS
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Registar o seu SQL Server VM com a [extensão SQL IaaS Agent](sql-server-iaas-agent-extension-automate-management.md) para desbloquear uma grande quantidade de benefícios de recurso para o seu SQL Server em Azure VM. 

Este artigo ensina-o a registar um único SQL Server VM com a extensão sql IaaS Agent. Em alternativa, pode registar automaticamente todos [](sql-agent-extension-automatic-registration-all-vms.md) os VMs do SQL Server ou [vários VMs scripts a granel](sql-agent-extension-manually-register-vms-bulk.md).


## <a name="overview"></a>Descrição Geral

Registar-se com a [extensão sql Server IaaS Agent](sql-server-iaas-agent-extension-automate-management.md) cria o _recurso_ **de máquina virtual SQL** dentro da sua subscrição, que é um recurso _separado_ do recurso da máquina virtual. Desregralar o seu SQL Server VM da extensão removerá o _recurso_ **da máquina virtual SQL,** mas não deixará cair a máquina virtual real.

A implementação de uma imagem do SQL Server VM Azure Marketplace através do portal Azure regista automaticamente o SQL Server VM com a extensão. No entanto, se optar por instalar o SQL Server numa máquina virtual Azure ou providenciar uma máquina virtual Azure a partir de um VHD personalizado, então deve registar o seu SQL Server VM com a extensão SQL IaaS Agent para desbloquear todos os benefícios de funcionalidade e gerenciabilidade. 

Para utilizar a extensão do Agente IAAS SQL, tem primeiro de [registar a sua subscrição com o fornecedor **Microsoft.SqlVirtualMachine**](#register-subscription-with-resource-provider), o que dá à extensão SQL IaaS a capacidade de criar recursos dentro dessa subscrição específica.

> [!IMPORTANT]
> A extensão do Agente IAAS SQL recolhe dados com o propósito expresso de dar aos clientes benefícios opcionais ao utilizar o SQL Server dentro de Máquinas Virtuais Azure. A Microsoft não utilizará estes dados para licenciar auditorias sem o consentimento prévio do cliente. Consulte o [suplemento de privacidade SQL Server](/sql/sql-server/sql-server-privacy#non-personal-data) para obter mais informações.

## <a name="prerequisites"></a>Pré-requisitos

Para registar o seu SQL Server VM com a extensão, necessitará de: 

- Uma [subscrição do Azure](https://azure.microsoft.com/free/).
- Uma máquina virtual do Azure Resource Model [Windows Server 2008 (ou maior)](../../../virtual-machines/windows/quick-create-portal.md) com [SQL Server 2008 (ou maior)](https://www.microsoft.com/sql-server/sql-server-downloads) implantada para o público ou nuvem do Governo Azure. 
- A versão mais recente de [Azure CLI](/cli/azure/install-azure-cli) ou [Azure PowerShell (5.0 mínimo)](/powershell/azure/install-az-ps). 


## <a name="register-subscription-with-resource-provider"></a>Registe a subscrição com o Fornecedor de Recursos

Para registar o seu SQL Server VM com a extensão SQL IaaS Agent, tem primeiro de registar a sua subscrição com o fornecedor de recursos **Microsoft.SqlVirtualMachine.** Isto dá ao SqL IaaS Agent a capacidade de criar recursos dentro da sua subscrição.  Pode fazê-lo utilizando o portal Azure, o Azure CLI ou o Azure PowerShell.

### <a name="azure-portal"></a>Portal do Azure

1. Abra o portal Azure e vá a **Todos os Serviços.** 
1. Vá a **Subscrições** e selecione a subscrição de juros.  
1. Na página **de Subscrições,** aceda às **extensões.** 
1. Introduza o **sql** no filtro para trazer as extensões relacionadas com o SQL. 
1. Selecione **Registar,** **re-registar- se** ou **não registar** para o fornecedor  **Microsoft.SqlVirtualMachine,** dependendo da ação desejada. 

   ![Modificar o fornecedor](./media/sql-agent-extension-manually-register-single-vm/select-resource-provider-sql.png)


### <a name="command-line"></a>Linha de comandos

Registe a sua subscrição Azure com o fornecedor **Microsoft.SqlVirtualMachine** utilizando a Azure CLI ou a Azure PowerShell. 

# <a name="azure-cli"></a>[CLI do Azure](#tab/bash)

```azurecli-interactive
# Register the SQL IaaS Agent extension to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

```powershell-interactive
# Register the SQL IaaS Agent extension to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```

---

## <a name="register-with-extension"></a>Registar-se com extensão

Existem três modos de gestão para a [extensão do Agente IAAS do SQL Server](sql-server-iaas-agent-extension-automate-management.md#management-modes). 

Registar a extensão em modo de gestão completa reinicia o serviço SQL Server, pelo que é aconselhável registar a extensão em modo leve primeiro e, em seguida, [atualizar para a totalidade](#upgrade-to-full) durante uma janela de manutenção. 

### <a name="lightweight-management-mode"></a>Modo de gestão leve

Utilize o Azure CLI ou a Azure PowerShell para registar o seu SQL Server VM com a extensão em modo leve. Isto não reiniciará o serviço SQL Server. Em seguida, pode atualizar para o modo completo a qualquer momento, mas ao fazê-lo reiniciará o serviço SQL Server, pelo que é aconselhável aguardar até uma janela de manutenção programada. 

Forneça o tipo de licença SQL Server como pagamento como -como-você-go ) `PAYG` para pagar por utilização, Azure Hybrid Benefit ( `AHUB` ) para usar a sua própria licença, ou recuperação de desastres ( `DR` ) para ativar a licença de réplica DR [gratuita](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure).

As instâncias de cluster de falha e as implementações em várias instâncias só podem ser registadas com a extensão do Agente IAAS SQL em modo leve. 

# <a name="azure-cli"></a>[CLI do Azure](#tab/bash)

Registe um SQL Server VM em modo leve com o Azure CLI: 

  ```azurecli-interactive
  # Register Enterprise or Standard self-installed VM in Lightweight mode
  az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type <license_type> 
  ```


# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Registar um SQL Server VM em modo leve com Azure PowerShell:  


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  # Register SQL VM with 'Lightweight' SQL IaaS agent
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType <license_type>  -SqlManagementType LightWeight  
  ```

---

### <a name="full-management-mode"></a>Modo de gestão completa

Registar o seu SQL Server VM em modo completo reiniciará o serviço SQL Server. Prossiga com cuidado. 

Para registar o seu SQL Server VM diretamente em modo completo (e possivelmente reiniciar o seu serviço SQL Server), utilize o seguinte comando Azure PowerShell: 

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL IaaS Agent extension in full mode
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

### <a name="noagent-management-mode"></a>Modo de gestão NoAgent 

SQL Server 2008 e 2008 R2 instalado no Windows Server 2008 _(não R2_) pode ser registado com a extensão SQL IaaS Agent no [modo NoAgent](sql-server-iaas-agent-extension-automate-management.md#management-modes). Esta opção garante a conformidade e permite que o SQL Server VM seja monitorizado no portal Azure com funcionalidade limitada.


Para o **tipo de licença,** especifique: `AHUB` , ou `PAYG` `DR` . Para a **oferta de imagem**, especifique ou `SQL2008-WS2008``SQL2008R2-WS2008`

Para registar o seu SQL Server 2008 ( `SQL2008-WS2008` ) ou 2008 R2 ( `SQL2008R2-WS2008` ) na instância do Windows Server 2008, utilize o seguinte corte de código Azure CLI ou Azure PowerShell: 


# <a name="azure-cli"></a>[CLI do Azure](#tab/bash)

Registe a sua máquina virtual SQL Server no modo NoAgent com o Azure CLI: 

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type <license type>  --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer <image offer> 
 ```
 

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)


Registe a sua máquina virtual SQL Server no modo NoAgent com a Azure PowerShell: 

  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType <license type> -SqlManagementType NoAgent -Sku Standard -Offer <image offer>
  ```

---

## <a name="verify-mode"></a>Verificar o modo

Pode ver o modo atual do seu agente IAAS do Servidor SQL utilizando o Azure PowerShell: 

```powershell-interactive
# Get the SqlVirtualMachine
$sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
$sqlvm.SqlManagementType
```

## <a name="upgrade-to-full"></a>Upgrade para completo  

Os VMs do SERVIDOR SQL que registaram a extensão em modo *leve* podem atualizar-se para _a totalidade_ utilizando o portal Azure CLI ou Azure PowerShell. OS VMs do SQL Server no modo _NoAgent_ podem ser atualizados para _a totalidade_ depois de o SISTEMA ser atualizado para o Windows 2008 R2 e acima. Não é possível desclassificar - para tal, terá de [desagrear](#unregister-from-extension) o SQL Server VM a partir da extensão do Agente IAAS SQL. Ao fazê-lo, removerá o _recurso_ **de máquina virtual SQL,** mas não eliminará a máquina virtual real. 

> [!NOTE]
> Quando atualizar o modo de gestão para a extensão SQL IaaS para a totalidade, reiniciará o serviço SQL Server. Em alguns casos, o reinício pode fazer com que os nomes principais do serviço (SPNs) associados ao serviço SQL Server mudem para a conta de utilizador errada. Se tiver problemas de conectividade depois de atualizar o modo de gestão para o pleno, [não registar e voltar a registar as suas SPNs](/sql/database-engine/configure-windows/register-a-service-principal-name-for-kerberos-connections).


### <a name="azure-portal"></a>Portal do Azure

Para atualizar a extensão para o modo completo utilizando o portal Azure, siga estes passos: 

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Vá ao seu recurso [de máquinas virtuais SQL.](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource) 
1. Selecione o seu SQL Server VM e **selecione A Visão Geral**. 
1. Para VMs do servidor SQL com o modo IaaS noAgent ou leve, selecione as **atualizações do tipo de licença Única e as atualizações de edição estão disponíveis com a mensagem de extensão SQL IaaS.**

   ![Seleções para alterar o modo a partir do portal](./media/sql-agent-extension-manually-register-single-vm/change-sql-iaas-mode-portal.png)

1. Selecione o **I agree to restart the SQL Server service on the virtual machine** check box and then select **Confirm** to upgrade your IaaS mode to full. 

    ![Caixa de verificação para concordar em reiniciar o serviço SQL Server na máquina virtual](./media/sql-agent-extension-manually-register-single-vm/enable-full-mode-iaas.png)

### <a name="command-line"></a>Linha de comandos

# <a name="azure-cli"></a>[CLI do Azure](#tab/bash)

Para atualizar a extensão para o modo completo, executar o seguinte corte de código Azure CLI:

  ```azurecli-interactive
  # Update to full mode
  az sql vm update --name <vm_name> --resource-group <resource_group_name> --sql-mgmt-type full  
  ```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Para atualizar a extensão para o modo completo, executar o seguinte corte de código Azure PowerShell:

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL IaaS Agent extension in full mode
  Update-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

---

## <a name="verify-registration-status"></a>Verificar o estado do registo
Pode verificar se o seu SQL Server VM já foi registado com a extensão sql IaaS Agent utilizando o portal Azure, o Azure CLI ou o Azure PowerShell. 

### <a name="azure-portal"></a>Portal do Azure 

Para verificar o estado de registo utilizando o portal Azure, siga estes passos: 

1. Inicie sessão no [portal do Azure](https://portal.azure.com). 
1. Vá para os seus [VMs do seu servidor SQL](manage-sql-vm-portal.md).
1. Selecione o seu SQL Server VM da lista. Se o seu SQL Server VM não estiver listado aqui, provavelmente não foi registado com a extensão do Agente IAAS SQL. 
1. Ver o valor em **Estado**. Se **o Status** for Bem **sucedido,** então o SQL Server VM foi registado com sucesso na extensão do Agente IAAS SQL. 

   ![Verificar o estado com o registo SQL RP](./media/sql-agent-extension-manually-register-single-vm/verify-registration-status.png)

### <a name="command-line"></a>Linha de comandos

Verifique o estado atual do registo do SQL Server VM utilizando o Azure CLI ou o Azure PowerShell. `ProvisioningState` mostrará `Succeeded` se o registo foi bem sucedido. 

# <a name="azure-cli"></a>[CLI do Azure](#tab/bash)

Para verificar o estado de registo utilizando o Azure CLI, execute o seguinte corte de código:  

  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
 ```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Para verificar o estado de registo utilizando o Azure PowerShell, execute o seguinte corte de código:

  ```powershell-interactive
  Get-AzSqlVM -Name <vm_name> -ResourceGroupName <resource_group>
  ```

---

Um erro indica que o SQL Server VM não foi registado com a extensão. 


## <a name="unregister-from-extension"></a>Não registro da extensão

Para desregistralar o seu SQL Server VM com a extensão SQL IaaS Agent, elimine o *recurso* de máquina virtual SQL utilizando o portal Azure ou O CLI Azure. A eliminação do *recurso* de máquina virtual SQL não elimina o SQL Server VM. No entanto, tenha cuidado e siga os passos cuidadosamente porque é possível eliminar inadvertidamente a máquina virtual ao tentar remover o *recurso*. 

Não registar a máquina virtual SQL com a extensão SQL IaaS Agent é necessário para reduzir o modo de gestão de forma completa. 

### <a name="azure-portal"></a>Portal do Azure

Para desregralar o seu SQL Server VM a partir da extensão utilizando o portal Azure, siga estes passos:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
1. Navegue para o recurso SQL VM. 
  
   ![Recurso de máquinas virtuais SQL](./media/sql-agent-extension-manually-register-single-vm/sql-vm-manage.png)

1. Selecione **Eliminar**. 

   ![Selecione eliminar na navegação superior](./media/sql-agent-extension-manually-register-single-vm/delete-sql-vm-resource.png)

1. Digite o nome da máquina virtual SQL e **limpe a caixa de verificação ao lado da máquina virtual**.

   ![Desmarque o VM para evitar a eliminação da máquina virtual real e, em seguida, selecione Eliminar para proceder à eliminação do recurso SQL VM](./media/sql-agent-extension-manually-register-single-vm/confirm-delete-of-resource-uncheck-box.png)

   >[!WARNING]
   > Se não limpar a caixa de verificação ao lado do nome da máquina *virtual, eliminará* completamente a máquina virtual. Limpe a caixa de verificação para desmarcar o SQL Server VM da extensão, mas *não elimine a máquina virtual real*. 

1. **Selecione Eliminar** para confirmar a eliminação do *recurso* de máquina virtual SQL, e não o SQL Server VM. 

### <a name="command-line"></a>Linha de comandos

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para desregralar o seu SQL Server VM a partir da extensão com O Azure CLI, utilize o comando [de exclusão de az sql vm.](/cli/azure/sql/vm#az-sql-vm-delete) Isto removerá o *recurso* VM do Servidor SQL, mas não eliminará a máquina virtual. 


```azurecli-interactive
az sql vm delete 
  --name <SQL VM resource name> |
  --resource-group <Resource group name> |
  --yes 
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para desregralar o seu SQL Server VM a partir da extensão com Azure PowerShell, utilize o comando [Remove-AzSqlVM.](/powershell/module/az.sqlvirtualmachine/remove-azsqlvm) Isto removerá o *recurso* VM do Servidor SQL, mas não eliminará a máquina virtual. 

```powershell-interactive
Remove-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name>
```

---


## <a name="next-steps"></a>Próximos passos

Para obter mais informações, veja os seguintes artigos: 

* [Visão geral do SQL Server num VM do Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [FAQ para SQL Server em um VM Windows](frequently-asked-questions-faq.md)  
* [Orientação de preços para o SQL Server num VM do Windows](pricing-guidance.md)
* [Notas de lançamento para SQL Server num VM do Windows](../../database/doc-changes-updates-release-notes.md)
