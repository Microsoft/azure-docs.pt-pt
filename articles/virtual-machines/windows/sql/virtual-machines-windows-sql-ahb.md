---
title: Alterar o modelo de licença para uma VM SQL Server no Azure
description: Saiba como alternar o licenciamento para uma máquina virtual SQL Server no Azure a partir do pré-pago para traga sua própria licença usando o Benefício Híbrido do Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/05/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 05cd68c7be005a5b148b7d3e691c46a0d067b0c0
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262858"
---
# <a name="change-the-license-model-for-a-sql-server-virtual-machine-in-azure"></a>Alterar o modelo de licença para uma máquina virtual SQL Server no Azure
Este artigo descreve como alterar o modelo de licença para uma VM (máquina virtual) SQL Server no Azure usando o novo provedor de recursos de VM do SQL, **Microsoft. SqlVirtualMachine**.

Há dois modelos de licença para uma VM que está hospedando SQL Server: pré-pago e Benefício Híbrido do Azure. Você pode modificar o modelo de licença de sua VM SQL Server usando o portal do Azure, o CLI do Azure ou o PowerShell. 

O modelo pago conforme o uso significa que o custo por segundo da execução da VM do Azure inclui o custo da licença de SQL Server.
[Benefício híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) permite que você use sua própria licença de SQL Server com uma VM que está executando o SQL Server. 

Benefício Híbrido do Azure permite o uso de licenças SQL Server com o Software Assurance ("licença qualificada") em máquinas virtuais do Azure. Com o Benefício Híbrido do Azure, os clientes não são cobrados pelo uso de uma licença SQL Server em uma VM. Mas eles ainda devem pagar pelo custo da computação em nuvem subjacente (ou seja, a taxa base), o armazenamento e os backups. Eles também devem pagar pela e/s associada ao uso dos serviços (conforme aplicável).

De acordo com os termos de produto da Microsoft: "Os clientes devem indicar que estão usando o banco de dados SQL do Azure (Instância Gerenciada, Pool Elástico e Banco de Dados Individual), Azure Data Factory, SQL Server Integration Services ou SQL Server máquinas virtuais em Benefício Híbrido do Azure para SQL Server ao configurar cargas de trabalho no Azure. "

Para indicar o uso de Benefício Híbrido do Azure para SQL Server em uma VM do Azure e estar em conformidade, você tem três opções:

- Provisione uma máquina virtual usando uma imagem de SQL Server traga sua própria licença do Azure Marketplace. Essa opção está disponível somente para clientes que têm um Enterprise Agreement.
- Provisione uma máquina virtual usando uma imagem de SQL Server paga conforme o uso do Azure Marketplace e ative Benefício Híbrido do Azure.
- SQL Server de instalação automática em uma VM do Azure, [Registre manualmente a VM SQL Server](virtual-machines-windows-sql-register-with-resource-provider.md)e ative benefício híbrido do Azure.

O tipo de licença de SQL Server é definido quando a VM é provisionada. Você pode alterá-lo a qualquer momento depois. Alternar entre modelos de licença incorre em nenhum tempo de inatividade, não reinicia a VM, não acrescenta nenhum custo adicional e entra em vigor imediatamente. Na verdade, a ativação de Benefício Híbrido do Azure *reduz* o custo.

## <a name="prerequisites"></a>Pré-requisitos

