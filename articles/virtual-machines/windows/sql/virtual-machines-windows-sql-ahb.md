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
ms.date: 11/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 0aa2cbad75319de93c34128a09f94971e5c70216
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74790625"
---
# <a name="change-the-license-model-for-a-sql-server-virtual-machine-in-azure"></a>Alterar o modelo de licença para uma máquina virtual SQL Server no Azure
Este artigo descreve como alterar o modelo de licença para uma VM (máquina virtual) SQL Server no Azure usando o novo provedor de recursos de VM do SQL, **Microsoft. SqlVirtualMachine**.

Há dois modelos de licença para uma VM que está hospedando SQL Server: pré-pago e Benefício Híbrido do Azure. Você pode modificar o modelo de licença de sua VM SQL Server usando o portal do Azure, o CLI do Azure ou o PowerShell. 

O modelo pago conforme o uso significa que o custo por segundo da execução da VM do Azure inclui o custo da licença de SQL Server.
[Benefício híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) permite que você use sua própria licença de SQL Server com uma VM que está executando o SQL Server. 

Benefício Híbrido do Azure permite o uso de licenças SQL Server com o Software Assurance ("licença qualificada") em máquinas virtuais do Azure. Com o Benefício Híbrido do Azure, os clientes não são cobrados pelo uso de uma licença SQL Server em uma VM. Mas eles ainda devem pagar pelo custo da computação em nuvem subjacente (ou seja, a taxa base), o armazenamento e os backups. Eles também devem pagar pela e/s associada ao uso dos serviços (conforme aplicável).

De acordo com os termos de produto da Microsoft: "os clientes devem indicar que estão usando o banco de dados SQL do Azure (Instância Gerenciada, Pool Elástico e Banco de Dados Individual), Azure Data Factory, SQL Server Integration Services ou SQL Server máquinas virtuais no Azure Benefício híbrido para SQL Server ao configurar cargas de trabalho no Azure. "

Para indicar o uso de Benefício Híbrido do Azure para SQL Server em uma VM do Azure e estar em conformidade, você tem três opções:

- Provisione uma máquina virtual usando uma imagem de SQL Server traga sua própria licença do Azure Marketplace. Essa opção está disponível somente para clientes que têm um Enterprise Agreement.
- Provisione uma máquina virtual usando uma imagem de SQL Server paga conforme o uso do Azure Marketplace e ative o Benefício Híbrido do Azure.
- SQL Server de instalação automática em uma VM do Azure, [Registre-se manualmente com o provedor de recursos de VM do SQL](virtual-machines-windows-sql-register-with-resource-provider.md)e ative benefício híbrido do Azure.

O tipo de licença de SQL Server é definido quando a VM é provisionada. Você pode alterá-lo a qualquer momento depois. Alternar entre modelos de licença não provoca nenhum tempo de inatividade, não reinicia a VM ou o serviço de SQL Server, não adiciona custos adicionais e entra em vigor imediatamente. Na verdade, a ativação de Benefício Híbrido do Azure *reduz* o custo.

## <a name="prerequisites"></a>Pré-requisitos

Alterar o modelo de licenciamento de sua VM SQL Server tem os seguintes requisitos: 

- Uma [assinatura do Azure](https://azure.microsoft.com/free/).
- Uma [vm SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) registrada com o [provedor de recursos de VM do SQL](virtual-machines-windows-sql-register-with-resource-provider.md).
- O [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) é um requisito para utilizar o [benefício híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/). 


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
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType AHUB
```

O trecho de código a seguir alterna seu modelo traga sua própria licença para o pré-pago:

```powershell-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType PAYG
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

A alteração do modelo de licença é:
   - Disponível somente para clientes com [Software Assurance](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-overview).
   - Com suporte apenas para as edições Standard e Enterprise do SQL Server. Não há suporte para alterações de licença para o Express, Web e Developer. 
   - Com suporte apenas para máquinas virtuais implantadas por meio do modelo de Azure Resource Manager. Não há suporte para máquinas virtuais implantadas por meio do modelo clássico. 
   - Disponível somente para instalações de nuvem pública. 
   - Somente com suporte em máquinas virtuais que têm uma única interface de rede (NIC). 


## <a name="known-errors"></a>Erros conhecidos

### <a name="the-resource-microsoftsqlvirtualmachinesqlvirtualmachinesresource-group-under-resource-group-resource-group-was-not-found"></a>O recurso ' Microsoft. SqlVirtualMachine/SqlVirtualMachines/\<Resource-Group > ' no grupo de recursos '\<> do grupo de recursos ' não foi encontrado.

Esse erro ocorre quando você tenta alterar o modelo de licença em uma VM SQL Server que não foi registrada com o provedor de recursos de VM do SQL:

`The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<resource-group>' under resource group '\<resource-group>' was not found. The property 'sqlServerLicenseType' cannot be found on this object. Verify that the property exists and can be set.`

Você precisará registrar sua assinatura com o provedor de recursos e [registrar sua VM de SQL Server com o provedor de recursos](virtual-machines-windows-sql-register-with-resource-provider.md). 


## <a name="the-virtual-machine-vmname-has-more-than-one-nic-associated"></a>A máquina virtual '\<vmname\>' tem mais de uma NIC associada

Esse erro ocorre em máquinas virtuais que têm mais de uma NIC. Remova uma das NICs antes de alterar o modelo de licenciamento. Embora você possa adicionar a NIC de volta à VM depois de alterar o modelo de licença, as operações no portal do Azure, como backup automático e aplicação de patches, não terão mais suporte. 


## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, veja os artigos seguintes: 

* [Visão geral de SQL Server em uma VM do Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [Perguntas frequentes para SQL Server em uma VM do Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Diretrizes de preços para SQL Server em uma VM do Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Notas de versão para SQL Server em uma VM do Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)


