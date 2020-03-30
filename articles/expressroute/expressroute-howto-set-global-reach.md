---
title: 'Azure ExpressRoute: Configure Global Reach'
description: Este artigo ajuda-o a ligar os circuitos ExpressRoute para fazer uma rede privada entre as suas redes no local e permitir o Alcance Global.
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: jaredro
ms.openlocfilehash: 76de7a8854a58deb924cbbe3177ad5a7b5fd57a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74083470"
---
# <a name="configure-expressroute-global-reach"></a>Configurar o Alcance Global do ExpressRoute

Este artigo ajuda-o a configurar o ExpressRoute Global Reach utilizando o PowerShell. Para mais informações, consulte [ExpressRouteRoute Global Reach](expressroute-global-reach.md).

 ## <a name="before-you-begin"></a>Antes de começar

Antes de iniciar a configuração, confirme o seguinte:

* Compreende fluxos de [trabalho](expressroute-workflows.md)de fornecimento de circuitoexpressRoute.
* Os seus circuitos ExpressRoute estão num estado provisionado.
* O peering privado Azure está configurado nos seus circuitos ExpressRoute.
* Se pretender executar o PowerShell localmente, verifique se a versão mais recente do Azure PowerShell está instalada no seu computador.

### <a name="working-with-azure-powershell"></a>Trabalhar com a Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="identify-circuits"></a>Identificar circuitos

1. Para iniciar a configuração, inicie o sessão na sua conta Azure e selecione a subscrição que pretende utilizar.

   [!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]
2. Identifique os circuitos ExpressRoute que pretende utilizar. Pode permitir o ExpressRoute Global Reach entre dois circuitos ExpressRoute desde que estejam localizados nos países/regiões apoiados e tenham sido criados em diferentes locais de observação. 

   * Se a sua subscrição possuir ambos os circuitos, pode escolher qualquer circuito para executar a configuração nas seguintes secções.
   * Se os dois circuitos estiverem em diferentes subscrições do Azure, precisa de autorização de uma subscrição Azure. Em seguida, passa na chave de autorização quando executa o comando de configuração na outra subscrição do Azure.

## <a name="enable-connectivity"></a>Ativar conectividade

Ative a conectividade entre as suas redes no local. Existem conjuntos separados de instruções para circuitos que estão na mesma subscrição do Azure, e circuitos que são assinaturas diferentes.

### <a name="expressroute-circuits-in-the-same-azure-subscription"></a>Circuitos ExpressRoute na mesma subscrição do Azure

1. Utilize os seguintes comandos para obter o circuito 1 e o circuito 2. Os dois circuitos estão na mesma subscrição.

   ```azurepowershell-interactive
   $ckt_1 = Get-AzExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
   $ckt_2 = Get-AzExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
   ```
2. Corra o seguinte comando contra o circuito 1 e passe no id de observação privado do circuito 2. Ao executar o comando, note o seguinte:

   * O ID de observação privada é semelhante ao seguinte exemplo: 

     ```
     /subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}/peerings/AzurePrivatePeering
     ```
   * *-AddressPrefix* deve ser uma sub-rede IPv4 /29, por exemplo, "10.0.0.0.0/29". Utilizamos endereços IP nesta subnet para estabelecer conectividade entre os dois circuitos ExpressRoute. Não deve utilizar os endereços desta subnet nas suas redes virtuais Azure ou na sua rede no local.

     ```azurepowershell-interactive
     Add-AzExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering $ckt_2.Peerings[0].Id -AddressPrefix '__.__.__.__/29'
     ```
3. Guarde a configuração no circuito 1 da seguinte forma:

   ```azurepowershell-interactive
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_1
   ```

Quando a operação anterior estiver concluída, terá conectividade entre as suas redes no local de ambos os lados através dos seus dois circuitos ExpressRoute.

### <a name="expressroute-circuits-in-different-azure-subscriptions"></a>Circuitos ExpressRoute em diferentes subscrições do Azure

Se os dois circuitos não estiverem na mesma subscrição do Azure, precisa de autorização. Na seguinte configuração, a autorização é gerada na assinatura do circuito 2, e a chave de autorização é passada para o circuito 1.

1. Gerar uma chave de autorização.

   ```azurepowershell-interactive
   $ckt_2 = Get-AzExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
   Add-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $ckt_2 -Name "Name_for_auth_key"
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_2
   ```

   Tome nota da identificação privada do circuito 2, bem como da chave de autorização.
2. Corra o seguinte comando contra o circuito 1. Passe na identificação privada do circuito 2 e na chave de autorização.

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering "circuit_2_private_peering_id" -AddressPrefix '__.__.__.__/29' -AuthorizationKey '########-####-####-####-############'
   ```
3. Guarde a configuração no circuito 1.

   ```azurepowershell-interactive
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_1
   ```

Quando a operação anterior estiver concluída, terá conectividade entre as suas redes no local de ambos os lados através dos seus dois circuitos ExpressRoute.

## <a name="verify-the-configuration"></a>Verificar a configuração

Utilize o seguinte comando para verificar a configuração no circuito onde foi feita a configuração (por exemplo, o circuito 1 no exemplo anterior).
```azurepowershell-interactive
$ckt1 = Get-AzExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
```

Se simplesmente executar *$ckt1* no PowerShell, verá *CircuitConnectionStatus* na saída. Diz-lhe se a conectividade está estabelecida, "Conectada", ou "Desligada". 

## <a name="disable-connectivity"></a>Desativar a conectividade

Para desativar a conectividade entre as suas redes no local, passe os comandos contra o circuito onde a configuração foi feita (por exemplo, o circuito 1 no exemplo anterior).

```azurepowershell-interactive
$ckt1 = Get-AzExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
Remove-AzExpressRouteCircuitConnectionConfig -Name "Your_connection_name" -ExpressRouteCircuit $ckt_1
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

Pode executar a operação Get para verificar o estado.

Após a operação anterior estar concluída, já não tem conectividade entre a sua rede no local através dos seus circuitos ExpressRoute.

## <a name="next-steps"></a>Passos seguintes
1. [Saiba mais sobre o ExpressRoute Global Reach](expressroute-global-reach.md)
2. [Verifique a conectividade ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
3. [Ligue um circuito ExpressRoute a uma rede virtual Azure](expressroute-howto-linkvnet-arm.md)
