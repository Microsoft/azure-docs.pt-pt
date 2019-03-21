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
ms.date: 02/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 66e94ed4f68ed43891ad3e81bd66cdc57799d204
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58117902"
---
# <a name="how-to-change-the-licensing-model-for-a-sql-server-virtual-machine-in-azure"></a>Como alterar o modelo de licenciamento para uma máquina virtual do SQL Server no Azure
Este artigo descreve como alterar o modelo de licenciamento para uma máquina virtual do SQL Server no Azure com o novo fornecedor de recursos de VM do SQL - **Microsoft.SqlVirtualMachine**. Há dois modelos para uma máquina virtual (VM) a alojar o SQL Server - pay as you go, de licenciamento e traga a sua própria licença (BYOL). E agora, com o PowerShell ou a CLI do Azure, pode modificar o modelo de licenciamento sua VM do SQL Server utiliza. 

O **pay as you go** modelo (PAYG) significa que o custo por segundo de execução da VM do Azure inclui o custo da licença do SQL Server.

O **bring-your-own-license** modelo (BYOL) também é conhecido como o [Azure híbrido benefício (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/), e permite que use sua própria licença do SQL Server com uma VM a executar o SQL Server. Para obter mais informações sobre os preços, consulte [guia de preços de VM do SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance).

Alternar entre os dois modelos de licença incorre **sem tempo de inatividade**, não reinicia a VM, adiciona **sem custos adicionais** (na verdade, ativar AHB *reduz* custo) e é **em vigor imediatamente**. 

  >[!NOTE]
  > - Neste momento, a capacidade de converter o modelo de licenciamento só está disponível ao iniciar com uma imagem de VM do SQL Server pay as you go. Se iniciar com uma imagem de traga a sua própria licença a partir do portal, não será capaz de converter essa imagem em pay as you go.
  > - Clientes CSP podem utilizar o benefício AHB, primeiro a implementar uma VM de pay as you go e, em seguida, convertê-las bring-your-own-license. 
  > - Atualmente, esta capacidade só está ativada para instalações de Cloud pública.


## <a name="prerequisites"></a>Pré-requisitos
A utilização do fornecedor de recursos de VM do SQL Server requer a extensão SQL IaaS. Assim, continuar a utilizar o fornecedor de recursos de VM do SQL Server, precisa do seguinte:
- Uma [subscrição do Azure](https://azure.microsoft.com/free/).
- [Do software assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default). 
- R *pay as you go* [VM do SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) com o [extensão SQL IaaS](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension) instalado. 


## <a name="register-sql-resource-provider-with-your-subscription"></a>Registar o fornecedor de recursos do SQL com a sua subscrição 

A capacidade de alternar entre modelos de licenciamento é um recurso fornecido pelo fornecedor de recursos de nova VM do SQL (Microsoft.SqlVirtualMachine). VMs do SQL Server implementada depois de Dezembro de 2018 são automaticamente registrados com o fornecedor de recursos novo. No entanto, as VMs existentes que foram implementadas até essa data tem de ser manualmente registados com o fornecedor de recursos antes de poderem mudar o respetivo modelo de licenciamento. 

  > [!NOTE] 
  > Se remover o seu recurso de VM do SQL Server, irá voltar para a definição de licença hard-coded da imagem. 

Para registar a sua VM do SQL Server com o fornecedor de recursos do SQL, tem de registar o fornecedor de recursos à sua subscrição. Pode fazê-lo com a CLI do Azure, PowerShell, ou com o portal do Azure. 

### <a name="with-the-azure-portal"></a>Com o portal do Azure
Os passos seguintes irão registar o fornecedor de recursos do SQL com a sua subscrição do Azure no portal do Azure. 

1. Abra o portal do Azure e navegue para **todos os serviços**. 
1. Navegue para **subscrições** e selecione a subscrição de interesse.  
1. Na **subscrições** painel, navegue até à **fornecedores de recursos**. 
1. Tipo de `sql` no filtro para apresentar os fornecedores de recursos relacionados com o SQL. 
1. Selecione *registar*, *voltar a registar*, ou *Unregister* para o **Microsoft.SqlVirtualMachine** fornecedor consoante sua ação desejada. 

   ![Modificar o fornecedor](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)

### <a name="with-azure-cli"></a>Com a CLI do Azure
O fragmento de código seguinte irá registar o fornecedor de recursos do SQL com a sua subscrição do Azure. 

```cli
# Register the new SQL resource provider for your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

### <a name="with-powershell"></a>Com o PowerShell
O fragmento de código seguinte irá registar o fornecedor de recursos do SQL com a sua subscrição do Azure. 

```powershell
# Register the new SQL resource provider for your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```


## <a name="register-sql-server-vm-with-sql-resource-provider"></a>Registar a VM do SQL Server com o fornecedor de recursos do SQL
Assim que o fornecedor de recursos do SQL foi registado com a sua subscrição, em seguida, pode registrar sua VM do SQL Server com o fornecedor de recursos. Pode fazer isso usando a CLI do Azure e PowerShell. 

### <a name="with-azure-cli"></a>Com a CLI do Azure

Registe-se a VM do SQL Server com a CLI do Azure com o seguinte fragmento de código: 

```cli
# Register your existing SQL Server VM with the new resource provider
az sql vm create -n <VMName> -g <ResourceGroupName> -l <VMLocation>
```

### <a name="with-powershell"></a>Com o PowerShell

Registe-se a VM do SQL Server com o PowerShell com o seguinte fragmento de código: 
```powershell
# Register your existing SQL Server VM with the new resource provider
# example: $vm=Get-AzureRmVm -ResourceGroupName AHBTest -Name AHBTest
$vm=Get-AzureRmVm -ResourceGroupName <ResourceGroupName> -Name <VMName>
New-AzureRmResource -ResourceName $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location -ResourceType Microsoft.SqlVirtualMachine/sqlVirtualMachines -Properties @{virtualMachineResourceId=$vm.Id}
```


## <a name="change-licensing-model"></a>Alterar o modelo de licenciamento
Assim que a sua VM do SQL Server foi registado com o fornecedor de recursos, pode alterar o modelo de licenciamento usando o portal do Azure, CLI do Azure ou PowerShell. 


  >[!NOTE]
  >  Neste momento, a capacidade de converter o modelo de licenciamento só está disponível ao iniciar com uma imagem de VM do SQL Server pay as you go. Se iniciar com uma imagem de traga a sua própria licença a partir do portal, não será capaz de converter essa imagem em pay as you go. 

### <a name="with-the-azure-portal"></a>Com o portal do Azure
Pode modificar o modelo de licenciamento diretamente no portal. 

1. Navegue para a sua VM do SQL Server dentro de [portal do Azure](https://portal.azure.com). 
1. Selecione **configuração do SQL Server** no **definições** painel. 
1. Selecione **edite** no **licença do SQL Server** painel para modificar a licença. 

![AHB no Portal](media/virtual-machines-windows-sql-ahb/ahb-in-portal.png)

  >[!NOTE]
  > Esta opção não está disponível para imagens de bring-your-own-license. 

### <a name="with-azure-cli"></a>Com a CLI do Azure
Pode utilizar a CLI do Azure para alterar o seu modelo de licenciamento.  

O fragmento de código seguinte muda o seu modelo de licença de pay as you go para BYOL (ou utilizar o benefício híbrido do Azure):
```azurecli
# Switch  your SQL Server VM license from pay-as-you-go to bring-your-own
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type AHUB
```

O fragmento de código seguinte muda o seu modelo BYOL para pay as you go: 
```azurecli
# Switch  your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type PAYG

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type PAYG
```

### <a name="with-powershell"></a>Com o PowerShell 
Pode utilizar o PowerShell para alterar o seu modelo de licenciamento. 

O fragmento de código seguinte muda o seu modelo de licença de pay as you go para BYOL (ou utilizar o benefício híbrido do Azure): 
```PowerShell
# Switch  your SQL Server VM license from pay-as-you-go to bring-your-own
#example: $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest

$SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="AHUB"
<# the following code snippet is only necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new() #>
$SqlVm | Set-AzResource -Force 
```

O fragmento de código seguinte muda o seu modelo BYOL para pay as you go:
```PowerShell
# Switch  your SQL Server VM license from bring-your-own to pay-as-you-go
#example: $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest

$SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="PAYG"
<# the following code snippet is only necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new() #>
$SqlVm | Set-AzResource -Force 
``` 


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

### <a name="the-resource-microsoftsqlvirtualmachinesqlvirtualmachinesresource-group-under-resource-group-resource-group-was-not-found-the-property-sqlserverlicensetype-cannot-be-found-on-this-object-verify-that-the-property-exists-and-can-be-set"></a>O recurso 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/ < resource-group >' no grupo de recursos '< resource-group >' não foi encontrado. Não é possível localizar a propriedade 'sqlServerLicenseType' neste objeto. Certifique-se de que a propriedade existe e que pode ser definida.
Este erro ocorre quando a VM do SQL Server não foi registado com o fornecedor de recursos do SQL. Terá de registar o fornecedor de recursos com o seu [subscrição](#register-sql-resource-provider-with-your-subscription)e, em seguida, registe-se a VM do SQL Server com o SQL [fornecedor de recursos](#register-sql-server-vm-with-sql-resource-provider). 

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações, veja os artigos seguintes: 

* [Descrição geral do SQL Server num VM do Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL Server num FAQ de VM do Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [SQL Server numa VM do Windows preços orientações](virtual-machines-windows-sql-server-pricing-guidance.md)
* [SQL Server num notas de versão de VM do Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)


