---
title: Registe-se junto do fornecedor de recursos SQL VM
description: Registe a sua máquina virtual Azure SQL Server com o fornecedor de recursos SQL VM para permitir funcionalidades para máquinas virtuais SQL Server implantadas fora do Mercado Azure, bem como a conformidade e uma melhor gestão.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 09/21/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: b48f0429525822d09f08965128df0ceb1e32898a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91761316"
---
# <a name="register-a-sql-server-vm-in-azure-with-the-sql-vm-resource-provider-rp"></a>Registar um SQL Server VM em Azure com o fornecedor de recursos SQL VM (RP)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Este artigo descreve como registar a sua máquina virtual SQL Server (VM) em Azure com o fornecedor de recursos SQL VM (RP). 

Este artigo ensina-o a registar um único SQL Server VM com o fornecedor de recursos SQL VM. Em alternativa, pode registar todos os VMs do SQL Server [automaticamente](sql-vm-resource-provider-automatic-registration.md) ou [scriptados a granel](sql-vm-resource-provider-bulk-register.md).

## <a name="overview"></a>Descrição geral

Registar-se com o fornecedor de recursos cria o _recurso_ **de máquina virtual SQL** dentro da sua subscrição, que é um recurso separado do recurso de máquina virtual. Desregralar o seu SQL Server VM do fornecedor de recursos removerá o _recurso_ **da máquina virtual SQL,** mas não deixará cair a máquina virtual real.

A implementação de uma imagem do SQL Server VM Azure Marketplace através do portal Azure regista automaticamente o SQL Server VM com o fornecedor de recursos. No entanto, se optar por instalar o SQL Server numa máquina virtual Azure ou providenciar uma máquina virtual Azure a partir de um VHD personalizado, deverá registar o seu SQL Server VM com o fornecedor de recursos para:

- **Benefícios da funcionalidade**: Registar o seu SQL Server VM com o fornecedor de recursos desbloqueia [a correção automatizada,](automated-patching.md) [cópia de segurança automatizada,](automated-backup.md)bem como capacidades de monitorização e gestão. Também desbloqueia a flexibilidade [de licenciamento](licensing-model-azure-hybrid-benefit-ahb-change.md) e [edição.](change-sql-server-edition.md) Anteriormente, estas funcionalidades estavam apenas disponíveis para imagens VM do SQL Server implementadas a partir do Azure Marketplace. 

- **Conformidade**: Registar-se com o fornecedor de recursos SQL VM oferece um método simplificado de cumprimento do requisito de notificar a Microsoft de que o Benefício Híbrido Azure foi ativado conforme especificado nos termos do produto. Este processo nega a necessidade de gerir formulários de registo de licenciamento para cada recurso.  

- **Gestão gratuita**: Registar-se com o fornecedor de recursos SQL VM nos três modos de gestão é completamente gratuito. Não existe um custo adicional associado ao fornecedor de recursos, nem com a alteração dos modos de gestão. 

