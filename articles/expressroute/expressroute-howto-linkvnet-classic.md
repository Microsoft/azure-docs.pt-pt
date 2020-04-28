---
title: 'Azure ExpressRoute: Ligue um VNet a um circuito: clássico'
description: Este documento fornece uma visão geral de como ligar redes virtuais (VNets) aos circuitos ExpressRoute utilizando o modelo de implementação clássico e powerShell.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/06/2019
ms.author: cherylmc
ms.openlocfilehash: 53c200b01dfa6bce09cfc058dc24ab8e38d253a6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74930039"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-powershell-classic"></a>Ligue uma rede virtual a um circuito ExpressRoute usando powerShell (clássico)
> [!div class="op_single_selector"]
> * [Portal do Azure](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [CLI do Azure](howto-linkvnet-cli.md)
> * [Vídeo - Portal Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (clássico)](expressroute-howto-linkvnet-classic.md)
>

Este artigo irá ajudá-lo a ligar redes virtuais (VNets) aos circuitos Azure ExpressRoute utilizando o PowerShell. Um único VNet pode ser ligado a até quatro circuitos ExpressRoute. Utilize os passos deste artigo para criar uma nova ligação a cada circuito ExpressRoute a que está a ligar. Os circuitos ExpressRoute podem estar na mesma subscrição, subscrições diferentes ou uma mistura de ambos. Este artigo aplica-se a redes virtuais criadas utilizando o modelo de implementação clássico.

Pode ligar até 10 redes virtuais a um circuito ExpressRoute. Todas as redes virtuais devem estar na mesma região geopolítica. Pode ligar um maior número de redes virtuais ao circuito ExpressRoute ou ligar redes virtuais que se encontram noutras regiões geopolíticas se permitir o addon premium ExpressRoute. Consulte as [FAQ](expressroute-faqs.md) para obter mais detalhes sobre o complemento premium.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Acerca dos modelos de implementação do Azure**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]


## <a name="configuration-prerequisites"></a>Pré-requisitos da configuração

* Reveja os [pré-requisitos,](expressroute-prerequisites.md) [os requisitos de encaminhamento](expressroute-routing.md)e [os fluxos de trabalho](expressroute-workflows.md) antes de iniciar a configuração.
* Deve ter um circuito ExpressRoute ativo.
   * Siga as instruções para [criar um circuito ExpressRoute](expressroute-howto-circuit-classic.md) e que o seu fornecedor de conectividade ative o circuito.
   * Certifique-se de que tem o olho privado Azure configurado para o seu circuito. Consulte o artigo de [encaminhamento configure](expressroute-howto-routing-classic.md) para obter instruções de encaminhamento.
   * Certifique-se de que o epeering privado Do Azure está configurado e que o BGP está a espreitar entre a sua rede e a Microsoft para que possa permitir a conectividade de ponta a ponta.
   * Você deve ter uma rede virtual e um gateway de rede virtual criado e totalmente provisionado. Siga as instruções para [configurar uma rede virtual para a ExpressRoute](expressroute-howto-vnet-portal-classic.md).

### <a name="download-the-latest-powershell-cmdlets"></a>Descarregue os mais recentes cmdlets PowerShell

[!INCLUDE [classic powershell install instructions](../../includes/expressroute-poweshell-classic-install-include.md)]

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Ligue uma rede virtual na mesma subscrição a um circuito
Pode ligar uma rede virtual a um circuito ExpressRoute utilizando o seguinte cmdlet. Certifique-se de que o portal de rede virtual está criado e está pronto para ser ligado antes de executar o cmdlet.

```powershell
New-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
Provisioned
```
    
## <a name="remove-a-virtual-network-link-to-a-circuit"></a>Remova uma ligação de rede virtual a um circuito
Pode remover uma ligação de rede virtual a um circuito ExpressRoute utilizando o seguinte cmdlet. Certifique-se de que a subscrição atual é selecionada para a rede virtual dada. 

```powershell
Remove-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
```
 

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Ligar uma rede virtual de uma subscrição diferente a um circuito
Pode partilhar um circuito ExpressRoute em várias subscrições. A figura que se segue mostra um simples esquema de como a partilha funciona para circuitos ExpressRoute em várias subscrições.

Cada uma das nuvens menores dentro da grande nuvem é usada para representar subscrições que pertencem a diferentes departamentos dentro de uma organização. Cada um dos departamentos da organização pode usar a sua própria subscrição para implementar os seus serviços-- mas os departamentos podem partilhar um único circuito ExpressRoute para ligar de volta à sua rede no local. Um único departamento (neste exemplo: TI) pode ser dono do circuito ExpressRoute. Outras subscrições dentro da organização podem usar o circuito ExpressRoute.

