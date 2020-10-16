---
title: 'Tutorial: Ligue um VNet a um circuito ExpressRoute - Azure CLI'
description: Este tutorial mostra-lhe como ligar redes virtuais (VNets) aos circuitos ExpressRoute utilizando o modelo de implementação do Gestor de Recursos e do Azure CLI.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/08/2020
ms.author: duau
ms.openlocfilehash: 41bb72ba4c220a0dd2ebb93f2bd313a15d108faa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91856284"
---
# <a name="tutorial-connect-a-virtual-network-to-an-expressroute-circuit-using-cli"></a>Tutorial: Ligue uma rede virtual a um circuito ExpressRoute utilizando o CLI

Este tutorial mostra-lhe como ligar redes virtuais (VNets) aos circuitos Azure ExpressRoute utilizando o Azure CLI. Para ligar usando o Azure CLI, as redes virtuais devem ser criadas utilizando o modelo de implementação do Gestor de Recursos. Podem estar na mesma subscrição, ou fazer parte de outra subscrição. Se pretender utilizar um método diferente para ligar o seu VNet a um circuito ExpressRoute, pode selecionar um artigo da seguinte lista:

> [!div class="op_single_selector"]
> * [Portal do Azure](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [CLI do Azure](howto-linkvnet-cli.md)
> * [Vídeo - Portal Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (clássico)](expressroute-howto-linkvnet-classic.md)
> 

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> - Ligue uma rede virtual na mesma subscrição a um circuito
> - Ligar uma rede virtual de uma subscrição diferente a um circuito
> - Modificar uma ligação de rede virtual
> - Configurar ExpressRoute FastPath

## <a name="prerequisites"></a>Pré-requisitos

* Precisa da versão mais recente da interface de linha de comando (CLI). Para mais informações, consulte [instalar o Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).
* Reveja os [pré-requisitos,](expressroute-prerequisites.md) [requisitos de encaminhamento](expressroute-routing.md)e [fluxos de trabalho](expressroute-workflows.md) antes de iniciar a configuração.
* Deve ter um circuito ExpressRoute ativo. 
  * Siga as instruções para [criar um circuito ExpressRoute](howto-circuit-cli.md) e tenha o circuito ativado pelo seu fornecedor de conectividade. 
  * Certifique-se de que tem o Azure a espreitar para o seu circuito. Consulte o artigo [de encaminhamento de configuração](howto-routing-cli.md) para obter instruções de encaminhamento. 
  * Certifique-se de que o espreitamento privado do Azure está configurado. O perspção BGP entre a sua rede e a Microsoft deve ser estabelecido para que possa ativar a conectividade de ponta a ponta.
  * Certifique-se de que tem uma rede virtual e um gateway de rede virtual criado e totalmente a provisionado. Siga as instruções para [configurar uma porta de rede virtual para o ExpressRoute](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli). Certifique-se de que utiliza `--gateway-type ExpressRoute` .
* Pode ligar até 10 redes virtuais a um circuito Standard ExpressRoute. Todas as redes virtuais devem estar na mesma região geopolítica quando utilizarem um circuito ExpressRoute padrão. 
* Um único VNet pode ser ligado a até quatro circuitos ExpressRoute. Utilize o seguinte processo para criar um novo objeto de ligação para cada circuito ExpressRoute a que está a ligar. Os circuitos ExpressRoute podem estar na mesma subscrição, subscrições diferentes ou uma mistura de ambos.
* Se ativar o addon premium ExpressRoute, pode ligar redes virtuais fora da região geopolítica do circuito ExpressRoute. O addon premium também lhe permitirá ligar mais de 10 redes virtuais ao seu circuito ExpressRoute, dependendo da largura de banda escolhida. Consulte as [FAQ](expressroute-faqs.md) para obter mais detalhes sobre o complemento premium.

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Ligue uma rede virtual na mesma subscrição a um circuito

Pode ligar uma porta de rede virtual a um circuito ExpressRoute usando o exemplo. Certifique-se de que o gateway de rede virtual é criado e está pronto para ser ligado antes de executar o comando.

```azurecli-interactive
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit
```

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Ligar uma rede virtual de uma subscrição diferente a um circuito

Pode partilhar um circuito ExpressRoute através de várias subscrições. A figura a seguir mostra um simples esquema de como a partilha funciona para circuitos ExpressRoute em várias subscrições.

Cada uma das nuvens menores dentro da grande nuvem é usada para representar subscrições que pertencem a diferentes departamentos dentro de uma organização. Cada um dos departamentos da organização usa a sua própria subscrição para implantar os seus serviços, mas podem partilhar um único circuito ExpressRoute para ligar de volta à sua rede no local. Um único departamento (neste exemplo: IT) pode ser dono do circuito ExpressRoute. Outras subscrições dentro da organização podem utilizar o circuito ExpressRoute.

> [!NOTE]
> Os custos de conectividade e largura de banda para o circuito dedicado serão aplicados ao Proprietário do Circuito ExpressRoute. Todas as redes virtuais partilham a mesma largura de banda.
> 
> 

![Conectividade de subscrição cruzada](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration---circuit-owners-and-circuit-users"></a>Administração - Proprietários de Circuitos e Utilizadores de Circuitos

O 'Circuit Owner' é um utilizador autorizado do recurso de circuito ExpressRoute. O Proprietário do Circuito pode criar autorizações que podem ser resgatadas por 'Utilizadores de Circuitos'. Os Utilizadores de Circuitos são proprietários de gateways de rede virtuais que não estão dentro da mesma subscrição que o circuito ExpressRoute. Circuito Os utilizadores podem resgatar autorizações (uma autorização por rede virtual).

O Proprietário do Circuito tem o poder de modificar e revogar autorizações a qualquer momento. Quando uma autorização é revogada, todas as ligações de ligação são eliminadas da subscrição cujo acesso foi revogado.

### <a name="circuit-owner-operations"></a>Operações do Proprietário do Circuito

**Para criar uma autorização**

O proprietário do circuito cria uma autorização, que cria uma chave de autorização para ser usada por um utilizador de circuito para ligar os seus gateways de rede virtuais ao circuito ExpressRoute. Uma autorização é válida para apenas uma ligação.

O exemplo a seguir mostra como criar uma autorização:

```azurecli-interactive
az network express-route auth create --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization
```

A resposta contém a chave e o estado da autorização:

```output
"authorizationKey": "0a7f3020-541f-4b4b-844a-5fb43472e3d7",
"authorizationUseStatus": "Available",
"etag": "W/\"010353d4-8955-4984-807a-585c21a22ae0\"",
"id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/authorizations/MyAuthorization1",
"name": "MyAuthorization1",
"provisioningState": "Succeeded",
"resourceGroup": "ExpressRouteResourceGroup"
```

**Para rever autorizações**

O Proprietário do Circuito pode rever todas as autorizações emitidas num determinado circuito executando o seguinte exemplo:

```azurecli-interactive
az network express-route auth list --circuit-name MyCircuit -g ExpressRouteResourceGroup
```

**Para adicionar autorizações**

O Proprietário do Circuito pode adicionar autorizações utilizando o seguinte exemplo:

```azurecli-interactive
az network express-route auth create --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization1
```

**Para apagar autorizações**

O Proprietário do Circuito pode revogar/eliminar autorizações ao utilizador executando o seguinte exemplo:

```azurecli-interactive
az network express-route auth delete --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization1
```

### <a name="circuit-user-operations"></a>Operações do utilizador do circuito

O Utilizador do Circuito necessita do ID do par e de uma chave de autorização do Proprietário do Circuito. A chave de autorização é um GUID.

```azurecli-interactive
az network express-route show -n MyCircuit -g ExpressRouteResourceGroup
```

**Para resgatar uma autorização de ligação**

O Utilizador do Circuito pode executar o seguinte exemplo para resgatar uma autorização de ligação:

```azurecli-interactive
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit --authorization-key "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"
```

**Para libertar uma autorização de ligação**

Pode libertar uma autorização eliminando a ligação que liga o circuito ExpressRoute à rede virtual.

## <a name="modify-a-virtual-network-connection"></a>Modificar uma ligação de rede virtual
Pode atualizar certas propriedades de uma ligação de rede virtual. 

**Para atualizar o peso da ligação**

A sua rede virtual pode ser ligada a vários circuitos ExpressRoute. Pode receber o mesmo prefixo de mais de um circuito ExpressRoute. Para escolher qual ligação enviar o tráfego destinado a este prefixo, pode alterar o Peso de *Encaminhamento* de uma ligação. O tráfego será enviado na ligação com o mais alto *Peso de Encaminhamento*.

```azurecli-interactive
az network vpn-connection update --name ERConnection --resource-group ExpressRouteResourceGroup --routing-weight 100
```

O intervalo de *RoutingWeight* é de 0 a 32000. O valor predefinido é 0.

## <a name="configure-expressroute-fastpath"></a>Configurar ExpressRoute FastPath 
Pode ativar [o ExpressRoute FastPath](expressroute-about-virtual-network-gateways.md) se o seu gateway de rede virtual for Ultra Performance ou ErGw3AZ. O FastPath melhora o desempenho do caminho dos dados, como pacotes por segundo e ligações por segundo entre a sua rede no local e a sua rede virtual. 

**Configure fastPath em uma nova ligação**

```azurecli-interactive
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --express-route-gateway-bypass true --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit
```

**Atualizar uma ligação existente para ativar o FastPath**

```azurecli-interactive
az network vpn-connection update --name ERConnection --resource-group ExpressRouteResourceGroup --express-route-gateway-bypass true
```
## <a name="clean-up-resources"></a>Limpar os recursos

Se já não necessitar da ligação ExpressRoute, a partir da subscrição onde se encontra o gateway, utilize o `az network vpn-connection delete` comando para remover a ligação entre o gateway e o circuito.

```azurecli-interactive
az network vpn-connection delete --name ERConnection --resource-group ExpressRouteResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a ligar uma rede virtual a um circuito na mesma subscrição e a uma subscrição diferente. Para mais informações sobre o gateway ExpressRoute, consulte: 

> [!div class="nextstepaction"]
> [Sobre os gateways de rede virtual ExpressRoute](expressroute-about-virtual-network-gateways.md)
