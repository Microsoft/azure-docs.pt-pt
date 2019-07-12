---
title: Registar a máquina virtual do SQL Server no Azure com o fornecedor de recursos de VM do SQL Server | Documentos da Microsoft
description: Registre-se a sua VM do SQL Server com o fornecedor de recursos de VM do SQL para melhorar a capacidade de gestão.
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
ms.openlocfilehash: c78b5d71fffbf579b15f747c048bd65259039749
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786740"
---
# <a name="register-sql-server-virtual-machine-in-azure-with-the-sql-vm-resource-provider"></a>Registar a máquina virtual do SQL Server no Azure com o fornecedor de recursos de VM do SQL Server

Este artigo descreve como registar a sua máquina virtual de servidor SQL do Azure (VM) com o fornecedor de recursos de VM do SQL Server. 

Implementar uma imagem do marketplace de VM do SQL Server através do portal do Azure automaticamente registra a VM do SQL Server com o fornecedor de recursos. No entanto, se optar por Self-instalar o SQL Server numa máquina Virtual em vez de selecionar uma imagem do Azure Marketplace, em seguida, deve registrar sua VM do SQL Server com o fornecedor de recursos hoje para:

-  **Conformidade** – pelos termos de produto da Microsoft, os clientes que utilizam o [benefício híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) tem de indicar à Microsoft ao utilizar o benefício híbrido do Azure - e por isso, eles devem ser registrados com o fornecedor de recursos de VM do SQL Server. 

-  **Benefícios da funcionalidade** -registar a sua VM do SQL Server com o fornecedor de recursos desbloqueia [correção automática](virtual-machines-windows-sql-automated-patching.md), [cópia de segurança automática](virtual-machines-windows-sql-automated-backup-v2.md), capacidades de monitorização e capacidade de gestão, bem como [licenciamento](virtual-machines-windows-sql-ahb.md) e [edition](virtual-machines-windows-sql-change-edition.md) flexibilidade. Anteriormente, esses eram apenas disponíveis para imagens de VM do SQL Server no Azure Marketplace.

Instalação automática do SQL Server numa VM do Azure ou aprovisionar uma VM do Azure a partir de um VHD personalizado com o SQL Server está em conformidade, através do benefício híbrido do SQL Server, com a condição que os clientes indicam que utilizam para a Microsoft registrando-se com o recurso de VM do SQL Server fornecedor. 

Para utilizar o fornecedor de recursos de VM do SQL Server, também tem de registar o fornecedor de recursos de VM do SQL Server com a sua subscrição. Isso pode ser feito com o portal do Azure, CLI do Azure e PowerShell. 

## <a name="prerequisites"></a>Pré-requisitos

Para registar a sua VM do SQL Server com o fornecedor de recursos, terá de fazer o seguinte: 