> [!NOTE]
> As tarifas de conectividade e largura de banda para o circuito dedicado serão aplicadas ao proprietário do circuito ExpressRoute. Todas as redes virtuais partilham a mesma largura de banda.
> 
> 

![Conectividade de subscrição cruzada](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration"></a>Administração
O proprietário do *circuito* é o administrador/coadministrador da subscrição em que o circuito ExpressRoute é criado. O proprietário do circuito pode autorizar administradores/coadministradores de outras subscrições, referidas como utilizadores de *circuitos,* a utilizar o circuito dedicado que possuem. Os utilizadores de circuitos autorizados a utilizar o circuito ExpressRoute da organização podem ligar a rede virtual na sua subscrição ao circuito ExpressRoute depois de autorizados.

O dono do circuito tem o poder de modificar e revogar as autorizações a qualquer momento. A revogação de uma autorização resultará na eliminação de todas as ligações da subscrição cujo acesso foi revogado.

### <a name="circuit-owner-operations"></a>Operações do proprietário do circuito

**Criação de uma autorização**

O proprietário do circuito autoriza os administradores de outras subscrições a utilizar o circuito especificado. No exemplo seguinte, o administrador do circuito (Contoso IT) permite ao administrador de outra subscrição (Dev-Test) ligar até duas redes virtuais ao circuito. O administrador de TI Contoso permite-o especificando o ID microsoft de Dev-Test. O cmdlet não envia e-mail para o ID específico da Microsoft. O proprietário do circuito precisa notificar explicitamente o outro proprietário da subscrição de que a autorização está completa.

```powershell
New-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -Description "Dev-Test Links" -Limit 2 -MicrosoftIds 'devtest@contoso.com'
```

  Devolvem:

  ```powershell
  Description         : Dev-Test Links
  Limit               : 2
  LinkAuthorizationId : **********************************
  MicrosoftIds        : devtest@contoso.com
  Used                : 0
  ```

**Revisão de autorizações**

O proprietário do circuito pode rever todas as autorizações emitidas num determinado circuito, executando o seguinte cmdlet:

```powershell
Get-AzureDedicatedCircuitLinkAuthorization -ServiceKey: "**************************"
```
  Devolvem:

  ```powershell
  Description         : EngineeringTeam
  Limit               : 3
  LinkAuthorizationId : ####################################
  MicrosoftIds        : engadmin@contoso.com
  Used                : 1

  Description         : MarketingTeam
  Limit               : 1
  LinkAuthorizationId : @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
  MicrosoftIds        : marketingadmin@contoso.com
  Used                : 0

  Description         : Dev-Test Links
  Limit               : 2
  LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
  MicrosoftIds        : salesadmin@contoso.com
  Used                : 2
  ```

**Atualização de autorizações**

O proprietário do circuito pode modificar as autorizações utilizando o seguinte cmdlet:

```powershell
Set-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -AuthorizationId "&&&&&&&&&&&&&&&&&&&&&&&&&&&&"-Limit 5
```

  Devolvem:

  ```powershell
  Description         : Dev-Test Links
  Limit               : 5
  LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
  MicrosoftIds        : devtest@contoso.com
  Used                : 0
  ```

**Autorização de adesão**

O proprietário do circuito pode revogar/excluir autorizações ao utilizador executando o seguinte cmdlet:

```powershell
Remove-AzureDedicatedCircuitLinkAuthorization -ServiceKey "*****************************" -AuthorizationId "###############################"
```

### <a name="circuit-user-operations"></a>Operações de utilizador de circuitos

**Revisão de autorizações**

O utilizador do circuito pode rever as autorizações utilizando o seguinte cmdlet:

```powershell
Get-AzureAuthorizedDedicatedCircuit
```

  Devolvem:

  ```powershell
  Bandwidth                        : 200
  CircuitName                      : ContosoIT
  Location                         : Washington DC
  MaximumAllowedLinks              : 2
  ServiceKey                       : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
  ServiceProviderName              : equinix
  ServiceProviderProvisioningState : Provisioned
  Status                           : Enabled
  UsedLinks                        : 0
  ```

**Redentoras de autorizações de ligação**

O utilizador do circuito pode executar o seguinte cmdlet para resgatar uma autorização de ligação:

```powershell
New-AzureDedicatedCircuitLink –servicekey "&&&&&&&&&&&&&&&&&&&&&&&&&&" –VnetName 'SalesVNET1'
```

  Devolvem:

  ```powershell
  State VnetName
  ----- --------
  Provisioned SalesVNET1
  ```

Execute este comando na subscrição recentemente ligada para a rede virtual:

```powershell
New-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
```

## <a name="next-steps"></a>Passos seguintes

Para mais informações sobre o ExpressRoute, consulte o [ExpressRoute FAQ](expressroute-faqs.md).
