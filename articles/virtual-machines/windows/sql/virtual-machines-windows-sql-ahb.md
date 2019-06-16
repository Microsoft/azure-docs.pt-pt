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
ms.openlocfilehash: 667a696e96234aca33981946a5b063ab5bfb080b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67075894"
---
# <a name="how-to-change-the-licensing-model-for-a-sql-server-virtual-machine-in-azure"></a>Como alterar o modelo de licenciamento para uma máquina virtual do SQL Server no Azure
Este artigo descreve como alterar o modelo de licenciamento para uma máquina virtual do SQL Server no Azure com o novo fornecedor de recursos de VM do SQL - **Microsoft.SqlVirtualMachine**. Há dois modelos para uma máquina virtual (VM) a alojar o SQL Server - pay as you go, de licenciamento e traga a sua própria licença (BYOL). E agora, usando o portal do Azure, CLI do Azure ou PowerShell pode modificar o modelo de licenciamento sua VM do SQL Server utiliza. 

O **pay as you go** modelo (PAYG) significa que o custo por segundo de execução da VM do Azure inclui o custo da licença do SQL Server.

O **bring-your-own-license** modelo (BYOL) também é conhecido como o [Azure híbrido benefício (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/), e permite que use sua própria licença do SQL Server com uma VM a executar o SQL Server. Para obter mais informações sobre os preços, consulte [guia de preços de VM do SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance).

Alternar entre os dois modelos de licença incorre **sem tempo de inatividade**, não reinicia a VM, adiciona **sem custos adicionais** (na verdade, ativar AHB *reduz* custo) e é **em vigor imediatamente**. 

## <a name="remarks"></a>Observações


 - Os clientes de parceiro de soluções Cloud (CSP) do Azure podem utilizar o benefício híbrido do Azure, primeiro a implementar uma VM de pay as you go e, em seguida, convertê-las bring-your-own-license. 
 - Ao registar uma imagem de VM do SQL Server personalizada com o fornecedor de recursos, especifique o tipo de licença = 'AHUB'. Deixando a licença escreva em branco ou especificar "PAYG" fará com que o registo efetuar a ativação. 
 - Se remover o seu recurso de VM do SQL Server, irá voltar para a definição de licença codificada da imagem. 
 - Adicionar uma VM do SQL Server a um conjunto de disponibilidade necessita de recriar a VM. Como tais, qualquer VMs adicionadas a um disponibilidade conjunto serão enviadas de volta para o tipo de licença de pay as you go padrão e AHB tem de ser ativado novamente. 
 - A capacidade de alterar o modelo de licenciamento é uma funcionalidade do fornecedor de recursos de VM do SQL Server. Implementar uma imagem do marketplace através do portal do Azure automaticamente registra uma VM do SQL Server com o fornecedor de recursos. No entanto, os clientes que Self-estão a instalar o SQL Server serão necessário manualmente [registar a sua VM do SQL Server](#register-sql-server-vm-with-the-sql-vm-resource-provider). 
 

 
## <a name="limitations"></a>Limitações

 - Neste momento, a capacidade de converter o modelo de licenciamento só está disponível ao iniciar com uma imagem de VM do SQL Server pay as you go. Se iniciar com uma imagem de traga a sua própria licença a partir do portal, não será capaz de converter essa imagem em pay as you go.
 - Alterar o modelo de licenciamento só é suportada para máquinas virtuais implementadas com o modelo do Resource Manager. As VMs implementadas com o modelo clássico não são suportadas. 
 - Alterar o modelo de licenciamento só está ativado para instalações de Cloud pública.
 - Alterar o modelo de licenciamento só é suportada em máquinas virtuais que tenham um único NIC (interface de rede). Nas máquinas virtuais que têm mais de uma NIC, deve primeiro de remover um dos NICs (ao utilizar o portal do Azure) antes de tentar o procedimento. Caso contrário, será executado num erro semelhante ao seguinte: `The virtual machine '\<vmname\>' has more than one NIC associated.` Embora possa adicionar a NIC de volta para a VM depois de alterar o modo de licenciamento, operações feitas por meio do painel de configuração do SQL, como aplicação de patches automática e a cópia de segurança, já não serão consideradas suportado.

## <a name="prerequisites"></a>Pré-requisitos

A utilização do fornecedor de recursos de VM do SQL Server requer a extensão SQL IaaS. Assim, continuar a utilizar o fornecedor de recursos de VM do SQL Server, precisa do seguinte:
- Uma [subscrição do Azure](https://azure.microsoft.com/free/).
- [Do software assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default). 
- R *pay as you go* [VM do SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) com o [extensão SQL IaaS](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension) instalado. 

## <a name="with-the-azure-portal"></a>Com o portal do Azure

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Pode modificar o modelo de licenciamento diretamente no portal. 

1. Abra o [portal do Azure](https://portal.azure.com) e inicie a [recursos de máquinas virtuais do SQL](virtual-machines-windows-sql-manage-portal.md#access-sql-virtual-machine-resource) para a sua VM do SQL Server. 
1. Selecione **Configure** sob **definições**. 
1. Selecione o **benefício híbrido do Azure** opção e confirme que tem uma licença do SQL Server com Software Assurance. 
1. Selecione **aplicar** na parte inferior da **configurar** página. 

![AHB no Portal](media/virtual-machines-windows-sql-ahb/ahb-in-portal.png)

  >[!NOTE]
  > Esta opção não está disponível para imagens de bring-your-own-license. 

## <a name="with-azure-cli"></a>Com a CLI do Azure

Pode utilizar a CLI do Azure para alterar o seu modelo de licenciamento.  

O fragmento de código seguinte muda o seu modelo de licença de pay as you go para BYOL (ou utilizar o benefício híbrido do Azure):

```azurecli-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type AHUB
```

O fragmento de código seguinte muda o seu modelo de bring-your-own-license para pay as you go: 

```azurecli-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type PAYG

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type PAYG
```

## <a name="with-powershell"></a>Com o PowerShell
Pode utilizar o PowerShell para alterar o seu modelo de licenciamento.

O fragmento de código seguinte muda o seu modelo de licença de pay as you go para BYOL (ou utilizar o benefício híbrido do Azure):

```powershell-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
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

```powershell-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
#example: $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="PAYG"
<# the following code snippet is only necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new() #>
$SqlVm | Set-AzResource -Force 
```


## <a name="register-sql-server-vm-with-the-sql-vm-resource-provider"></a>Registar a VM do SQL Server com o fornecedor de recursos de VM do SQL Server
Em determinadas situações, terá de registar manualmente a VM do SQL Server com o fornecedor de recursos de VM do SQL Server. Para fazer isso, também poderá ter de registar manualmente o fornecedor de recursos com a sua subscrição. 


### <a name="register-sql-vm-resource-provider-with-subscription"></a>Registar o fornecedor de recursos de VM do SQL Server com a subscrição 

Para registar a sua VM do SQL Server com o fornecedor de recursos do SQL, tem de registar o fornecedor de recursos à sua subscrição. Pode fazê-lo com o portal do Azure ou a CLI do Azure. 

#### <a name="with-the-azure-portal"></a>Com o portal do Azure
Os passos seguintes irão registar o fornecedor de recursos do SQL para a sua subscrição do Azure no portal do Azure. 

1. Abra o portal do Azure e navegue para **todos os serviços**. 
1. Navegue para **subscrições** e selecione a subscrição de interesse.  
1. Na **subscrições** painel, navegue até à **fornecedores de recursos**. 
1. Tipo de `sql` no filtro para apresentar os fornecedores de recursos relacionados com o SQL. 
1. Selecione *registar*, *voltar a registar*, ou *Unregister* para o **Microsoft.SqlVirtualMachine** fornecedor consoante sua ação desejada. 

   ![Modificar o fornecedor](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)

#### <a name="with-azure-cli"></a>Com a CLI do Azure
O fragmento de código seguinte irá registar o fornecedor de recursos de VM do SQL Server para a sua subscrição do Azure. 

```azurecli-interactive
# Register the new SQL resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

#### <a name="with-powershell"></a>Com o PowerShell

O fragmento de código seguinte irá registar o fornecedor de recursos do SQL para a sua subscrição do Azure.

```powershell-interactive
# Register the new SQL resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```

### <a name="register-sql-server-vm-with-sql-resource-provider"></a>Registar a VM do SQL Server com o fornecedor de recursos do SQL
Assim que a sua subscrição ter sido registado o fornecedor de recursos de VM do SQL Server, em seguida, pode registrar sua VM do SQL Server com o fornecedor de recursos com a CLI do Azure. 

#### <a name="with-azure-cli"></a>Com a CLI do Azure

Registe-se a VM do SQL Server com a CLI do Azure com o abaixo trecho de código: 

```azurecli-interactive
# Register your existing SQL Server VM with the new resource provider
az sql vm create -n <VMName> -g <ResourceGroupName> -l <VMLocation> --license-type <AHUB or PAYG>
```

#### <a name="with-powershell"></a>Com o PowerShell
Registe-se a VM do SQL Server com o PowerShell com o seguinte fragmento de código:

```powershell-interactive
# Register your existing SQL Server VM with the new resource provider
# example: $vm=Get-AzVm -ResourceGroupName AHBTest -Name AHBTest
$vm=Get-AzVm -ResourceGroupName <ResourceGroupName> -Name <VMName>
New-AzResource -ResourceName $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location -ResourceType Microsoft.SqlVirtualMachine/sqlVirtualMachines -Properties @{virtualMachineResourceId=$vm.Id}
```



## <a name="known-errors"></a>Erros conhecidos

### <a name="sql-iaas-extension-is-not-installed-on-virtual-machine"></a>Extensão de IaaS do SQL não está instalado na máquina Virtual
A extensão SQL IaaS é um pré-requisito necessário para registar a sua VM do SQL Server com o fornecedor de recursos de VM do SQL Server. Se tentar registar a sua VM do SQL Server antes de instalar a extensão SQL IaaS, o que encontrará o erro seguinte:

`Sql IaaS Extension is not installed on Virtual Machine: '{0}'. Please make sure it is installed and in running state and try again later.`

Para resolver este problema, instale a extensão SQL IaaS antes de tentar registar a sua VM do SQL Server. 

  > [!NOTE]
  > Instalar o SQL IaaS extensão irá reiniciar o serviço do SQL Server e apenas deve ser feito durante uma janela de manutenção. Para obter mais informações, consulte [a instalação da extensão de IaaS do SQL](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension#installation). 


### <a name="the-resource-microsoftsqlvirtualmachinesqlvirtualmachinesresource-group-under-resource-group-resource-group-was-not-found-the-property-sqlserverlicensetype-cannot-be-found-on-this-object-verify-that-the-property-exists-and-can-be-set"></a>O recurso ' Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<grupo de recursos >' no grupo de recursos "\<grupo de recursos >' não foi encontrado. Não é possível localizar a propriedade 'sqlServerLicenseType' neste objeto. Certifique-se de que a propriedade existe e que pode ser definida.
Este erro ocorre durante a tentativa de alterar o modelo de licenciamento numa VM do SQL Server que não foi registado com o fornecedor de recursos do SQL. Terá de registar o fornecedor de recursos para sua [subscrição](#register-sql-vm-resource-provider-with-subscription)e, em seguida, registe-se a VM do SQL Server com o SQL [fornecedor de recursos](#register-sql-server-vm-with-sql-resource-provider). 

### <a name="cannot-validate-argument-on-parameter-sku"></a>Não é possível validar o argumento no parâmetro "Sku"
Pode encontrar este erro quando tentar alterar o seu modelo de licenciamento de VM do SQL Server ao utilizar o Azure PowerShell > 4.0: Set-AzResource: Não é possível validar o argumento no parâmetro "Sku". O argumento é nulo ou estar vazio. Forneça um argumento que não é nulo ou estar vazio e, em seguida, tente novamente o comando.
Para resolver este erro, uncomment estas linhas no fragmento de código mencionada anteriormente do PowerShell ao alternar o seu modelo de licenciamento:

```powershell
# the following code snippet is necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new()
```

Utilize o seguinte código para verificar a sua versão do Azure PowerShell:

```powershell-interactive
Get-Module -ListAvailable -Name Azure -Refresh
```

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações, veja os artigos seguintes: 

* [Descrição geral do SQL Server num VM do Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL Server num FAQ de VM do Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [SQL Server numa VM do Windows preços orientações](virtual-machines-windows-sql-server-pricing-guidance.md)
* [SQL Server num notas de versão de VM do Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)


