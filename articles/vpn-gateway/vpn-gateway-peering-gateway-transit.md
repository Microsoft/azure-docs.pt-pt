---
title: 'Configure o trânsito de gateway VPN para o peering de rede virtual: O Azure Resource Manager | Documentos da Microsoft'
description: Configure o trânsito do gateway de VPN para peering de rede virtual.
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: ''
tags: azure-resource-manager
ms.assetid: 0683c664-9c03-40a4-b198-a6529bf1ce8b
ms.service: vpn-gateway
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/25/2018
ms.author: yushwang
ms.openlocfilehash: 05b25a524894248152114ca9c756d4a0f8944ad8
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58199635"
---
# <a name="configure-vpn-gateway-transit-for-virtual-network-peering"></a>Configurar o trânsito do gateway de VPN para peering de rede virtual

Este artigo ajuda-o a configurar o trânsito do gateway para peering de rede virtual. O [Peering de rede virtual](../virtual-network/virtual-network-peering-overview.md) liga facilmente duas redes virtuais do Azure, ao intercalar as duas redes virtuais numa só para fins de conectividade. O [trânsito do gateway](../virtual-network/virtual-network-peering-overview.md#gateways-and-on-premises-connectivity) é uma propriedade de peering que permite a uma rede virtual utilizar o gateway de VPN na rede virtual no modo de peering e, assim, permitir a conetividade entre vários locais ou VNet a VNet. O diagrama seguinte mostra como funciona o trânsito do gateway com peering de rede virtual.

![gateway-transit](./media/vpn-gateway-peering-gateway-transit/gatewaytransit.png)

No diagrama, o trânsito do gateway permite que as redes virtuais em modo de peering utilizem o gateway de VPN do Azure no Hub-RM. A conectividade disponível no gateway de VPN, incluindo ligações S2S, P2S e VNet a VNet, aplica-se às três redes virtuais. A opção de trânsito está disponível para peering entre modelos de implementação idênticos ou diferentes. A restrição é que o gateway de VPN só pode estar na rede virtual com o modelo de implementação Resource Manager, conforme mostrado no diagrama.

Na arquitetura de rede hub-and-spoke, o trânsito do gateway permite que as redes virtuais spoke partilhem o gateway de VPN no hub, em vez de implementar gateways de VPN em todas as redes virtuais spoke. Encaminha para as redes virtuais ligadas de gateway ou as redes no local vão ser propagadas para as tabelas de encaminhamento para as redes virtuais em modo de peering com o trânsito do gateway. Pode desativar a propagação automática da rota do gateway de VPN. Crie uma tabela de encaminhamento com a opção “**Desativar propagação de rotas BGP**” e associe a tabela de encaminhamento às sub-redes para impedir a distribuição de rotas para essas sub-redes. Para obter mais informações, veja [Tabela de encaminhamento da rede virtual](../virtual-network/manage-route-table.md).

Neste documento, descrevemos dois cenários:

1. Ambas as redes virtuais estão a utilizar o modelo de implementação Resource Manager
2. A rede virtual spoke é clássica e a rede virtual do hub com o gateway está no Resource Manager

> [!IMPORTANT]  
> O trânsito de gateway é atualmente suportado com o peering de redes virtuais global em pré-visualização. A pré-visualização está disponível em todas as regiões do Azure, regiões da cloud na China e regiões de cloud de governo, mas não em nuvens. Não é necessária nenhuma lista de permissões. Pode testar em pré-visualização através da CLI, PowerShell, modelos ou API. Portal não é suportado na pré-visualização. 

## <a name="requirements"></a>Requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

O exemplo neste documento precisa que os seguintes recursos sejam criados:

1. Rede virtual Hub-RM com um gateway de VPN
2. Rede virtual Spoke-RM
3. Rede virtual Spoke-Classic com o modelo de implementação clássico
4. A conta que utiliza precisa das funções e da permissão necessárias. Veja a secção [Permissões](#permissions) deste artigo para obter mais detalhes.

Consulte os seguintes documentos para obter instruções:

1. [Criar um gateway de VPN numa rede virtual](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
2. [Criar peering de rede virtual com o mesmo modelo de implementação](../virtual-network/tutorial-connect-virtual-networks-portal.md)
3. [Criar peering de rede virtual com diferentes modelos de implementação](../virtual-network/create-peering-different-deployment-models.md)

## <a name="permissions"></a>Permissões

As contas que utiliza para criar um peering de rede virtual têm de ter as funções ou permissões necessárias. No exemplo abaixo, se estava a realizar o peering de duas redes virtuais com o nome Hub-RM e Spoke-Classic, a conta tem de ter as seguintes funções ou permissões para cada rede virtual:
    
|Rede virtual|Modelo de implementação|Função|Permissões|
|---|---|---|---|
|Hub-RM|Resource Manager|[Contribuidor de Rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
| |Clássica|[Contribuidor de Rede Clássica](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|N/A|
|Spoke-Classic|Resource Manager|[Contribuidor de Rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|
||Clássica|[Contribuidor de Rede Clássica](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Microsoft.ClassicNetwork/virtualNetworks/peer|

Saiba mais sobre [funções incorporadas](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) e a atribuição de permissões específicas para [funções personalizadas](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (apenas Resource Manager).

## <a name="resource-manager-to-resource-manager-peering-with-gateway-transit"></a>Peering de Resource Manager para Resource Manager com trânsito do gateway

Siga as instruções para criar ou atualizar os peerings de rede virtual para ativar o trânsito do gateway.

1. Crie ou atualize o peering de rede virtual de Spoke-RM para Hub-RM no portal do Azure. Navegue até ao recurso de rede virtual Spoke-RM, clique em “Peerings” e em “Adicionar”:
    - Defina a opção “Resource Manager”
    - Selecione a rede virtual Hub-RM na subscrição correspondente
    - Confirme se a opção “Permitir acesso de rede virtual” está “Ativada”
    - Defina a opção “**Utilizar gateways remotos**”
    - Clique em “OK”

      ![spokerm-to-hubrm](./media/vpn-gateway-peering-gateway-transit/spokerm-hubrm-peering.png)

2. Se o peering já tiver sido criado, navegue até ao recurso de peering e, em seguida, ative a opção “**Utilizar gateways remotos**”, semelhante à captura de ecrã mostrada no passo (1)

3. Crie ou atualize o peering de rede virtual de Hub-RM para Spoke-RM no portal do Azure. Navegue até ao recurso de rede virtual Hub-RM, clique em “Peerings” e em “Adicionar”:
    - Defina a opção “Resource Manager”
    - Confirme se a opção “Permitir acesso de rede virtual” está “Ativada”
    - Selecione a rede virtual “Spoke-RM” na subscrição correspondente
    - Defina a opção “**Permitir trânsito do gateway**”
    - Clique em “OK”

      ![hubrm-to-spokerm](./media/vpn-gateway-peering-gateway-transit/hubrm-spokerm-peering.png)

4. Se o peering já tiver sido criado, navegue até ao recurso de peering e, em seguida, ative a opção “**Permitir trânsito do gateway**”, semelhante à captura de ecrã mostrada no passo (3)

5. Verifique se o estado do peering em ambas as redes virtuais é “**Ligado**”

### <a name="powershell-sample"></a>Exemplo do PowerShell

Também pode utilizar o PowerShell para criar ou atualizar o peering com o exemplo acima. Substitua as variáveis pelos nomes dos grupos de recursos e das redes virtuais.

```azurepowershell-interactive
$SpokeRG = "SpokeRG1"
$SpokeRM = "Spoke-RM"
$HubRG   = "HubRG1"
$HubRM   = "Hub-RM"

$spokermvnet = Get-AzVirtualNetwork -Name $SpokeRM -ResourceGroup $SpokeRG
$hubrmvnet   = Get-AzVirtualNetwork -Name $HubRM -ResourceGroup $HubRG

Add-AzVirtualNetworkPeering `
  -Name SpokeRMtoHubRM `
  -VirtualNetwork $spokermvnet `
  -RemoteVirtualNetworkId $hubrmvnet.Id `
  -UseRemoteGateways

Add-AzVirtualNetworkPeering `
  -Name HubRMToSpokeRM `
  -VirtualNetwork $hubrmvnet `
  -RemoteVirtualNetworkId $spokermvnet.Id `
  -AllowGatewayTransit
```

## <a name="classic-to-resource-manager-peering-with-gateway-transit"></a>Peering Clássico para Resource Manager com trânsito do gateway

Os passos são semelhantes ao exemplo do Resource Manager, mas as operações são aplicadas apenas na rede virtual Hub-RM.

1. Crie ou atualize o peering de rede virtual de Hub-RM para Spoke-RM no portal do Azure. Navegue até ao recurso de rede virtual Hub-RM, clique em “Peerings” e em “Adicionar”:
   - Defina a opção “Clássico” do modelo de implementação de Rede virtual
   - Selecione a rede virtual “Spoke-Classic” na subscrição correspondente
   - Confirme se a opção “Permitir acesso de rede virtual” está “Ativada”
   - Defina a opção “**Permitir trânsito do gateway**”
   - Clique em “OK”

     ![hubrm-to-spokeclassic](./media/vpn-gateway-peering-gateway-transit/hubrm-spokeclassic-peering.png)

2. Se o peering já tiver sido criado, navegue até ao recurso de peering e, em seguida, ative a opção “**Permitir trânsito do gateway**”, semelhante à captura de ecrã mostrada no passo (1)

3. Não há nenhuma operação na rede virtual Spoke-Classic

4. Verifique se o estado do peering de rede virtual Hub-RM é “**Ligado**”

Assim que o estado mostrar “Ligado”, as redes virtuais spoke podem começar a utilizar a conectividade VNet a VNet ou em vários locais através do gateway de VPN na rede virtual do hub.

### <a name="powershell-sample"></a>Exemplo do PowerShell

Também pode utilizar o PowerShell para criar ou atualizar o peering com o exemplo acima. Substitua as variáveis e o ID de subscrição por valores da sua rede virtual e dos grupos de recursos e da subscrição. Só precisa de criar peering de rede virtual na rede virtual do hub.

```azurepowershell-interactive
$HubRG   = "HubRG1"
$HubRM   = "Hub-RM"

$hubrmvnet   = Get-AzVirtualNetwork -Name $HubRM -ResourceGroup $HubRG

Add-AzVirtualNetworkPeering `
  -Name HubRMToSpokeRM `
  -VirtualNetwork $hubrmvnet `
  -RemoteVirtualNetworkId "/subscriptions/<subscription Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/Spoke-Classic" `
  -AllowGatewayTransit
```

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais acerca das [restrições e comportamentos do peering de rede virtual](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints) e das [definições do peering de rede virtual](../virtual-network/virtual-network-manage-peering.md#create-a-peering) antes de criar um peering de rede virtual para a utilização de produção.
* Saiba como [criar uma topologia hub-and-spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) com peering de rede virtual e trânsito do gateway.
