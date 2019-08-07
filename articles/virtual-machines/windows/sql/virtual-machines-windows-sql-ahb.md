---
title: Como alterar o modelo de licenciamento para uma VM SQL Server no Azure
description: Saiba como alternar o licenciamento para uma máquina virtual SQL no Azure de ' pré-pago ' para ' traga sua própria licença ' usando o Benefício Híbrido do Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/05/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 37457d8ce1189f9282f4763633e944e3c2d639c9
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816733"
---
# <a name="how-to-change-licensing-model-for-a-sql-server-virtual-machine-in-azure"></a>Como alterar o modelo de licenciamento para uma máquina virtual SQL Server no Azure
Este artigo descreve como alterar o modelo de licenciamento de uma máquina virtual SQL Server no Azure usando o novo provedor de recursos de VM do SQL- **Microsoft. SqlVirtualMachine**.

Há dois modelos de licenciamento para uma VM (máquina virtual) que hospeda SQL Server-pré-pago e Benefício Híbrido do Azure (AHB). E agora, usando o portal do Azure, o CLI do Azure ou o PowerShell, você pode modificar o modelo de licenciamento de sua VM de SQL Server. 

O modelo **pago pelo uso** (PAYG) significa que o custo por segundo da execução da VM do Azure inclui o custo da licença de SQL Server.
O [benefício híbrido do Azure (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/) permite que você use sua própria licença de SQL Server com uma VM que executa SQL Server. 

Microsoft Azure benefício híbrido do SQL Server permite o uso de licenças do SQL Server com o Software Assurance ("licença qualificada") em máquinas virtuais do Azure. Com Benefício Híbrido do Azure para SQL Server, os clientes não serão cobrados pelo uso da licença de SQL Server na VM, mas eles ainda devem pagar pelo custo da computação em nuvem subjacente (ou seja, pela taxa base), armazenamento e back-ups, bem como a e/s associada ao seu u Se os serviços (conforme aplicável).

De acordo com os termos de produto da Microsoft ", os clientes devem indicar que estão usando o banco de dados SQL do Azure (Instância Gerenciada, Pool Elástico e Banco de Dados Individual), Azure Data Factory, SQL Server Integration Services ou SQL Server máquinas virtuais no Azure híbrido Benefício para SQL Server ao configurar cargas de trabalho no Azure. "

Para indicar o uso do Benefício Híbrido do Azure para SQL Server na VM do Azure e estar em conformidade, há três opções:

1. Provisione uma máquina virtual usando uma imagem de SQL Server BYOL do Azure Marketplace, disponível somente para clientes com Enterprise Agreement.
1. Provisione uma máquina virtual usando uma imagem de SQL Server do PAYG do Azure Marketplace e ative o AHB.
1. SQL Server de auto-instalação em uma VM do Azure, [Registre manualmente sua VM de SQL Server](virtual-machines-windows-sql-register-with-resource-provider.md) e ative AHB.

O tipo de licença de SQL Server é definido quando a VM é provisionada e pode ser alterada a qualquer momento depois. Alternar entre modelos de licença incorre em **nenhum tempo**de inatividade, não reinicia a VM, não acrescenta **nenhum custo adicional** (na verdade, a ativação do AHB *reduz* o custo) e **entra em vigor imediatamente**. 

## <a name="prerequisites"></a>Pré-requisitos

O uso do provedor de recursos da VM do SQL requer a extensão SQL IaaS. Assim, para continuar com a utilização do provedor de recursos de VM do SQL, você precisará do seguinte:
- Uma [subscrição do Azure](https://azure.microsoft.com/free/).
- [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default). 
- Uma [vm SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) registrada com o [provedor de recursos de VM do SQL](virtual-machines-windows-sql-register-with-resource-provider.md) instalado. 


## <a name="change-license-for-vms-already-registered-with-resource-provider"></a>Licença de alteração para VMs já registradas com o provedor de recursos 

# <a name="azure-portaltabazure-portal"></a>[Azure portal](#tab/azure-portal)

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Você pode modificar o modelo de licenciamento diretamente do Portal. 

1. Abra o [portal do Azure](https://portal.azure.com) e inicie o [recurso de máquinas virtuais do SQL](virtual-machines-windows-sql-manage-portal.md#access-sql-virtual-machine-resource) para sua VM SQL Server. 
1. Selecione **definir** em **configurações**. 
1. Selecione a opção **benefício híbrido do Azure** e marque a caixa de seleção para confirmar que você tem uma licença de SQL Server com o Software Assurance. 
1. Selecione **aplicar** na parte inferior da página **Configurar** . 

![AHB no portal](media/virtual-machines-windows-sql-ahb/ahb-in-portal.png)


# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

Você pode usar CLI do Azure para alterar seu modelo de licenciamento.  

O trecho de código a seguir alterna seu modelo de licença pago conforme o uso para BYOL (ou usando Benefício Híbrido do Azure):

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

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
Você pode usar o PowerShell para alterar seu modelo de licenciamento.

O trecho de código a seguir alterna seu modelo de licença pago conforme o uso para BYOL (ou usando Benefício Híbrido do Azure):

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

O trecho de código a seguir alterna seu modelo BYOL para pré-pago:

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

## <a name="change-license-for-vms-not-registered-with-resource-provider"></a>Licença de alteração para VMs não registradas com o provedor de recursos

Se você provisionou uma VM SQL Server de imagens do Azure Marketplace, o tipo de licença do SQL será PAYG. Se você provisionou uma VM SQL Server usando uma imagem BYOL do Azure Marketplace, o tipo de licença será AHUB. Todas as SQL Server VMs provisionadas a partir de imagens padrão (PAYG) ou BYOL do Azure Marketplace serão automaticamente registradas com o provedor de recursos de VM do SQL, para que possam alterar o [tipo de licença](#change-license-for-vms-already-registered-with-resource-provider)

Você só está qualificado para instalar automaticamente SQL Server na VM do Azure por meio de Benefício Híbrido do Azure e deve [registrar essas VMs com o provedor de recursos da VM do SQL](virtual-machines-windows-sql-register-with-resource-provider.md) definindo a licença de SQL Server como AHB para indicar o uso de AHB de acordo com os termos de produto da Microsoft.

Você só pode alterar o tipo de licença de uma VM SQL Server como PAYG ou AHB se a VM do SQL estiver registrada com o provedor de recursos de VM do SQL; e todas as VMs do SQL devem ser registradas com o RP da VM do SQL para conformidade com licenças.

## <a name="remarks"></a>Observações

 - Os clientes do provedor de soluções de nuvem do Azure (CSP) podem utilizar a Benefício Híbrido do Azure implantando primeiro uma VM pré-paga e, em seguida, convertendo-a para sua própria licença se tiverem o SA ativo.
 - Se você descartar o recurso de VM SQL Server, voltará para a configuração de licença embutida em código da imagem. 
  - A capacidade de alterar o modelo de licenciamento é um recurso do provedor de recursos da VM do SQL. Implantar uma imagem do Marketplace por meio do portal do Azure registra automaticamente uma VM SQL Server com o provedor de recursos. No entanto, os clientes que estiverem instalando automaticamente SQL Server precisarão [registrar manualmente sua VM SQL Server](virtual-machines-windows-sql-register-with-resource-provider.md). 
- Adicionar uma VM SQL Server a um conjunto de disponibilidade requer a recriação da VM. Assim, todas as VMs adicionadas a um conjunto de disponibilidade voltarão ao tipo de licença pago conforme o uso padrão, e o AHB precisará ser habilitado novamente. 


## <a name="limitations"></a>Limitações

 - A alteração do modelo de licenciamento só está disponível para clientes com Software Assurance.
 - A alteração do modelo de licenciamento tem suporte apenas para a edição Standard e Enterprise do SQL Server. Não há suporte para alterações de licença para o Express, Web e Developer. 
 - Só há suporte para a alteração do modelo de licenciamento para máquinas virtuais implantadas usando o modelo do Resource Manager. Não há suporte para VMs implantadas usando o modelo clássico. Você pode migrar sua VM do modelo clássico para o Gerenciador de recursos (ARM) e registrar com o provedor de recursos de VM do SQL. Depois que a VM estiver registrada com o provedor de recursos de VM do SQL, as alterações no modelo de licenciamento estarão disponíveis na VM. 
 - A alteração do modelo de licenciamento só é habilitada para instalações de nuvem pública.
 - A alteração do modelo de licenciamento tem suporte apenas em máquinas virtuais que têm uma única NIC (interface de rede). Em máquinas virtuais que têm mais de uma NIC, primeiro você deve remover uma das NICs (usando o portal do Azure) antes de tentar o procedimento. Caso contrário, você encontrará um erro semelhante ao seguinte: `The virtual machine '\<vmname\>' has more than one NIC associated.`Embora você possa adicionar a NIC de volta à VM depois de alterar o modo de licenciamento, as operações realizadas por meio da página de configuração do SQL na portal do Azure, como aplicação de patch e backup automáticos, não serão mais consideradas suportadas.


## <a name="known-errors"></a>Erros conhecidos

### <a name="the-resource-microsoftsqlvirtualmachinesqlvirtualmachinesresource-group-under-resource-group-resource-group-was-not-found-the-property-sqlserverlicensetype-cannot-be-found-on-this-object-verify-that-the-property-exists-and-can-be-set"></a>O recurso ' Microsoft. SqlVirtualMachine/SqlVirtualMachines/\<Resource-Group > ' no grupo de recursos\<' > grupo de recursos ' não foi encontrado. A propriedade ' sqlServerLicenseType ' não pode ser encontrada neste objeto. Verifique se a propriedade existe e pode ser definida.
Esse erro ocorre ao tentar alterar o modelo de licenciamento em uma VM SQL Server que não foi registrada com o provedor de recursos de VM do SQL. Você precisará registrar o provedor de recursos em sua [assinatura](virtual-machines-windows-sql-register-with-resource-provider.md#register-sql-vm-resource-provider-with-subscription)e registrar sua VM SQL Server com o [provedor de recursos](virtual-machines-windows-sql-register-with-resource-provider.md)SQL. 

### <a name="cannot-validate-argument-on-parameter-sku"></a>Não é possível validar o argumento no parâmetro ' SKU '
Você pode encontrar esse erro ao tentar alterar seu modelo de licenciamento de VM SQL Server ao usar Azure PowerShell > 4,0:`Set-AzResource: Cannot validate argument on parameter 'Sku'. The argument is null or empty. Provide an argument that is not null or empty, and then try the command again.`

Para resolver esse erro, remova os comentários dessas linhas no trecho de código do PowerShell mencionado anteriormente ao alternar o modelo de licenciamento:

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

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações, veja os artigos seguintes: 

* [Visão geral de SQL Server em uma VM do Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [Perguntas frequentes sobre o SQL Server em uma VM do Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Diretrizes de preços do SQL Server em uma VM do Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [SQL Server em notas de versão da VM do Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)


