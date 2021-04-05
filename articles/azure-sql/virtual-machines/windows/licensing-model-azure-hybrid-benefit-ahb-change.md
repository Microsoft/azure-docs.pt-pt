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
ms.subservice: management
ms.workload: iaas-sql-server
ms.date: 11/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 5813331d5eafd953d776dd19d9cc885ff71b8be0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100361558"
---
# <a name="change-the-license-model-for-a-sql-virtual-machine-in-azure"></a>Alterar o modelo de licença de uma máquina virtual do SQL no Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]


Este artigo descreve como alterar o modelo de licença para uma máquina virtual SQL Server (VM) em Azure utilizando a Extensão do [Agente SQL IaaS](./sql-server-iaas-agent-extension-automate-management.md).

## <a name="overview"></a>Descrição Geral

Existem três modelos de licença para um Azure VM que está hospedado no SQL Server: pay-as-you-go, Azure Hybrid Benefit (AHB) e High Availability/Disaster Recovery (HA/DR). Pode modificar o modelo de licença do seu SQL Server VM utilizando o portal Azure, o Azure CLI ou o PowerShell. 

- O modelo **pay-as-you-go** significa que o custo por segundo de execução do Azure VM inclui o custo da licença SQL Server.
- [O Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) permite-lhe utilizar a sua própria licença SQL Server com um VM que está a executar o SQL Server. 
- O tipo de licença **HA/DR** é utilizado para a [réplica HA/DR gratuita](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure) em Azure. 

O Azure Hybrid Benefit permite a utilização de licenças SQL Server com Garantia de Software ("Licença Qualificada") em máquinas virtuais Azure. Com a Azure Hybrid Benefit, os clientes não são cobrados pelo uso de uma licença SQL Server num VM. Mas ainda têm de pagar o custo do cálculo subjacente da nuvem (isto é, a taxa base), armazenamento e backups. Devem ainda pagar por E/S associado à sua utilização dos serviços (conforme aplicável).

