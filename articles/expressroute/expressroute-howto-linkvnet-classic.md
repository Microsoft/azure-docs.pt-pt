---
title: 'Azure ExpressRoute: vincular uma VNet a um circuito: clássico'
description: Este documento fornece uma visão geral de como vincular redes virtuais (VNets) a circuitos de ExpressRoute usando o modelo de implantação clássico e o PowerShell.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/06/2019
ms.author: cherylmc
ms.openlocfilehash: 53c200b01dfa6bce09cfc058dc24ab8e38d253a6
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930039"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-powershell-classic"></a>Conectar uma rede virtual a um circuito do ExpressRoute usando o PowerShell (clássico)
> [!div class="op_single_selector"]
> * [Portal do Azure](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [CLI do Azure](howto-linkvnet-cli.md)
> * [Vídeo - portal do Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (clássico)](expressroute-howto-linkvnet-classic.md)
>

Este artigo o ajudará a vincular redes virtuais (VNets) a circuitos do Azure ExpressRoute usando o PowerShell. Pode ser associada uma VNet única para até quatro circuitos do ExpressRoute. Use as etapas neste artigo para criar um novo link para cada circuito de ExpressRoute ao qual você está se conectando. Os circuitos do ExpressRoute podem ser na mesma subscrição, subscrições diferentes ou uma combinação de ambos. Este artigo se aplica a redes virtuais criadas usando o modelo de implantação clássico.

Você pode vincular até 10 redes virtuais a um circuito do ExpressRoute. Todas as redes virtuais devem estar na mesma região geopolítica. Você pode vincular um número maior de redes virtuais ao circuito do ExpressRoute ou vincular redes virtuais que estão em outras regiões do geopolítica se você habilitar o complemento Premium do ExpressRoute. Consulte as [perguntas frequentes](expressroute-faqs.md) para obter mais detalhes sobre o complemento Premium.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Acerca dos modelos de implementação do Azure**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]


## <a name="configuration-prerequisites"></a>Pré-requisitos da configuração

* Reveja os [pré-requisitos](expressroute-prerequisites.md), [requisitos de encaminhamento](expressroute-routing.md), e [fluxos de trabalho](expressroute-workflows.md) antes de iniciar a configuração.
* Deve ter um circuito ExpressRoute ativo.
   * Siga as instruções para [criar um circuito do ExpressRoute](expressroute-howto-circuit-classic.md) e fazer com que seu provedor de conectividade habilite o circuito.
   * Certifique-se de que tem o peering privado do Azure configurado para o seu circuito. Consulte a [configurar encaminhamento](expressroute-howto-routing-classic.md) artigo para obter instruções de encaminhamento.
   * Certifique-se de que o peering privado do Azure está configurado e o peering de BGP entre a rede e a Microsoft está ativo, para que pode habilitar a conectividade de ponto-a-ponto.
   * Você deve ter uma rede virtual e um gateway de rede virtual criados e totalmente provisionados. Siga as instruções para [Configurar uma rede virtual para o ExpressRoute](expressroute-howto-vnet-portal-classic.md).

### <a name="download-the-latest-powershell-cmdlets"></a>Baixe os cmdlets mais recentes do PowerShell

[!INCLUDE [classic powershell install instructions](../../includes/expressroute-poweshell-classic-install-include.md)]

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Ligar uma rede virtual na mesma subscrição a um circuito
Você pode vincular uma rede virtual a um circuito do ExpressRoute usando o cmdlet a seguir. Verifique se o gateway de rede virtual foi criado e está pronto para vinculação antes de executar o cmdlet.

```powershell
New-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
Provisioned
```
    
## <a name="remove-a-virtual-network-link-to-a-circuit"></a>Remover um link de rede virtual para um circuito
Você pode remover um link de rede virtual para um circuito do ExpressRoute usando o cmdlet a seguir. Verifique se a assinatura atual está selecionada para a rede virtual fornecida. 

```powershell
Remove-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
```
 

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Ligar uma rede virtual de uma subscrição diferente a um circuito
Pode partilhar um circuito do ExpressRoute em várias subscrições. A figura seguinte mostra um simples esquemática funciona como a partilha dos circuitos do ExpressRoute em várias subscrições.

Cada uma das nuvens menores na cloud da grande é utilizada para representar as subscrições que pertencem a diferentes departamentos dentro de uma organização. Cada um dos departamentos dentro da organização pode usar sua própria assinatura para implantar seus serviços, mas os departamentos podem compartilhar um único circuito do ExpressRoute para se conectar de volta à sua rede local. Um único departamento (neste exemplo: IT) pode ser proprietário do circuito do ExpressRoute. Outras subscrições dentro da organização podem utilizar o circuito do ExpressRoute.

> [!NOTE]
> Custos de largura de banda e de conectividade para o circuito dedicado serão aplicados para o proprietário do circuito ExpressRoute. Todas as redes virtuais partilham a mesma largura de banda.
> 
> 

![Conectividade entre subscrições](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration"></a>Administração
O *proprietário do circuito* é o administrador/coadministrador da assinatura na qual o circuito do ExpressRoute é criado. O proprietário do circuito pode autorizar administradores/coadministradores de outras assinaturas, chamados de *usuários de circuito*, para usar o circuito dedicado que eles possuem. Os usuários de circuito que estão autorizados a usar o circuito de ExpressRoute da organização podem vincular a rede virtual em sua assinatura ao circuito do ExpressRoute depois de serem autorizados.

O proprietário do circuito tem o poder para modificar e revogar autorizações em qualquer altura. A revogação de uma autorização resultará na exclusão de todos os links da assinatura, cujo acesso foi revogado.

### <a name="circuit-owner-operations"></a>Operações de proprietário do circuito

**Criando uma autorização**

O proprietário do circuito autoriza os administradores de outras assinaturas a usar o circuito especificado. No exemplo a seguir, o administrador do circuito (TI da Contoso) permite que o administrador de outra assinatura (desenvolvimento/teste) vincule até duas redes virtuais ao circuito. O administrador de ti da Contoso permite isso especificando a ID da Microsoft de desenvolvimento/teste. O cmdlet não envia email para a ID da Microsoft especificada. O proprietário do circuito precisa notificar explicitamente o outro proprietário da assinatura de que a autorização foi concluída.

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

**Revisando autorizações**

O proprietário do circuito pode rever todas as autorizações emitidos num determinado circuito, execute o seguinte cmdlet:

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

**Atualizando autorizações**

O proprietário do circuito pode modificar autorizações usando o seguinte cmdlet:

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

**Excluindo autorizações**

O proprietário do circuito pode revogar/eliminar autorizações para o usuário executando o seguinte cmdlet:

```powershell
Remove-AzureDedicatedCircuitLinkAuthorization -ServiceKey "*****************************" -AuthorizationId "###############################"
```

### <a name="circuit-user-operations"></a>Operações de utilizador do circuito

**Revisando autorizações**

O usuário do circuito pode examinar autorizações usando o seguinte cmdlet:

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

**Resgatando autorizações de link**

O utilizador de circuito pode executar o cmdlet seguinte para resgatar uma autorização de ligação:

```powershell
New-AzureDedicatedCircuitLink –servicekey "&&&&&&&&&&&&&&&&&&&&&&&&&&" –VnetName 'SalesVNET1'
```

  Devolvem:

  ```powershell
  State VnetName
  ----- --------
  Provisioned SalesVNET1
  ```

Execute este comando na assinatura recém vinculada para a rede virtual:

```powershell
New-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
```

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações acerca do ExpressRoute, veja as [FAQs do ExpressRoute](expressroute-faqs.md).