O uso do provedor de recursos da VM do SQL requer a extensão SQL Server IaaS. Assim, você precisa do seguinte:
- Uma [subscrição do Azure](https://azure.microsoft.com/free/).
- [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default). 
- Uma [vm SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) registrada com o [provedor de recursos de VM do SQL](virtual-machines-windows-sql-register-with-resource-provider.md).


## <a name="change-the-license-for-vms-already-registered-with-the-resource-provider"></a>Alterar a licença para VMs já registradas com o provedor de recursos 

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Você pode modificar o modelo de licença diretamente do portal: 

1. Abra o [portal do Azure](https://portal.azure.com) e abra o [recurso de máquinas virtuais do SQL](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) para sua VM SQL Server. 
1. Selecione **definir** em **configurações**. 
1. Selecione a opção **benefício híbrido do Azure** e marque a caixa de seleção para confirmar que você tem uma licença de SQL Server com o Software Assurance. 
1. Selecione **aplicar** na parte inferior da página **Configurar** . 

![Benefício Híbrido do Azure no portal](media/virtual-machines-windows-sql-ahb/ahb-in-portal.png)


# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

Você pode usar o CLI do Azure para alterar o modelo de licença.  

O trecho de código a seguir alterna seu modelo de licença pago conforme o uso para trazer sua própria licença (ou usando Benefício Híbrido do Azure):

```azurecli-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type AHUB
```

O trecho de código a seguir alterna seu modelo traga sua própria licença para o pré-pago: 

```azurecli-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type PAYG

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type PAYG
```

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Você pode usar o PowerShell para alterar seu modelo de licença.

O trecho de código a seguir alterna seu modelo de licença pago conforme o uso para trazer sua própria licença (ou usando Benefício Híbrido do Azure):

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

O trecho de código a seguir alterna seu modelo traga sua própria licença para o pré-pago:

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
---

## <a name="change-the-license-for-vms-not-registered-with-the-resource-provider"></a>Alterar a licença para VMs não registradas com o provedor de recursos

Se você provisionou uma VM SQL Server das imagens pré-pagas do Azure Marketplace, o tipo de licença de SQL Server será pago conforme o uso. Se você provisionou uma VM SQL Server usando uma imagem traga sua própria licença do Azure Marketplace, o tipo de licença será AHUB. Todas as SQL Server VMs provisionadas a partir do padrão (pré-pago) ou suas imagens do Azure Marketplace de traga sua própria licença serão automaticamente registradas com o provedor de recursos de VM do SQL, para que possam alterar o [tipo de licença](#change-the-license-for-vms-already-registered-with-the-resource-provider).

Você só está qualificado para instalar automaticamente SQL Server em uma VM do Azure por meio de Benefício Híbrido do Azure. Você deve [registrar essas VMs com o provedor de recursos de VM do SQL](virtual-machines-windows-sql-register-with-resource-provider.md) definindo a licença de SQL Server como benefício híbrido do Azure, para indicar o uso de benefício híbrido do Azure de acordo com os termos de produto da Microsoft.

Você pode alterar o tipo de licença de uma VM SQL Server como pré-pago ou Benefício Híbrido do Azure somente se a VM SQL Server estiver registrada com o provedor de recursos de VM do SQL.

## <a name="remarks"></a>Observações

- Os clientes do CSP (provedor de soluções na nuvem) do Azure podem usar a Benefício Híbrido do Azure primeiro implantando uma VM pré-paga e, em seguida, convertendo-a para sua própria licença, se tiver o Software Assurance ativo.
- Se você descartar o recurso de VM SQL Server, voltará para a configuração de licença embutida em código da imagem. 
- A capacidade de alterar o modelo de licença é um recurso do provedor de recursos de VM do SQL. A implantação de uma imagem do Azure Marketplace por meio do portal do Azure registra automaticamente uma VM SQL Server com o provedor de recursos. Mas os clientes que estiverem instalando automaticamente SQL Server precisarão [registrar manualmente sua VM SQL Server](virtual-machines-windows-sql-register-with-resource-provider.md). 
- Adicionar uma VM SQL Server a um conjunto de disponibilidade requer a recriação da VM. Assim, todas as VMs adicionadas a um conjunto de disponibilidade voltarão para o tipo de licença pago conforme o uso padrão. Benefício Híbrido do Azure precisará ser habilitado novamente. 


## <a name="limitations"></a>Limitações

- A alteração do modelo de licença está disponível somente para clientes que têm o Software Assurance.
- A alteração do modelo de licença tem suporte apenas para as edições Standard e Enterprise do SQL Server. Não há suporte para alterações de licença para o Express, Web e Developer. 
- A alteração do modelo de licença só tem suporte para máquinas virtuais implantadas por meio do modelo de Azure Resource Manager. Não há suporte para VMs implantadas por meio do modelo clássico. Você pode migrar sua VM do clássico para o modelo do Resource Manager e registrá-la com o provedor de recursos da VM do SQL. Depois que a VM for registrada com o provedor de recursos de VM do SQL, as alterações no modelo de licença estarão disponíveis na VM.
- A alteração do modelo de licença é habilitada somente para instalações de nuvem pública.
- A alteração do modelo de licença tem suporte apenas em máquinas virtuais que têm uma única NIC (interface de rede). Em máquinas virtuais que têm mais de uma NIC, primeiro você deve remover uma das NICs (usando o portal do Azure) antes de tentar o procedimento. Caso contrário, você receberá um erro semelhante ao seguinte: 
   
  `The virtual machine '\<vmname\>' has more than one NIC associated.` 
   
  Embora você possa adicionar a NIC de volta à VM depois de alterar o modelo de licença, as operações realizadas por meio da página de configuração SQL Server no portal do Azure, como aplicação de patch e backup automáticos, não serão mais consideradas suportadas.

## <a name="known-errors"></a>Erros conhecidos

### <a name="the-resource-microsoftsqlvirtualmachinesqlvirtualmachinesresource-group-under-resource-group-resource-group-was-not-found"></a>O recurso ' Microsoft. SqlVirtualMachine/SqlVirtualMachines/\<Resource-Group > ' no grupo de recursos\<' > grupo de recursos ' não foi encontrado.
Esse erro ocorre quando você tenta alterar o modelo de licença em uma VM SQL Server que não foi registrada com o provedor de recursos de VM do SQL:

`The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<resource-group>' under resource group '\<resource-group>' was not found. The property 'sqlServerLicenseType' cannot be found on this object. Verify that the property exists and can be set.`

Você precisará registrar o provedor de recursos em sua [assinatura](virtual-machines-windows-sql-register-with-resource-provider.md#register-the-sql-vm-resource-provider-with-a-subscription)e [registrar sua VM de SQL Server com o provedor de recursos](virtual-machines-windows-sql-register-with-resource-provider.md). 

### <a name="cannot-validate-argument-on-parameter-sku"></a>Não é possível validar o argumento no parâmetro ' SKU '
Você pode encontrar esse erro ao tentar alterar seu modelo de licença de VM SQL Server usando Azure PowerShell versões posteriores a 4,0:

`Set-AzResource: Cannot validate argument on parameter 'Sku'. The argument is null or empty. Provide an argument that is not null or empty, and then try the command again.`

Para resolver esse erro, remova os comentários dessas linhas no trecho de código do PowerShell mencionado anteriormente ao alternar o modelo de licença:

  ```powershell-interactive
  # the following code snippet is necessary if using Azure Powershell version > 4
  $SqlVm.Kind= "LicenseChange"
  $SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
  $SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new()
  ```
  
Use o código a seguir para verificar sua versão do Azure PowerShell:
  
  ```powershell-interactive
  Get-Module -ListAvailable -Name Azure -Refresh
  ```

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, veja os artigos seguintes: 

* [Visão geral de SQL Server em uma VM do Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [Perguntas frequentes para SQL Server em uma VM do Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Diretrizes de preços para SQL Server em uma VM do Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Notas de versão para SQL Server em uma VM do Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)


