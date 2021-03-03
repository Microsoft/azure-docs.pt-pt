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
ms.openlocfilehash: f61d45b6c46830064c3b58608e2eca0787d559c2
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101675117"
---
# <a name="quickstart-create-a-public-ip-address-using-azure-powershell"></a>Quickstart: Criar um endereço IP público utilizando a Azure PowerShell

Este artigo mostra-lhe como criar um recurso de endereço IP público usando a Azure PowerShell. Para obter mais informações sobre quais os recursos a que estes recursos podem estar associados, a diferença entre O SKU Básico e Padrão, e outras informações [relacionadas, consulte endereços IP públicos.](./public-ip-addresses.md)  Para este exemplo, centrar-nos-emos apenas nos endereços IPv4; para obter mais informações sobre endereços IPv6, consulte [IPv6 para Azure VNet](./ipv6-overview.md).

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
>O comando a seguir funciona para a versão 4.5.0 ou posterior do módulo Az.Network.  Para obter mais informações sobre os módulos Powershell atualmente utilizados, consulte a [documentação powerShellGet](/powershell/module/powershellget/?view=powershell-7.1).

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
> Para os módulos Az.Network com mais de 4.5.0, executar o comando acima sem especificar um parâmetro de zona para criar um endereço IP redundante de zona. 
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

Note que as opções acima para zonas são apenas seleções válidas em regiões com [Zonas de Disponibilidade.](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)

# <a name="standard-sku---no-zones"></a>[**SKU Padrão - Sem zonas**](#tab/option-create-public-ip-standard)

>[!NOTE]
>O comando a seguir funciona para a versão 4.5.0 ou posterior do módulo Az.Network.  Para obter mais informações sobre os módulos Powershell atualmente utilizados, consulte a [documentação powerShellGet](/powershell/module/powershellget/?view=powershell-7.1).

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

Esta seleção é válida em todas as regiões e é a seleção padrão para endereços IP públicos padrão em regiões sem [Zonas de Disponibilidade.](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)

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

Para obter mais detalhes sobre as variáveis individuais listadas acima, consulte [Gerir os endereços IP públicos](./virtual-network-public-ip-address.md#create-a-public-ip-address).

## <a name="next-steps"></a>Passos seguintes
- Associar um [endereço IP público a uma Máquina Virtual](./associate-public-ip-address-vm.md#azure-portal)
- Saiba mais sobre [endereços IP públicos](./public-ip-addresses.md#public-ip-addresses) em Azure.
- Saiba mais sobre todas as [definições de endereços IP públicos](virtual-network-public-ip-address.md#create-a-public-ip-address).