- **Gestão simplificada da licença**: Registar-se com o fornecedor de recursos SQL VM simplifica a gestão da licença do SQL Server e permite identificar rapidamente os VMs do Servidor SQL com o Benefício Híbrido Azure habilitado através do [portal Azure](manage-sql-vm-portal.md), o Azure CLI ou PowerShell: 

   # <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

   ```azurecli-interactive
   $vms = az sql vm list | ConvertFrom-Json
   $vms | Where-Object {$_.sqlServerLicenseType -eq "AHUB"}
   ```

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell-interactive
   Get-AzSqlVM | Where-Object {$_.LicenseType -eq 'AHUB'}
   ```

   ---

Para utilizar o fornecedor de recursos SQL VM, tem primeiro de [registar a sua subscrição junto do fornecedor de recursos, o](#register-subscription-with-rp)que dá ao fornecedor de recursos a capacidade de criar recursos dentro dessa subscrição específica.

## <a name="prerequisites"></a>Pré-requisitos

Para registar o seu SQL Server VM com o fornecedor de recursos, necessitará de: 

- Uma [subscrição do Azure](https://azure.microsoft.com/free/).
- Uma [máquina virtual](../../../virtual-machines/windows/quick-create-portal.md) do Modelo de Recursos Azure Windows com O [Servidor SQL](https://www.microsoft.com/sql-server/sql-server-downloads) implantado para o público ou nuvem do Governo Azure. 
- A versão mais recente de [Azure CLI](/cli/azure/install-azure-cli) ou [PowerShell](/powershell/azure/new-azureps-module-az). 

## <a name="management-modes"></a>Modos de gestão

Se a [extensão SQL IaaS](sql-server-iaas-agent-extension-automate-management.md) ainda não tiver sido instalada, o registo com o fornecedor de recursos SQL VM instala automaticamente a extensão SQL Server IaaS num dos três modos de gestão, especificados durante o processo de registo. Não especificando o modo de gestão instalará a extensão SQL IaaS em modo de gestão completa.  

Se a extensão SQL IaaS já tiver sido instalada manualmente, então já se encontra em modo de gestão completa e o registo com o fornecedor de recursos em modo completo não reiniciará o serviço SQL Server.

Os três modos de gestão são:

- **O** modo leve não requer o reinício do SQL Server, mas suporta apenas alterar o tipo de licença e edição do SQL Server. Utilize esta opção para VMs do SERVIDOR SQL com múltiplas instâncias, ou participe num caso de cluster de failover (FCI). Não há impacto na memória ou CPU quando se utiliza o modo leve, e não há custos associados. É aconselhável registar primeiro o seu SQL Server VM em modo leve e, em seguida, atualizar para o modo Full durante uma janela de manutenção programada.  

- **O** modo completo fornece todas as funcionalidades, mas requer um reinício das permissões do SqL Server e do administrador do sistema. Esta é a opção que é instalada por padrão ao instalar manualmente a extensão SQL IaaS. Utilize-o para gerir um SQL Server VM com uma única instância. O modo completo instala dois serviços windows que têm um impacto mínimo na memória e CPU - estes podem ser monitorizados através do gestor de tarefas. Não há qualquer custo associado à utilização do modo de gestão completa. 

- O modo **NoAgent** é dedicado ao SQL Server 2008 e ao SQL Server 2008 R2 instalado no Windows Server 2008. Não há impacto na memória ou CPU quando se utiliza o modo NoAgent. Não há qualquer custo associado à utilização do modo de gestão NoAgent. 

Pode ver o modo atual do seu agente IAAS do Servidor SQL utilizando o PowerShell: 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```

## <a name="register-subscription-with-rp"></a>Registar subscrição com RP

Para registar o seu SQL Server VM com o fornecedor de recursos SQL VM, tem primeiro de registar a sua subscrição junto do fornecedor de recursos. Isto dá ao fornecedor de recursos SQL VM a capacidade de criar recursos dentro da sua subscrição.  Pode fazê-lo utilizando o portal Azure, o Azure CLI ou o PowerShell.

### <a name="azure-portal"></a>Portal do Azure

1. Abra o portal Azure e vá a **Todos os Serviços.** 
1. Vá a **Subscrições** e selecione a subscrição de juros.  
1. Na página **de Subscrições,** aceda aos **fornecedores de recursos.** 
1. Introduza o **sql** no filtro para trazer os fornecedores de recursos relacionados com o SQL. 
1. Selecione **Registar,** **re-registar- se**ou **não registar** para o fornecedor  **Microsoft.SqlVirtualMachine,** dependendo da ação desejada. 

   ![Modificar o fornecedor](./media/sql-vm-resource-provider-register/select-resource-provider-sql.png)


### <a name="command-line"></a>Linha de comandos

Registe o seu fornecedor de recursos SQL VM na sua assinatura Azure utilizando a Azure CLI ou a PowerShell. 

# <a name="azure-cli"></a>[CLI do Azure](#tab/bash)

```azurecli-interactive
# Register the SQL VM resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell-interactive
# Register the SQL VM resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```

---

## <a name="register-with-rp"></a>Registe-se com RP

### <a name="lightweight-management-mode"></a>Modo de gestão leve

