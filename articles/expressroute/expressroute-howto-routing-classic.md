---
title: 'Configure o peering para um circuito - ExpressRoute: Azure: clássico | Documentos da Microsoft'
description: Este artigo explica-lhe os passos para criar e aprovisionar o peering privado, público e da Microsoft de um circuito ExpressRoute. Este artigo também mostra como verificar o estado, atualizar ou eliminar peerings no seu circuito.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/11/2018
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: b7ac8c0f5c45382dd3beb943699efb198a94e0eb
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57402347"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit-classic"></a>Criar e modificar um peering para um circuito do ExpressRoute (clássico)
> [!div class="op_single_selector"]
> * [Portal do Azure](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [CLI do Azure](howto-routing-cli.md)
> * [Vídeo - peering privado](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Vídeo - peering público](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Vídeo - peering da Microsoft](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (clássico)](expressroute-howto-routing-classic.md)
> 

Este artigo orienta-o pelos passos para criar e gerir a configuração de peering/encaminhamento de mensagens em fila para um circuito do ExpressRoute com o PowerShell e o modelo de implementação clássica. Os passos abaixo também irão mostrar como verificar o estado, atualizar ou eliminar e retirar o aprovisionamento do peerings para um circuito ExpressRoute. Pode configurar um, dois ou todos os três peerings (Azure privado, Azure público e Microsoft) para um circuito do ExpressRoute. Pode configurar peerings em qualquer ordem que escolha. No entanto, tem de confirmar que conclui a configuração de cada peering, um de cada vez. 

Estas instruções aplicam-se apenas aos circuitos criados com fornecedores de serviços que oferecem serviços de conectividade de camada 2. Se estiver a utilizar um fornecedor de serviços que oferece geridos de camada 3 serviços (normalmente uma VPN de IP, como MPLS), o seu fornecedor de conectividade irá configurar e gerir encaminhamento por si.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Acerca dos modelos de implementação do Azure**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="configuration-prerequisites"></a>Pré-requisitos da configuração

* Confirme que reviu as páginas [pré-requisitos](expressroute-prerequisites.md), [requisitos de encaminhamento](expressroute-routing.md) página e [fluxos de trabalho](expressroute-workflows.md) antes de iniciar a configuração.
* Deve ter um circuito ExpressRoute ativo. Siga as instruções para [criar um circuito do ExpressRoute](expressroute-howto-circuit-classic.md) e ter o circuito ativado pelo seu fornecedor de conectividade antes de continuar. O circuito ExpressRoute tem de estar num estado aprovisionado e ativado para que seja capaz de executar os cmdlets descritos abaixo.

### <a name="download-the-latest-powershell-cmdlets"></a>Transferir os cmdlets do PowerShell mais recente

Instale as versões mais recentes de módulos do PowerShell de gestão de serviço do Azure (SM) e o módulo do ExpressRoute. Ao utilizar o exemplo seguinte, tenha em atenção que o número de versão (neste exemplo, 5.1.1) será alterado à medida que as versões mais recentes dos cmdlets são lançadas.

```powershell
Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\Azure\Azure.psd1'
Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\ExpressRoute\ExpressRoute.psd1'
```

Para obter mais informações, consulte [introdução aos cmdlets do Azure PowerShell](/powershell/azure/overview) para obter orientações passo a passo sobre como configurar o seu computador para utilizar os módulos do Azure PowerShell.

### <a name="sign-in"></a>Iniciar sessão

Para iniciar sessão na sua conta do Azure, utilize os exemplos seguintes:

1. Abra a consola do PowerShell com direitos elevados e ligue-se à sua conta.

  ```powershell
  Connect-AzureRmAccount
  ```
2. Verifique as subscrições da conta.

  ```powershell
  Get-AzureRmSubscription
  ```
3. Se tiver mais do que uma subscrição, selecione a subscrição que pretende utilizar.

  ```powershell
  Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
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
2. **Verifique o circuito de ExpressRoute para se certificar de que está aprovisionado.**
   
  Verifique se o circuito do ExpressRoute está aprovisionado e também ativado.

  ```powershell
  Get-AzureDedicatedCircuit -ServiceKey "*********************************"
  ```

  Retorno:

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
   
  Certifique-se de que o circuito mostra como aprovisionado e ativado. Se não estiver, trabalhe com o fornecedor de conectividade para obter o seu circuito para o estado necessário e o estado.

  ```powershell
  ServiceProviderProvisioningState : Provisioned
  Status                           : Enabled
  ```
3. **Configure o peering privado do Azure para o circuito.**

  Confirme que tem os seguintes itens antes de continuar com os passos seguintes:
   
  * Uma sub-rede /30 para a ligação primária. Esta não pode fazer parte de qualquer espaço de endereço reservado para redes virtuais.
  * Uma sub-rede /30 para a ligação secundária. Esta não pode fazer parte de qualquer espaço de endereço reservado para redes virtuais.
  * Um ID de VLAN válido para estabelecer este peering. Certifique-se de que nenhum peering no circuito utiliza o mesmo ID de VLAN.
  * Número AS para peering. Pode utilizar números AS de 2 e 4 bytes. Pode utilizar um número AS privado para este peering. Certifique-se de que não está a utilizar 65515.
  * Um hash MD5 se optar por utilizar um. **Opcional**.
     
  Pode utilizar o exemplo seguinte para configurar o peering privado do Azure para o seu circuito:

  ```powershell
  New-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 100
  ```    

  Se pretender utilizar um hash MD5, utilize o exemplo seguinte para configurar o peering privado para o seu circuito:

  ```powershell
  New-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 100 -SharedKey "A1B2C3D4"
  ```
     
  > [!IMPORTANT]
  > Certifique-se de que especifica o seu número as como ASN de peering, não cliente ASN.
  > 

### <a name="to-view-azure-private-peering-details"></a>Para ver os detalhes do peering privado do Azure

Pode ver os detalhes de configuração com o seguinte cmdlet:

```powershell
Get-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"
```

Retorno:

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

Pode atualizar qualquer parte da configuração com o cmdlet seguinte. No exemplo a seguir, o ID de VLAN do circuito está a ser atualizado de 100 para 500.

```powershell
Set-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 500 -SharedKey "A1B2C3D4"
```

### <a name="to-delete-azure-private-peering"></a>Para eliminar um peering privado do Azure

Pode remover a sua configuração de peering executando o cmdlet seguinte. Deve certificar-se de que todas as redes virtuais são desassociadas do circuito do ExpressRoute antes de executar este cmdlet.

```powershell
Remove-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"
```

## <a name="azure-public-peering"></a>Peering público do Azure

Esta secção fornece instruções sobre como criar, obter, atualizar e eliminar a configuração do peering público do Azure para um circuito ExpressRoute.

### <a name="to-create-azure-public-peering"></a>Para criar um peering público do Azure

1. **Crie um circuito do ExpressRoute**

  Siga as instruções para criar um [circuito ExpressRoute](expressroute-howto-circuit-classic.md) e solicite ao fornecedor de conectividade que o aprovisione. Se o seu fornecedor de conectividade oferecer serviços geridos de Camada 3, pode solicitar-lhe que ative o peering público do Azure por si. Nesse caso, não necessita de seguir as instruções indicadas nas secções seguintes. No entanto, se o seu fornecedor de conectividade não fizer a gestão do encaminhamento por si, depois de criar o seu circuito, siga as instruções abaixo.
2. **Verifique o circuito de ExpressRoute para verificar se é aprovisionado**

  Verifique primeiro se o circuito ExpressRoute está aprovisionado e também Ativado.

  ```powershell
  Get-AzureDedicatedCircuit -ServiceKey "*********************************"
  ```

  Retorno:

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
   
  Certifique-se de que o circuito mostra como aprovisionado e ativado. Se não estiver, trabalhe com o fornecedor de conectividade para obter o seu circuito para o estado necessário e o estado.

  ```powershell
  ServiceProviderProvisioningState : Provisioned
  Status                           : Enabled
  ```
4. **Configurar o peering público do Azure para o circuito**
   
  Certifique-se de que tem as seguintes informações antes de continuar:
   
  * Uma sub-rede /30 para a ligação primária. Esta tem de ser um prefixo IPv4 válido.
  * Uma sub-rede /30 para a ligação secundária. Esta tem de ser um prefixo IPv4 válido.
  * Um ID de VLAN válido para estabelecer este peering. Certifique-se de que nenhum peering no circuito utiliza o mesmo ID de VLAN.
  * Número AS para peering. Pode utilizar números AS de 2 e 4 bytes.
  * Um hash MD5 se optar por utilizar um. **Opcional**.

  > [!IMPORTANT]
  > Certifique-se de que especifica o seu número as como ASN de peering e não cliente ASN.
  >  
     
  Pode utilizar o exemplo seguinte para configurar o peering público do Azure para o seu circuito:

  ```powershell
  New-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 200
  ```
     
  Se pretender utilizar um hash MD5, utilize o exemplo seguinte para configurar o seu circuito:
     
  ```powershell
  New-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 200 -SharedKey "A1B2C3D4"
  ```
     
### <a name="to-view-azure-public-peering-details"></a>Para ver os detalhes do peering público do Azure

Para ver os detalhes de configuração, utilize o seguinte cmdlet:

```powershell
Get-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"
```

Retorno:

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

Certifique-se de que o circuito mostra como aprovisionado e ativado. 
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
2. **Verifique o circuito de ExpressRoute para verificar se é aprovisionado**

  Certifique-se de que o circuito mostra como aprovisionado e ativado. 
   
  ```powershell
  Get-AzureDedicatedCircuit -ServiceKey "*********************************"
  ```

  Retorno:
   
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
   
  Certifique-se de que o circuito mostra como aprovisionado e ativado. Se não estiver, trabalhe com o fornecedor de conectividade para obter o seu circuito para o estado necessário e o estado.

  ```powershell
  ServiceProviderProvisioningState : Provisioned
  Status                           : Enabled
  ```
3. **Configurar o peering da Microsoft para o circuito**
   
    Confirme que tem as seguintes informações antes de continuar.
   
   * Uma sub-rede /30 para a ligação primária. Esta tem de ser um prefixo IPv4 público válido detido por si e registado num RIR/TIR.
   * Uma sub-rede /30 para a ligação secundária. Esta tem de ser um prefixo IPv4 público válido detido por si e registado num RIR/TIR.
   * Um ID de VLAN válido para estabelecer este peering. Certifique-se de que nenhum peering no circuito utiliza o mesmo ID de VLAN.
   * Número AS para peering. Pode utilizar números AS de 2 e 4 bytes.
   * Prefixos anunciados: Tem de fornecer uma lista de todos os prefixos que planeia anunciar durante a sessão BGP. São aceites apenas prefixos de endereços IP públicos. Pode enviar uma lista separada por vírgulas se pretender enviar um conjunto de prefixos. Estes prefixos têm de ser registados para si num RIR/TIR.
   * Cliente ASN: Se estiver a anunciar prefixos que não estão registados para o peering número, pode especificar o número as no qual estão registados. **Opcional**.
   * Nome do registo de encaminhamento: Pode especificar o RIR / IRR em relação aos quais o número e os prefixos são registados.
   * Um hash MD5, se optar por utilizar um. **Opcional.**
     
  Execute o cmdlet seguinte para configurar o peering da Microsoft para o seu circuito:
 
  ```powershell
  New-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -VlanId 300 -PeerAsn 1234 -CustomerAsn 2245 -AdvertisedPublicPrefixes "123.0.0.0/30" -RoutingRegistryName "ARIN" -SharedKey "A1B2C3D4"
  ```

### <a name="to-view-microsoft-peering-details"></a>Para ver os detalhes do peering da Microsoft

Pode ver os detalhes de configuração com o seguinte cmdlet:

```powershell
Get-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"
```
Retorno:

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

Pode atualizar qualquer parte da configuração com o seguinte cmdlet:

```powershell
Set-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -VlanId 300 -PeerAsn 1234 -CustomerAsn 2245 -AdvertisedPublicPrefixes "123.0.0.0/30" -RoutingRegistryName "ARIN" -SharedKey "A1B2C3D4"
```

### <a name="to-delete-microsoft-peering"></a>Para eliminar o peering da Microsoft

Pode remover a sua configuração de peering executando o seguinte cmdlet:

```powershell
Remove-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"
```

## <a name="next-steps"></a>Passos Seguintes

Em seguida, [ligar uma VNet a um circuito do ExpressRoute](expressroute-howto-linkvnet-classic.md).

* Para obter mais informações sobre fluxos de trabalho, consulte [fluxos de trabalho do ExpressRoute](expressroute-workflows.md).
* Para obter mais informações sobre peering do circuito, veja [Circuitos ExpressRoute e domínios de encaminhamento](expressroute-circuit-peerings.md).
