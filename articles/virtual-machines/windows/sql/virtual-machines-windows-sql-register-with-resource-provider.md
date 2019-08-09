---
title: Registrar uma máquina virtual SQL Server no Azure com o provedor de recursos de VM do SQL | Microsoft Docs
description: Registre sua VM do SQL Server com o provedor de recursos da VM do SQL para melhorar a capacidade de gerenciamento.
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
ms.date: 06/24/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 552caf0f09dcfa291981ef73152cf4febfc4a840
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68882382"
---
# <a name="register-a-sql-server-virtual-machine-in-azure-with-the-sql-vm-resource-provider"></a>Registrar uma máquina virtual SQL Server no Azure com o provedor de recursos de VM do SQL

Este artigo descreve como registrar seu SQL Server VM (máquina virtual) no Azure com o provedor de recursos de VM do SQL. 

A implantação de uma imagem SQL Server VM do Azure Marketplace por meio do portal do Azure registra automaticamente a VM SQL Server com o provedor de recursos. Se você optar por instalar automaticamente SQL Server em uma máquina virtual do Azure em vez de escolher uma imagem do Azure Marketplace ou se provisionar uma VM do Azure de um VHD personalizado com SQL Server, deverá registrar sua VM do SQL Server com o provedor de recursos para :

- **Conformidade**: De acordo com os termos de produto da Microsoft, os clientes devem informar à Microsoft quando estiverem usando o [benefício híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/). Para fazer isso, eles devem se registrar com o provedor de recursos de VM do SQL. 

- **Benefícios do recurso**: Registrar seu SQL Server VM com o provedor de recursos desbloqueia a [aplicação de patch automatizada](virtual-machines-windows-sql-automated-patching.md), o [backup automatizado](virtual-machines-windows-sql-automated-backup-v2.md)e os recursos de monitoramento e capacidade de gerenciamento. Ele também desbloqueia a [](virtual-machines-windows-sql-ahb.md) flexibilidade de licenciamento e de [edição](virtual-machines-windows-sql-change-edition.md) . Anteriormente, esses recursos estavam disponíveis apenas para SQL Server imagens de VM do Azure Marketplace.

Para utilizar o provedor de recursos da VM do SQL, você também deve registrar o provedor de recursos da VM do SQL com sua assinatura. Você pode fazer isso usando o portal do Azure, o CLI do Azure ou o PowerShell. 

## <a name="prerequisites"></a>Pré-requisitos

Para registrar sua VM SQL Server com o provedor de recursos, você precisará do seguinte: 

