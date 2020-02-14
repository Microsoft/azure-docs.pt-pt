---
title: Registe-se no fornecedor de recursos SQL VM
description: Registe a sua máquina virtual Azure SQL Server com o fornecedor de recursos SQL VM para ativar as funcionalidades para VMs do Servidor SQL implantados fora do Azure Marketplace, bem como a conformidade, e uma melhor gestão.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 01e683e31905281d25fdcf976bc58397c052a6c3
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77201633"
---
# <a name="register-a-sql-server-virtual-machine-in-azure-with-the-sql-vm-resource-provider"></a>Registe uma máquina virtual SQL Server em Azure com o fornecedor de recursos SQL VM

Este artigo descreve como registar a sua máquina virtual SQL Server (VM) em Azure com o fornecedor de recursos SQL VM. Registar-se com o fornecedor de recursos cria o _recurso_ **de máquina virtual SQL** dentro da sua subscrição, que é um recurso separado do recurso virtual da máquina. O desregistar o seu VM do Servidor SQL do fornecedor de recursos removerá o _recurso_ da **máquina virtual SQL,** mas não deixará cair a máquina virtual real. 

A implementação de uma imagem Do SQL Server VM Azure Marketplace através do portal Azure regista automaticamente o VM do Servidor SQL com o fornecedor de recursos. No entanto, se optar por instalar o SQL Server numa máquina virtual Azure ou fornecer uma máquina virtual Azure a partir de um VHD personalizado, deverá registar o seu VM do Servidor SQL com o fornecedor de recursos para:

- **Benefícios da funcionalidade**: Registar o seu VM do Servidor SQL com o fornecedor de recursos desbloqueia [patching automatizado,](virtual-machines-windows-sql-automated-patching.md) [backup automatizado,](virtual-machines-windows-sql-automated-backup-v2.md)bem como capacidades de monitorização e gestão. Também desbloqueia a flexibilidade de [licenciamento](virtual-machines-windows-sql-ahb.md) e [edição.](virtual-machines-windows-sql-change-edition.md) Anteriormente, estas funcionalidades só estavam disponíveis para imagens VM do SQL Server implantadas a partir do Azure Marketplace. 

- **Conformidade**: O registo com o fornecedor de recursos SQL VM oferece um método simplificado de cumprimento do requisito de notificação à Microsoft de que o Benefício Híbrido Azure foi ativado como especificado nos termos do produto. Este processo nega a necessidade de gerir formulários de registo de licenciamento para cada recurso.  

- **Gestão gratuita**: Registar-se com o fornecedor de recursos SQL VM nos três modos de gestão é completamente gratuito. Não existe um custo adicional associado ao fornecedor de recursos, ou com a alteração dos modos de gestão. 