De acordo com os [Termos do Produto da](https://www.microsoft.com/licensing/terms/productoffering/MicrosoftAzureServices/EAEAS)Microsoft : "Os clientes devem indicar que estão a utilizar a Base de Dados Azure SQL (Instância Gerida, Piscina Elástica e Base de Dados Única), Azure Data Factory, SQL Server Integration Services ou SQL Server Virtual Machines under Azure Hybrid Benefit for SQL Server quando configurar cargas de trabalho no Azure."

Para indicar a utilização do Benefício Híbrido Azure para O Servidor SQL no Azure VM e ser conforme, tem três opções:

- Provisionar uma máquina virtual utilizando uma imagem do SqL Server de "bring-your-your-own-license" do Azure Marketplace. Esta opção está disponível apenas para clientes que tenham um Contrato de Empresa.
- Forneça uma máquina virtual utilizando uma imagem do Servidor SQL pay-as-you-go do Azure Marketplace e ative o Azure Hybrid Benefit.
- Autoinstalar o SqL Server em Azure VM, registar-se manualmente [com a extensão do agente SQL IaaS](sql-agent-extension-manually-register-single-vm.md)e ativar o Benefício Híbrido Azure.

O tipo de licença do SQL Server pode ser configurado quando o VM é a provisionado, ou a qualquer momento depois. A troca entre os modelos de licença não incorre em tempo de inatividade, não reinicia o serviço VM ou o serviço SQL Server, não adiciona quaisquer custos adicionais e tem efeito imediato. De facto, ativar o Benefício Híbrido Azure *reduz os custos.*

## <a name="prerequisites"></a>Pré-requisitos

Alterar o modelo de licenciamento do seu SQL Server VM tem os seguintes requisitos: 

- Uma [subscrição do Azure](https://azure.microsoft.com/free/).
- Um [SQL Server VM](./create-sql-vm-portal.md) registado com a [extensão do agente SQL IaaS](./sql-server-iaas-agent-extension-automate-management.md).
- [A Garantia de Software](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) é um requisito para utilizar o [Benefício Híbrido Azure](https://azure.microsoft.com/pricing/hybrid-benefit/). 


## <a name="change-license-model"></a>Alterar o modelo de licenciamento

# <a name="azure-portal"></a>[Portal do Azure](#tab/azure-portal)

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Pode modificar o modelo de licença diretamente do portal: 

1. Abra o [portal Azure](https://portal.azure.com) e abra o [recurso de máquinas virtuais SQL](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource) para o seu SQL Server VM. 
1. Selecione **Configurar** em **Definições**. 
1. Selecione a opção **Azure Hybrid Benefit** e selecione a caixa de verificação para confirmar que tem uma licença SQL Server com Garantia de Software. 
1. **Selecione Aplicar** na parte inferior da página **Configure.** 

![Benefício Híbrido Azure no portal](./media/licensing-model-azure-hybrid-benefit-ahb-change/ahb-in-portal.png)


# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Pode utilizar o Azure CLI para alterar o seu modelo de licença.  

Especificar os seguintes valores para **o tipo de licença:**
- `AHUB` para o Benefício Híbrido Azure
- `PAYG` para pagar à medida que vai
- `DR` para ativar a réplica ha/DR gratuita


```azurecli-interactive
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type <license-type>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pode utilizar o PowerShell para alterar o seu modelo de licença.

Especificar os seguintes valores para **o tipo de licença:**
- `AHUB` para o Benefício Híbrido Azure
- `PAYG` para pagar à medida que vai
- `DR` para ativar a réplica ha/DR gratuita


```powershell-interactive
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType <license-type>
```

---

## <a name="remarks"></a>Observações

- Os clientes do Azure Cloud Solution Provider (CSP) podem utilizar o Azure Hybrid Benefit, implantando primeiro um VM pay-as-you-go e, em seguida, convertendo-o para trazer a sua própria licença, se tiverem uma Garantia de Software ativa.
- Se deixar cair o seu recurso de máquina virtual SQL, voltará à definição de licença codificada pela imagem. 
- A capacidade de alterar o modelo de licença é uma característica da Extensão do Agente SQL IaaS. A implementação de uma imagem do Azure Marketplace através do portal Azure regista automaticamente um SQL Server VM com a extensão. Mas os clientes que se instalam automaticamente no SQL Server terão de registar manualmente [o seu VM do Servidor SQL](sql-agent-extension-manually-register-single-vm.md). 
- Adicionar um SQL Server VM a um conjunto de disponibilidade requer recriar o VM. Como tal, quaisquer VMs adicionados a um conjunto de disponibilidade voltarão ao tipo de licença de pagamento padrão. O Azure Hybrid Benefit terá de ser ativado novamente. 


## <a name="limitations"></a>Limitações

Mudar o modelo de licença é:
   - Disponível apenas para clientes com [Garantia de Software.](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-overview)
   - Suportado apenas para as edições Standard e Enterprise do SQL Server. As alterações de licença para Express, Web e Developer não são suportadas. 
   - Suportado apenas para máquinas virtuais implantadas através do modelo Azure Resource Manager. As máquinas virtuais implantadas através do modelo clássico não são suportadas. 
   - Disponível apenas para o público ou nuvens do Governo Azure. 

> [!Note]
> Apenas o licenciamento baseado no núcleo do SQL Server com garantia de software ou licenças de subscrição são elegíveis para O Benefício Híbrido Azure. Se estiver a utilizar o licenciamento Server + CAL para o SQL Server e tiver Garantia de Software, pode utilizar a sua própria licença para uma imagem de máquina virtual do Azure SQL Server para alavancar a mobilidade de licenças para estes servidores, mas não pode aproveitar as outras funcionalidades do Azure Hybrid Benefit. 

## <a name="known-errors"></a>Erros conhecidos

Reveja os erros conhecidos e as suas resoluções. 

**O recurso 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/ \<resource-group> ' under resource group ' não foi \<resource-group> encontrado.**

Este erro ocorre quando tenta alterar o modelo de licença num SQL Server VM que não foi registado na Extensão do Agente IAAS do SqL Server:

`The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<resource-group>' under resource group '\<resource-group>' was not found. The property 'sqlServerLicenseType' cannot be found on this object. Verify that the property exists and can be set.`

Terá de registar a sua subscrição junto do fornecedor de recursos e, em seguida, [registar o seu SQL Server VM com a Extensão do Agente SQL IaaS](sql-agent-extension-manually-register-single-vm.md). 



## <a name="next-steps"></a>Próximos passos

Para obter mais informações, veja os seguintes artigos: 

* [Visão geral do SQL Server num VM do Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [FAQ para SQL Server em um VM Windows](frequently-asked-questions-faq.md)
* [Orientação de preços para o SQL Server num VM do Windows](pricing-guidance.md)
* [Notas de lançamento para SQL Server num VM do Windows](../../database/doc-changes-updates-release-notes.md)
* [Visão geral da extensão do agente da SQL IaaS](./sql-server-iaas-agent-extension-automate-management.md)
