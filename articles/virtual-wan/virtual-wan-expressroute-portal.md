---
title: Utilize o WAN Virtual do Azure para criar as ligações ExpressRoute para ambientes do Azure e no local | Documentos da Microsoft
description: Neste tutorial, saiba como utilizar o WAN Virtual do Azure para criar as ligações ExpressRoute para ambientes do Azure e no local.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 10/5/2018
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect my corporate on-premises network(s) to my VNets using Virtual WAN and ExpressRoute.
ms.openlocfilehash: 6ac9fce9a6903b95156f43f41cea7bffdb1a072d
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57402851"
---
# <a name="tutorial-create-an-expressroute-association-using-azure-virtual-wan-preview"></a>Tutorial: Criar uma associação de ExpressRoute com o Azure WAN Virtual (pré-visualização)

Este tutorial mostra-lhe como utilizar a WAN Virtual para se ligar aos seus recursos no Azure através de um circuito e associação do ExpressRoute. Para obter mais informações sobre a WAN Virtual, veja [Virtual WAN Overview](virtual-wan-about.md) (Descrição Geral da WAN Virtual)

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um vWAN
> * Criar um hub
> * Encontrar e associar um circuito a um hub
> * Associar o circuito ao(s) hub(s)
> * Ligar uma VNet a um hub
> * Ver a WAN Virtual
> * Ver o estados de funcionamento dos recursos
> * Monitorizar uma ligação

> [!IMPORTANT]
> Esta pré-visualização pública é disponibilizada sem um contrato de nível de serviço e não deve ser utilizada para cargas de trabalho de produção. Algumas funcionalidades podem não ser suportadas, podem ter capacidades restringidas ou podem não estar disponíveis em todas as localizações do Azure. Veja os [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter mais informações.
>

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [Before you begin](../../includes/virtual-wan-tutorial-vwan-before-include.md)]

## <a name="register"></a>Registar esta funcionalidade

Antes de poder configurar uma WAN Virtual, tem de inscrever primeiro a sua subscrição na Pré-visualização. Caso contrário, não poderá trabalhar com a WAN Virtual no portal. Para se inscrever, envie um e-mail para **azurevirtualwan@microsoft.com** com o ID da subscrição. Receberá um e-mail assim que a sua subscrição tiver sido inscrita.

**Considerações sobre a Pré-visualização:**

* Disponibilidade de região: EUA Centro-Oeste
* O circuito do ExpressRoute tem de estar ativado num país que suporte o [Alcance Global do ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-faqs#where-is-expressroute-global-reach-supported)

## <a name="vnet"></a>1. Criar uma rede virtual

[!INCLUDE [Create a virtual network](../../includes/virtual-wan-tutorial-vnet-include.md)]

## <a name="openvwan"></a>2. Criar uma WAN Virtual

Num browser, navegue para o [portal do Azure (pré-visualização)](https://aka.ms/azurevirtualwanpreviewfeatures) e inicie sessão com a sua conta do Azure.

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-vwan-include.md)]

### <a name="getting-started-page"></a>Página de Introdução

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-gettingstarted-include.md)]

## <a name="hub"></a>3. Criar um hub

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-hub-include.md)]

## <a name="hub"></a>4. Encontrar e associar um circuito a um hub

1. Selecione o seu vWAN e em **Arquitetura do WAN Virtual**, selecione **Circuitos do ExpressRoute**
2. Se o circuito do ExpressRoute estiver na mesma subscrição que o seu vWAN, clique em **Selecionar circuito do ExpressRoute** na sua subscrição(ões) 
3. Utilizando a lista pendente, selecione o ExpressRoute que pretende associar ao hub.
4. Se o circuito do ExpressRoute não estiver na mesma subscrição ou foram fornecidos [um ID de chave e o mesmo nível de autorização](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md), selecione **localizar um circuito resgatar uma chave de autorização**
5. Introduza os seguintes detalhes:
* **Chave de autorização** – Gerado pelo proprietário do circuito, conforme descrito acima
* **URI do circuito do elemento** – URI do circuito que é apresentado pelo proprietário do circuito e é o identificador exclusivo do circuito
* **Peso de encaminhamento** - [peso de encaminhamento](../expressroute/expressroute-optimize-routing.md) permite-lhe dar preferência a determinados caminhos quando vários circuitos de diferentes localizações de peering estão ligados ao mesmo hub
6. Clique em **Encontrar circuito** e selecione o circuito, se for encontrado
7. Selecione 1 ou mais hubs no menu pendente e clique em **Guardar**

## <a name="vnet"></a>5. Ligar a VNet a um hub

Neste passo, vai criar a ligação de peering entre o hub e uma VNet. Repita estes passos para cada VNet que queira ligar.

1. Na página da WAN virtual, clique em **Virtual network connection** (Ligação de rede virtual).
2. Na página de ligação da rede virtual, clique em **+Add connection** (+Adicionar ligação).
3. Na página **Add connection** (Adicionar ligação), preencha os seguintes campos:

    * **Connection name** (Nome da ligação) - dê um nome à ligação.
    * **Hubs** - selecione o hub que pretende associar a esta ligação.
    * **Subscription** (Subscrição) - verifique a subscrição.
    * **Virtual network** (Rede virtual) - selecione a rede virtual que pretende ligar a este hub. A rede virtual não pode ter um gateway de rede virtual já existente.


## <a name="viewwan"></a>6. Ver a WAN Virtual

1. Navegue para a WAN virtual.
2. Na página Overview, cada ponto no mapa representa um hub. Coloque o cursor sobre um ponto para ver o resumo do estado de funcionamento do hub.
3. Na secção Hubs e ligações, pode ver o estado do hub, o site, a região, o estado da ligação VPN e os bytes de entrada e saída.

## <a name="viewhealth"></a>7. Ver o estado de funcionamento do seu recurso

1. Navegue para a WAN.
2. Na página WAN, na secção **SUPPORT + Troubleshooting** (SUPORTE + Resolução de Problemas), clique em **Health** (Estado de funcionamento) e veja o recurso.

## <a name="connectmon"></a>8. Monitorizar uma ligação

Crie uma ligação para monitorizar a comunicação entre uma VM do Azure e um site remoto. Para obter informações sobre como configurar um monitor de ligação, veja [Monitorizar a comunicação de rede](~/articles/network-watcher/connection-monitor.md). O campo de origem é o IP da VM no Azure e o IP de destino é o IP do site.

## <a name="cleanup"></a>9. Limpar recursos

Quando já não precisa destes recursos, pode utilizar [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos e todos os recursos nele contidos. Substitua "myResourceGroup" pelo nome do grupo de recursos e execute o seguinte comando do PowerShell:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Criar um vWAN
> * Criar um hub
> * Encontrar e associar um circuito a um hub
> * Associar o circuito ao(s) hub(s)
> * Ligar uma VNet a um hub
> * Ver a WAN Virtual
> * Ver o estados de funcionamento dos recursos
> * Monitorizar uma ligação

Para saber mais sobre a WAN Virtual, veja a página [Virtual WAN Overview](virtual-wan-about.md) (Descrição Geral da WAN Virtual).
