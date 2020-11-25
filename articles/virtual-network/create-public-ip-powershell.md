---
title: Criar um IP público - Azure PowerShell
description: Saiba como criar um IP público usando a Azure PowerShell
services: virtual-network
documentationcenter: na
author: blehr
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2020
ms.author: blehr
ms.openlocfilehash: 99e79e4d094fe6e93510d139d2f4d08f260102df
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96010049"
---
# <a name="quickstart-create-a-public-ip-address-using-azure-powershell"></a>Quickstart: Criar um endereço IP público utilizando a Azure PowerShell

Este artigo mostra-lhe como criar um recurso de endereço IP público usando a Azure PowerShell. Para obter mais informações sobre quais os recursos a que estes recursos podem estar associados, a diferença entre O SKU Básico e Padrão, e outras informações [relacionadas, consulte endereços IP públicos.](https://docs.microsoft.com/azure/virtual-network/public-ip-addresses)  Para este exemplo, centrar-nos-emos apenas nos endereços IPv4; para obter mais informações sobre endereços IPv6, consulte [IPv6 para Azure VNet](https://docs.microsoft.com/azure/virtual-network/ipv6-overview).

## <a name="prerequisites"></a>Pré-requisitos

- Azure PowerShell instalado localmente ou Azure Cloud Shell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar o PowerShell localmente, este artigo requer a versão 5.4.1 ou posterior do módulo Azure PowerShell. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-Az-ps). Se estiver a executar o PowerShell localmente, também precisa de correr `Connect-AzAccount` para criar uma ligação com o Azure.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos.

Crie um grupo de recursos com [o New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) nomeado **myResourceGroup** na localização **eastus2.**

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$loc = 'eastus2'

New-AzResourceGroup -Name $rg -Location $loc
```
## <a name="create-public-ip"></a>Criar IP público

---
# <a name="standard-sku---using-zones"></a>[**SKU Padrão - Utilização de zonas**](#tab/option-create-public-ip-standard-zones)

>[!NOTE]
>O seguinte comando funciona para a versão API 2020-08-01 ou posterior.  Para obter mais informações sobre a versão API atualmente utilizada, consulte os [Fornecedores e Tipos de Recursos.](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types)

Utilize [o New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) para criar um endereço IP público redundante de zona padrão chamado **myStandardZRPublicIP** no **myResourceGroup**.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$loc = 'eastus2'
$pubIP = 'myStandardZRPublicIP'
$sku = 'Standard'
$alloc = 'Static'
$zone = 1,2,3

New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $alloc -SKU $sku -zone $zone
```
> [!IMPORTANT]
> Para versões da API com mais de 2020-08-01, executar o comando acima sem especificar um parâmetro de zona para criar um endereço IP redundante de zona. 
>

Para criar um endereço IP público zonal padrão na Zona 2 denominado **myStandardZonalPublicIP** no **myResourceGroup,** utilize o seguinte comando:

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$loc = 'eastus2'
$pubIP = 'myStandardZonalPublicIP'
$sku = 'Standard'
$alloc = 'Static'
$zone = 2

New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $alloc -SKU $sku -zone $zone
```

Note que as opções acima para zonas são apenas seleções válidas em regiões com [Zonas de Disponibilidade.](https://docs.microsoft.com/azure/availability-zones/az-overview?toc=/azure/virtual-network/toc.json#availability-zones)

# <a name="standard-sku---no-zones"></a>[**SKU Padrão - Sem zonas**](#tab/option-create-public-ip-standard)

>[!NOTE]
>O seguinte comando funciona para a versão API 2020-08-01 ou posterior.  Para obter mais informações sobre a versão API atualmente utilizada, consulte os [Fornecedores e Tipos de Recursos.](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types)

Utilize [o New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) para criar um endereço IP público padrão como um recurso não-zonal chamado **myStandardPublicIP** no **myResourceGroup**.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$loc = 'eastus2'
$pubIP = 'myStandardPublicIP'
$sku = 'Standard'
$alloc = 'Static'

New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $alloc -SKU $sku
```

Esta seleção é válida em todas as regiões e é a seleção padrão para endereços IP públicos padrão em regiões sem [Zonas de Disponibilidade.](https://docs.microsoft.com/azure/availability-zones/az-overview?toc=/azure/virtual-network/toc.json#availability-zones)

# <a name="basic-sku"></a>[**SKU Básico**](#tab/option-create-public-ip-basic)

Utilize [o New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) para criar um endereço IP público estático básico chamado **myBasicPublicIP** no **myResourceGroup**.  Os IPs públicos básicos não têm o conceito de zonas de disponibilidade.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$loc = 'eastus2'
$pubIP = 'myBasicPublicIP'
$sku = 'Basic'
$alloc = 'Static'

New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $alloc -SKU $sku
```
Se for aceitável que o endereço IP se altere ao longo do tempo, a atribuição de IP **dinâmico** pode ser selecionada alterando o Método de Atribuição para 'Dynamic'.

---

## <a name="additional-information"></a>Informações adicionais 

Para obter mais detalhes sobre as variáveis individuais listadas acima, consulte [Gerir os endereços IP públicos](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address#create-a-public-ip-address).

## <a name="next-steps"></a>Passos seguintes
- Associar um [endereço IP público a uma Máquina Virtual](https://docs.microsoft.com/azure/virtual-network/associate-public-ip-address-vm#azure-portal)
- Saiba mais sobre [endereços IP públicos](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) em Azure.
- Saiba mais sobre todas as [definições de endereços IP públicos](virtual-network-public-ip-address.md#create-a-public-ip-address).
