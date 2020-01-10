---
title: Criar e gerenciar o emparelhamento público do Azure ExpressRoute
description: Saiba mais e gerencie o emparelhamento público do Azure
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: cherylmc
ms.openlocfilehash: bae44f67a485546ba29148a114d88df198f7c3e6
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75483092"
---
# <a name="create-and-manage-expressroute-public-peering"></a>Criar e gerenciar o emparelhamento público do ExpressRoute

> [!div class="op_single_selector"]
> * [Artigo – emparelhamento público](about-public-peering.md)
> * [Vídeo - peering público](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Artigo-emparelhamento da Microsoft](expressroute-circuit-peerings.md#microsoftpeering)
>

Este artigo ajuda você a criar e gerenciar a configuração de roteamento de emparelhamento público para um circuito do ExpressRoute. Você também pode verificar o status, atualizar ou excluir e desprovisionar emparelhamentos. Este artigo se aplica a circuitos do Resource Manager que foram criados antes do emparelhamento público ter sido preterido. Se você tiver um circuito já existente (criado antes do emparelhamento público ser preterido), você poderá gerenciar/configurar o emparelhamento público usando [Azure PowerShell](#powershell), [CLI do Azure](#cli)e o [portal do Azure](#portal).

>[!NOTE]
>O emparelhamento público foi preterido. Não é possível criar um emparelhamento público em novos circuitos do ExpressRoute. Se você tiver um novo circuito do ExpressRoute, use o [emparelhamento da Microsoft](expressroute-circuit-peerings.md#microsoftpeering) para seus serviços do Azure.
>

## <a name="connectivity"></a>Ligação

Conectividade é sempre iniciada a partir da WAN aos serviços do Microsoft Azure. Serviços do Microsoft Azure não será capazes de iniciar as ligações na sua rede através deste domínio de encaminhamento. Se o circuito do ExpressRoute estiver habilitado para o emparelhamento público do Azure, você poderá acessar os [intervalos de IP públicos usados no Azure](../virtual-network/virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) por meio do circuito.

Depois que o emparelhamento público estiver habilitado, você poderá se conectar à maioria dos serviços do Azure. Não podemos permitem-lhe escolher seletivamente os serviços para os quais podemos anunciar rotas.

* Serviços como o armazenamento do Azure, bancos de dados SQL e sites são oferecidos em endereços IP públicos.
* Por meio do domínio de roteamento de emparelhamento público, você pode se conectar de modo privado aos serviços hospedados em endereços IP públicos, incluindo VIPs de seus serviços de nuvem.
* Pode ligar o domínio de peering público para sua rede de Perímetro e ligar a todos os serviços do Azure em seus endereços IP públicos a partir da WAN sem ter de se ligar através da internet.

## <a name="services"></a>Serviços

Esta seção mostra os serviços disponíveis em emparelhamento público. Como o emparelhamento público é preterido, não há nenhum plano para adicionar serviços novos ou adicionais ao emparelhamento público. Se você usar o emparelhamento público e o serviço que deseja usar for compatível apenas com o emparelhamento da Microsoft, será necessário alternar para o emparelhamento da Microsoft. Consulte [emparelhamento da Microsoft](expressroute-faqs.md#microsoft-peering) para obter uma lista de serviços com suporte.

**Porta**

* Power BI
* A maioria dos serviços do Azure é suportada. Verifique diretamente com o serviço que você deseja usar para verificar o suporte.

**Sem suporte:**
  * CDN
  * Azure Front Door
  * Servidor de autenticação multifator (Herdado)
  * Gestor de Tráfego

Para validar a disponibilidade de um serviço específico, você pode verificar a documentação desse serviço para ver se há um intervalo reservado publicado para esse serviço. Em seguida, você pode pesquisar os intervalos de IP do serviço de destino e comparar com os intervalos listados nas [marcas de serviço e intervalos de IP do Azure – arquivo XML de nuvem pública](https://www.microsoft.com/download/details.aspx?id=56519). Como alternativa, você pode abrir um tíquete de suporte para o serviço em questão para fins de esclarecimento.

## <a name="compare"></a>Comparação de peering

[!INCLUDE [peering comparison](../../includes/expressroute-peering-comparison.md)]

> [!NOTE]
> O emparelhamento público do Azure tem 1 endereço IP NAT associado a cada sessão BGP. Para mais de 2 endereços IP NAT, mude para o emparelhamento da Microsoft. O emparelhamento da Microsoft permite que você configure suas próprias alocações NAT, bem como Use filtros de rota para anúncios de prefixo seletivo. Para obter mais informações, consulte [mover para o emparelhamento da Microsoft](https://docs.microsoft.com/azure/expressroute/how-to-move-peering).
>

## <a name="custom-route-filters"></a>Filtros de rota personalizados

Pode definir filtros de rota personalizada dentro da sua rede para consumir apenas as rotas que precisa. Consulte a [encaminhamento](expressroute-routing.md) página para obter informações detalhadas sobre a configuração do encaminhamento.

## <a name="powershell"></a>Etapas de Azure PowerShell


[!INCLUDE [CloudShell](../../includes/expressroute-cloudshell-powershell-about.md)]

Como o emparelhamento público é preterido, não é possível configurar o emparelhamento público em um novo circuito do ExpressRoute.

1. Verifique se você tem um circuito do ExpressRoute provisionado e também habilitado. Utilize o seguinte exemplo:

   ```azurepowershell-interactive
   Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   ```

   A resposta é semelhante ao seguinte exemplo:

   ```
   Name                             : ExpressRouteARMCircuit
   ResourceGroupName                : ExpressRouteResourceGroup
   Location                         : westus
   Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
   Etag                             : W/"################################"
   ProvisioningState                : Succeeded
   Sku                              : {
                                      "Name": "Standard_MeteredData",
                                       "Tier": "Standard",
                                       "Family": "MeteredData"
                                     }
   CircuitProvisioningState         : Enabled
   ServiceProviderProvisioningState : Provisioned
   ServiceProviderNotes             : 
   ServiceProviderProperties        : {
                                       "ServiceProviderName": "Equinix",
                                       "PeeringLocation": "Silicon Valley",
                                       "BandwidthInMbps": 200
                                     }
   ServiceKey                       : **************************************
   Peerings                         : []
   ```
2. Configure o peering público do Azure para o circuito. Certifique-se de que tem as seguintes informações antes de prosseguir.

   * Uma sub-rede /30 para a ligação primária. Esta tem de ser um prefixo IPv4 válido.
   * Uma sub-rede /30 para a ligação secundária. Esta tem de ser um prefixo IPv4 válido.
   * Um ID de VLAN válido para estabelecer este peering. Assegure que nenhum peering no circuito utiliza o mesmo ID de VLAN.
   * Número AS para peering. Pode utilizar números AS de 2 e 4 bytes.
   * Opcional:
   * Um hash MD5 se optar por utilizar um.

   Executar o exemplo seguinte para configurar o peering público do Azure para o seu circuito

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

   Se optar por utilizar um hash MD5, utilize o seguinte exemplo:

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100  -SharedKey "A1B2C3D4"

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

   > [!IMPORTANT]
   > Assegure que especifica o seu número AS como ASN de peering, não cliente ASN.
   > 
   >

### <a name="getpublic"></a>Para obter detalhes de peering públicos do Azure

Pode obter detalhes de configuração com o seguinte cmdlet:

```azurepowershell-interactive
  $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

  Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -Circuit $ckt
  ```

### <a name="updatepublic"></a>Para atualizar a configuração do peering público do Azure

Pode atualizar qualquer parte da configuração com o exemplo seguinte. Neste exemplo, o ID de VLAN do circuito está a ser atualizado de 200 para 600.

```azurepowershell-interactive
Set-AzExpressRouteCircuitPeeringConfig  -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 600

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="deletepublic"></a>Para eliminar o peering público do Azure

Pode remover a sua configuração de peering executando o seguinte exemplo:

```azurepowershell-interactive
Remove-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="cli"></a>Etapas de CLI do Azure


[!INCLUDE [CloudShell](../../includes/expressroute-cloudshell-powershell-about.md)]

1. Verifique o circuito de ExpressRoute para garantir que é aprovisionado e também ativado. Utilize o seguinte exemplo:

   ```azurecli-interactive
   az network express-route list
   ```

   A resposta é semelhante ao seguinte exemplo:

   ```azurecli
   "allowClassicOperations": false,
   "authorizations": [],
   "circuitProvisioningState": "Enabled",
   "etag": "W/\"1262c492-ffef-4a63-95a8-a6002736b8c4\"",
   "gatewayManagerEtag": null,
   "id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit",
   "location": "westus",
   "name": "MyCircuit",
   "peerings": [],
   "provisioningState": "Succeeded",
   "resourceGroup": "ExpressRouteResourceGroup",
   "serviceKey": "1d05cf70-1db5-419f-ad86-1ca62c3c125b",
   "serviceProviderNotes": null,
   "serviceProviderProperties": {
    "bandwidthInMbps": 200,
    "peeringLocation": "Silicon Valley",
    "serviceProviderName": "Equinix"
   },
   "serviceProviderProvisioningState": "Provisioned",
   "sku": {
    "family": "UnlimitedData",
    "name": "Standard_MeteredData",
    "tier": "Standard"
   },
   "tags": null,
   "type": "Microsoft.Network/expressRouteCircuits]
   ```

2. Configure o peering público do Azure para o circuito. Certifique-se de que tem as seguintes informações antes de prosseguir.

   * Uma sub-rede /30 para a ligação primária. Esta tem de ser um prefixo IPv4 válido.
   * Uma sub-rede /30 para a ligação secundária. Esta tem de ser um prefixo IPv4 válido.
   * Um ID de VLAN válido para estabelecer este peering. Assegure que nenhum peering no circuito utiliza o mesmo ID de VLAN.
   * Número AS para peering. Pode utilizar números AS de 2 e 4 bytes.
   * **Opcional –** um hash MD5 se optar por utilizar um.

   Execute o exemplo seguinte para configurar o peering público do Azure para o seu circuito:

   ```azurecli-interactive
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 12.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 12.0.0.4/30 --vlan-id 200 --peering-type AzurePublicPeering
   ```

   Se optar por utilizar um hash MD5, utilize o seguinte exemplo:

   ```azurecli-interactive
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 12.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 12.0.0.4/30 --vlan-id 200 --peering-type AzurePublicPeering --SharedKey "A1B2C3D4"
   ```

   > [!IMPORTANT]
   > Assegure que especifica o seu número AS como ASN de peering, não cliente ASN.

### <a name="getpublic"></a>Para ver os detalhes de peering públicos do Azure

Pode obter detalhes de configuração com o exemplo seguinte:

```azurecli
az network express-route peering show -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePublicPeering
```

O resultado é semelhante ao seguinte exemplo:

```azurecli
{
  "azureAsn": 12076,
  "etag": "W/\"2e97be83-a684-4f29-bf3c-96191e270666\"",
  "gatewayManagerEtag": "18",
  "id": "/subscriptions/9a0c2943-e0c2-4608-876c-e0ddffd1211b/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/peerings/AzurePublicPeering",
  "lastModifiedBy": "Customer",
  "microsoftPeeringConfig": null,
  "name": "AzurePublicPeering",
  "peerAsn": 7671,
  "peeringType": "AzurePublicPeering",
  "primaryAzurePort": "",
  "primaryPeerAddressPrefix": "",
  "provisioningState": "Succeeded",
  "resourceGroup": "ExpressRouteResourceGroup",
  "routeFilter": null,
  "secondaryAzurePort": "",
  "secondaryPeerAddressPrefix": "",
  "sharedKey": null,
  "state": "Enabled",
  "stats": null,
  "vlanId": 100
}
```

### <a name="updatepublic"></a>Para atualizar a configuração do peering público do Azure

Pode atualizar qualquer parte da configuração com o exemplo seguinte. Neste exemplo, o ID de VLAN do circuito está a ser atualizado de 200 para 600.

```azurecli-interactive
az network express-route peering update --vlan-id 600 -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePublicPeering
```

### <a name="deletepublic"></a>Para eliminar o peering público do Azure

Pode remover a sua configuração de peering executando o seguinte exemplo:

```azurecli-interactive
az network express-route peering delete -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePublicPeering
```

## <a name="portal"></a>Etapas de portal do Azure

Para configurar o emparelhamento, use as etapas do PowerShell ou da CLI contidas neste artigo. Para gerenciar um emparelhamento, você pode usar as seções abaixo. Para referência, essas etapas são semelhantes ao gerenciamento de um [emparelhamento da Microsoft no portal](expressroute-howto-routing-portal-resource-manager.md#msft).

### <a name="get"></a>Para ver os detalhes de peering públicos do Azure

Exiba as propriedades do emparelhamento público do Azure selecionando o emparelhamento no Portal.

### <a name="update"></a>Para atualizar a configuração do peering público do Azure

Selecione a linha para emparelhamento e, em seguida, modifique as propriedades de emparelhamento.

### <a name="delete"></a>Para eliminar o peering público do Azure

Remova a configuração de emparelhamento selecionando o ícone Excluir.

## <a name="next-steps"></a>Passos seguintes

Próxima etapa, [vincular uma rede virtual a um circuito do ExpressRoute](expressroute-howto-linkvnet-arm.md).

* Para obter mais informações sobre o fluxo de trabalho do ExpressRoute, veja [Fluxos de trabalho do ExpressRoute](expressroute-workflows.md).
* Para obter mais informações sobre peering do circuito, veja [Circuitos ExpressRoute e domínios de encaminhamento](expressroute-circuit-peerings.md).
* Para obter mais informações sobre como trabalhar com redes virtuais, veja [Descrição geral da rede virtual](../virtual-network/virtual-networks-overview.md).