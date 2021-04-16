---
title: Gamas de endereços IP privados Azure Firewall SNAT
description: Pode configurar intervalos de endereço IP para SNAT.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 04/14/2021
ms.author: victorh
ms.openlocfilehash: 91d4d631376c03b668128936f3840ce1119f9b6f
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482755"
---
# <a name="azure-firewall-snat-private-ip-address-ranges"></a>Gamas de endereços IP privados Azure Firewall SNAT

O Azure Firewall fornece SNAT automático para todo o tráfego de saída para endereços IP públicos. Por padrão, o Azure Firewall não sNAT com regras de rede quando o endereço IP de destino está em uma gama de endereços IP privada por [IANA RFC 1918](https://tools.ietf.org/html/rfc1918). As regras de aplicação são sempre aplicadas usando um [representante transparente,](https://wikipedia.org/wiki/Proxy_server#Transparent_proxy) qualquer que seja o endereço IP de destino.

Esta lógica funciona bem quando se encaminha o tráfego diretamente para a Internet. No entanto, se tiver ativado [um túnel forçado,](forced-tunneling.md)o tráfego ligado à Internet é SNATed para um dos endereços IP privados de firewall em AzureFirewallSubnet, escondendo a fonte da sua firewall no local.

Se a sua organização utilizar um intervalo de endereços IP público para redes privadas, o Azure Firewall SNATs faz o tráfego para um dos endereços IP privados de firewall em AzureFirewallSubnet. No entanto, pode configurar o Azure Firewall para **não** SNAT o seu intervalo de endereço IP público. Por exemplo, para especificar um endereço IP individual pode especiá-lo desta forma: `192.168.1.10` . Para especificar uma gama de endereços IP, pode especiá-lo desta forma: `192.168.1.0/24` .

- Para configurar o Azure Firewall para **nunca** o SNAT, independentemente do endereço IP de destino, utilize **0.0.0.0/0** como o seu intervalo de endereço IP privado. Com esta configuração, o Azure Firewall nunca poderá encaminhar o tráfego diretamente para a Internet. 

- Para configurar a firewall para **sempre** SNAT, independentemente do endereço de destino, utilize **255.255.255.255/32** como a sua gama de endereços IP privado.

> [!IMPORTANT]
> O intervalo de endereços privados que especifica apenas aplica-se às regras da rede. Atualmente, as regras de aplicação sempre SNAT.

> [!IMPORTANT]
> Se pretender especificar os seus próprios intervalos de endereços IP privados e manter os intervalos de endereços IANA RFC 1918 padrão, certifique-se de que a sua lista personalizada ainda inclui a gama IANA RFC 1918. 

Pode configurar os endereços IP privados SNAT utilizando os seguintes métodos. Tem de configurar os endereços privados SNAT utilizando o método adequado para a sua configuração. As firewalls associadas a uma política de firewall devem especificar o alcance da apólice e não a utilização `AdditionalProperties` .


|Método            |Usando regras clássicas  |Usando a política de firewall  |
|---------|---------|---------|
|Portal do Azure     | [apoiado](#classic-rules-3)| [apoiado](#firewall-policy-1)|
|Azure PowerShell     |[configuração `PrivateRange`](#classic-rules)|atualmente sem apoio|
|CLI do Azure|[configuração `--private-ranges`](#classic-rules-1)|atualmente sem apoio|
|Modelo ARM     |[configurar `AdditionalProperties` na propriedade firewall](#classic-rules-2)|[configurar `snat/privateRanges` na política de firewall](#firewall-policy)|


## <a name="configure-snat-private-ip-address-ranges---azure-powershell"></a>Configurar gamas de endereços IP privados SNAT - Azure PowerShell
### <a name="classic-rules"></a>Regras clássicas

Pode utilizar o Azure PowerShell para especificar os intervalos de endereço IP privados para a firewall.

> [!NOTE]
> A propriedade firewall `PrivateRange` é ignorada para firewalls associados a uma Política de Firewall. Você deve usar a `SNAT` propriedade em como descrito em `firewallPolicies` [ConfiguraR Gamas de endereços IP privados SNAT - modelo ARM](#firewall-policy).

#### <a name="new-firewall"></a>Nova firewall

Para uma nova firewall usando regras clássicas, o cmdlet Azure PowerShell é:

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

Para mais informações, consulte [New-AzFirewall](/powershell/module/az.network/new-azfirewall).

#### <a name="existing-firewall"></a>Firewall existente

Para configurar uma firewall existente utilizando regras clássicas, utilize os seguintes cmdlets Azure PowerShell:

```azurepowershell
$azfw = Get-AzFirewall -Name '<fw-name>' -ResourceGroupName '<resourcegroup-name>'
$azfw.PrivateRange = @("IANAPrivateRanges","192.168.1.0/24", "192.168.1.10")
Set-AzFirewall -AzureFirewall $azfw
```

## <a name="configure-snat-private-ip-address-ranges---azure-cli"></a>Configurar gamas de endereços IP privados SNAT - Azure CLI
### <a name="classic-rules"></a>Regras clássicas

Pode utilizar o Azure CLI para especificar intervalos de endereços IP privados para a firewall utilizando regras clássicas. 

#### <a name="new-firewall"></a>Nova firewall

Para uma nova firewall usando regras clássicas, o comando Azure CLI é:

```azurecli-interactive
az network firewall create \
-n <fw-name> \
-g <resourcegroup-name> \
--private-ranges 192.168.1.0/24 192.168.1.10 IANAPrivateRanges
```

> [!NOTE]
> A implementação do Azure Firewall utilizando o comando Azure CLI `az network firewall create` requer medidas de configuração adicionais para criar endereços IP públicos e configuração IP. Consulte [implementar e configurar a Firewall Azure utilizando o Azure CLI](deploy-cli.md) para obter um guia de implantação completo.

> [!NOTE]
> IANAPrivateRanges é expandido para os padrãos atuais no Azure Firewall enquanto as outras gamas são adicionadas ao mesmo. Para manter o PADRÃO IANAPrivateRanges na sua especificação de gama privada, deve permanecer na sua `private-ranges` especificação como mostrado nos seguintes exemplos.

#### <a name="existing-firewall"></a>Firewall existente

Para configurar uma firewall existente usando regras clássicas, o comando Azure CLI é:

```azurecli-interactive
az network firewall update \
-n <fw-name> \
-g <resourcegroup-name> \
--private-ranges 192.168.1.0/24 192.168.1.10 IANAPrivateRanges
```

## <a name="configure-snat-private-ip-address-ranges---arm-template"></a>Configurar gamas de endereços IP privados SNAT - modelo ARM
### <a name="classic-rules"></a>Regras clássicas

Para configurar o SNAT durante a implementação do modelo ARM, pode adicionar o seguinte à `additionalProperties` propriedade:

```json
"additionalProperties": {
   "Network.SNAT.PrivateRanges": "IANAPrivateRanges , IPRange1, IPRange2"
},
```
### <a name="firewall-policy"></a>Política de firewall

A Azure Firewalls associada a uma política de firewall tem suportado gamas privadas SNAT desde a versão API 2020-11-01. Atualmente, pode utilizar um modelo para atualizar a gama privada SNAT na Política de Firewall. A amostra que se segue configura a firewall para **sempre** o tráfego da rede SNAT:

```json
{ 

            "type": "Microsoft.Network/firewallPolicies", 
            "apiVersion": "2020-11-01", 
            "name": "[parameters('firewallPolicies_DatabasePolicy_name')]", 
            "location": "eastus", 
            "properties": { 
                "sku": { 
                    "tier": "Standard" 
                }, 
                "snat": { 
                    "privateRanges": [255.255.255.255/32] 
                } 
            } 
```

## <a name="configure-snat-private-ip-address-ranges---azure-portal"></a>Configurar gamas de endereços IP privados SNAT - Portal Azure
### <a name="classic-rules"></a>Regras clássicas

Pode utilizar o portal Azure para especificar intervalos de endereços IP privados para a firewall.

1. Selecione o seu grupo de recursos e, em seguida, selecione a sua firewall.
2. Na página **'Visão Geral',** **Gamas IP Privadas**, selecione o valor padrão **IANA RFC 1918**.

   A página **de prefixos IP privados** de edição abre:

   :::image type="content" source="media/snat-private-range/private-ip.png" alt-text="Editar prefixos IP privados":::

1. Por predefinição, **o IANAPrivateRanges** está configurado.
2. Edite os intervalos de endereço IP privados para o seu ambiente e, em seguida, **selecione Guardar**.

### <a name="firewall-policy"></a>Política de firewall

1.  Selecione o seu grupo de recursos e, em seguida, selecione a sua política de firewall.
2.  Selecione **gamas IP privadas (SNAT)** na coluna **Definições.**

    Por **predefinição, Utilize o comportamento SNAT da Política de Firewall Azure padrão.** 
3. Para personalizar a configuração SNAT, limpe a caixa de verificação e em **Perform SNAT** selecione as condições para executar O SNAT para o seu ambiente.
      :::image type="content" source="media/snat-private-range/private-ip-ranges-snat.png" alt-text="Gamas IP privadas (SNAT)":::


4.   Selecione **Aplicar**.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [o túnel forçado Azure Firewall](forced-tunneling.md).