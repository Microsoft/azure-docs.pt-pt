---
title: 'Ligar uma rede virtual a um circuito do ExpressRoute: CLI: Azure| Microsoft Docs'
description: Este artigo mostra-lhe como ligar redes virtuais (VNets) para circuitos do ExpressRoute com o modelo de implementação do Resource Manager e a CLI.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: anzaman,cherylmc
ms.custom: seodec18
ms.openlocfilehash: 5ddcfe14873d13384b043f7a977dc4f069dbe8dd
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57408263"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-cli"></a>Ligar uma rede virtual a um circuito do ExpressRoute com a CLI

Este artigo ajuda-o a ligar redes virtuais (VNets) para circuitos do ExpressRoute do Azure com a CLI. Para ligar a CLI do Azure, as redes virtuais devem ser criadas com o modelo de implementação do Resource Manager. Eles podem estar na mesma subscrição ou parte de outra subscrição. Se pretender utilizar um método diferente para ligar a VNet a um circuito do ExpressRoute, pode selecionar um artigo da lista seguinte:

> [!div class="op_single_selector"]
> * [Portal do Azure](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [CLI do Azure](howto-linkvnet-cli.md)
> * [Vídeo - portal do Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (clássico)](expressroute-howto-linkvnet-classic.md)
> 

## <a name="configuration-prerequisites"></a>Pré-requisitos da configuração

* Tem a versão mais recente da interface de linha de comandos (CLI). Para obter mais informações, consulte [instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

* Tem de rever o [pré-requisitos](expressroute-prerequisites.md), [requisitos de encaminhamento](expressroute-routing.md), e [fluxos de trabalho](expressroute-workflows.md) antes de iniciar a configuração.

* Deve ter um circuito ExpressRoute ativo. 
  * Siga as instruções para [criar um circuito do ExpressRoute](howto-circuit-cli.md) e ter o circuito ativado pelo seu fornecedor de conectividade. 
  * Certifique-se de que tem o peering privado do Azure configurado para o seu circuito. Consulte a [configurar o encaminhamento](howto-routing-cli.md) artigo para obter instruções de encaminhamento. 
  * Certifique-se de que o peering privado do Azure está configurado. O peering BGP entre a rede e a Microsoft tem de ser cópia de segurança para que pode habilitar a conectividade de ponto-a-ponto.
  * Certifique-se de que tem uma rede virtual e um gateway de rede virtual criada e totalmente aprovisionado. Siga as instruções para [configurar um gateway de rede virtual para o ExpressRoute](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli). Certifique-se de que utilize `--gateway-type ExpressRoute`.

* Pode ligar até 10 redes virtuais a um circuito do ExpressRoute standard. Todas as redes virtuais tem de ser na mesma região geopolítica quando utilizar um circuito de ExpressRoute standard. 

* Pode ser associada uma VNet única para até quatro circuitos do ExpressRoute. Utilize o processo abaixo para criar um novo objeto de ligação para cada circuito de ExpressRoute que está a ligar. Os circuitos do ExpressRoute podem ser na mesma subscrição, subscrições diferentes ou uma combinação de ambos.

* Se ativar o suplemento ExpressRoute premium, pode ligar uma rede virtual fora da região geopolítica do circuito ExpressRoute ou ligar um grande número de redes virtuais para o seu circuito do ExpressRoute. Para obter mais informações sobre o suplemento premium, consulte a [FAQ](expressroute-faqs.md).

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Ligar uma rede virtual na mesma subscrição a um circuito

Pode ligar um gateway de rede virtual a um circuito do ExpressRoute, utilizando o exemplo. Certifique-se de que o gateway de rede virtual é criado e está pronto para a ligação antes de executar o comando.

```azurecli
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit
```

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Ligar uma rede virtual de uma subscrição diferente a um circuito

Pode partilhar um circuito do ExpressRoute em várias subscrições. A figura abaixo mostra um simples esquemática funciona como a partilha dos circuitos do ExpressRoute em várias subscrições.

Cada uma das nuvens menores na cloud da grande é utilizada para representar as subscrições que pertencem a diferentes departamentos dentro de uma organização. Cada um dos departamentos dentro da organização pode utilizar a sua própria subscrição para a implementação de seus serviços, mas eles podem partilhar um único circuito de ExpressRoute para ligar a voltar à sua rede no local. Um único departamento (neste exemplo: IT) pode ser proprietário do circuito do ExpressRoute. Outras subscrições dentro da organização podem utilizar o circuito do ExpressRoute.

> [!NOTE]
> Custos de largura de banda e de conectividade para o circuito dedicado serão aplicados para o proprietário do circuito ExpressRoute. Todas as redes virtuais partilham a mesma largura de banda.
> 
> 

![Conectividade entre subscrições](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration---circuit-owners-and-circuit-users"></a>Administração - circuito proprietários e os utilizadores do circuito

O "proprietário do circuito' é um utilizador autorizado de energia do recurso de circuito do ExpressRoute. O proprietário do circuito pode criar autorizações que podem ser resgatadas por "Utilizadores do circuito". Os utilizadores do circuito são proprietários de gateways de rede virtual que não estão na mesma subscrição que o circuito do ExpressRoute. Os utilizadores do circuito podem resgatar autorizações (uma autorização por rede virtual).

O proprietário do circuito tem o poder para modificar e revogar autorizações em qualquer altura. Quando uma autorização é revogada, todas as ligações de ligação são eliminadas da subscrição cujo acesso foi revogado.

### <a name="circuit-owner-operations"></a>Operações de proprietário do circuito

**Para criar uma autorização**

O proprietário do circuito cria uma autorização, que cria uma chave de autorização que pode ser utilizada por um utilizador do circuito para ligar os seus gateways de rede virtual para o circuito do ExpressRoute. Uma autorização é válida para apenas uma ligação.

O exemplo seguinte mostra como criar uma autorização:

```azurecli
az network express-route auth create --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization
```

A resposta contém a chave de autorização e o estado:

```azurecli
"authorizationKey": "0a7f3020-541f-4b4b-844a-5fb43472e3d7",
"authorizationUseStatus": "Available",
"etag": "W/\"010353d4-8955-4984-807a-585c21a22ae0\"",
"id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/authorizations/MyAuthorization1",
"name": "MyAuthorization1",
"provisioningState": "Succeeded",
"resourceGroup": "ExpressRouteResourceGroup"
```

**Para rever as autorizações**

O proprietário do circuito pode rever todas as autorizações emitidos num determinado circuito ao executar o exemplo seguinte:

```azurecli
az network express-route auth list --circuit-name MyCircuit -g ExpressRouteResourceGroup
```

**Para adicionar as autorizações**

O proprietário do circuito pode adicionar as autorizações utilizando o exemplo seguinte:

```azurecli
az network express-route auth create --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization1
```

**Para eliminar autorizações**

O proprietário do circuito pode revogar/eliminar autorizações para o usuário ao executar o exemplo seguinte:

```azurecli
az network express-route auth delete --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization1
```

### <a name="circuit-user-operations"></a>Operações de utilizador do circuito

O utilizador de circuito tem o ID de elemento de rede e uma chave de autorização do proprietário do circuito. A chave de autorização é um GUID.

```azurecli
Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
```

**Para resgatar uma autorização de conexão**

O utilizador de circuito pode executar o exemplo a seguir para resgatar uma autorização de ligação:

```azurecli
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit --authorization-key "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"
```

**Para liberar uma autorização de conexão**

Pode libertar uma autorização ao eliminar a ligação que ligue o circuito do ExpressRoute para a rede virtual.

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações acerca do ExpressRoute, veja as [FAQs do ExpressRoute](expressroute-faqs.md).
