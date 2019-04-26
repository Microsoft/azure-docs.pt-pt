---
title: Criar uma tabela de rotas de virtual hub WAN Virtual do Azure - portal do Azure | Documentos da Microsoft
description: Tabela de rotas de Virtual WAN virtual hub para conduzir o tráfego para uma aplicação virtual de rede com o portal.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to create a route table using the portal.
ms.openlocfilehash: 2c8b3b4671fd14f9b10b8491861ae2c652f0188b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60461659"
---
# <a name="create-a-virtual-wan-hub-route-table-for-nvas-azure-portal"></a>Crie uma tabela de rotas de hub Virtual WAN para NVAs: Portal do Azure

Este artigo mostra-lhe como conduzir o tráfego a partir de um hub para uma aplicação de rede Virtual (NVA).

![Diagrama da WAN Virtual](./media/virtual-wan-route-table/vwanroute.png)

## <a name="before-you-begin"></a>Antes de começar

Certifique-se de que cumpriu os seguintes critérios:

*  Tem uma aplicação Virtual de rede (NVA). Uma aplicação Virtual de rede é um software de terceiros à sua escolha, que normalmente é aprovisionado do Azure Marketplace numa rede virtual.

    * Um endereço IP privado tem de ser atribuído à interface de rede NVA.

    * Não está implementada a NVA no hub virtual. Tem de ser implementada numa VNet separada.

    *  A VNet NVA pode ter uma ou várias redes virtuais ligadas à mesma. Neste artigo, nos Referimos à NVA VNet como um "indireta VNet spoke". Nestas VNets podem ser ligadas à NVA VNet ao utilizar o VNet peering.
*  Criar 2 VNets. Que serão utilizados como spoke VNets.

    * Para este exercício, os espaços de endereços do VNet spoke são: VNet1: 10.0.2.0/24 e VNet2: 10.0.3.0/24. Se precisar de informações sobre como criar uma VNet, veja [criar uma rede virtual](../virtual-network/quick-create-portal.md).

    * Certifique-se de que existem não gateways de rede virtual em qualquer uma das VNets.
    * Para esta configuração, nestas VNets não necessitam de uma sub-rede de gateway.

## <a name="signin"></a>1. Iniciar sessão

Num browser, navegue para o [Portal do Azure](https://portal.azure.com) e inicie sessão com a sua conta do Azure.

## <a name="vwan"></a>2. Criar uma WAN Virtual

Crie uma WAN virtual. Para os fins deste exercício, pode utilizar os seguintes valores:

* **Nome WAN virtual:** myVirtualWAN
* **Grupo de recursos:** testRG
* **Localização:** EUA Oeste

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-vwan-include.md)]

## <a name="hub"></a>3. Criar um hub

Crie o hub. Para os fins deste exercício, pode utilizar os seguintes valores:

* **Localização:** EUA Oeste
* **Nome:** westushub
* **Espaço de endereços privados de Hub:** 10.0.1.0/24

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-hub-include.md)]

## <a name="route"></a>4. Criar e aplicar uma tabela de rotas de hub

Atualize o hub com uma tabela de rotas de hub. Para os fins deste exercício, pode utilizar os seguintes valores:

* **Espaços de endereços VNet spoke indireta:** 10.0.2.0/24 (VNet1 e VNet2) e 10.0.3.0/24
* **Rede de Perímetro NVA interface privado endereço IP de rede:** 10.0.4.5

1. Navegue até à sua WAN virtual.
2. Clique no hub de para o qual pretende criar uma tabela de rotas.
3. Clique no **...** e, em seguida, clique em **hub virtual da edição**.
4. Sobre o **hub virtual de edição** página, desloque para baixo e selecione a caixa de verificação **tabela de utilização para o encaminhamento**.
5. Na **se o prefixo de destino é** coluna, adicionar os espaços de endereços. Na **enviar para o próximo salto** coluna, adicione o endereço IP privado do DMZ NVA rede interface.
6. Clique em **confirmar** para atualizar o recurso de hub com as definições de tabela de rota.

## <a name="connections"></a>5. Criar as ligações de VNet

Crie uma ligação a partir de cada spoke indireta VNet (VNet1 e VNet2) para o hub. Em seguida, crie uma ligação de VNet a NVA para o hub.

 Para este passo, pode utilizar os seguintes valores:

| Nome da VNet| Nome da ligação|
| --- | --- |
| VNet1 | testconnection1 |
| VNet2 | testconnection2 |
| NVAVNet | testconnection3 |

Repita o procedimento seguinte para cada VNet à qual pretende ligar.

1. Na página da WAN virtual, clique em **Ligações de rede virtual**.
2. Na página de ligação da rede virtual, clique em **+Add connection** (+Adicionar ligação).
3. Na página **Add connection** (Adicionar ligação), preencha os seguintes campos:

    * **Connection name** (Nome da ligação) - dê um nome à ligação.
    * **Hubs** - selecione o hub que pretende associar a esta ligação.
    * **Subscription** (Subscrição) - verifique a subscrição.
    * **Virtual network** (Rede virtual) - selecione a rede virtual que pretende ligar a este hub. A rede virtual não pode ter um gateway de rede virtual já existente.
4. Clique em **OK** para criar a ligação.

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre a WAN Virtual, veja a página [Virtual WAN Overview](virtual-wan-about.md) (Descrição Geral da WAN Virtual).