- Uma [subscrição do Azure](https://azure.microsoft.com/free/).
- Uma [VM SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision). 
- O [CLI do Azure](/cli/azure/install-azure-cli) e o [PowerShell](/powershell/azure/new-azureps-module-az). 


## <a name="register-with-sql-vm-resource-provider"></a>Registrar com provedor de recursos de VM do SQL
Se a [extensão do agente IaaS SQL Server](virtual-machines-windows-sql-server-agent-extension.md) não estiver instalada na VM, você poderá se registrar com o provedor de recursos da VM do SQL especificando o modo de gerenciamento leve do SQL. No modo de gerenciamento leve do SQL, o provedor de recursos da VM do SQL instalará automaticamente a extensão de IaaS do SQL no [modo leve](virtual-machines-windows-sql-server-agent-extension.md#install-in-lightweight-mode) e verificará SQL Server metadados da instância; Isso não reiniciará SQL Server serviço. Você precisa fornecer o tipo de licença de SQL Server desejado ao registrar com o provedor de recursos de VM do SQL como ' PAYG ' ou ' AHUB '.

O registro com o provedor de recursos de VM do SQL no [modo leve](virtual-machines-windows-sql-server-agent-extension.md#install-in-lightweight-mode) garantirá a conformidade e habilitará o licenciamento flexível, bem como as atualizações de edição in-loco SQL Server. As instâncias de cluster de failover e as implantações de várias instâncias podem ser registradas com o provedor de recursos de VM do SQL somente no modo leve. Você pode seguir as instruções encontradas no portal do Azure para atualizar para o [modo completo](virtual-machines-windows-sql-server-agent-extension.md#install-in-full-mode) e habilitar o conjunto de recursos de gerenciamento abrangente com um SQL Server reiniciar a qualquer momento. 


# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
Use o trecho de código a seguir para se registrar com o provedor de recursos da VM do SQL se a extensão de IaaS SQL Server já estiver instalada na VM. Você precisa fornecer o tipo de licença de SQL Server que deseja ao registrar com o provedor de recursos de VM do SQL: pago conforme o uso (`PAYG`) ou benefício híbrido do Azure (`AHUB`). 

Registre a VM SQL Server usando o seguinte trecho de código do PowerShell:

  ```powershell-interactive
     # Get the existing compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     # Register SQL VM with 'Lightweight' SQL IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='AHUB';sqlManagement='LightWeight'}  
  
  ```

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

Para as edições pagas (Enterprise ou Standard):

  ```azurecli-interactive
  # Register Enterprise or Standard self-installed VM in Lightweight mode

  az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type AHUB 

  ```

Para edições gratuitas (Developer, Web ou Express):

  ```azurecli-interactive
  # Register Developer, Web, or Express self-installed VM in Lightweight mode

  az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type PAYG 
  ```
---

Se a extensão de IaaS do SQL já estiver instalada na VM, o registro com o provedor de recursos de VM do SQL estará simplesmente criando um recurso de metadados do tipo Microsoft. SqlVirtualMachine/SqlVirtualMachines. Abaixo está o trecho de código para se registrar no provedor de recursos da VM do SQL se a extensão IaaS do SQL já estiver instalada na VM. Você precisa fornecer o tipo de licença de SQL Server desejado ao registrar com o provedor de recursos de VM do SQL como ' PAYG ' ou ' AHUB '.

  ```powershell-interactive
  # Get the existing  Compute VM
   $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
   # Register with SQL VM resource provider
   New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
      -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
      -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='AHUB'}
  ```


## <a name="register-sql-server-2008-or-2008-r2-on-windows-server-2008-vms"></a>Registrar SQL Server 2008 ou 2008 R2 em VMs do Windows Server 2008

SQL Server 2008 e 2008 R2 instalados no Windows Server 2008 podem ser registrados com o provedor de recursos de VM do SQL no modo [sem agente](virtual-machines-windows-sql-server-agent-extension.md) . Essa opção garante a conformidade e permite que a VM SQL Server seja monitorada no portal do Azure com funcionalidade limitada.

A tabela a seguir detalha os valores aceitáveis para os parâmetros fornecidos durante o registro:

| Parâmetro | Valores aceitáveis                                 |
| :------------------| :--------------------------------------- |
| **sqlLicenseType** | `AHUB` ou `PAYG`                    |
| **sqlImageOffer**  | `SQL2008-WS2008` ou `SQL2008R2-WS2008`|
| &nbsp;             | &nbsp;                                   |


Para registrar sua instância do SQL Server 2008 ou 2008 R2 na instância do Windows Server 2008, use o seguinte trecho de código do PowerShell ou AZ CLI:  

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
  ```powershell-interactive
     # Get the existing compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
    New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
      -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
      -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='AHUB'; `
       sqlManagement='NoAgent';sqlImageSku='Standard';sqlImageOffer='SQL2008R2-WS2008'}
  ```

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type AHUB --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer SQL2008-WS2008R2
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

Verifique o status atual de registro da VM SQL Server usando o AZ CLI ou o PowerShell. `ProvisioningState`mostrará `Succeeded` se o registro foi bem-sucedido. 

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

## <a name="register-the-sql-vm-resource-provider-with-a-subscription"></a>Registrar o provedor de recursos de VM do SQL com uma assinatura 

Para registrar sua VM SQL Server com o provedor de recursos de VM do SQL, você deve registrar o provedor de recursos com sua assinatura. Você pode fazer isso usando o portal do Azure, o CLI do Azure ou o PowerShell.

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
O trecho de código a seguir registrará o provedor de recursos da VM do SQL em sua assinatura do Azure. 

```azurecli-interactive
# Register the new SQL VM resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

```powershell-interactive
# Register the new SQL VM resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```
---

## <a name="remarks"></a>Observações

- O provedor de recursos de VM do SQL dá suporte apenas a VMs SQL Server implantadas por meio de Azure Resource Manager Não há suporte para VMs SQL Server implantadas por meio do modelo clássico. 
- O provedor de recursos da VM do SQL dá suporte apenas a VMs SQL Server implantadas na nuvem pública. Não há suporte para implantações na nuvem privada ou governamental. 
 

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes 

**Devo registrar minha VM SQL Server provisionada de uma imagem de SQL Server no Azure Marketplace?**

Não. A Microsoft registra automaticamente as VMs provisionadas a partir das imagens de SQL Server no Azure Marketplace. O registro com o provedor de recursos de VM do SQL será necessário somente se a VM *não* tiver sido provisionada a partir das imagens SQL Server no Azure Marketplace e SQL Server tiver sido autoinstalado.

**O fornecedor de recursos de VM do SQL está disponível para todos os clientes?** 

Sim. Os clientes devem registrar suas VMs SQL Server com o provedor de recursos de VM do SQL se não usavam uma imagem de SQL Server do Azure Marketplace e, em vez disso, o SQL Server autoinstalado, ou se eles trouxeram seu VHD personalizado. As VMs de propriedade de todos os tipos de assinaturas (direta, Enterprise Agreement e provedor de soluções na nuvem) podem se registrar no provedor de recursos da VM do SQL.

**Devo registrar com o provedor de recursos de VM do SQL se minha VM de SQL Server já tiver a extensão de IaaS SQL Server instalada?**

Se sua VM SQL Server for autoinstalada e não for provisionada a partir das imagens SQL Server no Azure Marketplace, você deverá se registrar no provedor de recursos da VM do SQL mesmo se tiver instalado a extensão SQL Server IaaS. O registro com o provedor de recursos de VM do SQL cria um novo recurso do tipo Microsoft. SqlVirtualMachines. Instalar a extensão de IaaS SQL Server não cria esse recurso.

**Qual é o modo de gerenciamento padrão ao registrar-se com o provedor de recursos de VM do SQL?**

O modo de gerenciamento padrão quando você se registra no provedor de recursos de VM do SQL está *cheio*. Se a propriedade de gerenciamento de SQL Server não estiver definida quando você se registrar no provedor de recursos de VM do SQL, o modo será definido como capacidade de gerenciamento completa. Ter o SQL Server extensão de IaaS instalado na VM é o pré-requisito para se registrar com o provedor de recursos de VM do SQL no modo de gerenciamento completo.

**Quais são os pré-requisitos para se registrar com o provedor de recursos de VM do SQL?**

Não há nenhum pré-requisito para registrar com o provedor de recursos de VM do SQL no modo leve ou no modo sem agente. O pré-requisito para registrar com o provedor de recursos de VM do SQL no modo completo é ter a extensão SQL Server IaaS instalada na VM.

**Posso registrar com o provedor de recursos de VM do SQL se eu não tiver a extensão de IaaS SQL Server instalada na VM?**

Sim, você pode registrar com o provedor de recursos de VM do SQL no modo de gerenciamento leve se não tiver a extensão de SQL Server IaaS instalada na VM. No modo leve, o provedor de recursos da VM do SQL usará um aplicativo de console para verificar a versão e a edição da instância de SQL Server. 

O modo de gerenciamento SQL padrão ao registrar com o provedor de recursos de VM do SQL está _cheio_. Se a propriedade de gerenciamento do SQL não for definida durante o registro com o provedor de recursos de VM do SQL, o modo será definido como capacidade de gerenciamento completa. Ter a extensão de IaaS do SQL instalada na VM é o pré-requisito para registrar-se com o provedor de recursos de VM do SQL no modo de gerenciamento completo.

**O registro com o provedor de recursos da VM do SQL instalará um agente em minha VM?**

Não. O registro com o provedor de recursos da VM do SQL criará apenas um novo recurso de metadados. Ele não instalará um agente na VM.

A extensão de IaaS SQL Server é necessária apenas para habilitar a capacidade de gerenciamento total. A atualização do modo de gerenciamento de Lightweight para Full instalará a extensão SQL Server IaaS e será reiniciada SQL Server.

**O registro com o SQL Server provedor de recursos de VM reinicia SQL Server em minha VM?**

Não. O registro com o provedor de recursos da VM do SQL criará apenas um novo recurso de metadados. Ele não reiniciará SQL Server na VM. 

A reinicialização de SQL Server é necessária apenas para instalar a extensão de IaaS SQL Server. E a extensão SQL Server IaaS é necessária para habilitar a capacidade de gerenciamento total. A atualização do modo de gerenciamento de Lightweight para Full instalará a extensão SQL Server IaaS e será reiniciada SQL Server.

**Qual é a diferença entre os modos de gerenciamento Lightweight e no-Agent ao se registrar com o provedor de recursos de VM do SQL?** 

O modo de gerenciamento não agente está disponível somente para SQL Server 2008 e SQL Server 2008 R2 no Windows Server 2008. É o único modo de gerenciamento disponível para essas versões. Para todas as outras versões do SQL Server, os dois modos de gerenciamento disponíveis são leves e completos. 

O modo no-Agent requer SQL Server Propriedades de versão e edição a serem definidas pelo cliente. O modo leve consulta a VM para localizar a versão e a edição da instância de SQL Server.

**Posso registrar com o provedor de recursos de VM do SQL no modo leve ou sem agente usando o CLI do Azure?**

Não. A propriedade modo de gerenciamento está disponível somente quando você está registrando com o provedor de recursos de VM do SQL usando Azure PowerShell. O CLI do Azure não dá suporte à definição da propriedade de capacidade de gerenciamento de SQL Server. Ele sempre registra com o provedor de recursos de VM do SQL no modo padrão, capacidade de gerenciamento total.

**Posso registrar com o provedor de recursos de VM do SQL sem especificar o tipo de licença de SQL Server?**

Não. O tipo de licença SQL Server não é uma propriedade opcional quando você está registrando com o provedor de recursos de VM do SQL. Você precisa definir o tipo de licença de SQL Server como pré-pago ou Benefício Híbrido do Azure ao registrar com o provedor de recursos de VM do SQL em todos os modos de gerenciamento (sem agente, leve e completo) usando o CLI do Azure e o PowerShell.

**Posso atualizar a extensão de IaaS SQL Server do modo sem agente para o modo completo?**

Não. A atualização do modo de gerenciamento para completo ou leve não está disponível para o modo sem agente. Essa é uma limitação técnica do Windows Server 2008.

**Posso atualizar a extensão de IaaS SQL Server do modo leve para o modo completo?**

Sim. A atualização do modo de gerenciamento de Lightweight para Full tem suporte por meio do PowerShell ou do portal do Azure. Ele requer a reinicialização de SQL Server.

**Posso fazer downgrade da extensão SQL Server IaaS do modo completo para o modo de gerenciamento sem agente ou leve?**

Não. Não há suporte para o downgrade do modo de gerenciamento de extensão IaaS SQL Server. Não é possível fazer downgrade do modo de gerenciamento completo para o modo leve ou sem agente, e ele não pode ser desatualizado do modo leve para o modo sem agente. 

Para alterar o modo de gerenciamento da capacidade de gerenciamento total, remova a extensão SQL Server IaaS. Em seguida, remova o recurso Microsoft. SqlVirtualMachine e registre novamente o SQL Server VM com o provedor de recursos de VM do SQL.

**Posso registrar com o provedor de recursos de VM do SQL na portal do Azure?**

Não. O registro com o provedor de recursos de VM do SQL não está disponível no portal do Azure. Há suporte para o registro com o provedor de recursos de VM do SQL no modo de gerenciamento completo com o CLI do Azure ou o PowerShell. O registro com o provedor de recursos de VM do SQL no modo de gerenciamento leve ou sem agente tem suporte apenas por APIs Azure PowerShell.

**Posso registrar uma VM com o provedor de recursos da VM do SQL antes que o SQL Server seja instalado?**

Não. Uma VM deve ter pelo menos uma instância de SQL Server para ser registrada com êxito com o provedor de recursos de VM do SQL. Se não houver nenhuma instância de SQL Server na VM, o novo recurso Microsoft. SqlVirtualMachine estará em um estado de falha.

**Posso registrar uma VM com o provedor de recursos de VM do SQL se houver várias instâncias de SQL Server?**

Sim. O provedor de recursos da VM do SQL registrará apenas uma instância do SQL Server. O provedor de recursos da VM do SQL registrará a instância de SQL Server padrão no caso de várias instâncias. Se não houver nenhuma instância padrão, haverá suporte apenas para o registro no modo leve. Para atualizar do modo de gerenciamento leve para o completo, a instância de SQL Server padrão deve existir ou a VM deve ter apenas uma instância nomeada SQL Server.

**Posso registrar uma instância de cluster de failover SQL Server com o provedor de recursos de VM do SQL?**

Sim. SQL Server instâncias de cluster de failover em uma VM do Azure podem ser registradas com o provedor de recursos de VM do SQL no modo leve. No entanto, SQL Server instâncias de cluster de failover não podem ser atualizadas para o modo de gerenciamento completo.

**Posso registrar minha VM com o provedor de recursos da VM do SQL se um grupo de disponibilidade Always On estiver configurado?**

Sim. Não há restrições para registrar uma instância de SQL Server em uma VM do Azure com o provedor de recursos de VM do SQL se você estiver participando de uma configuração de grupo de disponibilidade Always On.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, veja os artigos seguintes: 

* [Visão geral de SQL Server em uma VM do Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [Perguntas frequentes para SQL Server em uma VM do Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Diretrizes de preços para SQL Server em uma VM do Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Notas de versão para SQL Server em uma VM do Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)