- **Gestão simplificada da licença**: Registar-se com o fornecedor de recursos SQL VM simplifica a gestão da licença SQL Server e permite identificar rapidamente VMs do Servidor SQL com o Benefício Híbrido Azure habilitado através do [portal Azure,](virtual-machines-windows-sql-manage-portal.md)o Az CLI ou PowerShell: 

   # <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

   ```azurecli-interactive
   $vms = az sql vm list | ConvertFrom-Json
   $vms | Where-Object {$_.sqlServerLicenseType -eq "AHUB"}
   ```

   # <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell-interactive
   Get-AzSqlVM | Where-Object {$_.LicenseType -eq 'AHUB'}
   ```

   ---

Para utilizar o fornecedor de recursos SQL VM, tem primeiro de registar a [sua subscrição junto do fornecedor](#register-subscription-with-rp)de recursos, o que dá ao fornecedor de recursos a capacidade de criar recursos dentro dessa subscrição específica.

Para obter mais informações sobre os benefícios da utilização do fornecedor de recursos SQL VM, veja este vídeo [do canal9:](https://channel9.msdn.com/Shows/Data-Exposed/Benefit-from-SQL-VM-Resource-Provider-when-self-installing-SQL-Server-on-Azure?WT.mc_id=dataexposed-c9-niner) 

<iframe src="https://channel9.msdn.com/Shows/Data-Exposed/Benefit-from-SQL-VM-Resource-Provider-when-self-installing-SQL-Server-on-Azure/player" width="960" height="540" allowFullScreen frameBorder="0" title="Beneficie do Fornecedor de Recursos SQL VM ao instalar o Servidor SQL em Azure - Microsoft Channel 9 Video"></iframe>


## <a name="prerequisites"></a>Pré-requisitos

Para registar o seu VM do Servidor SQL com o fornecedor de recursos, necessitará de: 

- Uma [subscrição Azure.](https://azure.microsoft.com/free/)
- Um Modelo de Recursos Azure [SQL Server VM](virtual-machines-windows-portal-sql-server-provision.md) implantado para a nuvem pública ou do Governo Azure. 
- A versão mais recente do [Azure CLI](/cli/azure/install-azure-cli) ou [PowerShell.](/powershell/azure/new-azureps-module-az) 

## <a name="management-modes"></a>Modos de gestão

Se a [extensão SQL IaaS](virtual-machines-windows-sql-server-agent-extension.md) ainda não tiver sido instalada, o registo com o fornecedor de recursos SQL VM instala automaticamente a extensão SQL Server IaaS num dos três modos de gestão, especificados durante o processo de registo. Não especificando o modo de gestão irá instalar a extensão SQL IaaS em modo de gestão completo.  

Se a extensão SQL IaaS já tiver sido instalada manualmente, então já está em modo de gestão completa, e o registo com o fornecedor de recursos em modo completo não reiniciará o serviço SQL Server.

Os três modos de gestão são:

- **O** modo leve não requer o reinício do Servidor SQL, mas suporta apenas alterar o tipo de licença e a edição do SQL Server. Utilize esta opção para VMs do Servidor SQL com múltiplas instâncias, ou participando numa instância de cluster failover (FCI). Não há impacto na memória ou cpu quando se utiliza o modo leve, e não há custo associado. Recomenda-se registar primeiro o seu VM do Servidor SQL em modo leve e, em seguida, atualizar para o modo Full durante uma janela de manutenção programada.  

- **O** modo completo fornece toda a funcionalidade, mas requer um reinício das permissões do Servidor SQL e do administrador do sistema. Esta é a opção que é instalada por padrão ao instalar manualmente a extensão SQL IaaS. Use-o para gerir um VM de servidor SQL com uma única instância. O modo completo instala dois serviços windows que têm um impacto mínimo na memória e CPU - estes podem ser monitorizados através do gestor de tarefas. Não existe qualquer custo associado à utilização do modo de gestão total. 

- O modo **NoAgent** é dedicado ao SQL Server 2008 e ao SQL Server 2008 R2 instalado no Windows Server 2008. Não há impacto na memória ou cpU quando utilizar o modo NoAgent. Não existe qualquer custo associado à utilização do modo de gestão NoAgent. 

Pode visualizar o modo atual do seu agente SQL Server IaaS utilizando o PowerShell: 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```

## <a name="register-subscription-with-rp"></a>Registe a subscrição com RP

Para registar o seu VM De Servidor SQL com o fornecedor de recursos SQL VM, tem primeiro de registar a sua subscrição junto do fornecedor de recursos. Isto dá ao fornecedor de recursos SQL VM a capacidade de criar recursos dentro da sua subscrição.  Pode fazê-lo utilizando o portal Azure, o Azure CLI ou o PowerShell.

### <a name="azure-portal"></a>Portal do Azure

1. Abra o portal Azure e vá a **Todos os Serviços.** 
1. Vá a **Assinaturas** e selecione a subscrição de juros.  
1. Na página **de Subscrições,** vá aos fornecedores de **Recursos.** 
1. Introduza o **sql** no filtro para trazer os fornecedores de recursos relacionados com o SQL. 
1. Selecione **Register**, **Re-register,** ou **Desregistre-se** para o fornecedor **Microsoft.SqlVirtualMachine,** dependendo da ação desejada. 

![Modificar o fornecedor](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)


### <a name="command-line"></a>Linha de comandos

Registe o seu fornecedor de recursos SQL VM na sua subscrição Azure utilizando o Az CLI ou o PowerShell. 

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

