---
title: 'Azure ExpressRoute: Configure peering: classic'
description: Este artigo explica-lhe os passos para criar e aprovisionar o peering privado, público e da Microsoft de um circuito ExpressRoute. Este artigo também mostra como verificar o estado, atualizar ou eliminar peerings no seu circuito.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 12/06/2019
ms.author: duau
ms.openlocfilehash: a4a3bad1e868fa0e75611630ffb5db5ba13126b6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89395558"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit-classic"></a>Criar e modificar o espreitamento para um circuito ExpressRoute (clássico)
> [!div class="op_single_selector"]
> * [Portal do Azure](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [CLI do Azure](howto-routing-cli.md)
> * [Vídeo - Peering privado](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Vídeo - Olhando o público](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Vídeo - Microsoft a espreitar](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (clássico)](expressroute-howto-routing-classic.md)
> 

Este artigo percorre os passos para criar e gerir a configuração de espreitamento/encaminhamento para um circuito ExpressRoute utilizando o PowerShell e o modelo de implementação clássico. Os passos abaixo também irão mostrar como verificar o estado, atualizar ou eliminar e retirar o aprovisionamento do peerings para um circuito ExpressRoute. Pode configurar um, dois ou três espreiteiros (Azure private, Azure public, e Microsoft) para um circuito ExpressRoute. Pode configurar peerings em qualquer ordem que escolha. No entanto, tem de confirmar que conclui a configuração de cada peering, um de cada vez. 

Estas instruções aplicam-se apenas aos circuitos criados com prestadores de serviços que oferecem serviços de conectividade Camada 2. Se estiver a utilizar um prestador de serviços que oferece serviços geridos da Camada 3 (normalmente um IPVPN, como o MPLS), o seu fornecedor de conectividade configurará e gerirá o encaminhamento para si.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Acerca dos modelos de implementação do Azure**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="configuration-prerequisites"></a>Pré-requisitos da configuração

* Confirme que reviu as páginas [pré-requisitos](expressroute-prerequisites.md), [requisitos de encaminhamento](expressroute-routing.md) página e [fluxos de trabalho](expressroute-workflows.md) antes de iniciar a configuração.
* Deve ter um circuito ExpressRoute ativo. Siga as instruções para [criar um circuito ExpressRoute](expressroute-howto-circuit-classic.md) e tenha o circuito ativado pelo seu fornecedor de conectividade antes de prosseguir. O circuito ExpressRoute tem de estar num estado aprovisionado e ativado para que seja capaz de executar os cmdlets descritos abaixo.

### <a name="download-the-latest-powershell-cmdlets"></a>Descarregue os mais recentes cmdlets PowerShell

[!INCLUDE [classic powershell install instructions](../../includes/expressroute-poweshell-classic-install-include.md)]

## <a name="azure-private-peering"></a>Peering privado do Azure

Esta secção fornece instruções sobre como criar, obter, atualizar e eliminar a configuração do peering privado do Azure para um circuito ExpressRoute. 

### <a name="to-create-azure-private-peering"></a>Para criar um peering privado do Azure

1. **Criar um circuito ExpressRoute.**

   Siga as instruções para criar um [circuito ExpressRoute](expressroute-howto-circuit-classic.md) e solicite ao fornecedor de conectividade que o aprovisione. Se o seu fornecedor de conectividade oferecer serviços geridos de Camada 3, pode solicitar-lhe que ative o peering privado do Azure por si. Nesse caso, não necessita de seguir as instruções indicadas nas secções seguintes. No entanto, se o seu fornecedor de conectividade não fizer a gestão do encaminhamento por si, depois de criar o seu circuito, siga as instruções abaixo.
2. **Verifique o circuito ExpressRoute para se certificar de que está a provisionado.**
   
   Verifique se o circuito ExpressRoute está provisionado e também ativado.

   ```powershell
   Get-AzureDedicatedCircuit -ServiceKey "*********************************"
   ```

   Devolvem:

   ```powershell
   Bandwidth                        : 200
   CircuitName                      : MyTestCircuit
   Location                         : Silicon Valley
   ServiceKey                       : *********************************
   ServiceProviderName              : equinix
   ServiceProviderProvisioningState : Provisioned
   Sku                              : Standard
   Status                           : Enabled
   ```
   
   Certifique-se de que o circuito aparece como Provisionado e Ativado. Se não for, trabalhe com o seu fornecedor de conectividade para levar o seu circuito ao estado e estado exigidos.

   ```powershell
   ServiceProviderProvisioningState : Provisioned
   Status                           : Enabled
   ```
3. **Configure o peering privado do Azure para o circuito.**

   Confirme que tem os seguintes itens antes de continuar com os passos seguintes:
   
   * Uma sub-rede /30 para a ligação primária. Esta não pode fazer parte de qualquer espaço de endereço reservado para redes virtuais.
   * Uma sub-rede /30 para a ligação secundária. Esta não pode fazer parte de qualquer espaço de endereço reservado para redes virtuais.
   * Um ID de VLAN válido para estabelecer este peering. Verifique se nenhum outro espreitante no circuito usa o mesmo ID VLAN.
   * Número AS para peering. Pode utilizar números AS de 2 e 4 bytes. Pode utilizar um número AS privado para este peering. Verifique se não está a usar o 65515.
   * Um hash MD5 se optar por utilizar um. **Opcional.**
     
   Pode utilizar o seguinte exemplo para configurar o Azure private peering para o seu circuito:

   ```powershell
   New-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 100
   ```    

   Se quiser utilizar um haxixe MD5, utilize o seguinte exemplo para configurar o seu circuito:

   ```powershell
   New-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 100 -SharedKey "A1B2C3D4"
   ```
     
   > [!IMPORTANT]
   > Verifique se especifica o seu número DE como ASN, não como cliente ASN.
   > 

### <a name="to-view-azure-private-peering-details"></a>Para ver os detalhes do peering privado do Azure

Pode ver detalhes de configuração usando o seguinte cmdlet:

```powershell
Get-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"
```

Devolvem:

```
AdvertisedPublicPrefixes       : 
AdvertisedPublicPrefixesState  : Configured
AzureAsn                       : 12076
CustomerAutonomousSystemNumber : 
PeerAsn                        : 1234
PrimaryAzurePort               : 
PrimaryPeerSubnet              : 10.0.0.0/30
RoutingRegistryName            : 
SecondaryAzurePort             : 
SecondaryPeerSubnet            : 10.0.0.4/30
State                          : Enabled
VlanId                         : 100
```

### <a name="to-update-azure-private-peering-configuration"></a>Para atualizar a configuração do peering privado do Azure

Pode atualizar qualquer parte da configuração com o cmdlet seguinte. No exemplo seguinte, o ID VLAN do circuito está a ser atualizado de 100 a 500.

```powershell
Set-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 500 -SharedKey "A1B2C3D4"
```

### <a name="to-delete-azure-private-peering"></a>Para eliminar um peering privado do Azure

Pode remover a sua configuração de peering executando o cmdlet seguinte. Deve certificar-se de que todas as redes virtuais não estão desligadas do circuito ExpressRoute antes de executar este cmdlet.

```powershell
Remove-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"
```

## <a name="azure-public-peering"></a>Peering público do Azure

Esta secção fornece instruções sobre como criar, obter, atualizar e eliminar a configuração do peering público do Azure para um circuito ExpressRoute.

> [!NOTE]
> O olhar público azul é depreciado para novos circuitos.
>

### <a name="to-create-azure-public-peering"></a>Para criar um peering público do Azure

1. **Criar um circuito do ExpressRoute**

   Siga as instruções para criar um [circuito ExpressRoute](expressroute-howto-circuit-classic.md) e solicite ao fornecedor de conectividade que o aprovisione. Se o seu fornecedor de conectividade oferecer serviços geridos de Camada 3, pode solicitar-lhe que ative o peering público do Azure por si. Nesse caso, não necessita de seguir as instruções indicadas nas secções seguintes. No entanto, se o seu fornecedor de conectividade não fizer a gestão do encaminhamento por si, depois de criar o seu circuito, siga as instruções abaixo.
2. **Verifique o circuito ExpressRoute para verificar se está previsto**

   Verifique primeiro se o circuito ExpressRoute está aprovisionado e também Ativado.

   ```powershell
   Get-AzureDedicatedCircuit -ServiceKey "*********************************"
   ```

   Devolvem:

   ```powershell
   Bandwidth                        : 200
   CircuitName                      : MyTestCircuit
   Location                         : Silicon Valley
   ServiceKey                       : *********************************
   ServiceProviderName              : equinix
   ServiceProviderProvisioningState : Provisioned
   Sku                              : Standard
   Status                           : Enabled
   ```
   
   Verifique se o circuito aparece como Provisionado e Ativado. Se não for, trabalhe com o seu fornecedor de conectividade para levar o seu circuito ao estado e estado exigidos.

   ```powershell
   ServiceProviderProvisioningState : Provisioned
   Status                           : Enabled
   ```
4. **Configurar Azure olhar para o circuito**
   
   Certifique-se de que tem as seguintes informações antes de prosseguir:
   
   * Uma sub-rede /30 para a ligação primária. Esta tem de ser um prefixo IPv4 válido.
   * Uma sub-rede /30 para a ligação secundária. Esta tem de ser um prefixo IPv4 válido.
   * Um ID de VLAN válido para estabelecer este peering. Verifique se nenhum outro espreitante no circuito usa o mesmo ID VLAN.
   * Número AS para peering. Pode utilizar números AS de 2 e 4 bytes.
   * Um hash MD5 se optar por utilizar um. **Opcional.**

   > [!IMPORTANT]
   > Certifique-se de que especifica o seu número AS como observando ASN e não como cliente ASN.
   >  
     
   Pode utilizar o seguinte exemplo para configurar o olhar público do Azure para o seu circuito:

   ```powershell
   New-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 200
   ```
     
   Se pretender utilizar um haxixe MD5, utilize o seguinte exemplo para configurar o seu circuito:
     
   ```powershell
   New-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 200 -SharedKey "A1B2C3D4"
   ```
     
### <a name="to-view-azure-public-peering-details"></a>Para ver os detalhes do peering público do Azure

Para ver detalhes de configuração, utilize o seguinte cmdlet:

```powershell
Get-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"
```

Devolvem:

```powershell
AdvertisedPublicPrefixes       : 
AdvertisedPublicPrefixesState  : Configured
AzureAsn                       : 12076
CustomerAutonomousSystemNumber : 
PeerAsn                        : 1234
PrimaryAzurePort               : 
PrimaryPeerSubnet              : 131.107.0.0/30
RoutingRegistryName            : 
SecondaryAzurePort             : 
SecondaryPeerSubnet            : 131.107.0.4/30
State                          : Enabled
VlanId                         : 200
```

### <a name="to-update-azure-public-peering-configuration"></a>Para atualizar a configuração do peering público do Azure

Pode atualizar qualquer parte da configuração com o cmdlet seguinte. Neste exemplo, o ID VLAN do circuito está a ser atualizado de 200 para 600.

```powershell
Set-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 600 -SharedKey "A1B2C3D4"
```

Verifique se o circuito aparece como Provisionado e Ativado. 
### <a name="to-delete-azure-public-peering"></a>Para eliminar um peering público do Azure

Pode remover a sua configuração de espreguiçadamento executando o seguinte cmdlet:

```powershell
Remove-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"
```

## <a name="microsoft-peering"></a>Peering da Microsoft

Esta secção fornece instruções sobre como criar, obter, atualizar e eliminar a configuração do peering da Microsoft para um circuito ExpressRoute. 

### <a name="to-create-microsoft-peering"></a>Para criar peering da Microsoft

1. **Criar um circuito do ExpressRoute**
  
   Siga as instruções para criar um [circuito ExpressRoute](expressroute-howto-circuit-classic.md) e solicite ao fornecedor de conectividade que o aprovisione. Se o seu fornecedor de conectividade oferecer serviços geridos de Camada 3, pode solicitar-lhe que ative o peering privado do Azure por si. Nesse caso, não necessita de seguir as instruções indicadas nas secções seguintes. No entanto, se o seu fornecedor de conectividade não fizer a gestão do encaminhamento por si, depois de criar o seu circuito, siga as instruções abaixo.
2. **Verifique o circuito ExpressRoute para verificar se está previsto**

   Verifique se o circuito aparece como Provisionado e Ativado. 
   
   ```powershell
   Get-AzureDedicatedCircuit -ServiceKey "*********************************"
   ```

   Devolvem:
   
   ```powershell
   Bandwidth                        : 200
   CircuitName                      : MyTestCircuit
   Location                         : Silicon Valley
   ServiceKey                       : *********************************
   ServiceProviderName              : equinix
   ServiceProviderProvisioningState : Provisioned
   Sku                              : Standard
   Status                           : Enabled
   ```
   
   Verifique se o circuito aparece como Provisionado e Ativado. Se não for, trabalhe com o seu fornecedor de conectividade para levar o seu circuito ao estado e estado exigidos.

   ```powershell
   ServiceProviderProvisioningState : Provisioned
   Status                           : Enabled
   ```
3. **Configurar a Microsoft a espreitar para o circuito**
   
    Confirme que tem as seguintes informações antes de continuar.
   
   * Uma sub-rede /30 para a ligação primária. Esta tem de ser um prefixo IPv4 público válido detido por si e registado num RIR/TIR.
   * Uma sub-rede /30 para a ligação secundária. Esta tem de ser um prefixo IPv4 público válido detido por si e registado num RIR/TIR.
   * Um ID de VLAN válido para estabelecer este peering. Verifique se nenhum outro espreitante no circuito usa o mesmo ID VLAN.
   * Número AS para peering. Pode utilizar números AS de 2 e 4 bytes.
   * Prefixos anunciados: tem de fornecer uma lista de todos os prefixos que planeia anunciar durante a sessão de BGP. São aceites apenas prefixos de endereços IP públicos. Pode enviar uma lista separada por vírgula se pretender enviar um conjunto de prefixos. Estes prefixos têm de ser registados para si num RIR/TIR.
   * Cliente ASN: se estiver a anunciar prefixos que não estão registados para o número AS de peering, pode especificar o número AS no qual estão registados. **Opcional.**
   * Nome do registo de encaminhamento: pode especificar o RIR/TIR de acordo com o número AS e os prefixos aos quais estão registados.
   * Um hash MD5, se optar por utilizar um. **É opcional.**
     
   Executar o seguinte cmdlet para configurar o olho da Microsoft para o seu circuito:
 
   ```powershell
   New-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -VlanId 300 -PeerAsn 1234 -CustomerAsn 2245 -AdvertisedPublicPrefixes "123.0.0.0/30" -RoutingRegistryName "ARIN" -SharedKey "A1B2C3D4"
   ```

### <a name="to-view-microsoft-peering-details"></a>Para ver os detalhes do peering da Microsoft

Pode ver detalhes de configuração usando o seguinte cmdlet:

```powershell
Get-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"
```
Devolvem:

```powershell
AdvertisedPublicPrefixes       : 123.0.0.0/30
AdvertisedPublicPrefixesState  : Configured
AzureAsn                       : 12076
CustomerAutonomousSystemNumber : 2245
PeerAsn                        : 1234
PrimaryAzurePort               : 
PrimaryPeerSubnet              : 10.0.0.0/30
RoutingRegistryName            : ARIN
SecondaryAzurePort             : 
SecondaryPeerSubnet            : 10.0.0.4/30
State                          : Enabled
VlanId                         : 300
```

### <a name="to-update-microsoft-peering-configuration"></a>Para atualizar a configuração do peering da Microsoft

Pode atualizar qualquer parte da configuração utilizando o seguinte cmdlet:

```powershell
Set-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -VlanId 300 -PeerAsn 1234 -CustomerAsn 2245 -AdvertisedPublicPrefixes "123.0.0.0/30" -RoutingRegistryName "ARIN" -SharedKey "A1B2C3D4"
```

### <a name="to-delete-microsoft-peering"></a>Para eliminar o peering da Microsoft

Pode remover a sua configuração de espreguiçadamento executando o seguinte cmdlet:

```powershell
Remove-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"
```

## <a name="next-steps"></a>Passos seguintes

Em seguida, [ligue um VNet a um circuito ExpressRoute](expressroute-howto-linkvnet-classic.md).

* Para obter mais informações sobre fluxos de trabalho, consulte [os fluxos de trabalho ExpressRoute](expressroute-workflows.md).
* Para obter mais informações sobre peering do circuito, veja [Circuitos ExpressRoute e domínios de encaminhamento](expressroute-circuit-peerings.md).
