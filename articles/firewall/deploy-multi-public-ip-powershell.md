---
title: Implantar o Firewall do Azure com vários endereços IP públicos usando Azure PowerShell
description: Neste artigo, você aprenderá a implantar um firewall do Azure com vários endereços IP públicos usando o Azure PowerShell.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 07/19/2019
ms.author: victorh
ms.openlocfilehash: ba2736ae69d0bf7feff5f852da2446bfa7a722a6
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325239"
---
# <a name="deploy-an-azure-firewall-with-multiple-public-ip-addresses-using-azure-powershell"></a>Implantar um firewall do Azure com vários endereços IP públicos usando Azure PowerShell

Esse recurso habilita os seguintes cenários:

- **DNAT** – você pode converter várias instâncias de porta padrão em seus servidores de back-end. Por exemplo, se você tiver dois endereços IP públicos, poderá converter a porta TCP 3389 (RDP) para ambos os endereços IP.
- **SNAT** -portas adicionais estão disponíveis para conexões SNAT de saída, reduzindo o potencial para esgotamento de porta SNAT. Neste momento, o Firewall do Azure seleciona aleatoriamente o endereço IP público de origem a ser usado para uma conexão. Se você tiver alguma filtragem downstream em sua rede, será necessário permitir todos os endereços IP públicos associados ao firewall.
 
O Firewall do Azure com vários endereços IP públicos está disponível por meio do portal do Azure, Azure PowerShell, CLI do Azure, REST e modelos. Você pode implantar um firewall do Azure com até 100 endereços IP públicos.

Os exemplos de Azure PowerShell a seguir mostram como você pode configurar, adicionar e remover endereços IP públicos para o Firewall do Azure.

> [!NOTE]
> Não é possível remover a primeira ipConfiguration da página de configuração de endereço IP público do firewall do Azure. Se você quiser modificar o endereço IP, poderá usar Azure PowerShell.

## <a name="create-a-firewall-with-two-or-more-public-ip-addresses"></a>Criar um firewall com dois ou mais endereços IP públicos

Este exemplo cria um firewall anexado à *vnet* da rede virtual com dois endereços IP públicos.

```azurepowershell
$rgName = "resourceGroupName"

$vnet = Get-AzVirtualNetwork `
  -Name "vnet" `
  -ResourceGroupName $rgName

$pip1 = New-AzPublicIpAddress `
  -Name "AzFwPublicIp1" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

$pip2 = New-AzPublicIpAddress `
  -Name "AzFwPublicIp2" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

New-AzFirewall `
  -Name "azFw" `
  -ResourceGroupName $rgName `
  -Location centralus `
  -VirtualNetwork $vnet `
  -PublicIpAddress @($pip1, $pip2)
```

## <a name="add-a-public-ip-address-to-an-existing-firewall"></a>Adicionar um endereço IP público a um firewall existente

Neste exemplo, o endereço IP público *azFwPublicIp1* é anexado ao firewall.

```azurepowershell
$pip = New-AzPublicIpAddress `
  -Name "azFwPublicIp1" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

$azFw = Get-AzFirewall `
  -Name "AzureFirewall" `
  -ResourceGroupName "rg"

$azFw.AddPublicIpAddress($pip)

$azFw | Set-AzFirewall
```

## <a name="remove-a-public-ip-address-from-an-existing-firewall"></a>Remover um endereço IP público de um firewall existente

Neste exemplo, o endereço IP público *azFwPublicIp1* é desanexado do firewall.

```azurepowershell
$pip = Get-AzPublicIpAddress `
  -Name "azFwPublicIp1" `
  -ResourceGroupName "rg"

$azFw = Get-AzFirewall `
  -Name "AzureFirewall" `
  -ResourceGroupName "rg"

$azFw.RemovePublicIpAddress($pip)

$azFw | Set-AzFirewall
```

## <a name="next-steps"></a>Passos Seguintes

* [Tutorial: Monitorar logs de firewall do Azure](./tutorial-diagnostics.md)
