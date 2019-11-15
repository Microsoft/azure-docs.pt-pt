---
title: 'Azure ExpressRoute: configurar o emparelhamento: clássico'
description: Este artigo explica-lhe os passos para criar e aprovisionar o peering privado, público e da Microsoft de um circuito ExpressRoute. Este artigo também mostra como verificar o estado, atualizar ou eliminar peerings no seu circuito.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: cherylmc
ms.openlocfilehash: 814a73900b05b66d1bacc946b9f994135d3fc9f6
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083449"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit-classic"></a>Criar e modificar o emparelhamento para um circuito de ExpressRoute (clássico)
> [!div class="op_single_selector"]
> * [Portal do Azure](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [CLI do Azure](howto-routing-cli.md)
> * [Vídeo - peering privado](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Vídeo - peering público](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Vídeo - peering da Microsoft](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (clássico)](expressroute-howto-routing-classic.md)
> 

Este artigo orienta você pelas etapas para criar e gerenciar a configuração de emparelhamento/roteamento para um circuito do ExpressRoute usando o PowerShell e o modelo de implantação clássico. Os passos abaixo também irão mostrar como verificar o estado, atualizar ou eliminar e retirar o aprovisionamento do peerings para um circuito ExpressRoute. Pode configurar um, dois ou todos os três peerings (Azure privado, Azure público e Microsoft) para um circuito do ExpressRoute. Pode configurar peerings em qualquer ordem que escolha. No entanto, tem de confirmar que conclui a configuração de cada peering, um de cada vez. 

Essas instruções se aplicam somente a circuitos criados com provedores de serviço que oferecem serviços de conectividade de camada 2. Se estiver a utilizar um fornecedor de serviços que oferece geridos de camada 3 serviços (normalmente uma VPN de IP, como MPLS), o seu fornecedor de conectividade irá configurar e gerir encaminhamento por si.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Acerca dos modelos de implementação do Azure**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="configuration-prerequisites"></a>Pré-requisitos da configuração

* Confirme que reviu as páginas [pré-requisitos](expressroute-prerequisites.md), [requisitos de encaminhamento](expressroute-routing.md) página e [fluxos de trabalho](expressroute-workflows.md) antes de iniciar a configuração.
* Deve ter um circuito ExpressRoute ativo. Siga as instruções para [criar um circuito do ExpressRoute](expressroute-howto-circuit-classic.md) e ter o circuito habilitado pelo seu provedor de conectividade antes de continuar. O circuito ExpressRoute tem de estar num estado aprovisionado e ativado para que seja capaz de executar os cmdlets descritos abaixo.

### <a name="download-the-latest-powershell-cmdlets"></a>Baixe os cmdlets mais recentes do PowerShell

Instale as versões mais recentes de módulos do PowerShell de gestão de serviço do Azure (SM) e o módulo do ExpressRoute. Ao utilizar o exemplo seguinte, tenha em atenção que o número de versão (neste exemplo, 5.1.1) será alterado à medida que as versões mais recentes dos cmdlets são lançadas.

```powershell
Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\Azure\Azure.psd1'
Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\ExpressRoute\ExpressRoute.psd1'
```

Para obter mais informações, consulte [introdução aos cmdlets Azure PowerShell](/powershell/azure/overview) para obter diretrizes passo a passo sobre como configurar seu computador para usar os módulos Azure PowerShell.

### <a name="sign-in"></a>Iniciar sessão

Para entrar em sua conta do Azure, use os exemplos a seguir:

1. Abra a consola do PowerShell com direitos elevados e ligue-se à sua conta.

   ```powershell
   Connect-AzAccount
   ```
2. Verifique as subscrições da conta.

   ```powershell
   Get-AzSubscription
   ```
3. Se tiver mais do que uma subscrição, selecione a subscrição que pretende utilizar.

   ```powershell
   Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
   ```

4. Em seguida, utilize o cmdlet seguinte para adicionar a sua subscrição do Azure para o PowerShell para o modelo de implementação clássica.

   ```powershell
   Add-AzureAccount
   ```

## <a name="azure-private-peering"></a>Peering privado do Azure

Esta secção fornece instruções sobre como criar, obter, atualizar e eliminar a configuração do peering privado do Azure para um circuito ExpressRoute. 

### <a name="to-create-azure-private-peering"></a>Para criar um peering privado do Azure

1. **Crie um circuito do ExpressRoute.**

   Siga as instruções para criar um [circuito ExpressRoute](expressroute-howto-circuit-classic.md) e solicite ao fornecedor de conectividade que o aprovisione. Se o seu fornecedor de conectividade oferecer serviços geridos de Camada 3, pode solicitar-lhe que ative o peering privado do Azure por si. Nesse caso, não necessita de seguir as instruções indicadas nas secções seguintes. No entanto, se o seu fornecedor de conectividade não fizer a gestão do encaminhamento por si, depois de criar o seu circuito, siga as instruções abaixo.
2. **Verifique o circuito do ExpressRoute para certificar-se de que ele está provisionado.**
   
   Verifique se o circuito do ExpressRoute foi provisionado e também está habilitado.

   ```powershell
   Get-AzureDedicatedCircuit -ServiceKey "*********************************"
   ```

   Exibir

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
   
   Certifique-se de que o circuito seja mostrado como provisionado e habilitado. Se não estiver, trabalhe com seu provedor de conectividade para obter seu circuito para o estado e o status necessários.

   ```powershell
   ServiceProviderProvisioningState : Provisioned
   Status                           : Enabled
   ```
3. **Configure o emparelhamento privado do Azure para o circuito.**

   Confirme que tem os seguintes itens antes de continuar com os passos seguintes:
   
   * Uma sub-rede /30 para a ligação primária. Esta não pode fazer parte de qualquer espaço de endereço reservado para redes virtuais.
   * Uma sub-rede /30 para a ligação secundária. Esta não pode fazer parte de qualquer espaço de endereço reservado para redes virtuais.
   * Um ID de VLAN válido para estabelecer este peering. Verifique se nenhum outro emparelhamento no circuito usa a mesma ID de VLAN.
   * Número AS para peering. Pode utilizar números AS de 2 e 4 bytes. Pode utilizar um número AS privado para este peering. Verifique se você não está usando o 65515.
   * Um hash MD5 se optar por utilizar um. **Opcional**.
     
   Você pode usar o exemplo a seguir para configurar o emparelhamento privado do Azure para seu circuito:

   ```powershell
   New-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 100
   ```    

   Se você quiser usar um hash MD5, use o exemplo a seguir para configurar o emparelhamento privado para seu circuito:

   ```powershell
   New-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 100 -SharedKey "A1B2C3D4"
   ```
     
   > [!IMPORTANT]
   > Verifique se você especificou o número as como o ASN de emparelhamento, não o cliente ASN.
   > 

### <a name="to-view-azure-private-peering-details"></a>Para ver os detalhes do peering privado do Azure

Você pode exibir os detalhes de configuração usando o seguinte cmdlet:

```powershell
Get-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"
```

Exibir

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

Pode atualizar qualquer parte da configuração com o cmdlet seguinte. No exemplo a seguir, a ID de VLAN do circuito está sendo atualizada de 100 para 500.

```powershell
Set-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 500 -SharedKey "A1B2C3D4"
```

### <a name="to-delete-azure-private-peering"></a>Para eliminar um peering privado do Azure

Pode remover a sua configuração de peering executando o cmdlet seguinte. Você deve verificar se todas as redes virtuais estão desvinculadas do circuito do ExpressRoute antes de executar este cmdlet.

```powershell
Remove-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"
```

## <a name="azure-public-peering"></a>Peering público do Azure

Esta secção fornece instruções sobre como criar, obter, atualizar e eliminar a configuração do peering público do Azure para um circuito ExpressRoute.

> [!NOTE]
> O emparelhamento público do Azure foi preterido para novos circuitos.
>

### <a name="to-create-azure-public-peering"></a>Para criar um peering público do Azure

1. **Crie um circuito do ExpressRoute**

   Siga as instruções para criar um [circuito ExpressRoute](expressroute-howto-circuit-classic.md) e solicite ao fornecedor de conectividade que o aprovisione. Se o seu fornecedor de conectividade oferecer serviços geridos de Camada 3, pode solicitar-lhe que ative o peering público do Azure por si. Nesse caso, não necessita de seguir as instruções indicadas nas secções seguintes. No entanto, se o seu fornecedor de conectividade não fizer a gestão do encaminhamento por si, depois de criar o seu circuito, siga as instruções abaixo.
2. **Verifique o circuito do ExpressRoute para verificar se ele está provisionado**

   Verifique primeiro se o circuito ExpressRoute está aprovisionado e também Ativado.

   ```powershell
   Get-AzureDedicatedCircuit -ServiceKey "*********************************"
   ```

   Exibir

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
   
   Verifique se o circuito é mostrado como provisionado e habilitado. Se não estiver, trabalhe com seu provedor de conectividade para obter seu circuito para o estado e o status necessários.

   ```powershell
   ServiceProviderProvisioningState : Provisioned
   Status                           : Enabled
   ```
4. **Configurar o emparelhamento público do Azure para o circuito**
   
   Verifique se você tem as seguintes informações antes de prosseguir:
   
   * Uma sub-rede /30 para a ligação primária. Esta tem de ser um prefixo IPv4 válido.
   * Uma sub-rede /30 para a ligação secundária. Esta tem de ser um prefixo IPv4 válido.
   * Um ID de VLAN válido para estabelecer este peering. Verifique se nenhum outro emparelhamento no circuito usa a mesma ID de VLAN.
   * Número AS para peering. Pode utilizar números AS de 2 e 4 bytes.
   * Um hash MD5 se optar por utilizar um. **Opcional**.

   > [!IMPORTANT]
   > Verifique se você especificou o número as como o ASN de emparelhamento e não o ASN do cliente.
   >  
     
   Você pode usar o exemplo a seguir para configurar o emparelhamento público do Azure para seu circuito:

   ```powershell
   New-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 200
   ```
     
   Se você quiser usar um hash MD5, use o exemplo a seguir para configurar seu circuito:
     
   ```powershell
   New-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 200 -SharedKey "A1B2C3D4"
   ```
     
### <a name="to-view-azure-public-peering-details"></a>Para ver os detalhes do peering público do Azure

Para exibir os detalhes de configuração, use o seguinte cmdlet:

```powershell
Get-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"
```

Exibir

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

Pode atualizar qualquer parte da configuração com o cmdlet seguinte. Neste exemplo, o ID de VLAN do circuito está a ser atualizado de 200 para 600.

```powershell
Set-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 600 -SharedKey "A1B2C3D4"
```

Verifique se o circuito é mostrado como provisionado e habilitado. 
### <a name="to-delete-azure-public-peering"></a>Para eliminar um peering público do Azure

Pode remover a sua configuração de peering executando o seguinte cmdlet:

```powershell
Remove-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"
```

## <a name="microsoft-peering"></a>Peering da Microsoft

Esta secção fornece instruções sobre como criar, obter, atualizar e eliminar a configuração do peering da Microsoft para um circuito ExpressRoute. 

### <a name="to-create-microsoft-peering"></a>Para criar peering da Microsoft

1. **Crie um circuito do ExpressRoute**
  
   Siga as instruções para criar um [circuito ExpressRoute](expressroute-howto-circuit-classic.md) e solicite ao fornecedor de conectividade que o aprovisione. Se o seu fornecedor de conectividade oferecer serviços geridos de Camada 3, pode solicitar-lhe que ative o peering privado do Azure por si. Nesse caso, não necessita de seguir as instruções indicadas nas secções seguintes. No entanto, se o seu fornecedor de conectividade não fizer a gestão do encaminhamento por si, depois de criar o seu circuito, siga as instruções abaixo.
2. **Verifique o circuito do ExpressRoute para verificar se ele está provisionado**

   Verifique se o circuito é mostrado como provisionado e habilitado. 
   
   ```powershell
   Get-AzureDedicatedCircuit -ServiceKey "*********************************"
   ```

   Exibir
   
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
   
   Verifique se o circuito é mostrado como provisionado e habilitado. Se não estiver, trabalhe com seu provedor de conectividade para obter seu circuito para o estado e o status necessários.

   ```powershell
   ServiceProviderProvisioningState : Provisioned
   Status                           : Enabled
   ```
3. **Configurar o emparelhamento da Microsoft para o circuito**
   
    Confirme que tem as seguintes informações antes de continuar.
   
   * Uma sub-rede /30 para a ligação primária. Esta tem de ser um prefixo IPv4 público válido detido por si e registado num RIR/TIR.
   * Uma sub-rede /30 para a ligação secundária. Esta tem de ser um prefixo IPv4 público válido detido por si e registado num RIR/TIR.
   * Um ID de VLAN válido para estabelecer este peering. Verifique se nenhum outro emparelhamento no circuito usa a mesma ID de VLAN.
   * Número AS para peering. Pode utilizar números AS de 2 e 4 bytes.
   * Prefixos anunciados: tem de fornecer uma lista de todos os prefixos que planeia anunciar durante a sessão de BGP. São aceites apenas prefixos de endereços IP públicos. Você pode enviar uma lista separada por vírgulas se planeja enviar um conjunto de prefixos. Estes prefixos têm de ser registados para si num RIR/TIR.
   * Cliente ASN: se estiver a anunciar prefixos que não estão registados para o número AS de peering, pode especificar o número AS no qual estão registados. **Opcional**.
   * Nome do registo de encaminhamento: pode especificar o RIR/TIR de acordo com o número AS e os prefixos aos quais estão registados.
   * Um hash MD5, se optar por utilizar um. **Opcional.**
     
   Execute o seguinte cmdlet para configurar o emparelhamento da Microsoft para seu circuito:
 
   ```powershell
   New-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -VlanId 300 -PeerAsn 1234 -CustomerAsn 2245 -AdvertisedPublicPrefixes "123.0.0.0/30" -RoutingRegistryName "ARIN" -SharedKey "A1B2C3D4"
   ```

### <a name="to-view-microsoft-peering-details"></a>Para ver os detalhes do peering da Microsoft

Você pode exibir os detalhes de configuração usando o seguinte cmdlet:

```powershell
Get-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"
```
Exibir

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

Você pode atualizar qualquer parte da configuração usando o seguinte cmdlet:

```powershell
Set-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -VlanId 300 -PeerAsn 1234 -CustomerAsn 2245 -AdvertisedPublicPrefixes "123.0.0.0/30" -RoutingRegistryName "ARIN" -SharedKey "A1B2C3D4"
```

### <a name="to-delete-microsoft-peering"></a>Para eliminar o peering da Microsoft

Pode remover a sua configuração de peering executando o seguinte cmdlet:

```powershell
Remove-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"
```

## <a name="next-steps"></a>Passos seguintes

Em seguida, [vincule uma VNet a um circuito do ExpressRoute](expressroute-howto-linkvnet-classic.md).

* Para obter mais informações sobre fluxos de trabalho, consulte [fluxos de trabalho do ExpressRoute](expressroute-workflows.md).
* Para obter mais informações sobre peering do circuito, veja [Circuitos ExpressRoute e domínios de encaminhamento](expressroute-circuit-peerings.md).