Se a extensão do [agente iaaS do SQL Server](sql-server-iaas-agent-extension-automate-management.md) não tiver sido instalada na máquina virtual, então a recomendação é registar-se com o fornecedor de recursos SQL VM em modo leve. Isto instalará a extensão SQL IaaS em [modo leve](#management-modes) e impedirá o reinício do serviço SQL Server. Em seguida, pode atualizar para o modo completo a qualquer momento, mas ao fazê-lo reiniciará o serviço SQL Server, pelo que é aconselhável aguardar até uma janela de manutenção programada. 

Forneça o tipo de licença SQL Server como pagamento como pagamento `PAYG` ( ) para pagar por utilização, Azure Hybrid Benefit ( ) para usar a sua própria `AHUB` licença, ou recuperação de desastres ( `DR` ) para ativar a licença de réplica DR [gratuita](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure).

As instâncias de cluster de falha e as implementações em várias instâncias só podem ser registadas com o fornecedor de recursos SQL VM em modo leve. 

# <a name="azure-cli"></a>[CLI do Azure](#tab/bash)

Registe um SQL Server VM em modo leve com o Azure CLI: 

  ```azurecli-interactive
  # Register Enterprise or Standard self-installed VM in Lightweight mode
  az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type PAYG 
  ```


# <a name="powershell"></a>[PowerShell](#tab/powershell)

Registar um SQL Server VM em modo leve com PowerShell:  


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  # Register SQL VM with 'Lightweight' SQL IaaS agent
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType LightWeight  
  ```

---

### <a name="full-management-mode"></a>Modo de gestão completa


Se a Extensão SQL IaaS já tiver sido instalada manualmente na máquina virtual, então pode registar o SQL Server VM em modo completo sem reiniciar o serviço SQL Server. **No entanto, se a extensão SQL IaaS não tiver sido instalada, o registo em modo completo instalará a extensão SQL IaaS em pleno modo e reiniciará o serviço SQL Server. Por favor, proceda com cuidado.**


Para registar o seu SQL Server VM diretamente em modo completo (e possivelmente reiniciar o seu serviço SQL Server), utilize o seguinte comando PowerShell: 

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL VM resource provider in full mode
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

### <a name="noagent-management-mode"></a>Modo de gestão NoAgent 

O SQL Server 2008 e o 2008 R2 instalado no Windows Server 2008 _(não R2_) podem ser registados com o fornecedor de recursos SQL VM no [modo NoAgent](#management-modes). Esta opção garante a conformidade e permite que o SQL Server VM seja monitorizado no portal Azure com funcionalidade limitada.

Especificar ou `AHUB` `PAYG` , ou como `DR` **sqlLicenseType,** `SQL2008-WS2008` e como `SQL2008R2-WS2008` **sqlImageOffer**. 

Para registar o seu SQL Server 2008 ou 2008 R2 na instância do Windows Server 2008, utilize o seguinte corte de código Azure CLI ou PowerShell: 


# <a name="azure-cli"></a>[CLI do Azure](#tab/bash)

Registe a sua máquina virtual SQL Server 2008 no modo NoAgent com o Azure CLI: 

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type PAYG --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer SQL2008-WS2008
 ```
 
 
Registe a sua máquina virtual SQL Server 2008 R2 no modo NoAgent com o Azure CLI: 

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type PAYG --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer SQL2008R2-WS2008
 ```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Registe a sua máquina virtual SQL Server 2008 no modo NoAgent com PowerShell: 


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType NoAgent -Sku Standard -Offer SQL2008-WS2008
  ```
  
  Registe a sua máquina virtual SQL Server 2008 R2 no modo NoAgent com PowerShell: 


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType NoAgent -Sku Standard -Offer SQL2008R2-WS2008
  ```

---

## <a name="upgrade-to-full"></a>Upgrade para completo  

Os VMs do Servidor SQL que tenham a extensão IaaS *leve* instalada podem atualizar o modo para _a totalidade_ utilizando o portal Azure, o Azure CLI ou o PowerShell. OS VMs do SQL Server no modo _NoAgent_ podem ser atualizados para _a totalidade_ depois de o SISTEMA ser atualizado para o Windows 2008 R2 e acima. Não é possível desclassificar - para tal, terá de [desagrear](#unregister-from-rp) o SQL Server VM do fornecedor de recursos SQL VM. Ao fazê-lo, removerá o _recurso_ **de máquina virtual SQL,** mas não eliminará a máquina virtual real. 

Pode ver o modo atual do seu agente IAAS do Servidor SQL utilizando o PowerShell: 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```

Para atualizar o modo agente para a totalidade: 


### <a name="azure-portal"></a>Portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Vá ao seu recurso [de máquinas virtuais SQL.](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource) 
1. Selecione o seu SQL Server VM e **selecione A Visão Geral**. 
1. Para VMs do servidor SQL com o modo IaaS noAgent ou leve, selecione as **atualizações do tipo de licença Única e as atualizações de edição estão disponíveis com a mensagem de extensão SQL IaaS.**

   ![Seleções para alterar o modo a partir do portal](./media/sql-vm-resource-provider-register/change-sql-iaas-mode-portal.png)

1. Selecione o **I agree to restart the SQL Server service on the virtual machine** check box and then select **Confirm** to upgrade your IaaS mode to full. 

    ![Caixa de verificação para concordar em reiniciar o serviço SQL Server na máquina virtual](./media/sql-vm-resource-provider-register/enable-full-mode-iaas.png)

### <a name="command-line"></a>Linha de comandos

# <a name="azure-cli"></a>[CLI do Azure](#tab/bash)

Executar o seguinte corte de código Azure CLI:

  ```azurecli-interactive
  # Update to full mode
  az sql vm update --name <vm_name> --resource-group <resource_group_name> --sql-mgmt-type full  
  ```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Executar o seguinte corte de código PowerShell:

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL VM resource provider in full mode
  Update-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

---

## <a name="verify-registration-status"></a>Verificar o estado do registo
Pode verificar se o seu SQL Server VM já foi registado no fornecedor de recursos SQL VM utilizando o portal Azure, o Azure CLI ou o PowerShell. 

### <a name="azure-portal"></a>Portal do Azure 

1. Inicie sessão no [portal do Azure](https://portal.azure.com). 
1. Vá para os seus [VMs do seu servidor SQL](manage-sql-vm-portal.md).
1. Selecione o seu SQL Server VM da lista. Se o seu SQL Server VM não estiver listado aqui, provavelmente não foi registado no fornecedor de recursos SQL VM. 
1. Ver o valor em **Estado**. Se **o Status** for Bem **sucedido,** então o SQL Server VM foi registado com sucesso no fornecedor de recursos SQL VM. 

   ![Verificar o estado com o registo SQL RP](./media/sql-vm-resource-provider-register/verify-registration-status.png)

### <a name="command-line"></a>Linha de comandos

Verifique o estado atual do registo do SQL Server VM utilizando o Azure CLI ou o PowerShell. `ProvisioningState` mostrará `Succeeded` se o registo foi bem sucedido. 

# <a name="azure-cli"></a>[CLI do Azure](#tab/bash)


  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
 ```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

  ```powershell-interactive
  Get-AzSqlVM -Name <vm_name> -ResourceGroupName <resource_group>
  ```

---

Um erro indica que o SQL Server VM não foi registado com o fornecedor de recursos. 


## <a name="unregister-from-rp"></a>Não registro de RP

Para desregistralar o seu SQL Server VM com o fornecedor de recursos SQL VM, elimine o *recurso* de máquina virtual SQL utilizando o portal Azure ou O CLI Azure. A eliminação do *recurso* de máquina virtual SQL não elimina o SQL Server VM. No entanto, tenha cuidado e siga os passos cuidadosamente porque é possível eliminar inadvertidamente a máquina virtual ao tentar remover o *recurso*. 

Não registar a máquina virtual SQL com o fornecedor de recursos SQL VM é necessário para reduzir o modo de gestão da totalidade. 

### <a name="azure-portal"></a>Portal do Azure

Para desregistralr o seu SQL Server VM com o fornecedor de recursos utilizando o portal Azure, siga estes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Navegue para o recurso SQL VM. 
  
   ![Recurso de máquinas virtuais SQL](./media/sql-vm-resource-provider-register/sql-vm-manage.png)

1. Selecione **Eliminar**. 

   ![Selecione eliminar na navegação superior](./media/sql-vm-resource-provider-register/delete-sql-vm-resource-provider.png)

1. Digite o nome da máquina virtual SQL e **limpe a caixa de verificação ao lado da máquina virtual**.

   ![Desmarque o VM para evitar a eliminação da máquina virtual real e, em seguida, selecione Eliminar para proceder à eliminação do recurso SQL VM](./media/sql-vm-resource-provider-register/confirm-delete-of-resource-uncheck-box.png)

   >[!WARNING]
   > Se não limpar a caixa de verificação ao lado do nome da máquina *virtual, eliminará* completamente a máquina virtual. Limpe a caixa de verificação para desregiscer o SQL Server VM do fornecedor de recursos, mas *não eliminar a máquina virtual real*. 

1. **Selecione Eliminar** para confirmar a eliminação do *recurso*de máquina virtual SQL, e não o SQL Server VM. 

### <a name="command-line"></a>Linha de comandos

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
Para desregralar o seu SQL Server VM do fornecedor de recursos com o Azure CLI, utilize o comando [de eliminação de vm az sql.](/cli/azure/sql/vm?view=azure-cli-latest&preserve-view=true#az-sql-vm-delete) Isto removerá o *recurso* VM do Servidor SQL, mas não eliminará a máquina virtual. 


```azurecli-interactive
az sql vm delete 
  --name <SQL VM resource name> |
  --resource-group <Resource group name> |
  --yes 
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Para desregralar o seu SQL Server VM do fornecedor de recursos com o PowerShell, utilize o comando [Remove-AzSqlVM.](/powershell/module/az.sqlvirtualmachine/remove-azsqlvm) Isto removerá o *recurso* VM do Servidor SQL, mas não eliminará a máquina virtual. 

```powershell-interactive
Remove-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name>
```

---

## <a name="limitations"></a>Limitações

O fornecedor de recursos SQL VM só suporta:
- VMs do servidor SQL implantados através do Gestor de Recursos Azure. Os VMs do Servidor SQL implantados através do modelo clássico não são suportados. 
- VMs do servidor SQL implantados para o público ou nuvem do Governo Azure. As deslocações para outras nuvens privadas ou governamentais não são apoiadas. 


## <a name="frequently-asked-questions"></a>Perguntas mais frequentes 

**Devo registar o meu SQL Server VM a partir de uma imagem do SQL Server no Azure Marketplace?**

N.º A Microsoft regista automaticamente VMs a partir das imagens do SQL Server no Azure Marketplace. O registo com o fornecedor de recursos SQL VM só é necessário se o VM *não* tiver sido aprovisionado a partir das imagens do SQL Server no Azure Marketplace e no SQL Server.

**O fornecedor de recursos de VM do SQL está disponível para todos os clientes?** 

Sim. Os clientes devem registar os seus VMs sql server com o fornecedor de recursos SQL VM se não utilizarem uma imagem sql Server do Azure Marketplace e, em vez disso, o SqL Server auto-instalado, ou se trouxerem o seu VHD personalizado. Os VMs detidos por todos os tipos de subscrições (Direct, Enterprise Agreement e Cloud Solution Provider) podem registar-se junto do fornecedor de recursos SQL VM.

**Devo registar-me com o fornecedor de recursos SQL VM se o meu SQL Server VM já tiver a extensão IAAS do SQL Server?**

Se o seu SQL Server VM for auto-instalado e não for forvia a partir das imagens do SQL Server no Azure Marketplace, deverá registar-se com o fornecedor de recursos SQL VM mesmo que tenha instalado a extensão SQL Server IaaS. Registar-se com o fornecedor de recursos SQL VM cria um novo recurso do tipo Microsoft.SqlVirtualMachine. A instalação da extensão IAAS do Servidor SQL não cria esse recurso.

**Qual é o modo de gestão predefinido ao registar-se com o fornecedor de recursos SQL VM?**

O modo de gestão predefinido quando se regista com o fornecedor de recursos SQL VM está *cheio*. Se a propriedade de gestão do SQL Server não estiver definida quando se registar com o fornecedor de recursos SQL VM, o modo será definido como uma gestão completa e o seu serviço SQL Server reiniciará. Recomenda-se registar-se primeiro com o fornecedor de recursos SQL VM em modo leve e, em seguida, atualizar para a totalidade durante uma janela de manutenção. 

**Quais são os pré-requisitos para se registar com o fornecedor de recursos SQL VM?**

Não existem pré-requisitos para se registar com o fornecedor de recursos SQL VM em modo leve ou no-agente. O pré-requisito para se registar com o fornecedor de recursos SQL VM em modo completo é ter a extensão IAAS do Servidor SQL instalada no VM, caso contrário o serviço SQL Server será reiniciado. 

**Posso registar-me com o fornecedor de recursos SQL VM se não tiver a extensão IAAS do SQL Server instalada no VM?**

Sim, pode registar-se com o fornecedor de recursos SQL VM em modo de gestão leve se não tiver a extensão SQL Server IaaS instalada no VM. Em modo leve, o fornecedor de recursos SQL VM utilizará uma aplicação de consola para verificar a versão e edição da instância SQL Server. 

O modo de gestão SQL predefinido ao registar-se com o fornecedor de recursos SQL VM é _Full_. Se a propriedade SQL Management não estiver definida ao registar-se com o fornecedor de recursos SQL VM, então o modo será definido como Full Manageability. Recomenda-se registar-se primeiro com o fornecedor de recursos SQL VM em modo leve e, em seguida, atualizar para a totalidade durante uma janela de manutenção. 

**O registo com o fornecedor de recursos SQL VM vai instalar um agente no meu VM?**

Sim, registar-se com o fornecedor de recursos SQL VM instalará um agente no VM.

A extensão IAAS do SQL Server conta com o agente para consultar os metadados do SQL Server. A única altura em que um agente não é instalado é quando o fornecedor de recursos SQL VM está registado no modo NoAgent

**O registo com o fornecedor de recursos SQL VM reiniciará o SQL Server no meu VM?**

Depende do modo especificado durante o registo. Se for especificado o modo "NoAgent" leve ou noAgent, o serviço SQL Server não será reiniciado. No entanto, especificar o modo de gestão como completo, ou deixar o modo de gestão em branco, instalará a extensão SQL IaaS em modo de gestão completa, o que fará com que o serviço SQL Server reinicie. 

**Qual é a diferença entre modos de gestão leve e sem agente ao registar-se com o fornecedor de recursos SQL VM?** 

O modo de gestão de nenhum agente está disponível apenas para SQL Server 2008 e SQL Server 2008 R2 no Windows Server 2008. É o único modo de gestão disponível para estas versões. Para todas as outras versões do SQL Server, os dois modos de gestão disponíveis são leves e cheios. 

O modo no-agent requer que a versão e as propriedades de edição do SQL Server sejam definidas pelo cliente. O modo leve consulta o VM para encontrar a versão e edição da instância SQL Server.

**Posso registar-me com o fornecedor de recursos SQL VM sem especificar o tipo de licença SQL Server?**

N.º O tipo de licença SQL Server não é uma propriedade opcional quando se está a registar com o fornecedor de recursos SQL VM. Tem de definir o tipo de licença SQL Server como pay-as-you-go ou Azure Hybrid Benefit ao registar-se com o fornecedor de recursos SQL VM em todos os modos de gestão (sem agente, leve e cheio).

**Posso atualizar a extensão IAAS do SQL Server do modo sem agente para o modo completo?**

N.º A atualização do modo de gestão para o modo de gestão para o modo completo ou leve não está disponível para o modo sem agente. Esta é uma limitação técnica do Windows Server 2008. Terá de atualizar o SISTEMA primeiro para o Windows Server 2008 R2 ou superior, e depois poderá fazer o upgrade para o modo de gestão completa. 

**Posso atualizar a extensão IAAS do SQL Server do modo leve para o modo completo?**

Sim. A atualização do modo de gestão de peso para cheio é suportada através do PowerShell ou do portal Azure. Requer reiniciar o serviço SQL Server.

**Posso reduzir a extensão IAAS do SQL Server de modo completo para modo de gestão sem agente ou leve?**

N.º A redução do modo de gestão de extensão SQL Server IaaS não é suportada. O modo de gestão não pode ser desclassificado do modo completo para o modo leve ou sem agente, e não pode ser desclassificado do modo leve para o modo sem agente. 

Para alterar o modo de gestão da plena gestibilidade, [não registe](#unregister-from-rp) o SQL Server VM do fornecedor de recursos SQL VM, largando o *recurso* SQL Server e reregistando novamente o SQL Server VM com o fornecedor de recursos SQL VM num modo de gestão diferente.

**Posso inscrever-me no fornecedor de recursos SQL VM do portal Azure?**

N.º O registo com o fornecedor de recursos SQL VM não está disponível no portal Azure. O registo junto do fornecedor de recursos SQL VM só é suportado com o Azure CLI ou o PowerShell. 

**Posso registar um VM com o fornecedor de recursos SQL VM antes da instalação do SQL Server?**

N.º Um VM deve ter pelo menos uma instância SQL Server (Database Engine) para registar com sucesso com o fornecedor de recursos SQL VM. Se não houver nenhuma instância do SQL Server no VM, o novo recurso Microsoft.SqlVirtualMachine estará num estado falhado.

**Posso registar um VM com o fornecedor de recursos SQL VM se existirem várias instâncias do SQL Server?**

Sim. O fornecedor de recursos SQL VM registará apenas uma instância sql Server (Database Engine). O fornecedor de recursos SQL VM registará a instância padrão do SQL Server no caso de múltiplas instâncias. Se não houver uma instância predefinida, apenas é suportado o registo em modo leve. Para atualizar do modo de gestão leve para o modo de gestão completa, ou a instância padrão do SQL Server deve existir ou o VM deve ter apenas uma instância chamada SQL Server.

**Posso registar um cluster de falha do SQL Server com o fornecedor de recursos SQL VM?**

Sim. Sql Server falha casos de cluster num Azure VM pode ser registado com o fornecedor de recursos SQL VM em modo leve. No entanto, as instâncias de cluster de failover do SQL Server não podem ser atualizadas para o modo de gestão completa.

**Posso registar o meu VM com o fornecedor de recursos SQL VM se um grupo de disponibilidade Always On estiver configurado?**

Sim. Não existem restrições ao registo de uma instância do SQL Server num VM Azure com o fornecedor de recursos SQL VM se estiver a participar numa configuração do grupo de disponibilidade Always On.

**Qual o custo para o registo com o fornecedor de recursos SQL VM, ou com a atualização para o modo de gestão completa?**
Nenhum. Não existe qualquer taxa associada ao registo com o fornecedor de recursos SQL VM, ou à utilização de qualquer um dos três modos de gestão. Gerir o seu SQL Server VM com o fornecedor de recursos é completamente gratuito. 

**Qual é o impacto de desempenho da utilização dos diferentes modos de gestão?**
Não há impacto na utilização dos modos de gestão *NoAgent* e *leves.* Existe um impacto mínimo ao utilizar o modo de gestão *total* de dois serviços instalados no SISTEMA. Estes podem ser monitorizados através do gestor de tarefas e vistos na consola de Serviços Windows incorporada. 

Os dois nomes de serviço são:
- `SqlIaaSExtensionQuery` (Nome de exibição - `Microsoft SQL Server IaaS Query Service` )
- `SQLIaaSExtension` (Nome de exibição - `Microsoft SQL Server IaaS Agent` )


## <a name="next-steps"></a>Próximos passos

Para obter mais informações, veja os seguintes artigos: 

* [Visão geral do SQL Server num VM do Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [FAQ para SQL Server em um VM Windows](frequently-asked-questions-faq.md)  
* [Orientação de preços para o SQL Server num VM do Windows](pricing-guidance.md)
* [Notas de lançamento para SQL Server num VM do Windows](../../database/doc-changes-updates-release-notes.md)
