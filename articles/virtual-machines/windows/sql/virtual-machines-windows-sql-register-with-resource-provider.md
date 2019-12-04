---
title: Registrar com o provedor de recursos de VM do SQL
description: Registre sua máquina virtual do Azure SQL Server com o provedor de recursos da VM do SQL para habilitar recursos para SQL Server VMs implantadas fora do Azure Marketplace, bem como conformidade e capacidade de gerenciamento aprimorada.
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
ms.openlocfilehash: 6b2430b5135a5d3f7ad1f9ef0bd17d9149bf48ee
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74793465"
---
# <a name="register-a-sql-server-virtual-machine-in-azure-with-the-sql-vm-resource-provider"></a>Registrar uma máquina virtual SQL Server no Azure com o provedor de recursos de VM do SQL

Este artigo descreve como registrar seu SQL Server VM (máquina virtual) no Azure com o provedor de recursos de VM do SQL. O registro com o provedor de recursos cria o _recurso_ de **máquina virtual do SQL** em sua assinatura, que é um recurso separado do recurso de máquina virtual. Cancelar o registro do seu SQL Server VM do provedor de recursos removerá o _recurso_ de **máquina virtual do SQL** , mas não descartará a máquina virtual real. 

A implantação de uma imagem SQL Server VM do Azure Marketplace por meio do portal do Azure registra automaticamente a VM SQL Server com o provedor de recursos. No entanto, se você optar por instalar automaticamente o SQL Server em uma máquina virtual do Azure ou provisionar uma máquina virtual do Azure de um VHD personalizado, deverá registrar sua VM SQL Server com o provedor de recursos para:

- **Benefícios do recurso**: registrar seu SQL Server VM com o provedor de recursos desbloqueia a [aplicação de patch automatizada](virtual-machines-windows-sql-automated-patching.md), o [backup automatizado](virtual-machines-windows-sql-automated-backup-v2.md), bem como recursos de monitoramento e gerenciamento. Ele também desbloqueia a flexibilidade de [Licenciamento](virtual-machines-windows-sql-ahb.md) e de [edição](virtual-machines-windows-sql-change-edition.md) . Anteriormente, esses recursos estavam disponíveis apenas para SQL Server imagens de VM implantadas do Azure Marketplace. 

- **Conformidade**: o registro com o provedor de recursos de VM do SQL oferece um método simplificado para atender à necessidade de notificar a Microsoft de que o benefício híbrido do Azure foi habilitado conforme especificado nos termos do produto. Esse processo nega a necessidade de gerenciar os formulários de registro de licenciamento para cada recurso.  

- **Gerenciamento gratuito**: o registro com o provedor de recursos de VM do SQL nos três modos de gerenciamento é completamente gratuito. Não há custo adicional associado ao provedor de recursos ou com a alteração dos modos de gerenciamento. 

- **Gerenciamento de licenças simplificado**: o registro com o provedor de recursos de VM do SQL simplifica SQL Server o gerenciamento de licenças e permite identificar rapidamente SQL Server VMs com o benefício híbrido do Azure habilitado usando o [portal do Azure](virtual-machines-windows-sql-manage-portal.md), a CLI AZ ou o PowerShell: 

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

