---
title: Gamas de endereços IP privados Azure Firewall SNAT
description: Pode configurar gamas privadas de endereçoIP para que a firewall não o tráfego sNAT para esses endereços IP.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 03/20/2020
ms.author: victorh
ms.openlocfilehash: ed8cef00b7de67458c607373c724a3717f14a7cb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80064817"
---
# <a name="azure-firewall-snat-private-ip-address-ranges"></a>Gamas de endereços IP privados Azure Firewall SNAT

O Azure Firewall não snaT com regras de rede quando o endereço IP de destino está numa gama de endereços IP privado por [IANA RFC 1918](https://tools.ietf.org/html/rfc1918). As regras de aplicação são sempre aplicadas utilizando um [representante transparente,](https://wikipedia.org/wiki/Proxy_server#Transparent_proxy) independentemente do endereço IP de destino.

Se a sua organização utilizar uma gama pública de endereços IP para redes privadas, o Azure Firewall SNATs snats o tráfego para um dos endereços IP privados firewall em AzureFirewallSubnet. No entanto, pode configurar o Azure Firewall para **não** sNAT a sua gama pública de endereços IP.

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
$azfw.PrivateRange = @("IANAPrivateRanges","IPRange1", "IPRange2")
Set-AzFirewall -AzureFirewall $azfw
```

### <a name="templates"></a>Modelos

Pode adicionar o seguinte `additionalProperties` à secção:

```
"additionalProperties": {
                    "Network.SNAT.PrivateRanges": "IANAPrivateRanges , IPRange1, IPRange2"
                },
```

## <a name="next-steps"></a>Passos seguintes

- Aprenda a [implementar e configurar uma Firewall Azure](tutorial-firewall-deploy-portal.md).