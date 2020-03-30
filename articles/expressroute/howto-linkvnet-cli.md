---
title: 'Azure ExpressRoute: Ligue uma VNet ao circuito: CLI'
description: Este artigo mostra-lhe como ligar redes virtuais (VNets) aos circuitos ExpressRoute utilizando o modelo de implementação do Gestor de Recursos e o CLI.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: cherylmc
ms.openlocfilehash: fdd809bcba703dbd8f9ee1e7c18185fd20e4586f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476139"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-cli"></a>Ligue uma rede virtual a um circuito ExpressRoute usando o CLI

Este artigo ajuda-o a ligar redes virtuais (VNets) aos circuitos Azure ExpressRoute utilizando o CLI. Para ligar o Azure CLI, as redes virtuais devem ser criadas utilizando o modelo de implementação do Gestor de Recursos. Podem estar na mesma subscrição, ou parte de outra subscrição. Se pretender utilizar um método diferente para ligar o vNet a um circuito ExpressRoute, pode selecionar um artigo da seguinte lista:

> [!div class="op_single_selector"]
> * [Portal Azure](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [Vídeo - Portal Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (clássico)](expressroute-howto-linkvnet-classic.md)
> 

## <a name="configuration-prerequisites"></a>Pré-requisitos da configuração

* Precisa da versão mais recente da interface da linha de comando (CLI). Para mais informações, consulte [Instalar o Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

* É necessário rever os [pré-requisitos,](expressroute-prerequisites.md)os requisitos de [encaminhamento](expressroute-routing.md)e [os fluxos de trabalho](expressroute-workflows.md) antes de iniciar a configuração.

* Deve ter um circuito ExpressRoute ativo. 
  * Siga as instruções para [criar um circuito ExpressRoute](howto-circuit-cli.md) e tenha o circuito ativado pelo seu fornecedor de conectividade. 
  * Certifique-se de que tem o olho privado Azure configurado para o seu circuito. Consulte o artigo de [encaminhamento configurado](howto-routing-cli.md) para obter instruções de encaminhamento. 
  * Certifique-se de que o epeering privado Azure está configurado. O bGP que espreita entre a sua rede e a Microsoft tem de estar em alta para que possa permitir a conectividade de ponta a ponta.
  * Certifique-se de que tem uma rede virtual e um portal de rede virtual criado e totalmente provisionado. Siga as instruções para configurar uma porta de entrada de [rede virtual para a ExpressRoute](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli). Certifique-se `--gateway-type ExpressRoute`de que utiliza .

* Pode ligar até 10 redes virtuais a um circuito expressRoute padrão. Todas as redes virtuais devem estar na mesma região geopolítica quando utilizam um circuito expressroute padrão. 

* Um único VNet pode ser ligado a até quatro circuitos ExpressRoute. Utilize o processo abaixo para criar um novo objeto de ligação para cada circuito ExpressRoute a que está a ligar. Os circuitos ExpressRoute podem estar na mesma subscrição, subscrições diferentes ou uma mistura de ambos.

* Se ativar o addon premium ExpressRoute, pode ligar uma rede virtual fora da região geopolítica do circuito ExpressRoute ou ligar um maior número de redes virtuais ao circuito ExpressRoute. Para obter mais informações sobre o complemento premium, consulte as [FAQ](expressroute-faqs.md).

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Ligue uma rede virtual na mesma subscrição a um circuito

Pode ligar uma porta de entrada de rede virtual a um circuito ExpressRoute utilizando o exemplo. Certifique-se de que o portal de rede virtual está criado e está pronto para ser ligado antes de executar o comando.

```azurecli
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit
```

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Ligar uma rede virtual de uma subscrição diferente a um circuito

Pode partilhar um circuito ExpressRoute em várias subscrições. A figura abaixo mostra um simples esquema de como a partilha funciona para circuitos ExpressRoute em várias subscrições.

Cada uma das nuvens menores dentro da grande nuvem é usada para representar subscrições que pertencem a diferentes departamentos dentro de uma organização. Cada um dos departamentos da organização pode usar a sua própria subscrição para implementar os seus serviços,, mas eles podem partilhar um único circuito ExpressRoute para ligar de volta à sua rede no local. Um único departamento (neste exemplo: TI) pode ser dono do circuito ExpressRoute. Outras subscrições dentro da organização podem usar o circuito ExpressRoute.

> [!NOTE]
> As tarifas de conectividade e largura de banda para o circuito dedicado serão aplicadas ao Proprietário do Circuito ExpressRoute. Todas as redes virtuais partilham a mesma largura de banda.
> 
> 

![Conectividade de subscrição cruzada](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration---circuit-owners-and-circuit-users"></a>Administração - Proprietários de Circuitos e Utilizadores de Circuitos

O 'Proprietário do Circuito' é um utilizador autorizado de energia do recurso do circuito ExpressRoute. O Proprietário do Circuito pode criar autorizações que podem ser resgatadas por 'Utilizadores de Circuito'. Os Utilizadores de Circuito são proprietários de gateways de rede virtual que não estão dentro da mesma subscrição que o circuito ExpressRoute. Os Utilizadores de Circuitopodem resgatar autorizações (uma autorização por rede virtual).

O Proprietário do Circuito tem o poder de modificar e revogar as autorizações a qualquer momento. Quando uma autorização é revogada, todas as ligações de ligação são eliminadas da subscrição cujo acesso foi revogado.

### <a name="circuit-owner-operations"></a>Operações do Proprietário do Circuito

**Para criar uma autorização**

O Proprietário do Circuito cria uma autorização, que cria uma chave de autorização que pode ser usada por um Utilizador de Circuito para ligar os seus gateways de rede virtual ao circuito ExpressRoute. Uma autorização é válida para apenas uma ligação.

O exemplo que se segue mostra como criar uma autorização:

```azurecli
az network express-route auth create --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization
```

A resposta contém a chave de autorização e o estado:

```output
"authorizationKey": "0a7f3020-541f-4b4b-844a-5fb43472e3d7",
"authorizationUseStatus": "Available",
"etag": "W/\"010353d4-8955-4984-807a-585c21a22ae0\"",
"id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/authorizations/MyAuthorization1",
"name": "MyAuthorization1",
"provisioningState": "Succeeded",
"resourceGroup": "ExpressRouteResourceGroup"
```

**Rever as autorizações**

O Proprietário do Circuito pode rever todas as autorizações emitidas num determinado circuito, executando o seguinte exemplo:

```azurecli
az network express-route auth list --circuit-name MyCircuit -g ExpressRouteResourceGroup
```

**Para adicionar autorizações**

O Proprietário do Circuito pode adicionar autorizações utilizando o seguinte exemplo:

```azurecli
az network express-route auth create --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization1
```

**Para eliminar autorizações**

O Proprietário do Circuito pode revogar/excluir autorizações ao utilizador executando o seguinte exemplo:

```azurecli
az network express-route auth delete --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization1
```

### <a name="circuit-user-operations"></a>Operações de utilizador de circuito

O Utilizador do Circuito necessita do ID dos pares e de uma chave de autorização do Proprietário do Circuito. A chave de autorização é um GUID.

```powershell
Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
```

**Para resgatar uma autorização de ligação**

O Utilizador do Circuito pode executar o seguinte exemplo para resgatar uma autorização de ligação:

```azurecli
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit --authorization-key "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"
```

**Para libertar uma autorização de ligação**

Pode libertar uma autorização apagando a ligação que liga o circuito ExpressRoute à rede virtual.

## <a name="modify-a-virtual-network-connection"></a>Modificar uma ligação de rede virtual
Pode atualizar certas propriedades de uma ligação de rede virtual. 

**Para atualizar o peso da ligação**

A sua rede virtual pode ser ligada a vários circuitos ExpressRoute. Pode receber o mesmo prefixo de mais de um circuito ExpressRoute. Para escolher qual a ligação para enviar tráfego destinado a este prefixo, pode alterar *o Peso de Encaminhamento* de uma ligação. O tráfego será enviado na ligação com o peso de *encaminhamento*mais elevado .

```azurecli
az network vpn-connection update --name ERConnection --resource-group ExpressRouteResourceGroup --routing-weight 100
```

O intervalo de *Peso-Encaminhamento* é de 0 a 32000. O valor predefinido é 0.

## <a name="configure-expressroute-fastpath"></a>Configure ExpressRoute FastPath 
Pode ativar o [ExpressRoute FastPath](expressroute-about-virtual-network-gateways.md) se o seu circuito ExpressRoute estiver no [ExpressRoute Direct](expressroute-erdirect-about.md) e o seu portal virtual newtork for Ultra Performance ou ErGw3AZ. O FastPath melhora a preformação do caminho de dados, como pacotes por segundo e ligações por segundo entre a sua rede no local e a sua rede virtual. 

**Configure fastPath numa nova ligação**

```azurecli
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --express-route-gateway-bypass true --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit
```

**Atualizar uma ligação existente para ativar o FastPath**

```azurecli
az network vpn-connection update --name ERConnection --resource-group ExpressRouteResourceGroup --express-route-gateway-bypass true
```

## <a name="next-steps"></a>Passos seguintes

Para mais informações sobre o ExpressRoute, consulte o [ExpressRoute FAQ](expressroute-faqs.md).