- Uma [subscrição do Azure](https://azure.microsoft.com/free/).
- R [VM do SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision). 
- [CLI do Azure](/cli/azure/install-azure-cli) e [PowerShell](/powershell/azure/new-azureps-module-az). 

## <a name="register-with-sql-vm-resource-provider"></a>Registar com o fornecedor de recursos de VM do SQL Server
Se o [extensão de IaaS do SQL](virtual-machines-windows-sql-server-agent-extension.md) já está instalado na VM, em seguida, registar o fornecedor de recursos de VM do SQL Server é simplesmente a criação de um recurso de metadados do tipo Microsoft.SqlVirtualMachine/SqlVirtualMachines. Segue-se o fragmento de código para registar com o fornecedor de recursos de VM do SQL Server, se a extensão de IaaS do SQL já está instalada na VM. Tem de fornecer o tipo de licença do SQL Server pretendido ao registar com o fornecedor de recursos de VM do SQL Server como um "PAYG ' ou 'AHUB'. 

Registe-se a VM do SQL Server com o PowerShell com o seguinte fragmento de código:

  ```powershell-interactive
     // Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     // Register with SQL VM resource provider
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;sqlLicenseType='AHUB'}  
  
  ```

Se a extensão de IaaS do SQL não está instalada na VM, em seguida, pode registar com o fornecedor de recursos de VM do SQL, especificando o modo de gestão do SQL simples. No modo de gestão do SQL simples, VM do SQL Server será automaticamente o fornecedor de recursos de instalar a extensão do IaaS do SQL no [leve modo](virtual-machines-windows-sql-server-agent-extension.md#install-in-lightweight-mode) e verifique se o SQL Server metadados de instância; esta irá não reiniciar o serviço do SQL Server. Tem de fornecer o tipo de licença do SQL Server pretendido ao registar com o fornecedor de recursos de VM do SQL Server como um "PAYG ' ou 'AHUB'. 

Registe-se a VM do SQL Server no modo de gestão simples do SQL com o PowerShell com o seguinte fragmento de código:

  ```powershell-interactive
     // Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     // Register SQL VM with 'Lightweight' SQL IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;sqlLicenseType='AHUB';sqlManagement='LightWeight'}  
  
  ```
Registar o fornecedor de recursos de VM do SQL Server na [leve modo](virtual-machines-windows-sql-server-agent-extension.md#install-in-lightweight-mode) irá garantir a conformidade e ativar o licenciamento flexível, bem como atualizações de edição do SQL Server no local. Instâncias de Cluster de ativação pós-falha e implementações de várias instâncias podem ser registadas com o fornecedor de recursos de VM do SQL Server apenas no modo simples. Pode seguir as instruções disponíveis no portal do Azure para atualizar para o [modo de inteiro](virtual-machines-windows-sql-server-agent-extension.md#full-mode-installation) e ativar a capacidade de gerenciamento abrangente conjunto de recursos com um reinício do SQL Server em qualquer altura. 

## <a name="register-sql-server-2008r2-on-windows-server-2008-vms"></a>Registar o SQL Server 2008/R2 em VMs do Windows Server 2008

SQL Server 2008 e 2008 R2 instalado no Windows Server 2008 pode ser registado com a VM de SQL do fornecedor de recursos no [NoAgent](virtual-machines-windows-sql-server-agent-extension.md) modo. Esta opção assegura a conformidade e permite que a VM do SQL Server ser monitorizada no portal do Azure com funcionalidades limitadas.

A tabela seguinte detalha os valores aceitáveis para os parâmetros fornecidos durante o registo:

| Parâmetro | Valores aceitáveis                                 |
| :------------------| :--------------------------------------- |
| **sqlLicenseType** | `'AHUB'`, ou `'PAYG'`                    |
| **sqlImageOffer**  | `'SQL2008-WS2008'` ou `'SQL2008R2-WS2008`|
| &nbsp;             | &nbsp;                                   |


Para registar o seu SQL Server 2008 ou 2008 R2 na instância do Windows Server 2008, utilize o seguinte fragmento de código do Powershell:  

  ```powershell-interactive
     // Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
    New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
      -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
      -Properties @{virtualMachineResourceId=$vm.Id;sqlLicenseType='AHUB'; `
       sqlManagement='NoAgent';sqlImageSku='Standard';sqlImageOffer='SQL2008R2-WS2008'}
  ```



## <a name="verify-registration-status"></a>Verificar o estado de registo
Pode verificar se o SQL Server já foi registado com o fornecedor de recursos de VM do SQL Server com o portal do Azure, a CLI do Azure ou o PowerShell. 

# <a name="azure-portaltabazure-portal"></a>[Portal do Azure](#tab/azure-portal)
Para verificar o estado de registo no portal do Azure, faça o seguinte.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com). 
1. Navegue até à sua [máquinas virtuais do SQL](virtual-machines-windows-sql-manage-portal.md).
1. Selecione a VM do SQL Server na lista. Se a sua VM do SQL Server não está listado aqui, é provável que a VM do SQL Server não foi registrada com o fornecedor de recursos de VM do SQL Server. 
1. Ver o valor em `Status`. Se `Status = Succeeded`, em seguida, a VM do SQL Server foi registado com o fornecedor de recursos de VM do SQL Server com êxito. 

    ![Verificar o estado em registo RP de SQL](media/virtual-machines-windows-sql-register-with-rp/verify-registration-status.png)

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

Verifique se o estado atual do registo de VM do SQL Server com o seguinte comando AZ CLI. `ProvisioningState` mostrará `Succeeded` se o registo foi concluída com êxito. 

  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
  ```


# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

Verifique se o estado atual do registo de VM do SQL Server com o seguinte cmdlet do PowerShell. `ProvisioningState` mostrará `Succeeded` se o registo foi concluída com êxito. 

  ```powershell-interactive
  Get-AzResource -ResourceName <vm_name> -ResourceGroupName <resource_group> -ResourceType Microsoft.SqlVirtualMachine/sqlVirtualMachines
  ```
Um erro indica que a VM do SQL Server não foi registado com o fornecedor de recursos. 

---

## <a name="register-sql-vm-resource-provider-with-subscription"></a>Registar o fornecedor de recursos de VM do SQL Server com a subscrição 

Para registar a sua VM do SQL Server com o fornecedor de recursos de VM do SQL Server, tem de registar o fornecedor de recursos à sua subscrição. Pode fazê-lo com o portal do Azure ou a CLI do Azure.

# <a name="azure-portaltabazure-portal"></a>[Portal do Azure](#tab/azure-portal)


Os passos seguintes irão registar o fornecedor de recursos de VM do SQL Server para a sua subscrição do Azure no portal do Azure. 

1. Abra o portal do Azure e navegue para **todos os serviços**. 
1. Navegue para **subscrições** e selecione a subscrição de interesse.  
1. Sobre o **subscrições** página, navegue até **fornecedores de recursos**. 
1. Tipo de `sql` no filtro para apresentar os fornecedores de recursos relacionados com o SQL. 
1. Selecione *registar*, *voltar a registar*, ou *Unregister* para o **Microsoft.SqlVirtualMachine** fornecedor consoante sua ação desejada. 

   ![Modificar o fornecedor](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

O fragmento de código seguinte irá registar o fornecedor de recursos de VM do SQL Server para a sua subscrição do Azure. 

```azurecli-interactive
# Register the new SQL VM resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

O seguinte trecho de código do PowerShell irá registar o fornecedor de recursos de VM do SQL Server para a sua subscrição do Azure.

```powershell-interactive
# Register the new SQL VM resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```
---

## <a name="remarks"></a>Observações

 - O fornecedor de recursos de VM do SQL Server só suporta VMs do SQL Server implantados usando o "Gestor de recursos". VMs do SQL Server implementado com o que modelo clássico não é suportado. 
 - O fornecedor de recursos de VM do SQL Server só suporta VMs do SQL Server implementado na cloud pública. Implementações na privada ou na cloud de governo, não são suportadas. 

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações, veja os artigos seguintes: 

* [Descrição geral do SQL Server num VM do Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL Server num FAQ de VM do Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [SQL Server numa VM do Windows preços orientações](virtual-machines-windows-sql-server-pricing-guidance.md)
* [SQL Server num notas de versão de VM do Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)


