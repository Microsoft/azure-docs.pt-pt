---
title: Configurar o trânsito do gateway de VPN para peering de rede virtual
description: Configure o trânsito de gateway para o espreitamento da rede virtual, para ligar perfeitamente duas redes virtuais Azure numa só para fins de conectividade.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 11/30/2020
ms.author: cherylmc
ms.openlocfilehash: 2fc12385c78135269b6a73038fd0ad810ebaedd6
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96576205"
---
# <a name="configure-vpn-gateway-transit-for-virtual-network-peering"></a>Configurar o trânsito do gateway de VPN para peering de rede virtual

Este artigo ajuda-o a configurar o trânsito do gateway para peering de rede virtual. O [Peering de rede virtual](../virtual-network/virtual-network-peering-overview.md) liga facilmente duas redes virtuais do Azure, ao intercalar as duas redes virtuais numa só para fins de conectividade. [O trânsito gateway](../virtual-network/virtual-network-peering-overview.md#gateways-and-on-premises-connectivity) é uma propriedade que permite que uma rede virtual use o gateway VPN na rede virtual espreitada para instalações cruzadas ou conectividade VNet-to-VNet. O diagrama seguinte mostra como funciona o trânsito do gateway com peering de rede virtual.

![Diagrama de trânsito gateway](./media/vpn-gateway-peering-gateway-transit/gatewaytransit.png)

No diagrama, o trânsito do gateway permite que as redes virtuais em modo de peering utilizem o gateway de VPN do Azure no Hub-RM. A conectividade disponível no gateway de VPN, incluindo ligações S2S, P2S e VNet a VNet, aplica-se às três redes virtuais. A opção de trânsito está disponível para espreitar entre os mesmos modelos de implementação, ou diferentes. Se estiver a configurar o trânsito entre diferentes modelos de implementação, a rede virtual do hub e o gateway de rede virtual devem estar no modelo de implementação do Gestor de Recursos e não no modelo clássico de implementação.
>

Na arquitetura de rede hub-and-spoke, o trânsito do gateway permite que as redes virtuais spoke partilhem o gateway de VPN no hub, em vez de implementar gateways de VPN em todas as redes virtuais spoke. Encaminha para as redes virtuais ligadas de gateway ou as redes no local vão ser propagadas para as tabelas de encaminhamento para as redes virtuais em modo de peering com o trânsito do gateway. Pode desativar a propagação automática da rota do gateway de VPN. Crie uma tabela de encaminhamento com a opção “**Desativar propagação de rotas BGP**” e associe a tabela de encaminhamento às sub-redes para impedir a distribuição de rotas para essas sub-redes. Para obter mais informações, veja [Tabela de encaminhamento da rede virtual](../virtual-network/manage-route-table.md).

Há dois cenários neste artigo:

* **Mesmo modelo de implementação**: Ambas as redes virtuais são criadas no modelo de implementação do Gestor de Recursos.
* **Diferentes modelos de implementação**: A rede virtual falada é criada no modelo clássico de implementação, e a rede virtual do hub e gateway estão no modelo de implementação do Gestor de Recursos.

>[!NOTE]
> Se fizer uma alteração na topologia da sua rede e tiver clientes Windows VPN, o pacote de clientes VPN para clientes Windows deve ser descarregado e instalado novamente para que as alterações sejam aplicadas ao cliente.
>

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se tem as seguintes redes e permissões virtuais:

### <a name="virtual-networks"></a><a name="vnet"></a>Redes virtuais

|VNet|Modelo de implementação| Gateway de rede virtual|
|---|---|---|---|
| Hub-RM| [Resource Manager](vpn-gateway-howto-site-to-site-resource-manager-portal.md)| [Sim](tutorial-create-gateway-portal.md)|
| Spoke-RM | [Resource Manager](vpn-gateway-howto-site-to-site-resource-manager-portal.md)| Não |
| Spoke-Classic | [Clássico](vpn-gateway-howto-site-to-site-classic-portal.md#CreatVNet) | Não |

### <a name="permissions"></a><a name="permissions"></a>Permissões

As contas que utiliza para criar um peering de rede virtual têm de ter as funções ou permissões necessárias. No exemplo abaixo, se estiver a espreitar as duas redes virtuais chamadas **Hub-RM** e **Spoke-Classic,** a sua conta deve ter as seguintes funções ou permissões para cada rede virtual:

|VNet|Modelo de implementação|Função|Permissões|
|---|---|---|---|
|Hub-RM|Resource Manager|[Contribuidor de Rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
| |Clássico|[Contribuidor de Rede Clássica](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|N/D|
|Spoke-Classic|Resource Manager|[Contribuidor de Rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|
||Clássico|[Contribuidor de Rede Clássica](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Microsoft.ClassicNetwork/virtualNetworks/peer|

Saiba mais sobre [funções incorporadas](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) e a atribuição de permissões específicas para [funções personalizadas](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (apenas Resource Manager).

## <a name="same-deployment-model"></a><a name="same"></a>Mesmo modelo de implantação

Neste cenário, as redes virtuais estão ambas no modelo de implementação do Gestor de Recursos. Utilize os seguintes passos para criar ou atualizar os persiões de rede virtuais para permitir o trânsito de gateway.

### <a name="to-add-a-peering-and-enable-transit"></a>Para adicionar um espreitamento e permitir o trânsito

1. No [portal Azure,](https://portal.azure.com)crie ou atualize a rede virtual que espreita a partir do Hub-RM. Navegue para a rede virtual **Hub-RM.** **Selecione Peerings,** em seguida **, + Adicionar** para abrir Adicionar **olhando**.
1. Na página **'Adicionar olhando',** configure os valores **desta rede virtual**.

   * Nome do link de espreitar: Nomeie o link. Exemplo: **HubrmToSpokerm**
   * Tráfego para rede virtual remota: **Permitir**
   * Tráfego reencaminhado a partir de rede virtual remota: **Permitir**
   * Gateway de rede virtual: **Use o portal desta rede virtual**

     :::image type="content" source="./media/vpn-gateway-peering-gateway-transit/peering-vnet.png" alt-text="Os programas de screenshot adicionam o seu olhar.":::

1. Na mesma página, continue a configurar os valores da **rede virtual remota**.

   * Nome do link de espreitar: Nomeie o link. Exemplo: **SpokermtoHubrm**
   * Modelo de implementação: **Gestor de Recursos**
   * Rede Virtual: **Spoke-RM**
   * Tráfego para rede virtual remota: **Permitir**
   * Tráfego reencaminhado a partir de rede virtual remota: **Permitir**
   * Gateway de rede virtual: **Use o gateway da rede virtual remota**

     :::image type="content" source="./media/vpn-gateway-peering-gateway-transit/peering-remote.png" alt-text="A screenshot mostra valores para rede virtual remota.":::

1. **Selecione Adicionar** para criar o espreitamento.
1. Verifique o estado de observação como **Conectado** em ambas as redes virtuais.

### <a name="to-modify-an-existing-peering-for-transit"></a>Para modificar um espreitamento existente para o trânsito

Se o espreitamento já foi criado, pode modificar o espreitamento para o trânsito.

1. Navegue para a rede virtual. **Selecione Peerings** e selecione o espreitamento que pretende modificar.

   :::image type="content" source="./media/vpn-gateway-peering-gateway-transit/peering-modify.png" alt-text="A screenshot mostra os seus pares selecionados.":::

1. Atualize o olhar VNet.

   * Tráfego para rede virtual remota: **Permitir**
   * Tráfego encaminhado para rede virtual; **Permitir**
   * Gateway de rede virtual: **Use o gateway da rede virtual remota**

     :::image type="content" source="./media/vpn-gateway-peering-gateway-transit/modify-peering-settings.png" alt-text="A screenshot mostra modificar o gateway de espreitar.":::

1. **Salve** as definições de espreitar.

### <a name="powershell-sample"></a><a name="ps-same"></a>Exemplo do PowerShell

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

## <a name="different-deployment-models"></a><a name="different"></a>Diferentes modelos de implantação

Nesta configuração, o **Spoke-Classic** falado está no modelo de implementação clássico e o hub VNet **Hub-RM** está no modelo de implementação do Gestor de Recursos. Ao configurar o trânsito entre os modelos de implantação, o gateway de rede virtual deve ser configurado para o VNet do Gestor de Recursos, e não para o VNet clássico.

Para esta configuração, basta configurar a rede virtual **Hub-RM.** Não precisas de configurar nada no VNet **Clássico.**

1. No portal Azure, navegue para a rede virtual **Hub-RM,** **selecione Peerings,** em seguida, selecione **+ Adicionar**.
1. Na página **'Adicionar olhando',** configurar os seguintes valores:

   * Nome do link de espreitar: Nomeie o link. Exemplo: **HubrmToClassic**
   * Tráfego para rede virtual remota: **Permitir**
   * Tráfego reencaminhado a partir de rede virtual remota: **Permitir**
   * Gateway de rede virtual: **Use o portal desta rede virtual**
   * Rede virtual remota: **Clássico**

     :::image type="content" source="./media/vpn-gateway-peering-gateway-transit/peering-classic.png" alt-text="Adicionar página de peering para Spoke-Classic":::

1. Verifique se a subscrição está correta e, em seguida, selecione a rede virtual a partir do dropdown.
1. **Selecione Adicionar** para adicionar o espreitamento.
1. Verifique o estado de observação como **Conectado** na rede virtual Hub-RM. 

Para esta configuração, não precisa de configurar nada na rede virtual **Spoke-Classic.** Uma vez que o estado mostra **Connected**, a rede virtual falada pode utilizar a conectividade através do gateway VPN na rede virtual do hub.

### <a name="powershell-sample"></a><a name="ps-different"></a>Exemplo do PowerShell

Também pode utilizar o PowerShell para criar ou atualizar o peering com o exemplo acima. Substitua as variáveis e o ID de subscrição por valores da sua rede virtual e dos grupos de recursos e da subscrição. Só precisa de criar peering de rede virtual na rede virtual do hub.

```azurepowershell-interactive
$HubRG   = "HubRG1"
$HubRM   = "Hub-RM"

$hubrmvnet   = Get-AzVirtualNetwork -Name $HubRM -ResourceGroup $HubRG

Add-AzVirtualNetworkPeering `
  -Name HubRMToClassic `
  -VirtualNetwork $hubrmvnet `
  -RemoteVirtualNetworkId "/subscriptions/<subscription Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/Spoke-Classic" `
  -AllowGatewayTransit
```

## <a name="next-steps"></a>Passos seguintes

* Saiba mais acerca das [restrições e comportamentos do peering de rede virtual](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints) e das [definições do peering de rede virtual](../virtual-network/virtual-network-manage-peering.md#create-a-peering) antes de criar um peering de rede virtual para a utilização de produção.
* Saiba como [criar uma topologia hub-and-spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering) com peering de rede virtual e trânsito do gateway.
* [Criar redes virtuais a espreitar com o mesmo modelo de implementação.](../virtual-network/tutorial-connect-virtual-networks-portal.md)
* [Crie olhando para a rede virtual com diferentes modelos de implementação.](../virtual-network/create-peering-different-deployment-models.md)