---
title: Alterar o modelo de licença para um SQL VM em Azure
description: Aprenda a mudar o licenciamento para um SQL Server VM em Azure a partir do pay-as-you-go para trazer a sua própria licença usando o Azure Hybrid Benefit.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 28ab0a158507e3f29ecfdc026203d92d71877633
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92786518"
---
# <a name="change-the-license-model-for-a-sql-virtual-machine-in-azure"></a>Alterar o modelo de licença de uma máquina virtual do SQL no Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]


Este artigo descreve como alterar o modelo de licença para uma máquina virtual SQL Server (VM) em Azure, utilizando o novo fornecedor de recursos SQL Server VM, **Microsoft.SqlVirtualMachine** .

Existem três modelos de licença para um VM que está a hospedar o SQL Server: pay-as-you-go, Azure Hybrid Benefit (AHB) e recuperação de desastres (DR). Pode modificar o modelo de licença do seu SQL Server VM utilizando o portal Azure, o Azure CLI ou o PowerShell. 

- O modelo **pay-as-you-go** significa que o custo por segundo de execução do Azure VM inclui o custo da licença SQL Server.
- [O Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) permite-lhe utilizar a sua própria licença SQL Server com um VM que está a executar o SQL Server. 
- O tipo de licença **de recuperação de desastres** é usado para a [réplica gratuita de DR](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure) em Azure. 

O Azure Hybrid Benefit permite a utilização de licenças SQL Server com Garantia de Software ("Licença Qualificada") em máquinas virtuais Azure. Com a Azure Hybrid Benefit, os clientes não são cobrados pelo uso de uma licença SQL Server num VM. Mas ainda têm de pagar o custo do cálculo subjacente da nuvem (isto é, a taxa base), armazenamento e backups. Devem ainda pagar por E/S associado à sua utilização dos serviços (conforme aplicável).

De acordo com os Termos do Produto microsoft: "Os clientes devem indicar que estão a utilizar a Base de Dados Azure SQL (Instância Gerida, Piscina Elástica e Base de Dados Única), Azure Data Factory, SQL Server Integration Services ou SQL Server Virtual Machines under Azure Hybrid Benefit for SQL Server quando configurar cargas de trabalho no Azure."

Para indicar a utilização do Benefício Híbrido Azure para O Servidor SQL no Azure VM e ser conforme, tem três opções:

- Provisionar uma máquina virtual utilizando uma imagem do SqL Server de "bring-your-your-own-license" do Azure Marketplace. Esta opção está disponível apenas para clientes que tenham um Contrato de Empresa.
- Forneça uma máquina virtual utilizando uma imagem do Servidor SQL pay-as-you-go do Azure Marketplace e ative o Azure Hybrid Benefit.
- Autoinstalar o SQL Server no Azure VM, registar-se manualmente [com o fornecedor de recursos SQL VM](sql-vm-resource-provider-register.md)e ativar o Azure Hybrid Benefit.

O tipo de licença do SQL Server pode ser configurado quando o VM é a provisionado, ou a qualquer momento depois. A troca entre os modelos de licença não incorre em tempo de inatividade, não reinicia o serviço VM ou o serviço SQL Server, não adiciona quaisquer custos adicionais e tem efeito imediato. De facto, ativar o Benefício Híbrido Azure *reduz os custos.*

## <a name="prerequisites"></a>Pré-requisitos

Alterar o modelo de licenciamento do seu SQL Server VM tem os seguintes requisitos: 