```azurecli-interactive
# Register the SQL VM resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

```powershell-interactive
# Register the SQL VM resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```

---

## <a name="register-sql-vm-with-rp"></a>Registar SQL VM com RP 

### <a name="lightweight-management-mode"></a>Modo de gestão leve

Se a extensão do [agente SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md) não tiver sido instalada no VM, então a recomendação é registar-se com o fornecedor de recursos SQL VM em modo leve. Isto instalará a extensão SQL IaaS em [modo leve](#management-modes) e impedirá o reinício do serviço SQL Server. Em seguida, pode fazer o upgrade para o modo completo a qualquer momento, mas fazê-lo reiniciará o serviço SQL Server, pelo que é aconselhável esperar até uma janela de manutenção programada. 

Forneça o tipo de licença SQL Server como pagamento -as-you-go (`PAYG`) para pagar por utilização, Azure Hybrid Benefit (`AHUB`) para usar a sua própria licença, ou recuperação de desastres (`DR`) para ativar a [licença de réplica DR gratuita](virtual-machines-windows-sql-high-availability-dr.md#free-dr-replica-in-azure).

Falhas de cluster As instâncias e as implementações em várias instâncias só podem ser registadas com o fornecedor de recursos SQL VM em modo leve. 

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

Registe o VM do Servidor SQL em modo leve com o Az CLI: 

  ```azurecli-interactive
  # Register Enterprise or Standard self-installed VM in Lightweight mode
  az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type PAYG 
  ```


# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

Registe o VM do Servidor SQL em modo leve com a PowerShell:  


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  # Register SQL VM with 'Lightweight' SQL IaaS agent
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType LightWeight  
  ```

---

### <a name="full-management-mode"></a>Modo de gestão completa


Se a extensão SQL IaaS já tiver sido instalada manualmente no VM, então pode registar o VM do Servidor SQL em modo completo sem reiniciar o serviço SQL Server. **No entanto, se a extensão SQL IaaS não tiver sido instalada, o registo em modo completo instalará a extensão SQL IaaS em modo completo e reiniciará o serviço SQL Server. Por favor, proceda com cuidado.**


Para registar o vM do seu Servidor SQL diretamente em modo completo (e possivelmente reiniciar o seu serviço SQL Server), utilize o seguinte comando PowerShell: 

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL VM resource provider in full mode
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

### <a name="noagent-management-mode"></a>Modo de gestão NoAgent 

O SQL Server 2008 e 2008 R2 instalados no Windows Server 2008 _(não R2_) podem ser registados com o fornecedor de recursos SQL VM no [modo NoAgent](#management-modes). Esta opção garante a conformidade e permite que o VM do Servidor SQL seja monitorizado no portal Azure com funcionalidade limitada.

Especifique `AHUB`, `PAYG`, ou `DR` como o **sqlLicenseType,** e `SQL2008-WS2008` ou `SQL2008R2-WS2008` como o **sqlImageOffer**. 

Para registar a sua instância SQL Server 2008 ou 2008 R2 na instância Windows Server 2008, utilize o seguinte snippet de código Az CLI ou PowerShell: 


# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

Registe o seu VM SQL Server 2008 no modo NoAgent com o Az CLI: 

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type PAYG --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer SQL2008-WS2008R2
 ```
 
 
Registe o seu SQL Server 2008 R2 VM no modo NoAgent com o Az CLI: 

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type PAYG --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer SQL2008R2-WS2008R2
 ```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

Registe o SQL Server 2008 VM no modo NoAgent com powerShell: 


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType NoAgent -Sku Standard -Offer SQL2008-WS2008
  ```
  
  Registe o SQL Server 2008 R2 VM no modo NoAgent com powerShell: 


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType NoAgent -Sku Standard -Offer SQL2008R2-WS2008
  ```

---

## <a name="upgrade-to-full-management-mode"></a>Upgrade para o modo de gestão completa 

Os VMs do Servidor SQL que tenham a extensão IaaS *leve* instalada podem atualizar o modo para _o completo_ utilizando o portal Azure, o Az CLI ou o PowerShell. Os VMs do Servidor SQL no modo _NoAgent_ podem ser atualizados para _o completo_ após o OS ser atualizado para o Windows 2008 R2 ou superior. Não é possível desvalorizar - para o fazer, terá de [desregistar](#unregister-vm-from-rp) o VM do Servidor SQL do fornecedor de recursos SQL VM. Ao fazê-lo, removerá o _recurso_da **máquina virtual SQL,** mas não eliminará a máquina virtual real. 

Pode visualizar o modo atual do seu agente SQL Server IaaS utilizando o PowerShell: 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```

Para atualizar o modo de agente para o seu pleno: 


### <a name="azure-portal"></a>Portal do Azure

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
1. Vá ao seu recurso de [máquinas virtuais SQL.](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) 
1. Selecione a sua máquina virtual SQL Server e selecione **visão geral**. 
1. Para VMs de servidor SQL com o modo NoAgent ou leve IaaS, selecione as atualizações do tipo de licença only e edição estão disponíveis com a mensagem de **extensão SQL IaaS.**

   ![Seleções para alterar o modo a partir do portal](media/virtual-machines-windows-sql-server-agent-extension/change-sql-iaas-mode-portal.png)

1. Selecione o **I concordo em reiniciar o serviço SQL Server na** caixa de verificação de máquinas virtuais e, em seguida, selecione **Confirmar** para atualizar o seu modo IaaS para o seu completo. 

    ![Verifique a caixa para concordar em reiniciar o serviço SQL Server na máquina virtual](media/virtual-machines-windows-sql-server-agent-extension/enable-full-mode-iaas.png)

### <a name="command-line"></a>Linha de comandos

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

Executar o seguinte corte de código Az CLI:

  ```azurecli-interactive
  # Update to full mode
  az sql vm update --name <vm_name> --resource-group <resource_group_name> --sql-mgmt-type full  
  ```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

Executar o seguinte corte de código PowerShell:

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL VM resource provider in full mode
  Update-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

---

## <a name="verify-registration-status"></a>Verificar o estado do registo
Pode verificar se o seu VM de Servidor SQL já foi registado no fornecedor de recursos SQL VM utilizando o portal Azure, o Azure CLI ou o PowerShell. 

### <a name="azure-portal"></a>Portal do Azure 

1. Inicie sessão no [Portal do Azure](https://portal.azure.com). 
1. Vá às suas [máquinas virtuais SQL Server](virtual-machines-windows-sql-manage-portal.md).
1. Selecione o seu VM do Servidor SQL da lista. Se o seu VM de Servidor SQL não estiver listado aqui, provavelmente não foi registado no fornecedor de recursos SQL VM. 
1. Ver o valor em **status**. Se o **Status** for **bem sucedido,** então o VM do Servidor SQL foi registado com sucesso no fornecedor de recursos SQL VM. 

![Verificar o estado com o registo de RP SQL](media/virtual-machines-windows-sql-register-with-rp/verify-registration-status.png)

### <a name="command-line"></a>Linha de comandos

Verifique o estado de registo VM do Servidor SQL atual utilizando a Az CLI ou a PowerShell. `ProvisioningState` mostrará `Succeeded` se o registo tiver sido bem sucedido. 

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)


  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
 ```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

  ```powershell-interactive
  Get-AzSqlVM -Name <vm_name> -ResourceGroupName <resource_group>
  ```

---

Um erro indica que o VM do Servidor SQL não foi registado no fornecedor de recursos. 


## <a name="unregister-vm-from-rp"></a>VM não registro de RP

Para não registar o seu VM do Servidor SQL com o fornecedor de recursos SQL VM, elimine o *recurso* SQL Virtual Machine utilizando o portal Azure ou o Azure CLI. A eliminação do *recurso* SQL Virtual Machine não elimina o VM do Servidor SQL. No entanto, tenha cuidado e siga cuidadosamente os passos porque é possível eliminar inadvertidamente a máquina virtual ao tentar remover o *recurso*. 

O desinscrição do VM SQL com o fornecedor de recursos SQL VM é necessário para desvalorizar o modo de gestão da totalidade. 

### <a name="azure-portal"></a>Portal do Azure

Para não registar o seu VM do Servidor SQL com o fornecedor de recursos que utiliza o portal Azure, siga estes passos:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
1. Navegue para o recurso VM do Servidor SQL. 
  
   ![Recurso de máquinas virtuais SQL](media/virtual-machines-windows-sql-manage-portal/sql-vm-manage.png)

1. Selecione **Eliminar**. 

   ![Eliminar o fornecedor de recursos SQL VM](media/virtual-machines-windows-sql-register-with-rp/delete-sql-vm-resource-provider.png)

1. Digite o nome da máquina virtual SQL e limpe a caixa de **verificação ao lado da máquina virtual**.

   ![Eliminar o fornecedor de recursos SQL VM](media/virtual-machines-windows-sql-register-with-rp/confirm-delete-of-resource-uncheck-box.png)

   >[!WARNING]
   > Se não limpar a caixa de verificação ao lado do nome da máquina *virtual, a* máquina virtual eliminará totalmente a máquina virtual. Limpe a caixa de verificação para desregistar o VM do Servidor SQL do fornecedor de recursos, mas *não elimine a máquina virtual real*. 

1. **Selecione Eliminar** para confirmar a eliminação do *recurso*virtual da máquina SQL , e não da máquina virtual Do Servidor SQL. 

### <a name="command-line"></a>Linha de comandos

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)
Para desregistar a sua máquina virtual SQL Server do fornecedor de recursos com o Azure CLI, utilize o comando [az sql vm eliminar.](/cli/azure/sql/vm?view=azure-cli-latest#az-sql-vm-delete) Isto removerá o *recurso* virtual da máquina Do SQL Server, mas não eliminará a máquina virtual. 


```azurecli-interactive
az sql vm delete 
  --name <SQL VM resource name> |
  --resource-group <Resource group name> |
  --yes 
```

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Para desregistar a sua máquina virtual SQL Server do fornecedor de recursos com o Azure CLI, utilize o comando [New-AzSqlVM.](/powershell/module/az.sqlvirtualmachine/new-azsqlvm) Isto removerá o *recurso* virtual da máquina Do SQL Server, mas não eliminará a máquina virtual. 

```powershell-interactive
Remove-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name>
```

---

## <a name="limitations"></a>Limitações

O fornecedor de recursos SQL VM apenas suporta:
- VMs do Servidor SQL implantados através do Gestor de Recursos Azure. Os VMs do Servidor SQL implantados através do modelo clássico não são suportados. 
- VMs de servidor SQL implantados para o público ou nuvem do Governo Azure. Não são apoiados destacamentos para outras nuvens privadas ou governamentais. 


## <a name="frequently-asked-questions"></a>Perguntas mais frequentes 

**Devo registar o meu VM de servidor SQL a partir de uma imagem do Servidor SQL no Azure Marketplace?**

Não. A Microsoft regista automaticamente VMs a partir das imagens do SQL Server no Azure Marketplace. O registo com o fornecedor de recursos SQL VM só é necessário se o VM *não* tiver sido abastecido a partir das imagens do SQL Server no Azure Marketplace e no SQL Server foi auto-instalado.

**O fornecedor de recursos de VM do SQL está disponível para todos os clientes?** 

Sim. Os clientes devem registar os seus VMs de Servidor SQL com o fornecedor de recursos SQL VM se não utilizarem uma imagem do SQL Server do Azure Marketplace e, em vez disso, servidor SQL auto-instalado, ou se trouxeram o seu VHD personalizado. Os VMs detidos por todos os tipos de subscrições (Direct, Enterprise Agreement e Cloud Solution Provider) podem registar-se junto do fornecedor de recursos SQL VM.

**Devo registar-me no fornecedor de recursos SQL VM se o meu VM SQL Server já tiver a extensão SQL Server IaaS instalada?**

Se o seu VM do Servidor SQL estiver auto-instalado e não for aprovisionado a partir das imagens do SQL Server no Azure Marketplace, deverá registar-se junto do fornecedor de recursos SQL VM mesmo que tenha instalado a extensão SQL Server IaaS. Registar-se com o fornecedor de recursos SQL VM cria um novo recurso do tipo Microsoft.SqlVirtualMachines. A instalação da extensão SQL Server IaaS não cria esse recurso.

**Qual é o modo de gestão predefinido ao registar-se com o fornecedor de recursos SQL VM?**

O modo de gestão predefinido quando se regista no fornecedor de recursos SQL VM está *cheio*. Se a propriedade de gestão do SQL Server não estiver definida quando se registar com o fornecedor de recursos SQL VM, o modo será definido como uma gestão total, e o seu serviço SQL Server reiniciará. Recomenda-se registar-se primeiro com o fornecedor de recursos SQL VM em modo leve e, em seguida, atualizar para o seu pleno durante uma janela de manutenção. 

**Quais são os pré-requisitos para se registar com o fornecedor de recursos SQL VM?**

Não existem pré-requisitos para se registar com o fornecedor de recursos SQL VM em modo leve ou sem agente. O pré-requisito para se registar com o fornecedor de recursos SQL VM em modo completo é ter a extensão SQL Server IaaS instalada no VM, caso contrário o serviço SQL Server reiniciará. 

**Posso registar-me no fornecedor de recursos SQL VM se não tiver a extensão SQL Server IaaS instalada no VM?**

Sim, pode registar-se com o fornecedor de recursos SQL VM em modo de gestão leve se não tiver a extensão SQL Server IaaS instalada no VM. Em modo leve, o fornecedor de recursos SQL VM utilizará uma aplicação de consola para verificar a versão e edição da instância SQL Server. 

O modo de gestão SQL predefinido ao registar-se com o fornecedor de recursos SQL VM está _completo_. Se a propriedade sQL Management não for definida ao registar-se com o fornecedor de recursos SQL VM, então o modo será definido como Total Manageability. Recomenda-se registar-se primeiro com o fornecedor de recursos SQL VM em modo leve e, em seguida, atualizar para o seu pleno durante uma janela de manutenção. 

**O registo com o fornecedor de recursos SQL VM vai instalar um agente no meu VM?**

Não. Registar-se com o fornecedor de recursos SQL VM só criará um novo recurso de metadados. Não vai instalar um agente no VM.

A extensão SQL Server IaaS é necessária apenas para permitir a plena gestão. A atualização do modo de gestão de leve para o full instalará a extensão SQL Server IaaS e reiniciará o Servidor SQL.

**O registo com o fornecedor de recursos SQL Server VM reiniciará o Servidor SQL no meu VM?**

Depende do modo especificado durante o registo. Se for especificado o modo Leve ou NoAgent, o serviço SQL Server não reiniciará. No entanto, especificar o modo de gestão como completo, ou deixar o modo de gestão em branco, instalará a extensão SQL IaaS em modo de gestão completo, o que fará com que o serviço SQL Server reinicie. 

**Qual é a diferença entre modos de gestão leves e sem agente ao registar-se no fornecedor de recursos SQL VM?** 

O modo de gestão sem agente está disponível apenas para SQL Server 2008 e SQL Server 2008 R2 no Windows Server 2008. É o único modo de gestão disponível para estas versões. Para todas as outras versões do SQL Server, os dois modos de gestão disponíveis são leves e cheios. 

O modo no-agent requer que as propriedades da versão e edição do SQL Server sejam definidas pelo cliente. O modo leve consulta o VM para encontrar a versão e edição da instância SQL Server.

**Posso registar-me no fornecedor de recursos SQL VM sem especificar o tipo de licença Do Servidor SQL?**

Não. O tipo de licença SQL Server não é uma propriedade opcional quando está a registar-se com o fornecedor de recursos SQL VM. Tem de definir o tipo de licença SQL Server como pay-as-you-go ou Azure Hybrid Benefit ao registar-se com o fornecedor de recursos SQL VM em todos os modos de gestão (sem agente, leve e completo).

**Posso atualizar a extensão SQL Server IaaS do modo sem agente para o modo completo?**

Não. A atualização do modo de gestão para o modo completo ou leve não está disponível para o modo não-agente. Esta é uma limitação técnica do Windows Server 2008. Terá de atualizar o OS primeiro para o Windows Server 2008 R2 ou superior, e depois poderá fazer o upgrade para o modo de gestão completo. 

**Posso atualizar a extensão SQL Server IaaS do modo leve para o modo completo?**

Sim. A atualização do modo de gestão de peso leve para completo é suportada através da PowerShell ou do portal Azure. Requer reiniciar o serviço SQL Server.

**Posso desvalorizar a extensão SQL Server IaaS do modo completo para o modo de gestão sem agente ou leve?**

Não. Não é suportado o modo de gestão da extensão SQL Server IaaS. O modo de gestão não pode ser desclassificado do modo completo para o modo leve ou sem agente, e não pode ser desclassificado do modo leve para o modo sem agente. 

Para alterar o modo de gestão da capacidade de gestão total, [desregindo](#unregister-vm-from-rp) a máquina virtual do Servidor SQL do fornecedor de recursos do SQL Server, deixando cair o *recurso* SQL Server e reregistrando o VM do Servidor SQL com o fornecedor de recursos SQL VM novamente num modo de gestão diferente.

**Posso inscrever-me no fornecedor de recursos SQL VM do portal Azure?**

Não. O registo com o fornecedor de recursos SQL VM não está disponível no portal Azure. O registo com o fornecedor de recursos SQL VM é suportado apenas com o Azure CLI ou PowerShell. 

**Posso registar um VM com o fornecedor de recursos SQL VM antes da instalação do Servidor SQL?**

Não. Uma VM deve ter pelo menos uma instância de SQL Server (Mecanismo de Banco de Dados) para registrar com êxito com o provedor de recursos de VM do SQL. Se não houver uma instância do SQL Server no VM, o novo recurso Microsoft.SqlVirtualMachine estará em estado falhado.

**Posso registar um VM com o fornecedor de recursos SQL VM se houver várias instâncias do Servidor SQL?**

Sim. O provedor de recursos da VM do SQL registrará apenas uma instância do SQL Server (Mecanismo de Banco de Dados). O fornecedor de recursos SQL VM registará a instância padrão do Servidor SQL no caso de múltiplas instâncias. Se não houver uma instância predefinida, apenas é suportado o registo em modo leve. Para atualizar do modo de gestão leve para o modo de gestão total, ou a instância padrão do Servidor SQL deve existir ou o VM deve ter apenas uma chamada instância de Servidor SQL.

**Posso registar uma instância de cluster sQL Server failover com o fornecedor de recursos SQL VM?**

Sim. As instâncias de cluster de falha do SQL Server num VM Azure podem ser registadas com o fornecedor de recursos SQL VM em modo leve. No entanto, as instâncias de cluster de failover do SQL Server não podem ser atualizadas para o modo de gestão total.

**Posso registar o meu VM com o fornecedor de recursos SQL VM se um grupo de disponibilidade Always On estiver configurado?**

Sim. Não existem restrições ao registo de uma instância do SQL Server num VM Azure com o fornecedor de recursos SQL VM se estiver a participar numa configuração de grupo sempre em jogo.

**Qual é o custo para se registar com o fornecedor de recursos SQL VM, ou com a atualização para o modo de gestão completa?**
Nenhum. Não existe qualquer taxa associada ao registo com o fornecedor de recursos SQL VM, ou com a utilização de qualquer um dos três modos de gestão. Gerir o seu VM de Servidor SQL com o fornecedor de recursos é completamente gratuito. 

**Qual é o impacto do desempenho da utilização dos diferentes modos de gestão?**
Não há impacto na utilização dos modos de gestão *NoAgent* e *leve.* Existe um impacto mínimo ao utilizar o modo de gestão *total* de dois serviços instalados no SISTEMA. Estes podem ser monitorizados através do gestor de tarefas e vistos na consola incorporada do Windows Services. 

Os dois nomes de serviço são:
- `SqlIaaSExtensionQuery` (Nome de exibição - `Microsoft SQL Server IaaS Query Service`)
- `SQLIaaSExtension` (Nome de exibição - `Microsoft SQL Server IaaS Agent`)


## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, veja os artigos seguintes: 

* [Visão geral do Servidor SQL num VM do Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [FAQ para Servidor SQL em um VM do Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Orientação de preços para O Servidor SQL num VM do Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Notas de lançamento para SQL Server num VM do Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)
