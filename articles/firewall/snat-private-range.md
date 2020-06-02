---
title: Gamas de endereços IP privados Azure Firewall SNAT
description: Pode configurar intervalos de endereço IP para SNAT.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 06/01/2020
ms.author: victorh
ms.openlocfilehash: 28ec61c4aefeacb8014e0a5d48d0259cf7fcf7f3
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/01/2020
ms.locfileid: "84267035"
---
# <a name="azure-firewall-snat-private-ip-address-ranges"></a>Gamas de endereços IP privados Azure Firewall SNAT

O Azure Firewall fornece SNAT automático para todo o tráfego de saída para endereços IP públicos. Por padrão, o Azure Firewall não sNAT com regras de rede quando o endereço IP de destino está em uma gama de endereços IP privada por [IANA RFC 1918](https://tools.ietf.org/html/rfc1918). As regras de aplicação são sempre aplicadas usando um [representante transparente,](https://wikipedia.org/wiki/Proxy_server#Transparent_proxy) independentemente do endereço IP de destino.

Esta lógica funciona bem quando se encaminha o tráfego diretamente para a Internet. No entanto, se tiver ativado [um túnel forçado,](forced-tunneling.md)o tráfego ligado à Internet é SNATed para um dos endereços IP privados de firewall em AzureFirewallSubnet, escondendo a fonte da sua firewall no local.

Se a sua organização utilizar um intervalo de endereços IP público para redes privadas, o Azure Firewall SNATs faz o tráfego para um dos endereços IP privados de firewall em AzureFirewallSubnet. No entanto, pode configurar o Azure Firewall para **não** SNAT o seu intervalo de endereço IP público.

Para configurar o Azure Firewall para nunca o SNAT, independentemente do endereço IP de destino, utilize **0.0.0.0/0** como o seu intervalo de endereço IP privado. Com esta configuração, o Azure Firewall nunca poderá encaminhar o tráfego diretamente para a Internet. Para configurar a firewall para sempre SNAT, independentemente do endereço de destino, utilize **255.255.255.255/32** como a sua gama de endereços IP privado.

## <a name="configure-snat-private-ip-address-ranges---azure-powershell"></a>Configurar gamas de endereços IP privados SNAT - Azure PowerShell

Pode utilizar o Azure PowerShell para especificar os intervalos de endereço IP privados para a firewall.

### <a name="new-firewall"></a>Nova firewall

Para uma nova firewall, o comando Azure PowerShell é:

`New-AzFirewall -Name $GatewayName -ResourceGroupName $RG -Location $Location -VirtualNetworkName $vnet.Name -PublicIpName $LBPip.Name -PrivateRange @("IANAPrivateRanges","IPRange1", "IPRange2")`

> [!NOTE]
> IANAPrivateRanges é expandido para os padrãos atuais no Azure Firewall enquanto as outras gamas são adicionadas ao mesmo.

Para mais informações, consulte [New-AzFirewall](https://docs.microsoft.com/powershell/module/az.network/new-azfirewall?view=azps-3.3.0).

### <a name="existing-firewall"></a>Firewall existente

Para configurar uma firewall existente, utilize os seguintes comandos Azure PowerShell:

```azurepowershell
$azfw = Get-AzFirewall -ResourceGroupName "Firewall Resource Group name"
$azfw.PrivateRange = @("IANAPrivateRanges","IPRange1", "IPRange2")
Set-AzFirewall -AzureFirewall $azfw
```

### <a name="templates"></a>Modelos

Pode adicionar o seguinte à `additionalProperties` secção:

```
"additionalProperties": {
                    "Network.SNAT.PrivateRanges": "IANAPrivateRanges , IPRange1, IPRange2"
                },
```

## <a name="configure-snat-private-ip-address-ranges---azure-portal"></a>Configurar gamas de endereços IP privados SNAT - Portal Azure

Pode utilizar o portal Azure para especificar intervalos de endereços IP privados para a firewall.

1. Selecione o seu grupo de recursos e, em seguida, selecione a sua firewall.
2. Na página **'Visão Geral',** **Gamas IP Privadas**, selecione o valor padrão **IANA RFC 1918**.

   A página **de prefixos IP privados** de edição abre:

   :::image type="content" source="media/snat-private-range/private-ip.png" alt-text="Editar prefixos IP privados":::

1. Por predefinição, **o IANAPrivateRanges** está configurado.
2. Edite os intervalos de endereço IP privados para o seu ambiente e, em seguida, **selecione Guardar**.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [o túnel forçado Azure Firewall](forced-tunneling.md).