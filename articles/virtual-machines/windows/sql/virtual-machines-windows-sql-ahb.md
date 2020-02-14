---
title: Alterar o modelo de licença para um VM De Servidor SQL em Azure
description: Aprenda a mudar o licenciamento para uma máquina virtual SQL Server em Azure de pay-as-you-go para trazer a sua própria licença utilizando o Azure Hybrid Benefit.
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
ms.date: 11/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 502d1fe599accb29ccc99c9e527f8d1c8e1d52b8
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77201832"
---
# <a name="change-the-license-model-for-a-sql-server-virtual-machine-in-azure"></a>Altere o modelo de licença para uma máquina virtual SQL Server em Azure
Este artigo descreve como alterar o modelo de licença para uma máquina virtual SQL Server (VM) em Azure utilizando o novo fornecedor de recursos SQL VM, **Microsoft.SqlVirtualMachine**.

Existem três modelos de licença para um VM que está hospedando O Servidor SQL: pay-as-you-go, Azure Hybrid Benefit, e recuperação de desastres (DR). Pode modificar o modelo de licença do seu VM de servidor SQL utilizando o portal Azure, o Azure CLI ou powerShell. 

- O modelo **pay-as-you-go** significa que o custo por segundo de funcionamento do Azure VM inclui o custo da licença SQL Server.
- [O Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) permite-lhe utilizar a sua própria licença SQL Server com um VM que está a executar o SQL Server. 
- O tipo de licença de **recuperação** de desastres é usado para a [réplica de DR grátis](virtual-machines-windows-sql-high-availability-dr.md#free-dr-replica-in-azure) em Azure. 

O Azure Hybrid Benefit permite a utilização de licenças SQL Server com Garantia de Software ("Licença Qualificada") em máquinas virtuais Azure. Com o Azure Hybrid Benefit, os clientes não são cobrados pelo uso de uma licença SQL Server num VM. Mas ainda têm de pagar pelo custo da computação em nuvem subjacente (isto é, a taxa base), armazenamento e backups. Devem igualmente pagar por I/S associados à sua utilização dos serviços (conforme aplicável).

De acordo com os Termos do Produto da Microsoft: "Os clientes devem indicar que estão a usar base de dados Azure SQL (Instância Gerida, Piscina Elástica e Base de Dados Única), Azure Data Factory, SQL Server Integration Services ou SQL Server Virtual Machines no Azure Benefício Híbrido para o Servidor SQL ao configurar cargas de trabalho no Azure."

Para indicar a utilização do Azure Hybrid Benefit para o SQL Server num VM Azure e ser conforme, tem três opções:

- Forme uma máquina virtual utilizando uma imagem SQL Server de entrada própria da Azure Marketplace. Esta opção está disponível apenas para clientes que tenham um Contrato de Empresa.
- Forme uma máquina virtual utilizando uma imagem de Servidor SQL pay-as-you-go do Azure Marketplace e ative o Benefício Híbrido Azure.
- Auto-instale o Servidor SQL num VM Azure, registe-se manualmente com o fornecedor de [recursos SQL VM](virtual-machines-windows-sql-register-with-resource-provider.md)e ative o Azure Hybrid Benefit.

O tipo de licença do SQL Server é definido quando o VM é provisionado. Pode mudá-lo a qualquer momento depois. Alternar entre modelos de licença não incorre em tempo de inatividade, não reinicia o VM ou o serviço SQL Server, não adiciona custos adicionais e é eficaz imediatamente. De facto, ativar o Azure Hybrid Benefit reduz os *custos.*

## <a name="prerequisites"></a>Pré-requisitos

Alterar o modelo de licenciamento do seu VM servidor SQL tem os seguintes requisitos: 

- Uma [subscrição Azure.](https://azure.microsoft.com/free/)
- Um [VM de servidor SQL](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) registado no fornecedor de [recursos SQL VM](virtual-machines-windows-sql-register-with-resource-provider.md).
- [A Garantia de Software](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) é um requisito para utilizar o [Benefício Híbrido Azure.](https://azure.microsoft.com/pricing/hybrid-benefit/) 


## <a name="vms-already-registered-with-the-resource-provider"></a>VMs já registados no fornecedor de recursos 

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Pode modificar o modelo de licença diretamente a partir do portal: 

1. Abra o [portal Azure](https://portal.azure.com) e abra o recurso de [máquinas virtuais SQL](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) para o seu VM de Servidor SQL. 
1. **Selecione Configurar** em **Definições**. 
1. Selecione a opção **Benefício Híbrido Azure** e selecione a caixa de verificação para confirmar que tem uma licença SQL Server com Garantia de Software. 
1. Selecione **Aplicar** na parte inferior da página **Configurar.** 

![Benefício Híbrido Azure no portal](media/virtual-machines-windows-sql-ahb/ahb-in-portal.png)


# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

Pode utilizar o Azure CLI para alterar o modelo de licença.  


**Benefício híbrido azure**

```azurecli-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type AHUB
```

**Pague à medida que for:** 

```azurecli-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type PAYG

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type PAYG
```

**Recuperação de desastres (DR)**

```azurecli-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type DR

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type DR
```

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Pode utilizar o PowerShell para alterar o modelo de licença.

**Benefício Híbrido do Azure**

```powershell-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType AHUB
```

**Pague à medida que for.**

```powershell-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType PAYG
```

**Recuperação de Desastres** 

```powershell-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType DR
```

---

## <a name="vms-not-registered-with-the-resource-provider"></a>VMs não registados no fornecedor de recursos

Se for metododindo um VM do Servidor SQL a partir de imagens do Mercado Azure pay-as-you-go, então o tipo de licença SQL Server será pago como você-vai. Se for provisionado um VM de servidor SQL utilizando uma imagem de trazer a sua própria licença do Azure Marketplace, então o tipo de licença será AHUB. Todas as VMs do SQL Server disponibilizadas a partir de predefinição (pay-as-you-go) ou de trazer as imagens do Mercado Azure da sua própria licença serão automaticamente registadas no fornecedor de recursos SQL VM, para que possam alterar o tipo de [licença](#vms-already-registered-with-the-resource-provider).

Só é elegível para autoinstalar o SQL Server num Azure VM via Azure Hybrid Benefit. Deve [registar estes VMs com o fornecedor de recursos SQL VM,](virtual-machines-windows-sql-register-with-resource-provider.md) definindo a licença SQL Server como Benefício Híbrido Azure, para indicar o uso do Benefício Híbrido Azure de acordo com os Termos do Produto da Microsoft.

Só pode alterar o tipo de licença de um VM SQL Server como pay-as-you-go ou Azure Hybrid Benefit apenas se o VM do Servidor SQL estiver registado no fornecedor de recursos SQL VM.

## <a name="remarks"></a>Observações

- Os clientes do Azure Cloud Solution Provider (CSP) podem utilizar o Azure Hybrid Benefit implementando primeiro um VM pay-as-you-go e, em seguida, convertê-lo para trazer a sua própria licença, se tiverem garantia de software ativa.
- Se deixar cair o seu recurso VM do Servidor SQL, voltará à definição de licença codificada da imagem. 
- A capacidade de alterar o modelo de licença é uma característica do fornecedor de recursos SQL VM. A implementação de uma imagem do Azure Marketplace através do portal Azure regista automaticamente um VM de Servidor SQL com o fornecedor de recursos. Mas os clientes que se auto-instalarem no SQL Server terão de registar manualmente o seu VM do [Servidor SQL](virtual-machines-windows-sql-register-with-resource-provider.md). 
- Adicionar um VM de servidor SQL a um conjunto de disponibilidade requer recriar o VM. Como tal, quaisquer VMs adicionados a um conjunto de disponibilidade voltarão ao tipo de licença pay-as-you-go padrão. O Azure Hybrid Benefit terá de ser novamente ativado. 


## <a name="limitations"></a>Limitações

Mudar o modelo de licença é:
   - Disponível apenas para clientes com Garantia de [Software.](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-overview)
   - Apenas suportado para as edições Standard e Enterprise do SQL Server. As alterações de licença para Express, Web e Developer não são suportadas. 
   - Apenas suportado para máquinas virtuais implantadas através do modelo Azure Resource Manager. As máquinas virtuais implantadas através do modelo clássico não são suportadas. 
   - Disponível apenas para as nuvens públicas ou do Governo Azul. 
   - Apenas suportado em máquinas virtuais que tenham uma única interface de rede (NIC). 


## <a name="known-errors"></a>Erros conhecidos

### <a name="the-resource-microsoftsqlvirtualmachinesqlvirtualmachinesresource-group-under-resource-group-resource-group-was-not-found"></a>O Recurso 'Microsoft.SqlVirtualMachines/SqlVirtualMachines/\<resource-group>' no âmbito do grupo de recursos\<grupo de recursos>' não foi encontrado.

Este erro ocorre quando tenta alterar o modelo de licença num VM de servidor SQL que não foi registado no fornecedor de recursos SQL VM:

`The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<resource-group>' under resource group '\<resource-group>' was not found. The property 'sqlServerLicenseType' cannot be found on this object. Verify that the property exists and can be set.`

Terá de registar a sua subscrição junto do fornecedor de recursos e, em [seguida, registar o seu VM de Servidor SQL com o fornecedor de recursos](virtual-machines-windows-sql-register-with-resource-provider.md). 


### <a name="the-virtual-machine-vmname-has-more-than-one-nic-associated"></a>A máquina virtual '\<vmname\>' tem mais de um NIC associado

Este erro ocorre em máquinas virtuais que têm mais de um NIC. Retire um dos NICs antes de alterar o modelo de licenciamento. Embora possa adicionar o NIC de volta ao VM depois de alterar o modelo de licença, as operações no portal Azure, como cópia de segurança automática e patching, deixarão de ser suportadas. 


## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, veja os artigos seguintes: 

* [Visão geral do Servidor SQL num VM do Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [FAQ para Servidor SQL em um VM do Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Orientação de preços para O Servidor SQL num VM do Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Notas de lançamento para SQL Server num VM do Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)


