---
title: 'Quickstart: Criar e configurar o Servidor de Rota usando a Azure PowerShell'
description: Neste arranque rápido, aprende-se a criar e configurar um Servidor de Rota utilizando o Azure PowerShell.
services: route-server
author: duongau
ms.service: route-server
ms.topic: quickstart
ms.date: 03/02/2021
ms.author: duau
ms.openlocfilehash: e302cb9da410487dbea4ec5c5b256c4cb5dd186f
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102566384"
---
# <a name="quickstart-create-and-configure-route-server-using-azure-powershell"></a>Quickstart: Criar e configurar o Servidor de Rota usando a Azure PowerShell

Este artigo ajuda-o a configurar o Azure Route Server para espreitar com um Aparelho Virtual de Rede (NVA) na sua rede virtual utilizando o PowerShell. O Azure Route Server aprenderá as rotas a partir do NVA e programá-las-á nas máquinas virtuais da rede virtual. O Azure Route Server também anunciará as rotas de rede virtuais para o NVA. Para mais informações, leia [o Azure Route Server](overview.md).

> [!IMPORTANT]
> O Azure Route Server (Preview) encontra-se atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Certifique-se de que tem os mais recentes módulos PowerShell ou pode utilizar a Azure Cloud Shell no portal.
* Reveja os limites de [serviço para O Servidor de Rota Azure](route-server-faq.md#limitations).

## <a name="create-a-route-server"></a>Criar um servidor de rota

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Inicie sessão na sua conta do Azure e selecione a sua subscrição.

[!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]

### <a name="create-a-resource-group-and-virtual-network"></a>Criar um grupo de recursos e uma rede virtual

Antes de criar um Azure Route Server, precisará de uma rede virtual para hospedar a implementação. Utilize o comando seguinte para criar um grupo de recursos e uma rede virtual. Se já tiver uma rede virtual, pode saltar para a secção seguinte.

```azurepowershell-interactive
New-AzResourceGroup –Name “RouteServerRG” -Location “West US”
New-AzVirtualNetwork –ResourceGroupName “RouteServerRG -Location “West US” -Name myVirtualNetwork –AddressPrefix 10.0.0.0/16
```

### <a name="add-a-subnet"></a>Adicionar uma sub-rede

1. Adicione uma sub-rede chamada *RouteServerSubnet* para implementar o Azure Route Server. Esta sub-rede é uma sub-rede dedicada apenas para O Servidor de Rota Azure. O RouteServerSubnet deve ser /27 ou um prefixo mais curto (como /26, /25), ou receberá uma mensagem de erro quando adicionar o Azure Route Server.

    ```azurepowershell-interactive
    $vnet = Get-AzVirtualNetwork –Name “myVirtualNetwork” - ResourceGroupName “RouteServerRG”
    Add-AzVirtualNetworkSubnetConfig –Name “RouteServerSubnet” -AddressPrefix 10.0.0.0/24 -VirtualNetwork $vnet
    $vnet | Set-AzVirtualNetwork
    ```

1. Obtenha o ID RouteServerSubnet. Para ver o ID de todos os subredes da rede virtual, utilize este comando:

    ```azurepowershell-interactive
    $vnet = Get-AzVirtualNetwork –Name “vnet_name” -ResourceGroupName “
    $vnet.Subnets
    ```

O ID RouteServerSubnet parece o seguinte:

`/subscriptions/<subscriptionID>/resourceGroups/RouteServerRG/providers/Microsoft.Network/virtualNetworks/myVirtualNetwork/subnets/RouteServerSubnet`

## <a name="create-the-route-server"></a>Criar o Servidor de Rota

Crie o Servidor de Rota com este comando:

```azurepowershell-interactive 
New-AzRouteServer -RouteServerName myRouteServer -ResourceGroupName RouteServerRG -Location "West US” -HostedSubnet “RouteServerSubnet_ID”
```

A localização precisa de corresponder à localização da sua rede virtual. O HostedSubnet é o ID RouteServerSubnet que obteve na secção anterior.

## <a name="create-peering-with-an-nva"></a>Criar olhando com um NVA

Utilize o seguinte comando para estabelecer o perspível BGP do Servidor de Rota para o NVA:

```azurepowershell-interactive 
Add-AzRouteServerPeer -PeerName "myNVA" -PeerIp "nva_ip" -PeerAsn "nva_asn" -RouteServerName myRouteServer -ResourceGroupName RouteServerRG
```

"nva_ip" é o IP de rede virtual atribuído à NVA. "nva_asn" é o Número do Sistema Autónomo (ASN) configurado na NVA. A ASN pode ser qualquer número de 16 bits que não os do intervalo de 65515-65520. Esta gama de ASNs é reservada pela Microsoft.

Para configurar o seu espreitamento com diferentes NVA ou outro caso da mesma NVA para redundância, utilize este comando:

```azurepowershell-interactive 
Add-AzRouteServerPeer -PeerName "NVA2_name" -PeerIp "nva2_ip" -PeerAsn "nva2_asn" -RouteServerName myRouteServer -ResourceGroupName RouteServerRG 
```

## <a name="complete-the-configuration-on-the-nva"></a>Complete a configuração no NVA

Para completar a configuração no NVA e ativar as sessões de BGP, precisa do IP e do ASN do Azure Route Server. Pode obter esta informação usando este comando:

```azurepowershell-interactive 
Get-AzRouteServer -RouterServerName myRouteServer -ResourceGroupName RouteServerRG
```

A saída tem as seguintes informações:

``` 
RouteServerAsn : 65515
RouteServerIps : {10.5.10.4, 10.5.10.5}
```

## <a name="configure-route-exchange"></a><a name = "route-exchange"></a>Configure troca de rotas

Se tiver um gateway ExpressRoute e uma porta de entrada Azure VPN no mesmo VNet e quiser que troquem rotas, pode ativar a troca de rotas no Azure Route Server.

1. Para ativar a troca de rotas entre o Azure Route Server e o gateway(s) utilize este comando:

```azurepowershell-interactive 
Update-AzRouteServer -RouteServerName myRouteServer -ResourceGroupName RouteServerRG -AllowBranchToBranchTraffic 
```

2. Para desativar a troca de rotas entre o Azure Route Server e o gateway(s) utilize este comando:

```azurepowershell-interactive 
Update-AzRouteServer -RouteServerName myRouteServer -ResourceGroupName RouteServerRG
```

## <a name="troubleshooting"></a>Resolução de problemas

Pode ver as rotas anunciadas e recebidas pelo Azure Route Server com este comando:

```azurepowershell-interactive
Get-AzRouteServerPeerAdvertisedRoute
Get-AzRouteServerPeerLearnedRoute
```
## <a name="clean-up-resources"></a>Limpar os recursos

Se já não necessitar do Servidor de Rota Azure, utilize estes comandos para remover o perspível BGP e, em seguida, remova o Servidor de Rota. 

1. Remova o espreitamento BGP entre o Azure Route Server e um NVA com este comando:

```azurepowershell-interactive 
Remove-AzRouteServerPeer -PeerName “nva_name” -RouteServerName myRouteServer -ResourceGroupName RouteServerRG 
```

2. Remova o Servidor de Rota azul com este comando:

```azurepowershell-interactive 
Remove-AzRouteServer -RouteServerName myRouteServer -ResourceGroupName RouteServerRG
```

## <a name="next-steps"></a>Passos seguintes

Depois de criar o Azure Route Server, continue a aprender como o Azure Route Server interage com o ExpressRoute e o VPN Gateways: 

> [!div class="nextstepaction"]
> [Suporte Azure ExpressRoute e Azure VPN](expressroute-vpn-support.md)