Para utilizar o provedor de recursos de VM do SQL, você deve primeiro [registrar sua assinatura com o provedor de recursos](#register-subscription-with-rp), o que fornece ao provedor de recursos a capacidade de criar recursos dentro dessa assinatura específica.

Para obter mais informações sobre os benefícios de usar o provedor de recursos de VM do SQL, Assista a este vídeo do [channel9](https://channel9.msdn.com/Shows/Data-Exposed/Benefit-from-SQL-VM-Resource-Provider-when-self-installing-SQL-Server-on-Azure?WT.mc_id=dataexposed-c9-niner) : 

<iframe src="https://channel9.msdn.com/Shows/Data-Exposed/Benefit-from-SQL-VM-Resource-Provider-when-self-installing-SQL-Server-on-Azure/player" width="960" height="540" allowFullScreen frameBorder="0" title="Beneficie-se do provedor de recursos da VM do SQL ao instalar automaticamente SQL Server no Azure – vídeo do Microsoft Channel 9"></iframe>


## <a name="prerequisites"></a>Pré-requisitos

Para registrar sua VM SQL Server com o provedor de recursos, você precisará de: 

- Uma [assinatura do Azure](https://azure.microsoft.com/free/).
- Um modelo de recurso do Azure [SQL Server VM](virtual-machines-windows-portal-sql-server-provision.md) implantada na nuvem pública. 
- A versão mais recente do [CLI do Azure](/cli/azure/install-azure-cli) ou do [PowerShell](/powershell/azure/new-azureps-module-az). 

## <a name="management-modes"></a>Modos de gerenciamento

Se a [extensão de IaaS do SQL](virtual-machines-windows-sql-server-agent-extension.md) ainda não tiver sido instalada, o registro com o provedor de recursos da VM do SQL instalará automaticamente a extensão SQL Server IaaS em um dos três modos de gerenciamento, especificado durante o processo de registro. Não especificar o modo de gerenciamento instalará a extensão IaaS do SQL no modo de gerenciamento completo.  

Se a extensão de IaaS do SQL já tiver sido instalada manualmente, ela já estará no modo de gerenciamento completo e o registro com o provedor de recursos no modo completo não reiniciará o serviço SQL Server.

Os três modos de gerenciamento são:

- O modo **leve** não requer a reinicialização de SQL Server, mas dá suporte apenas à alteração do tipo de licença e da edição do SQL Server. Use essa opção para SQL Server VMs com várias instâncias ou para participar de uma FCI (instância de cluster de failover). Não há nenhum impacto na memória ou na CPU ao usar o modo lightweight, e não há nenhum custo associado. É recomendável registrar seu SQL Server VM no modo leve primeiro e, em seguida, atualizar para o modo completo durante uma janela de manutenção agendada.  

- O modo **completo** fornece todas as funcionalidades, mas requer uma reinicialização das permissões de SQL Server e administrador do sistema. Essa é a opção que é instalada por padrão ao instalar a extensão IaaS do SQL manualmente. Use-o para gerenciar uma VM SQL Server com uma única instância. O modo completo instala dois serviços do Windows que têm um impacto mínimo sobre a memória e a CPU. eles podem ser monitorados por meio do Gerenciador de tarefas. Não há nenhum custo associado ao uso do modo de gerenciamento completo. 

- O modo **noagent** é dedicado a SQL Server 2008 e SQL Server 2008 R2 instalado no Windows Server 2008. Não há nenhum impacto na memória ou na CPU ao usar o modo noagent. Não há nenhum custo associado ao uso do modo de gerenciamento noagent. 

Você pode exibir o modo atual de seu SQL Server agente IaaS usando o PowerShell: 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```

## <a name="register-subscription-with-rp"></a>Registrar assinatura com RP

Para registrar sua VM SQL Server com o provedor de recursos de VM do SQL, você deve primeiro registrar sua assinatura com o provedor de recursos. Isso fornece ao provedor de recursos da VM do SQL a capacidade de criar recursos em sua assinatura.  Você pode fazer isso usando o portal do Azure, o CLI do Azure ou o PowerShell.

### <a name="azure-portal"></a>Portal do Azure

1. Abra o portal do Azure e vá para **todos os serviços**. 
1. Vá para **assinaturas** e selecione a assinatura de interesse.  
1. Na página **assinaturas** , vá para **provedores de recursos**. 
1. Insira **SQL** no filtro para abrir os provedores de recursos relacionados ao SQL. 
1. Selecione **registrar**, **registrar novamente**ou cancelar **o registro** para o provedor **Microsoft. SqlVirtualMachine** , dependendo da ação desejada. 

![Modificar o provedor](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)


### <a name="command-line"></a>Linha de comandos

Registre seu provedor de recursos de VM do SQL em sua assinatura do Azure usando AZ CLI ou PowerShell. 

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

## <a name="register-sql-vm-with-rp"></a>Registrar VM do SQL com RP 

### <a name="lightweight-management-mode"></a>Modo de gerenciamento leve

Se a [extensão do agente IaaS SQL Server](virtual-machines-windows-sql-server-agent-extension.md) não tiver sido instalada na VM, a recomendação será registrar-se no provedor de recursos de VM do SQL no modo leve. Isso instalará a extensão IaaS do SQL no [modo leve](#management-modes) e impedirá que o serviço de SQL Server seja reiniciado. Em seguida, você pode atualizar para o modo completo a qualquer momento, mas isso reiniciará o serviço de SQL Server para que seja recomendável aguardar até uma janela de manutenção agendada. Você precisa fornecer o tipo de licença de SQL Server como um`PAYG`(pré-pago) para pagar por uso ou Benefício Híbrido do Azure (`AHUB`) para usar sua própria licença.

As instâncias de cluster de failover e as implantações de várias instâncias só podem ser registradas com o provedor de recursos de VM do SQL no modo leve. 

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

Registre SQL Server VM no modo leve com a CLI AZ: 

  ```azurecli-interactive
  # Register Enterprise or Standard self-installed VM in Lightweight mode
  az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type PAYG 
  ```


# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

Registrar SQL Server VM no modo leve com o PowerShell:  


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  # Register SQL VM with 'Lightweight' SQL IaaS agent
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType LightWeight  
  ```

---

### <a name="full-management-mode"></a>Modo de gerenciamento completo


Se a extensão de IaaS do SQL já tiver sido instalada na VM manualmente, você poderá registrar a VM SQL Server em modo completo sem reiniciar o serviço SQL Server. **No entanto, se a extensão de IaaS do SQL não tiver sido instalada, o registro no modo completo instalará a extensão IaaS do SQL no modo completo e reiniciará o serviço de SQL Server. Continue com cuidado.**

Abaixo está o trecho de código para registrar com o provedor de recursos de VM do SQL em modo completo. Para se registrar no modo de gerenciamento completo, use o seguinte comando do PowerShell:

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL VM resource provider in full mode
  Update-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```


### <a name="noagent-management-mode"></a>Modo de gerenciamento noagent 

SQL Server 2008 e 2008 R2 instalados no Windows Server 2008 podem ser registrados com o provedor de recursos de VM do SQL no [modo noagent](#management-modes). Essa opção garante a conformidade e permite que a VM SQL Server seja monitorada no portal do Azure com funcionalidade limitada.

Especifique `AHUB` ou `PAYG` como **Sqllicenciadotype**e `SQL2008-WS2008` ou `SQL2008R2-WS2008` como **sqlImageOffer**. 

Para registrar sua instância do SQL Server 2008 ou 2008 R2 na instância do Windows Server 2008, use os seguintes trechos de código AZ CLI ou PowerShell: 


# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

Registre SQL Server VM no modo noagent com a CLI AZ: 

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type PAYG --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer SQL2008-WS2008R2
 ```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

Registrar SQL Server VM no modo noagent com o PowerShell: 


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType NoAgent -Sku Standard -Offer SQL2008R2-WS2008
  ```

---

## <a name="upgrade-to-full-management-mode"></a>Atualizar para o modo de gerenciamento completo 

SQL Server VMs que têm a extensão de IaaS *leve* instalada podem atualizar o modo para _completo_ usando o portal do Azure, o AZ CLI ou o PowerShell. SQL Server VMs no modo _noagent_ podem ser atualizadas para _Full_ depois que o sistema operacional for atualizado para o Windows 2008 R2 e superior. Não é possível fazer downgrade – para isso, você precisará [cancelar o registro](#unregister-vm-from-rp) da VM SQL Server do provedor de recursos da VM do SQL. Isso removerá o _recurso_de **máquina virtual do SQL** , mas não excluirá a máquina virtual real. 

Você pode exibir o modo atual de seu SQL Server agente IaaS usando o PowerShell: 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```

Para atualizar o modo do agente para completo: 


### <a name="azure-portal"></a>Portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Vá para o recurso de [máquinas virtuais do SQL](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) . 
1. Selecione sua SQL Server máquina virtual e selecione **visão geral**. 
1. Para SQL Server VMs com o modo noagent ou Lightweight IaaS, selecione o **único tipo de licença e atualizações de edição estão disponíveis com a mensagem de extensão IaaS do SQL** .

   ![Seleções para alterar o modo do portal](media/virtual-machines-windows-sql-server-agent-extension/change-sql-iaas-mode-portal.png)

1. Selecione a caixa de seleção **eu concordo em reiniciar o serviço de SQL Server na máquina virtual** e, em seguida, selecione **confirmar** para atualizar o modo IaaS para completo. 

    ![Caixa de seleção para concordar em reiniciar o serviço de SQL Server na máquina virtual](media/virtual-machines-windows-sql-server-agent-extension/enable-full-mode-iaas.png)

### <a name="command-line"></a>Linha de comandos

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

Execute o seguinte trecho de código AZ CLI:

  ```azurecli-interactive
  # Update to full mode
  az sql vm update --name <vm_name> --resource-group <resource_group_name> --sql-mgmt-type full  
  ```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

Execute o seguinte trecho de código do PowerShell:

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>

  # Update to full mode
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
     -LicenseType PAYG -SqlManagementType Full
  ```

---

## <a name="verify-registration-status"></a>Verificar status do registro
Você pode verificar se sua VM de SQL Server já foi registrada com o provedor de recursos de VM do SQL usando o portal do Azure, o CLI do Azure ou o PowerShell. 

### <a name="azure-portal"></a>Portal do Azure 

1. Inicie sessão no [portal do Azure](https://portal.azure.com). 
1. Vá para o [SQL Server máquinas virtuais](virtual-machines-windows-sql-manage-portal.md).
1. Selecione seu SQL Server VM na lista. Se sua VM de SQL Server não estiver listada aqui, provavelmente ela não foi registrada com o provedor de recursos de VM do SQL. 
1. Exiba o valor em **status**. Se o **status** for **bem-sucedido**, a VM de SQL Server foi registrada com êxito no provedor de recursos de VM do SQL. 

![Verificar o status com o registro do SQL RP](media/virtual-machines-windows-sql-register-with-rp/verify-registration-status.png)

### <a name="command-line"></a>Linha de comandos

Verifique o status atual de registro da VM SQL Server usando o AZ CLI ou o PowerShell. `ProvisioningState` mostrará `Succeeded` se o registro tiver sido bem-sucedido. 

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)


  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
 ```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

  ```powershell-interactive
  Get-AzResource -ResourceName <vm_name> -ResourceGroupName <resource_group> `
  -ResourceType Microsoft.SqlVirtualMachine/sqlVirtualMachines
  ```

---

Um erro indica que a VM de SQL Server não foi registrada com o provedor de recursos. 


## <a name="unregister-vm-from-rp"></a>Cancelar o registro da VM do RP

Para cancelar o registro da VM SQL Server com o provedor de recursos de VM do SQL, exclua o *recurso* de máquina virtual do SQL usando o portal do Azure ou CLI do Azure. Excluir o *recurso* de máquina virtual do SQL não exclui a VM SQL Server. No entanto, tome cuidado e siga as etapas com cuidado, pois é possível excluir inadvertidamente a máquina virtual ao tentar remover o *recurso*. 

O cancelamento do registro da VM do SQL com o provedor de recursos da VM do SQL é necessário para fazer o downgrade completo do modo de gerenciamento. 

### <a name="azure-portal"></a>Portal do Azure

Para cancelar o registro da VM SQL Server com o provedor de recursos usando o portal do Azure, siga estas etapas:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
1. Navegue até o recurso SQL Server VM. 
  
   ![Recurso de máquinas virtuais do SQL](media/virtual-machines-windows-sql-manage-portal/sql-vm-manage.png)

1. Selecione **Eliminar**. 

   ![Excluir provedor de recursos da VM do SQL](media/virtual-machines-windows-sql-register-with-rp/delete-sql-vm-resource-provider.png)

1. Digite o nome da máquina virtual do SQL e **desmarque a caixa de seleção ao lado da máquina virtual**.

   ![Excluir provedor de recursos da VM do SQL](media/virtual-machines-windows-sql-register-with-rp/confirm-delete-of-resource-uncheck-box.png)

   >[!WARNING]
   > Falha ao desmarcar a caixa de seleção ao lado do nome da máquina virtual *excluirá* totalmente a máquina virtual. Desmarque a caixa de seleção para cancelar o registro da VM SQL Server do provedor de recursos, mas *não exclua a máquina virtual real*. 

1. Selecione **excluir** para confirmar a exclusão do *recurso*de máquina virtual do SQL e não o SQL Server máquina virtual. 

### <a name="command-line"></a>Linha de comandos

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)
Para cancelar o registro de sua máquina virtual SQL Server do provedor de recursos com CLI do Azure, use o comando [AZ SQL VM Delete](/cli/azure/sql/vm?view=azure-cli-latest#az-sql-vm-delete) . Isso removerá o SQL Server *recurso* de máquina virtual, mas não excluirá a máquina virtual. 


```azurecli-interactive
az sql vm delete 
  --name <SQL VM resource name> |
  --resource-group <Resource group name> |
  --yes 
```

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Para cancelar o registro de sua máquina virtual SQL Server do provedor de recursos com CLI do Azure, use o comando [New-AzSqlVM](/powershell/module/az.sqlvirtualmachine/new-azsqlvm). Isso removerá o SQL Server *recurso* de máquina virtual, mas não excluirá a máquina virtual. 

```powershell-interactive
Remove-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name>
```

---

## <a name="limitations"></a>Limitações

O provedor de recursos de VM do SQL só dá suporte a:
- SQL Server VMs implantadas por meio do Azure Resource Manager. Não há suporte para VMs SQL Server implantadas por meio do modelo clássico. 
- SQL Server VMs implantadas na nuvem pública. Não há suporte para implantações na nuvem privada ou governamental. 


## <a name="frequently-asked-questions"></a>Perguntas mais frequentes 

**Devo registrar minha VM SQL Server provisionada de uma imagem de SQL Server no Azure Marketplace?**

Não. A Microsoft registra automaticamente as VMs provisionadas a partir das imagens de SQL Server no Azure Marketplace. O registro com o provedor de recursos de VM do SQL será necessário somente se a VM *não* tiver sido provisionada a partir das imagens SQL Server no Azure Marketplace e SQL Server tiver sido autoinstalado.

**O fornecedor de recursos de VM do SQL está disponível para todos os clientes?** 

Sim. Os clientes devem registrar suas VMs SQL Server com o provedor de recursos de VM do SQL se não usavam uma imagem de SQL Server do Azure Marketplace e, em vez disso, o SQL Server autoinstalado, ou se eles trouxeram seu VHD personalizado. As VMs de propriedade de todos os tipos de assinaturas (direta, Enterprise Agreement e provedor de soluções na nuvem) podem se registrar no provedor de recursos da VM do SQL.

**Devo registrar com o provedor de recursos de VM do SQL se minha VM de SQL Server já tiver a extensão de IaaS SQL Server instalada?**

Se sua VM SQL Server for autoinstalada e não for provisionada a partir das imagens SQL Server no Azure Marketplace, você deverá se registrar no provedor de recursos da VM do SQL mesmo se tiver instalado a extensão SQL Server IaaS. O registro com o provedor de recursos de VM do SQL cria um novo recurso do tipo Microsoft. SqlVirtualMachines. Instalar a extensão de IaaS SQL Server não cria esse recurso.

**Qual é o modo de gerenciamento padrão ao registrar-se com o provedor de recursos de VM do SQL?**

O modo de gerenciamento padrão quando você se registra no provedor de recursos de VM do SQL está *cheio*. Se a propriedade de gerenciamento de SQL Server não estiver definida quando você se registrar no provedor de recursos de VM do SQL, o modo será definido como capacidade de gerenciamento total e o serviço de SQL Server será reiniciado. É recomendável registrar com o provedor de recursos de VM do SQL no modo leve primeiro e, em seguida, atualizar para completo durante uma janela de manutenção. 

**Quais são os pré-requisitos para se registrar com o provedor de recursos de VM do SQL?**

Não há nenhum pré-requisito para registrar com o provedor de recursos de VM do SQL no modo leve ou no modo sem agente. O pré-requisito para se registrar com o provedor de recursos de VM do SQL no modo completo é ter a extensão SQL Server IaaS instalada na VM, como caso contrário, o serviço de SQL Server será reiniciado. 

**Posso registrar com o provedor de recursos de VM do SQL se eu não tiver a extensão de IaaS SQL Server instalada na VM?**

Sim, você pode registrar com o provedor de recursos de VM do SQL no modo de gerenciamento leve se não tiver a extensão de SQL Server IaaS instalada na VM. No modo leve, o provedor de recursos da VM do SQL usará um aplicativo de console para verificar a versão e a edição da instância de SQL Server. 

O modo de gerenciamento SQL padrão ao registrar com o provedor de recursos de VM do SQL está _cheio_. Se a propriedade de gerenciamento do SQL não for definida durante o registro com o provedor de recursos de VM do SQL, o modo será definido como capacidade de gerenciamento completa. É recomendável registrar com o provedor de recursos de VM do SQL no modo leve primeiro e, em seguida, atualizar para completo durante uma janela de manutenção. 

**O registro com o provedor de recursos da VM do SQL instalará um agente em minha VM?**

Não. O registro com o provedor de recursos da VM do SQL criará apenas um novo recurso de metadados. Ele não instalará um agente na VM.

A extensão de IaaS SQL Server é necessária apenas para habilitar a capacidade de gerenciamento total. A atualização do modo de gerenciamento de Lightweight para Full instalará a extensão SQL Server IaaS e será reiniciada SQL Server.

**O registro com o SQL Server provedor de recursos de VM reinicia SQL Server em minha VM?**

Depende do modo especificado durante o registro. Se o modo lightweight ou noagent for especificado, o serviço de SQL Server não será reiniciado. No entanto, especificar o modo de gerenciamento como cheio ou deixar o modo de gerenciamento em branco instalará a extensão IaaS do SQL no modo de gerenciamento completo, o que fará com que o serviço de SQL Server seja reiniciado. 

**Qual é a diferença entre os modos de gerenciamento Lightweight e no-Agent ao se registrar com o provedor de recursos de VM do SQL?** 

O modo de gerenciamento não agente está disponível somente para SQL Server 2008 e SQL Server 2008 R2 no Windows Server 2008. É o único modo de gerenciamento disponível para essas versões. Para todas as outras versões do SQL Server, os dois modos de gerenciamento disponíveis são leves e completos. 

O modo no-Agent requer SQL Server Propriedades de versão e edição a serem definidas pelo cliente. O modo leve consulta a VM para localizar a versão e a edição da instância de SQL Server.

**Posso registrar com o provedor de recursos de VM do SQL sem especificar o tipo de licença de SQL Server?**

Não. O tipo de licença SQL Server não é uma propriedade opcional quando você está registrando com o provedor de recursos de VM do SQL. Você precisa definir o tipo de licença de SQL Server como pré-pago ou Benefício Híbrido do Azure ao registrar com o provedor de recursos de VM do SQL em todos os modos de gerenciamento (sem agente, leve e completo).

**Posso atualizar a extensão de IaaS SQL Server do modo sem agente para o modo completo?**

Não. A atualização do modo de gerenciamento para completo ou leve não está disponível para o modo sem agente. Essa é uma limitação técnica do Windows Server 2008. Você precisará atualizar o sistema operacional primeiro para o Windows Server 2008 R2 ou superior e, em seguida, poderá atualizar para o modo de gerenciamento completo. 

**Posso atualizar a extensão de IaaS SQL Server do modo leve para o modo completo?**

Sim. A atualização do modo de gerenciamento de Lightweight para Full tem suporte por meio do PowerShell ou do portal do Azure. Ele requer a reinicialização do serviço SQL Server.

**Posso fazer downgrade da extensão SQL Server IaaS do modo completo para o modo de gerenciamento sem agente ou leve?**

Não. Não há suporte para o downgrade do modo de gerenciamento de extensão IaaS SQL Server. Não é possível fazer downgrade do modo de gerenciamento completo para o modo leve ou sem agente, e ele não pode ser desatualizado do modo leve para o modo sem agente. 

Para alterar o modo de gerenciamento da capacidade de gerenciamento total, cancele o [registro](#unregister-vm-from-rp) da máquina virtual SQL Server do provedor de recursos SQL Server descartando o *recurso* SQL Server e registre novamente a VM SQL Server com o provedor de recursos da VM do SQL novamente em um modo de gerenciamento diferente.

**Posso registrar com o provedor de recursos de VM do SQL na portal do Azure?**

Não. O registro com o provedor de recursos de VM do SQL não está disponível no portal do Azure. O registro com o provedor de recursos da VM do SQL só tem suporte com o CLI do Azure ou o PowerShell. 

**Posso registrar uma VM com o provedor de recursos da VM do SQL antes que o SQL Server seja instalado?**

Não. Uma VM deve ter pelo menos uma instância de SQL Server para ser registrada com êxito com o provedor de recursos de VM do SQL. Se não houver nenhuma instância de SQL Server na VM, o novo recurso Microsoft. SqlVirtualMachine estará em um estado de falha.

**Posso registrar uma VM com o provedor de recursos de VM do SQL se houver várias instâncias de SQL Server?**

Sim. O provedor de recursos da VM do SQL registrará apenas uma instância do SQL Server. O provedor de recursos da VM do SQL registrará a instância de SQL Server padrão no caso de várias instâncias. Se não houver nenhuma instância padrão, haverá suporte apenas para o registro no modo leve. Para atualizar do modo de gerenciamento leve para o completo, a instância de SQL Server padrão deve existir ou a VM deve ter apenas uma instância nomeada SQL Server.

**Posso registrar uma instância de cluster de failover SQL Server com o provedor de recursos de VM do SQL?**

Sim. SQL Server instâncias de cluster de failover em uma VM do Azure podem ser registradas com o provedor de recursos de VM do SQL no modo leve. No entanto, SQL Server instâncias de cluster de failover não podem ser atualizadas para o modo de gerenciamento completo.

**Posso registrar minha VM com o provedor de recursos da VM do SQL se um grupo de disponibilidade Always On estiver configurado?**

Sim. Não há restrições para registrar uma instância de SQL Server em uma VM do Azure com o provedor de recursos de VM do SQL se você estiver participando de uma configuração de grupo de disponibilidade Always On.

**Qual é o custo de registro com o provedor de recursos da VM do SQL ou com a atualização para o modo de gerenciamento completo?**
Nenhum. Não há nenhuma taxa associada ao registro com o provedor de recursos da VM do SQL ou com o uso de qualquer um dos três modos de gerenciamento. O gerenciamento de sua VM SQL Server com o provedor de recursos é completamente gratuito. 

**Qual é o impacto no desempenho do uso dos diferentes modos de gerenciamento?**
Não há nenhum impacto ao usar os modos *noagent* e de gerenciamento *leve* . Há um impacto mínimo ao usar o modo de gerenciamento *completo* de dois serviços que são instalados no sistema operacional. Eles podem ser monitorados por meio do Gerenciador de tarefas e vistos no console interno de serviços do Windows. 

Os dois nomes de serviço são:
- `SqlIaaSExtensionQuery` (nome de exibição-`Microsoft SQL Server IaaS Query Service`)
- `SQLIaaSExtension` (nome de exibição-`Microsoft SQL Server IaaS Agent`)


## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, veja os artigos seguintes: 

* [Visão geral de SQL Server em uma VM do Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [Perguntas frequentes para SQL Server em uma VM do Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Diretrizes de preços para SQL Server em uma VM do Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Notas de versão para SQL Server em uma VM do Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)
