---
title: Como alterar o modelo de licenciamento de uma VM do SQL Server no Azure | Documentos da Microsoft
description: Saiba como mudar o licenciamento para uma máquina virtual do SQL no Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/14/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 0fbd15b1d29d877ad0066d01945389665126bbcc
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56649259"
---
# <a name="how-to-change-the-licensing-model-for-a-sql-server-virtual-machine-in-azure"></a>Como alterar o modelo de licenciamento para uma máquina virtual do SQL Server no Azure
Este artigo descreve como alterar o modelo de licenciamento para uma máquina virtual do SQL Server no Azure com o novo fornecedor de recursos de VM do SQL - **Microsoft.SqlVirtualMachine**. Há dois modelos para uma máquina virtual (VM) a alojar o SQL Server - pagamento por utilização, de licenciamento e traga a sua própria licença (BYOL). E agora, com o PowerShell ou a CLI do Azure, pode modificar o modelo de licenciamento sua VM do SQL Server utiliza. 

O **pagamento por utilização** modelo (PAYG) significa que o custo por segundo de execução da VM do Azure inclui o custo da licença do SQL Server.

O **Bring-your-own-license** modelo (BYOL) também é conhecido como o [benefício híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/), e permite que use sua própria licença do SQL Server com uma VM a executar o SQL Server. Para obter mais informações sobre os preços, consulte [guia de preços de VM do SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance).

Alternar entre os dois modelos de licença incorre **sem tempo de inatividade**, não reinicia a VM, adiciona **sem custos adicionais** (na verdade, ativar AHB *reduz* custo) e é **em vigor imediatamente**. 

  >[!NOTE]
  > - A capacidade de converter o modelo de licenciamento atualmente só está disponível ao iniciar com uma imagem de VM do SQL Server pay as you go. Se iniciar com uma imagem de bring-your-own-license a partir do portal, não será capaz de converter essa imagem para pay as you go. 
  > - Clientes CSP podem utilizar o benefício AHB, primeiro a implementar uma VM de pay as you go e, em seguida, convertê-las bring-your-own-license. 
  > - Atualmente, esta capacidade só está ativada para instalações de Cloud pública.


