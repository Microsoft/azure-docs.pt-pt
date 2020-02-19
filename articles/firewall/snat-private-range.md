---
title: Gamas de endereços IP privados Azure Firewall SNAT
description: Pode configurar gamas privadas de endereçoIP para que a firewall não o tráfego sNAT para esses endereços IP.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 01/09/2020
ms.author: victorh
ms.openlocfilehash: b190d07ceadea43ca572f5eb5be3eeeafa616971
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2020
ms.locfileid: "77444462"
---
# <a name="azure-firewall-snat-private-ip-address-ranges"></a>Gamas de endereços IP privados Azure Firewall SNAT

O Azure Firewall não snaT quando o endereço IP de destino está numa gama de endereços IP privado por [IANA RFC 1918](https://tools.ietf.org/html/rfc1918). 

Se a sua organização utilizar uma gama pública de endereços IP para redes privadas, o Azure Firewall irá snat o tráfego para um dos endereços IP privados firewall em AzureFirewallSubnet. No entanto, pode configurar o Azure Firewall para **não** sNAT a sua gama pública de endereços IP.

## <a name="configure-snat-private-ip-address-ranges"></a>Configure gamas de endereços IP privados SNAT

Pode utilizar o Azure PowerShell para especificar uma gama de endereços IP que a firewall não o snat.

### <a name="new-firewall"></a>Nova firewall

Para uma nova firewall, o comando Azure PowerShell é:

`New-AzFirewall -Name $GatewayName -ResourceGroupName $RG -Location $Location -VirtualNetworkName $vnet.Name -PublicIpName $LBPip.Name -PrivateRange @("IANAPrivateRanges","IPRange1", "IPRange2")`

> [!NOTE]
> O IANAPrivateRanges é expandido para os atuais incumprimentos no Firewall Azure, enquanto as outras gamas são adicionadas ao mesmo.

Para mais informações, consulte [New-AzFirewall](https://docs.microsoft.com/powershell/module/az.network/new-azfirewall?view=azps-3.3.0).

### <a name="existing-firewall"></a>Firewall existente

Para configurar uma firewall existente, utilize os seguintes comandos Azure PowerShell:

```azurepowershell
$azfw = Get-AzFirewall -ResourceGroupName "Firewall Resource Group name"
$azfw.PrivateRange = @(“IANAPrivateRanges”,“IPRange1”, “IPRange2”)
Set-AzFirewall -AzureFirewall $azfw
```

### <a name="templates"></a>Modelos

Pode adicionar o seguinte à secção `additionalProperties`:

```
"additionalProperties": {
                    "Network.SNAT.PrivateRanges": "IANAPrivateRanges , IPRange1, IPRange2"
                },
```

## <a name="next-steps"></a>Passos seguintes

- Aprenda a [implementar e configurar uma Firewall Azure](tutorial-firewall-deploy-portal.md).