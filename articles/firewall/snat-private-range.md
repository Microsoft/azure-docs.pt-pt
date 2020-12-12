---
title: Gamas de endereços IP privados Azure Firewall SNAT
description: Pode configurar intervalos de endereço IP para SNAT.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 11/16/2020
ms.author: victorh
ms.openlocfilehash: c5613dda7adbbc47f989bc2a772777e716620b3c
ms.sourcegitcommit: fa807e40d729bf066b9b81c76a0e8c5b1c03b536
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/11/2020
ms.locfileid: "97348038"
---
# <a name="azure-firewall-snat-private-ip-address-ranges"></a>Gamas de endereços IP privados Azure Firewall SNAT

O Azure Firewall fornece SNAT automático para todo o tráfego de saída para endereços IP públicos. Por padrão, o Azure Firewall não sNAT com regras de rede quando o endereço IP de destino está em uma gama de endereços IP privada por [IANA RFC 1918](https://tools.ietf.org/html/rfc1918). As regras de aplicação são sempre aplicadas usando um [representante transparente,](https://wikipedia.org/wiki/Proxy_server#Transparent_proxy) qualquer que seja o endereço IP de destino.

Esta lógica funciona bem quando se encaminha o tráfego diretamente para a Internet. No entanto, se tiver ativado [um túnel forçado,](forced-tunneling.md)o tráfego ligado à Internet é SNATed para um dos endereços IP privados de firewall em AzureFirewallSubnet, escondendo a fonte da sua firewall no local.

Se a sua organização utilizar um intervalo de endereços IP público para redes privadas, o Azure Firewall SNATs faz o tráfego para um dos endereços IP privados de firewall em AzureFirewallSubnet. No entanto, pode configurar o Azure Firewall para **não** SNAT o seu intervalo de endereço IP público. Por exemplo, para especificar um endereço IP individual pode especiá-lo desta forma: `192.168.1.10` . Para especificar uma gama de endereços IP, pode especiá-lo desta forma: `192.168.1.0/24` .

- Para configurar o Azure Firewall para **nunca** o SNAT, independentemente do endereço IP de destino, utilize **0.0.0.0/0** como o seu intervalo de endereço IP privado. Com esta configuração, o Azure Firewall nunca poderá encaminhar o tráfego diretamente para a Internet. 

- Para configurar a firewall para **sempre** SNAT, independentemente do endereço de destino, utilize **255.255.255.255/32** como a sua gama de endereços IP privado.

> [!IMPORTANT]
> Se pretender especificar os seus próprios intervalos de endereços IP privados e manter os intervalos de endereços IANA RFC 1918 padrão, certifique-se de que a sua lista personalizada ainda inclui a gama IANA RFC 1918. 

## <a name="configure-snat-private-ip-address-ranges---azure-powershell"></a>Configurar gamas de endereços IP privados SNAT - Azure PowerShell

Pode utilizar o Azure PowerShell para especificar os intervalos de endereço IP privados para a firewall.

### <a name="new-firewall"></a>Nova firewall

Para uma nova firewall, o cmdlet Azure PowerShell é:

```azurepowershell
$azFw = @{
    Name               = '<fw-name>'
    ResourceGroupName  = '<resourcegroup-name>'
    Location           = '<location>'
    VirtualNetworkName = '<vnet-name>'
    PublicIpName       = '<public-ip-name>'
    PrivateRange       = @("IANAPrivateRanges", "192.168.1.0/24", "192.168.1.10")
}

New-AzFirewall @azFw
```
> [!NOTE]
> A implementação do Azure Firewall `New-AzFirewall` requer um endereço VNet e IP público existente. Consulte [implementar e configurar a Firewall Azure utilizando o Azure PowerShell](deploy-ps.md) para obter um guia de implantação completo.

> [!NOTE]
> IANAPrivateRanges é expandido para os padrãos atuais no Azure Firewall enquanto as outras gamas são adicionadas ao mesmo. Para manter o PADRÃO IANAPrivateRanges na sua especificação de gama privada, deve permanecer na sua `PrivateRange` especificação como mostrado nos seguintes exemplos.

Para mais informações, consulte [New-AzFirewall](/powershell/module/az.network/new-azfirewall?view=azps-3.3.0).

### <a name="existing-firewall"></a>Firewall existente

Para configurar uma firewall existente, utilize os seguintes cmdlets Azure PowerShell:

```azurepowershell
$azfw = Get-AzFirewall -Name '<fw-name>' -ResourceGroupName '<resourcegroup-name>'
$azfw.PrivateRange = @("IANAPrivateRanges","192.168.1.0/24", "192.168.1.10")
Set-AzFirewall -AzureFirewall $azfw
```

## <a name="configure-snat-private-ip-address-ranges---azure-cli"></a>Configurar gamas de endereços IP privados SNAT - Azure CLI

Pode utilizar o Azure CLI para especificar intervalos de endereços IP privados para a firewall.

### <a name="new-firewall"></a>Nova firewall

Para uma nova firewall, o comando Azure CLI é:

```azurecli-interactive
az network firewall create \
-n <fw-name> \
-g <resourcegroup-name> \
--private-ranges 192.168.1.0/24 192.168.1.10 IANAPrivateRanges
```

> [!NOTE]
> A implementação do Azure Firewall utilizando o comando Azure CLI `az network firewall create` requer medidas de configuração adicionais para criar endereços IP públicos e configuração IP. Consulte [implementar e configurar a Firewall Azure utilizando o Azure CLI](deploy-cli.md) para obter um guia de implantação completo.

> [!NOTE]
> IANAPrivateRanges é expandido para os padrãos atuais no Azure Firewall enquanto as outras gamas são adicionadas ao mesmo. Para manter o PADRÃO IANAPrivateRanges na sua especificação de gama privada, deve permanecer na sua `PrivateRange` especificação como mostrado nos seguintes exemplos.

### <a name="existing-firewall"></a>Firewall existente

Para configurar uma firewall existente, o comando Azure CLI é:

```azurecli-interactive
az network firewall update \
-n <fw-name> \
-g <resourcegroup-name> \
--private-ranges 192.168.1.0/24 192.168.1.10 IANAPrivateRanges
```

## <a name="configure-snat-private-ip-address-ranges---arm-template"></a>Configurar gamas de endereços IP privados SNAT - Modelo ARM

Para configurar o SNAT durante a implementação do modelo ARM, pode adicionar o seguinte à `additionalProperties` propriedade:

```json
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