## <a name="prerequisites"></a>Pré-requisitos
A utilização do fornecedor de recursos de VM do SQL Server requer a extensão SQL IaaS. Assim, continuar a utilizar o fornecedor de recursos de VM do SQL Server, precisa do seguinte:
- Uma [subscrição do Azure](https://azure.microsoft.com/free/).
- R [VM do SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) com o [extensão SQL IaaS](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension) instalado. 


## <a name="register-existing-sql-server-vm-with-sql-resource-provider"></a>Registar a VM existente do SQL Server com o fornecedor de recursos do SQL
A capacidade de alternar entre modelos de licenciamento é um recurso fornecido pelo fornecedor de recursos de nova VM do SQL (Microsoft.SqlVirtualMachine). VMs do SQL Server implementada depois de Dezembro de 2018 são automaticamente registrados com o fornecedor de recursos novo. No entanto, as VMs existentes que foram implementadas até essa data tem de ser manualmente registados com o fornecedor de recursos antes de poderem mudar o respetivo modelo de licenciamento. 

  > [!NOTE] 
  > Se remover o seu recurso de VM do SQL Server, irá voltar para a definição de licença hard-coded da imagem. 

### <a name="register-sql-resource-provider-with-your-subscription"></a>Registar o fornecedor de recursos do SQL com a sua subscrição 

Para registar a sua VM do SQL Server com o fornecedor de recursos do SQL, tem de registar o fornecedor de recursos à sua subscrição. Pode fazê-lo com o PowerShell ou com o portal do Azure. 

#### <a name="using-powershell"></a>Com o PowerShell
O fragmento de código seguinte irá registar o fornecedor de recursos do SQL com a sua subscrição do Azure. 

```powershell
# Register the new SQL resource provider for your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```

#### <a name="using-azure-portal"></a>Com o Portal do Azure
Os passos seguintes irão registar o fornecedor de recursos do SQL com a sua subscrição do Azure no portal do Azure. 

1. Abra o portal do Azure e navegue para **todos os serviços**. 
1. Navegue para **subscrições** e selecione a subscrição de interesse.  
1. Na **subscrições** painel, navegue até à **fornecedores de recursos**. 
1. Tipo de `sql` no filtro para apresentar os fornecedores de recursos relacionados com o SQL. 
1. Selecione *registar*, *voltar a registar*, ou *Unregister* para o **Microsoft.SqlVirtualMachine** fornecedor consoante sua ação desejada. 

  ![Modificar o fornecedor](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)

### <a name="register-sql-server-vm-with-sql-resource-provider"></a>Registar a VM do SQL Server com o fornecedor de recursos do SQL
Assim que o fornecedor de recursos do SQL foi registado com a sua subscrição, pode utilizar o PowerShell para registar a sua VM do SQL Server com o fornecedor de recursos do SQL. 


```powershell
# Register your existing SQL Server VM with the new resource provider
# example: $vm=Get-AzureRmVm -ResourceGroupName AHBTest -Name AHBTest
$vm=Get-AzureRmVm -ResourceGroupName <ResourceGroupName> -Name <VMName>
New-AzureRmResource -ResourceName $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location -ResourceType Microsoft.SqlVirtualMachine/sqlVirtualMachines -Properties @{virtualMachineResourceId=$vm.Id}
```


## <a name="use-powershell"></a>Utilizar o PowerShell 
Pode utilizar o PowerShell para alterar o seu modelo de licenciamento.  Certifique-se de que a VM do SQL Server já foi registrada com o novo fornecedor de recursos do SQL antes de mudar o modelo de licenciamento. 

O fragmento de código seguinte muda o seu modelo de licença de pagamento por utilização para BYOL (ou utilizar o benefício híbrido do Azure): 
```PowerShell
#example: $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="AHUB"
<# the following code snippet is only necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new() #>
$SqlVm | Set-AzResource -Force 
``` 

O fragmento de código seguinte muda o seu modelo BYOL para pagamento por utilização:
```PowerShell
#example: $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="PAYG"
<# the following code snippet is only necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new() #>
$SqlVm | Set-AzResource -Force 
```

  >[!NOTE]
  > Para alternar entre licenças, tem de utilizar o novo fornecedor de recursos de VM do SQL Server. Se tentar executar estes comandos antes de registar a sua VM do SQL Server com o novo fornecedor, pode encontrar este erro: `Get-AzResource : The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/AHBTest' under resource group 'AHBTest' was not found. The property 'sqlServerLicenseType' cannot be found on this object. Verify that the property exists and can be set. ` Se vir este erro, registe a sua VM do SQL Server com o fornecedor de recursos novo. 

 

## <a name="use-azure-cli"></a>Utilizar a CLI do Azure
Pode utilizar a CLI do Azure para alterar o seu modelo de licenciamento.  Certifique-se de que a VM do SQL Server já foi registrada com o novo fornecedor de recursos do SQL antes de mudar o modelo de licenciamento. 

O fragmento de código seguinte muda o seu modelo de licença de pagamento por utilização para BYOL (ou utilizar o benefício híbrido do Azure):
```azurecli
az resource update -g <resource_group_name> -n <sql_virtual_machine_name> --resource-type "Microsoft.SqlVirtualMachine/SqlVirtualMachines" --set properties.sqlServerLicenseType=AHUB
# example: az resource update -g AHBTest -n AHBTest --resource-type "Microsoft.SqlVirtualMachine/SqlVirtualMachines" --set properties.sqlServerLicenseType=AHUB
```

O fragmento de código seguinte muda o seu modelo BYOL para pagamento por utilização: 
```azurecli
az resource update -g <resource_group_name> -n <sql_virtual_machine_name> --resource-type "Microsoft.SqlVirtualMachine/SqlVirtualMachines" --set properties.sqlServerLicenseType=PAYG
# example: az resource update -g AHBTest -n AHBTest --resource-type "Microsoft.SqlVirtualMachine/SqlVirtualMachines" --set properties.sqlServerLicenseType=PAYG
```

  >[!NOTE]
  >Para alternar entre licenças, tem de utilizar o novo fornecedor de recursos de VM do SQL Server. Se tentar executar estes comandos antes de registar a sua VM do SQL Server com o novo fornecedor, pode encontrar este erro: `The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/AHBTest' under resource group 'AHBTest' was not found. ` Se vir este erro, [registar a sua VM do SQL Server com o fornecedor de recursos novo](#register-existing-sql-server-vm-with-sql-resource-provider). 

## <a name="view-current-licensing"></a>Vista atual licenciamento 

O fragmento de código seguinte permite-lhe ver o modelo de licenciamento atual para a sua VM do SQL Server. 

```PowerShell
# View current licensing model for your SQL Server VM
#example: $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType
```

## <a name="known-errors"></a>Erros conhecidos

### <a name="sql-iaas-extension-is-not-installed-on-virtual-machine"></a>Extensão de IaaS do SQL não está instalado na máquina Virtual
A extensão SQL IaaS é um pré-requisito necessário para registar a sua VM do SQL Server com o fornecedor de recursos de VM do SQL Server. Se tentar registar a sua VM do SQL Server antes de instalar a extensão SQL IaaS, o que encontrará o erro seguinte:

`Sql IaaS Extension is not installed on Virtual Machine: '{0}'. Please make sure it is installed and in running state and try again later.`

Para resolver este problema, instale a extensão SQL IaaS antes de tentar registar a sua VM do SQL Server. 

  > [!NOTE]
  > Instalar o SQL IaaS extensão irá reiniciar o serviço do SQL Server e apenas deve ser feito durante uma janela de manutenção. Para obter mais informações, consulte [a instalação da extensão de IaaS do SQL](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension#installation). 

### <a name="cannot-validate-argument-on-parameter-sku"></a>Não é possível validar o argumento no parâmetro "Sku"
Pode encontrar este erro quando tentar alterar o seu modelo de licenciamento de VM do SQL Server ao utilizar o Azure PowerShell > 4.0:

`Set-AzResource : Cannot validate argument on parameter 'Sku'. The argument is null or empty. Provide an argument that is not null or empty, and then try the command again.`

Para resolver este erro, uncomment estas linhas no fragmento de código mencionada anteriormente do PowerShell ao alternar o seu modelo de licenciamento: 
```PowerShell
# the following code snippet is necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new()
```

Utilize o seguinte código para verificar a sua versão do Azure PowerShell:

```PowerShell
Get-Module -ListAvailable -Name Azure -Refresh
```

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações, veja os artigos seguintes: 

* [Descrição geral do SQL Server num VM do Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL Server num FAQ de VM do Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [SQL Server numa VM do Windows preços orientações](virtual-machines-windows-sql-server-pricing-guidance.md)
* [SQL Server num notas de versão de VM do Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)


