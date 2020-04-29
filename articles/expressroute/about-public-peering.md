---
title: Criar e gerir o público azure ExpressRoute
description: Conheça e gerea o público azure
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: cherylmc
ms.openlocfilehash: 8c1afac834fb9abb2cbf82f16f046a1624b251f1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79481138"
---
# <a name="create-and-manage-expressroute-public-peering"></a>Criar e gerir o público expressRoute peering

> [!div class="op_single_selector"]
> * [Artigo - Público](about-public-peering.md)
> * [Vídeo - Público a espreitar](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Artigo - Microsoft peering](expressroute-circuit-peerings.md#microsoftpeering)
>

Este artigo ajuda-o a criar e gerir a configuração de encaminhamento público para um circuito ExpressRoute. Também pode verificar o estado, atualizar ou apagar e desprovisionar os pares. Este artigo aplica-se aos circuitos do Gestor de Recursos que foram criados antes de o público ser depreciado. Se tiver um circuito anteriormente existente (criado antes de o público ser depreciado), pode gerir/configurar o público através [do Azure PowerShell,](#powershell) [Azure CLI](#cli)e do [portal Azure](#portal).

>[!NOTE]
>O público está premeditado. Não é possível criar o público a espreitar novos circuitos ExpressRoute. Se tiver um novo circuito ExpressRoute, utilize a [Microsoft a espreitar](expressroute-circuit-peerings.md#microsoftpeering) pelos seus serviços Azure.
>

## <a name="connectivity"></a>Conectividade

A conectividade é sempre iniciada desde os seus serviços WAN até microsoft Azure. Os serviços do Microsoft Azure não poderão iniciar ligações na sua rede através deste domínio de encaminhamento. Se o seu circuito ExpressRoute estiver ativado para o público azure, pode aceder às gamas públicas de [IP utilizadas em Azure](../virtual-network/virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) sobre o circuito.

Uma vez que o público esteja ativado, você pode ligar-se à maioria dos serviços Azure. Não lhe permitimos escolher seletivamente serviços para os quais anunciamos rotas.

* Serviços como o Armazenamento Azure, bases de dados SQL e Websites são oferecidos em endereços IP públicos.
* Através do domínio de encaminhamento público, pode conectar-se privadamente a serviços hospedados em endereços IP públicos, incluindo VIPs dos seus serviços na nuvem.
* Pode ligar o domínio público ao seu DMZ e ligar-se a todos os serviços Azure nos seus endereços IP públicos a partir do seu WAN sem ter de se ligar através da internet.

## <a name="services"></a><a name="services"></a>Serviços

Esta secção mostra os serviços disponíveis em público. Como o público é depreciado, não há plano para adicionar serviços novos ou adicionais ao público. Se utilizar o peering público e o serviço que pretende utilizar é suportado apenas por via da Microsoft, tem de mudar para o peering da Microsoft. Consulte [a Microsoft à procura](expressroute-faqs.md#microsoft-peering) de uma lista de serviços suportados.

**Apoiado:**

* Power BI
* A maioria dos serviços azure são apoiados. Verifique diretamente com o serviço que pretende utilizar para verificar o suporte.

**Não suportado:**
  * CDN
  * Azure Front Door
  * Servidor de Autenticação Multifactor (legado)
  * Gestor de Tráfego

Para validar a disponibilidade de um serviço específico, pode consultar a documentação desse serviço para ver se existe uma gama reservada publicada para esse serviço. Em seguida, poderá consultar as gamas IP do serviço alvo e comparar com as gamas listadas nas [gamas e etiquetas de serviço Azure IP – Ficheiro Public Cloud XML](https://www.microsoft.com/download/details.aspx?id=56519). Em alternativa, pode abrir um bilhete de apoio para o serviço em questão para esclarecimento.

## <a name="peering-comparison"></a><a name="compare"></a>Comparação de olhares

[!INCLUDE [peering comparison](../../includes/expressroute-peering-comparison.md)]

> [!NOTE]
> O público azure tem 1 endereço IP NAT associado a cada sessão de BGP. Para maiores de 2 endereços IP NAT, mude para o peering da Microsoft. O peering da Microsoft permite-lhe configurar as suas próprias alocações NAT, bem como utilizar filtros de rotas para anúncios de prefixo seletivo. Para mais informações, consulte [Move to Microsoft peering](https://docs.microsoft.com/azure/expressroute/how-to-move-peering).
>

## <a name="custom-route-filters"></a>Filtros de rota personalizadas

Pode definir filtros de rotas personalizados dentro da sua rede para consumir apenas as rotas de que necessita. Consulte a página [de Encaminhamento](expressroute-routing.md) para obter informações detalhadas sobre a configuração do encaminhamento.

## <a name="azure-powershell-steps"></a><a name="powershell"></a>Passos Azure PowerShell


[!INCLUDE [CloudShell](../../includes/expressroute-cloudshell-powershell-about.md)]

Como o público está premeditado, não é possível configurar o público num novo circuito ExpressRoute.

1. Verifique se tem um circuito ExpressRoute que está aprovisionado e também está ativado. Utilize o seguinte exemplo:

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
2. Configure o peering público do Azure para o circuito. Certifique-se de que tem as seguintes informações antes de avançar mais.

   * Uma sub-rede /30 para a ligação primária. Esta tem de ser um prefixo IPv4 válido.
   * Uma sub-rede /30 para a ligação secundária. Esta tem de ser um prefixo IPv4 válido.
   * Um ID de VLAN válido para estabelecer este peering. Assegure que nenhum peering no circuito utiliza o mesmo ID de VLAN.
   * Número AS para peering. Pode utilizar números AS de 2 e 4 bytes.
   * Opcional:
   * Um hash MD5 se optar por utilizar um.

   Corra o seguinte exemplo para configurar o público azure para o seu circuito

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

   Se optar por utilizar um haxixe MD5, utilize o seguinte exemplo:

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100  -SharedKey "A1B2C3D4"

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

   > [!IMPORTANT]
   > Assegure que especifica o seu número AS como ASN de peering, não cliente ASN.
   > 
   >

### <a name="to-get-azure-public-peering-details"></a><a name="getpublic"></a>Para obter detalhes públicos de Azure

Pode obter detalhes de configuração utilizando o seguinte cmdlet:

```azurepowershell-interactive
  $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

  Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -Circuit $ckt
  ```

### <a name="to-update-azure-public-peering-configuration"></a><a name="updatepublic"></a>Para atualizar a configuração do peering público do Azure

Pode atualizar qualquer parte da configuração utilizando o seguinte exemplo. Neste exemplo, o ID VLAN do circuito está a ser atualizado de 200 a 600.

```azurepowershell-interactive
Set-AzExpressRouteCircuitPeeringConfig  -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 600

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-delete-azure-public-peering"></a><a name="deletepublic"></a>Para eliminar um peering público do Azure

Pode remover a sua configuração de observação executando o seguinte exemplo:

```azurepowershell-interactive
Remove-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="azure-cli-steps"></a><a name="cli"></a>Passos Azure CLI


[!INCLUDE [CloudShell](../../includes/expressroute-cloudshell-powershell-about.md)]

1. Verifique o circuito ExpressRoute para garantir que está aprovisionado e também ativado. Utilize o seguinte exemplo:

   ```azurecli-interactive
   az network express-route list
   ```

   A resposta é semelhante ao seguinte exemplo:

   ```output
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

2. Configure o peering público do Azure para o circuito. Certifique-se de que tem as seguintes informações antes de avançar mais.

   * Uma sub-rede /30 para a ligação primária. Esta tem de ser um prefixo IPv4 válido.
   * Uma sub-rede /30 para a ligação secundária. Esta tem de ser um prefixo IPv4 válido.
   * Um ID de VLAN válido para estabelecer este peering. Assegure que nenhum peering no circuito utiliza o mesmo ID de VLAN.
   * Número AS para peering. Pode utilizar números AS de 2 e 4 bytes.
   * **Opcional -** Um haxixe MD5 se optar por usar um.

   Execute o seguinte exemplo para configurar o público azure para o seu circuito:

   ```azurecli-interactive
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 12.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 12.0.0.4/30 --vlan-id 200 --peering-type AzurePublicPeering
   ```

   Se optar por utilizar um haxixe MD5, utilize o seguinte exemplo:

   ```azurecli-interactive
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 12.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 12.0.0.4/30 --vlan-id 200 --peering-type AzurePublicPeering --SharedKey "A1B2C3D4"
   ```

   > [!IMPORTANT]
   > Assegure que especifica o seu número AS como ASN de peering, não cliente ASN.

### <a name="to-view-azure-public-peering-details"></a><a name="getpublic"></a>Para ver os detalhes do peering público do Azure

Pode obter detalhes de configuração utilizando o seguinte exemplo:

```azurecli
az network express-route peering show -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePublicPeering
```

O resultado é semelhante ao seguinte exemplo:

```output
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

### <a name="to-update-azure-public-peering-configuration"></a><a name="updatepublic"></a>Para atualizar a configuração do peering público do Azure

Pode atualizar qualquer parte da configuração utilizando o seguinte exemplo. Neste exemplo, o ID VLAN do circuito está a ser atualizado de 200 a 600.

```azurecli-interactive
az network express-route peering update --vlan-id 600 -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePublicPeering
```

### <a name="to-delete-azure-public-peering"></a><a name="deletepublic"></a>Para eliminar um peering público do Azure

Pode remover a sua configuração de observação executando o seguinte exemplo:

```azurecli-interactive
az network express-route peering delete -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePublicPeering
```

## <a name="azure-portal-steps"></a><a name="portal"></a>Passos do portal Azure

Para configurar o epeering, utilize os passos PowerShell ou CLI contidos neste artigo. Para gerir um epeering, pode utilizar as secções abaixo. Para referência, estes passos parecem semelhantes à gestão de um [microsoft peering no portal](expressroute-howto-routing-portal-resource-manager.md#msft).

### <a name="to-view-azure-public-peering-details"></a><a name="get"></a>Para ver os detalhes do peering público do Azure

Veja as propriedades do público azure espreitando selecionando o olhar no portal.

### <a name="to-update-azure-public-peering-configuration"></a><a name="update"></a>Para atualizar a configuração do peering público do Azure

Selecione a linha para espreitar e, em seguida, modifique as propriedades de observação.

### <a name="to-delete-azure-public-peering"></a><a name="delete"></a>Para eliminar um peering público do Azure

Remova a sua configuração de observação selecionando o ícone de exclusão.

## <a name="next-steps"></a>Passos seguintes

Próximo passo, [Ligue uma rede virtual a um circuito ExpressRoute](expressroute-howto-linkvnet-arm.md).

* Para obter mais informações sobre o fluxo de trabalho do ExpressRoute, veja [Fluxos de trabalho do ExpressRoute](expressroute-workflows.md).
* Para obter mais informações sobre peering do circuito, veja [Circuitos ExpressRoute e domínios de encaminhamento](expressroute-circuit-peerings.md).
* Para obter mais informações sobre como trabalhar com redes virtuais, veja [Descrição geral da rede virtual](../virtual-network/virtual-networks-overview.md).