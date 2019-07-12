---
title: Como alterar o modelo de licenciamento de uma VM do SQL Server no Azure
description: Saiba como mudar o licenciamento para uma máquina virtual do SQL, no Azure a partir de "pay as you go" para "bring-your-own-license' com o benefício híbrido do Azure.
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
ms.date: 02/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 78ad784a45d2b0063932791daedc9b1ec1aafd72
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786768"
---
# <a name="how-to-change-licensing-model-for-a-sql-server-virtual-machine-in-azure"></a>Como alterar o modelo de licenciamento para uma máquina virtual do SQL Server no Azure
Este artigo descreve como alterar o modelo de licenciamento para uma máquina virtual do SQL Server no Azure com o novo fornecedor de recursos de VM do SQL - **Microsoft.SqlVirtualMachine**.

Existem dois modelos de licenciamento para uma máquina virtual (VM) que aloja o SQL Server - pay as you go e o Azure Hybrid Benefit (AHB). E agora, usando o portal do Azure, CLI do Azure ou PowerShell pode modificar modelo de licenciamento da sua VM do SQL Server. 

O **pay as you go** modelo (PAYG) significa que o custo por segundo de execução da VM do Azure inclui o custo da licença do SQL Server.
O [Azure híbrido benefício (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/) permite-lhe utilizar a sua própria licença do SQL Server com uma VM a executar o SQL Server. 

Microsoft Azure Hybrid Benefit para o SQL Server permite o uso de licenças do SQL Server com Software Assurance ("licença qualificado") em máquinas de virtuais do Azure. Com o Azure Hybrid Benefit para o SQL Server, não são cobradas aos clientes para a utilização de licença do SQL Server na VM, mas eles ainda tem de pagar o custo da computação em nuvem subjacente (ou seja, a taxa base), armazenamento e cópias de segurança, bem como e/s associados com seus u Se os serviços (conforme aplicável).

Acordo com os termos de produto da Microsoft "tem de indicar os clientes que estão a utilizar o SQL Database do Azure (instância gerida, conjunto elástico e base de dados), o Azure Data Factory, SQL Server Integration Services ou máquinas de virtuais do SQL Server em híbrido do Azure Benefício para o SQL Server ao configurar cargas de trabalho no Azure."

Para indicar a utilização do benefício híbrido do Azure para SQL Server numa VM do Azure e estar em conformidade, existem três opções:

1. Aprovisione uma máquina virtual utilizando uma imagem BYOL SQL Server no Azure marketplace, só está disponível para clientes com contrato Enterprise.
1. Aprovisionar uma máquina virtual utilizando uma imagem de servidor de SQL PAYG do Azure marketplace e ativar AHB.
1. Self-instalar manualmente o SQL Server numa VM do Azure, [registar a sua VM do SQL Server](virtual-machines-windows-sql-register-with-resource-provider.md) e ativar AHB.

Tipo de licença do SQL Server é definido quando a VM é aprovisionada e pode ser alterada posteriormente em qualquer altura. Alternar entre os modelos de licença incorre **sem tempo de inatividade**, não reinicia a VM, adiciona **sem custos adicionais** (na verdade, ativar AHB *reduz* custo) e é **efeitos imediatos**. 

## <a name="prerequisites"></a>Pré-requisitos

A utilização do fornecedor de recursos de VM do SQL Server requer a extensão SQL IaaS. Assim, continuar a utilizar o fornecedor de recursos de VM do SQL Server, precisa do seguinte:
- Uma [subscrição do Azure](https://azure.microsoft.com/free/).
- [Do software assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default). 
- R [VM do SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) registado com o [fornecedor de recursos de VM do SQL](virtual-machines-windows-sql-register-with-resource-provider.md) instalado. 


## <a name="change-license-for-vms-already-registered-with-resource-provider"></a>Alterar licença para as VMs já registada no fornecedor de recursos 

# <a name="azure-portaltabazure-portal"></a>[Azure portal](#tab/azure-portal)

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Pode modificar o modelo de licenciamento diretamente no portal. 

1. Abra o [portal do Azure](https://portal.azure.com) e inicie a [recursos de máquinas virtuais do SQL](virtual-machines-windows-sql-manage-portal.md#access-sql-virtual-machine-resource) para a sua VM do SQL Server. 
1. Selecione **Configure** sob **definições**. 
1. Selecione o **benefício híbrido do Azure** opção e selecione a caixa de verificação para confirmar que tem uma licença do SQL Server com Software Assurance. 
1. Selecione **aplicar** na parte inferior da **configurar** página. 

![AHB no Portal](media/virtual-machines-windows-sql-ahb/ahb-in-portal.png)


# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

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

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
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
---

## <a name="change-license-for-vms-not-registered-with-resource-provider"></a>Alterar licença para as VMs não registada no fornecedor de recursos

Se aprovisionou uma VM do SQL Server a partir de imagens do PAYG do Azure Marketplace, o tipo de licença SQL será PAYG. Se aprovisionou uma VM do SQL Server com uma imagem BYOL no Azure Marketplace, o tipo de licença será AHUB. Todas as VMs do SQL Server aprovisionada a partir da predefinição (PAYG) ou imagens BYOL Azure Marketplace automaticamente serão registadas no fornecedor de recursos de VM do SQL Server, para que possam alterar o [tipo de licença](#change-license-for-vms-already-registered-with-resource-provider)

Só é elegíveis para o Self-instalar o SQL Server numa VM do Azure através do benefício híbrido do Azure e deve [registar estas VMs com o fornecedor de recursos de VM do SQL](virtual-machines-windows-sql-register-with-resource-provider.md) ao definir a licença do SQL Server como AHB para indicar a utilização AHB de acordo com a Termos de produto da Microsoft.

Só pode alterar o tipo de licença de uma VM do SQL Server como PAYG ou AHB se a VM do SQL está registada no fornecedor de recursos de VM do SQL Server; e todas as VMs de SQL deve ser registadas com a RP de VM do SQL para o cumprimento da licença.

## <a name="remarks"></a>Observações

 - Os clientes de parceiro de soluções Cloud (CSP) do Azure podem utilizar o benefício híbrido do Azure, primeiro a implementar uma VM de pay as you go e, em seguida, convertê-las bring-your-own-license se tiverem SA ativo.
 - Se remover o seu recurso de VM do SQL Server, irá voltar para a definição de licença codificada da imagem. 
  - A capacidade de alterar o modelo de licenciamento é uma funcionalidade do fornecedor de recursos de VM do SQL Server. Implementar uma imagem do marketplace através do portal do Azure automaticamente registra uma VM do SQL Server com o fornecedor de recursos. No entanto, os clientes que Self-estão a instalar o SQL Server serão necessário manualmente [registar a sua VM do SQL Server](virtual-machines-windows-sql-register-with-resource-provider.md). 
- Adicionar uma VM do SQL Server a um conjunto de disponibilidade necessita de recriar a VM. Como tais, qualquer VMs adicionadas a um disponibilidade conjunto serão enviadas de volta para o tipo de licença de pay as you go padrão e AHB tem de ser ativado novamente. 


## <a name="limitations"></a>Limitações

 - Alterar o modelo de licenciamento só está disponível para clientes com software assurance.
 - Alterar o modelo de licenciamento só é suportado para a edição standard e enterprise do SQL Server. As alterações de licença para Express, Web e o desenvolvedor não são suportadas. 
 - Alterar o modelo de licenciamento só é suportada para máquinas virtuais implementadas com o modelo do Resource Manager. As VMs implementadas com o modelo clássico não são suportadas. 
 - Alterar o modelo de licenciamento só está ativado para instalações de Cloud pública.
 - Alterar o modelo de licenciamento só é suportada em máquinas virtuais que tenham um único NIC (interface de rede). Nas máquinas virtuais que têm mais de uma NIC, deve primeiro de remover um dos NICs (ao utilizar o portal do Azure) antes de tentar o procedimento. Caso contrário, será executado num erro semelhante ao seguinte: `The virtual machine '\<vmname\>' has more than one NIC associated.` Embora possa adicionar a NIC de volta para a VM depois de alterar o modo de licenciamento, operações feitas através da página de configuração do SQL no portal do Azure, como aplicação de patches automática e a cópia de segurança, já não serão consideradas suportado.


## <a name="known-errors"></a>Erros conhecidos

### <a name="the-resource-microsoftsqlvirtualmachinesqlvirtualmachinesresource-group-under-resource-group-resource-group-was-not-found-the-property-sqlserverlicensetype-cannot-be-found-on-this-object-verify-that-the-property-exists-and-can-be-set"></a>O recurso ' Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<grupo de recursos >' no grupo de recursos "\<grupo de recursos >' não foi encontrado. Não é possível localizar a propriedade 'sqlServerLicenseType' neste objeto. Certifique-se de que a propriedade existe e que pode ser definida.
Este erro ocorre durante a tentativa de alterar o modelo de licenciamento numa VM do SQL Server que não foi registado com o fornecedor de recursos de VM do SQL Server. Terá de registar o fornecedor de recursos para sua [subscrição](virtual-machines-windows-sql-register-with-resource-provider.md#register-sql-vm-resource-provider-with-subscription)e, em seguida, registe-se a VM do SQL Server com o SQL [fornecedor de recursos](virtual-machines-windows-sql-register-with-resource-provider.md). 

### <a name="cannot-validate-argument-on-parameter-sku"></a>Não é possível validar o argumento no parâmetro "Sku"
Pode encontrar este erro quando tentar alterar o seu modelo de licenciamento de VM do SQL Server ao utilizar o Azure PowerShell > 4.0: `Set-AzResource: Cannot validate argument on parameter 'Sku'. The argument is null or empty. Provide an argument that is not null or empty, and then try the command again.`

Para resolver este erro, uncomment estas linhas no fragmento de código mencionada anteriormente do PowerShell ao alternar o seu modelo de licenciamento:

  ```powershell-interactive
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