- Uma [subscrição do Azure](https://azure.microsoft.com/free/).
- Um [SQL Server VM](./create-sql-vm-portal.md) registado com o [fornecedor de recursos SQL VM](sql-vm-resource-provider-register.md).
- [A Garantia de Software](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) é um requisito para utilizar o [Benefício Híbrido Azure](https://azure.microsoft.com/pricing/hybrid-benefit/). 


## <a name="vms-already-registered-with-the-resource-provider"></a>VMs já registados com o fornecedor de recursos 

# <a name="the-azure-portal"></a>[O portal do Azure](#tab/azure-portal)

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Pode modificar o modelo de licença diretamente do portal: 

1. Abra o [portal Azure](https://portal.azure.com) e abra o [recurso de máquinas virtuais SQL](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource) para o seu SQL Server VM. 
1. Selecione **Configurar** em **Definições** . 
1. Selecione a opção **Azure Hybrid Benefit** e selecione a caixa de verificação para confirmar que tem uma licença SQL Server com Garantia de Software. 
1. **Selecione Aplicar** na parte inferior da página **Configure.** 

![Benefício Híbrido Azure no portal](./media/licensing-model-azure-hybrid-benefit-ahb-change/ahb-in-portal.png)


# <a name="the-azure-cli"></a>[A CLI do Azure](#tab/azure-cli)

Pode utilizar o Azure CLI para alterar o seu modelo de licença.  


**Benefício Híbrido do Azure**

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

**Recuperação após desastre (DR)**

```azurecli-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type DR

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type DR
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pode utilizar o PowerShell para alterar o seu modelo de licença.

**Benefício Híbrido do Azure**

```powershell-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType AHUB
```

**Pay as you go**

```powershell-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType PAYG
```

**Recuperação após Desastre** 

```powershell-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType DR
```

---

## <a name="vms-not-registered-with-the-resource-provider"></a>VMs não registados no fornecedor de recursos

Se forte um SQL Server VM a partir de imagens pay-as-you-go Azure Marketplace, o tipo de licença SQL Server será pago como-você-go. Se forte um SQL Server VM utilizando uma imagem de "bring-your-your-own-license" do Azure Marketplace, o tipo de licença será AHUB. Todas as imagens SQL Server VMs provisidas por defeito (pay-as-you-go) ou imagens Azure Marketplace de sua própria licença serão automaticamente registadas com o fornecedor de recursos SQL VM, para que possam alterar o [tipo de licença](#vms-already-registered-with-the-resource-provider).

Só é elegível para autoinstalar o SQL Server no Azure VM via Azure Hybrid Benefit. Deverá [registar estes VMs com o fornecedor de recursos SQL VM](sql-vm-resource-provider-register.md) definindo a licença SQL Server como Azure Hybrid Benefit, para indicar o uso do Benefício Híbrido Azure de acordo com os Termos do Produto da Microsoft.

Só pode alterar o tipo de licença de um SQL Server VM como pay-as-you-go ou Azure Hybrid Benefit apenas se o SQL Server VM estiver registado no fornecedor de recursos SQL VM.

## <a name="remarks"></a>Observações

- Os clientes do Azure Cloud Solution Provider (CSP) podem utilizar o Azure Hybrid Benefit, implantando primeiro um VM pay-as-you-go e, em seguida, convertendo-o para trazer a sua própria licença, se tiverem uma Garantia de Software ativa.
- Se deixar cair o seu recurso VM do S SQL Server, voltará à definição de licença codificada por código rígido da imagem. 
- A capacidade de alterar o modelo de licença é uma característica do fornecedor de recursos SQL VM. A implementação de uma imagem do Azure Marketplace através do portal Azure regista automaticamente um SQL Server VM com o fornecedor de recursos. Mas os clientes que se instalam automaticamente no SQL Server terão de registar manualmente [o seu VM do Servidor SQL](sql-vm-resource-provider-register.md). 
- Adicionar um SQL Server VM a um conjunto de disponibilidade requer recriar o VM. Como tal, quaisquer VMs adicionados a um conjunto de disponibilidade voltarão ao tipo de licença de pagamento padrão. O Azure Hybrid Benefit terá de ser ativado novamente. 


## <a name="limitations"></a>Limitações

Mudar o modelo de licença é:
   - Disponível apenas para clientes com [Garantia de Software.](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-overview)
   - Suportado apenas para as edições Standard e Enterprise do SQL Server. As alterações de licença para Express, Web e Developer não são suportadas. 
   - Suportado apenas para máquinas virtuais implantadas através do modelo Azure Resource Manager. As máquinas virtuais implantadas através do modelo clássico não são suportadas. 
   - Disponível apenas para o público ou nuvens do Governo Azure. 
   - Suportado apenas em máquinas virtuais que tenham uma única interface de rede (NIC). 


## <a name="known-errors"></a>Erros conhecidos

Reveja os erros conhecidos e as suas resoluções. 

**O recurso 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/ \<resource-group> ' under resource group ' não foi \<resource-group> encontrado.**

Este erro ocorre quando tenta alterar o modelo de licença num SQL Server VM que não foi registado com o fornecedor de recursos SQL VM:

`The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<resource-group>' under resource group '\<resource-group>' was not found. The property 'sqlServerLicenseType' cannot be found on this object. Verify that the property exists and can be set.`

Terá de registar a sua subscrição junto do fornecedor de recursos e, em seguida, [registar o seu SQL Server VM com o fornecedor de recursos.](sql-vm-resource-provider-register.md) 


**A máquina virtual \<vmname\> ' ' tem mais de um NIC associado**

Este erro ocorre em máquinas virtuais que têm mais de um NIC. Remova um dos NICs antes de alterar o modelo de licenciamento. Embora possa adicionar o NIC de volta ao VM depois de alterar o modelo de licença, as operações no portal Azure, como cópia de segurança automática e remendos, deixarão de ser suportadas. 


## <a name="next-steps"></a>Próximos passos

Para obter mais informações, veja os seguintes artigos: 

* [Visão geral do SQL Server num VM do Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [FAQ para SQL Server em um VM Windows](frequently-asked-questions-faq.md)
* [Orientação de preços para o SQL Server num VM do Windows](pricing-guidance.md)
* [Notas de lançamento para SQL Server num VM do Windows](../../database/doc-changes-updates-release-notes.md)