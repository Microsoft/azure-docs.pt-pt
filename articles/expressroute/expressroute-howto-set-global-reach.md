---
title: 'Configure o alcance Global - ExpressRoute: Azure | Microsoft Docs'
description: Este artigo ajuda-o a ligar os circuitos do ExpressRoute em conjunto para garantir uma privada, de rede entre as redes no local e ativar o alcance Global.
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: jaredro
ms.custom: seodec18
ms.openlocfilehash: aff283da27197b11ee496faecdd8b69571d3547e
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56821341"
---
# <a name="configure-expressroute-global-reach"></a>Configurar o Alcance Global do ExpressRoute
Este artigo ajuda-o a configurar o ExpressRoute alcance Global com o PowerShell. Para obter mais informações, consulte [alcance Global do ExpressRouteRoute](expressroute-global-reach.md).

Antes de iniciar a configuração, confirme o seguinte:

* Se instalou a versão mais recente do Azure PowerShell. Para obter mais informações, veja [nstall and configure Azure PowerShell (Instalar e configurar o Azure PowerShell)](/powershell/azure/azurerm/install-azurerm-ps).
* Que compreende o aprovisionamento do circuito de ExpressRoute [fluxos de trabalho](expressroute-workflows.md).
* Que seus circuitos do ExpressRoute estão num Estado aprovisionado.
* Se o peering privado do Azure está configurado em seus circuitos do ExpressRoute.  

### <a name="sign-in-to-your-azure-account"></a>Inicie sessão na sua conta do Azure
Para iniciar a configuração, inicie sessão na sua conta do Azure. 

Abra a consola do PowerShell com privilégios elevados e, em seguida, ligar à sua conta. O comando pede-lhe as credenciais de início de sessão para a sua conta do Azure.  

```powershell
Connect-AzureRmAccount
```

Se tiver múltiplas subscrições do Azure, verifique as subscrições da conta.

```powershell
Get-AzureRmSubscription
```

Especifique a subscrição que pretende utilizar.

```powershell
Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
```

### <a name="identify-your-expressroute-circuits-for-configuration"></a>Identificar os circuitos do ExpressRoute para configuração
Pode ativar o alcance Global do ExpressRoute entre quaisquer dois circuitos do ExpressRoute, desde que eles estão localizados em países/regiões suportadas e foram criados em diferentes localizações de peering. Se a sua subscrição é a proprietária de ambos os circuitos, pode escolher qualquer circuito para executar a configuração nas seções a seguir. 

Se dois circuitos estiverem em diferentes subscrições do Azure, terá de autorização de uma subscrição do Azure. Em seguida, passar a chave de autorização ao executar o comando de configuração na subscrição do Azure.

## <a name="enable-connectivity-between-your-on-premises-networks"></a>Ativar a conectividade entre as redes no local

Utilize os seguintes comandos para obter o circuito 1 e 2 do circuito. Dois circuitos estão na mesma subscrição.

```powershell
$ckt_1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
$ckt_2 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
```

Execute o seguinte comando na circuito 1 e passar o ID de peering privado do circuito 2. Ao executar o comando, tenha em atenção o seguinte:

* O ID de peering privado é semelhante ao seguinte exemplo: 

  ```
  /subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}/peerings/AzurePrivatePeering
  ```
* *-AddressPrefix* tem de ser/29 IPv4 sub-rede, por exemplo, "10.0.0.0/29". Endereços IP são utilizadas nesta sub-rede para estabelecer a conectividade entre os dois circuitos do ExpressRoute. Não deve usar os endereços nesta sub-rede nas redes virtuais do Azure ou na sua rede no local.

```powershell
Add-AzureRmExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering $ckt_2.Peerings[0].Id -AddressPrefix '__.__.__.__/29'
```

Guarde a configuração no circuito 1 da seguinte forma:
```powershell
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

Quando a operação anterior está concluída, deve ter conectividade entre as redes no local em ambos os lados por meio de seus dois circuitos do ExpressRoute.

### <a name="expressroute-circuits-in-different-azure-subscriptions"></a>Circuitos do ExpressRoute em diferentes subscrições do Azure

Se dois circuitos não estiverem na mesma subscrição do Azure, terá de autorização. A seguinte configuração autorização é gerada na subscrição circuito 2, e a chave de autorização é passada para o circuito 1.

Gere uma chave de autorização. 
```powershell
$ckt_2 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $ckt_2 -Name "Name_for_auth_key"
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_2
```
Anote o ID de peering privado do circuito 2, bem como a chave de autorização.

Execute o seguinte comando na circuito 1. Transmita o ID de peering privado do circuito 2 e a chave de autorização.
```powershell
Add-AzureRmExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering "circuit_2_private_peering_id" -AddressPrefix '__.__.__.__/29' -AuthorizationKey '########-####-####-####-############'
```

Guarde a configuração no circuito 1.
```powershell
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

Quando a operação anterior está concluída, deve ter conectividade entre as redes no local em ambos os lados por meio de seus dois circuitos do ExpressRoute.

## <a name="get-and-verify-the-configuration"></a>Obter e verificar a configuração

Utilize o seguinte comando para verificar a configuração no circuito onde a configuração foi efetuada (por exemplo, o circuito 1 no exemplo anterior).

```powershell
$ckt1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
```

Se simplesmente executar *us $ckt1* no PowerShell, verá *CircuitConnectionStatus* na saída. Indica se a conectividade é estabelecida, "Ligada" ou "Desconectada". 

## <a name="disable-connectivity-between-your-on-premises-networks"></a>Desativar a conectividade entre as redes no local

Para desativar a conectividade, execute os comandos contra o circuito onde a configuração foi efetuada (por exemplo, o circuito 1 no exemplo anterior).

```powershell
$ckt1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
Remove-AzureRmExpressRouteCircuitConnectionConfig -Name "Your_connection_name" -ExpressRouteCircuit $ckt_1
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

Pode executar a operação Get para verificar o estado. 

Depois de concluída a operação anterior, já não tem conectividade entre a rede no local através de circuitos de ExpressRoute. 


## <a name="next-steps"></a>Passos Seguintes
1. [Saiba mais sobre o alcance Global do ExpressRoute](expressroute-global-reach.md)
2. [Verificar a conectividade do ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
3. [Ligar um circuito do ExpressRoute para uma rede virtual do Azure](expressroute-howto-linkvnet-arm.md)


