---
title: 'Virtual WAN: Criar mesa de rota de hub virtual para NVA: Portal Azure'
description: Tabela de rota virtual WAN para orientar o tráfego para um aparelho virtual de rede usando o portal.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to create a route table using the portal.
ms.openlocfilehash: 8de7ad2808e5319819410b3125472e28496647b0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91267147"
---
# <a name="create-a-virtual-wan-hub-route-table-for-nvas-azure-portal"></a>Crie uma tabela de rota de hub VIRTUAL WAN para NVAs: Portal Azure

Este artigo mostra-lhe como orientar o tráfego de um ramo (site no local) ligado ao centro DE WAN Virtual a uma rede virtual Spoke (VNet) através de um Aparelho Virtual de Rede (NVA).

![Diagrama da WAN Virtual](./media/virtual-wan-route-table-nva/vwanroute.png)

## <a name="before-you-begin"></a>Antes de começar

Verifique se cumpriu os seguintes critérios:

*  Tem um Aparelho Virtual de Rede (NVA). Um Aparelho Virtual de Rede é um software de terceiros à sua escolha que é normalmente a provisionado a partir do Azure Marketplace numa rede virtual.

    * Um endereço IP privado deve ser atribuído à interface de rede NVA.

    * A NVA não está implantada no centro virtual. Deve ser implantado numa rede virtual separada.

    *  A rede virtual NVA pode ter uma ou muitas redes virtuais ligadas à rede. Neste artigo, referimo-nos à rede virtual NVA como um "VNet indireto falado". Estas redes virtuais podem ser ligadas ao NVA VNet utilizando o peering VNet. As ligações VNet Peering são representadas por setas pretas na figura acima entre VNet 1, VNet 2 e NVA VNet.
*  Criaste duas redes virtuais. Serão usados como VNets falados.

    * Os espaços de endereço de fala VNet são: VNet1: 10.0.2.0/24 e VNet2: 10.0.3.0/24. Se precisar de informações sobre como criar uma rede virtual, consulte [criar uma rede virtual.](../virtual-network/quick-create-portal.md)

    * Certifique-se de que não existem portas de rede virtuais em nenhum dos VNets.

    * Os VNets não requerem uma sub-rede de gateway.

## <a name="1-sign-in"></a><a name="signin"></a>1. Iniciar sção

Num browser, navegue para o [Portal do Azure](https://portal.azure.com) e inicie sessão com a sua conta do Azure.

## <a name="2-create-a-virtual-wan"></a><a name="vwan"></a>2. Criar um WAN virtual

Crie um WAN virtual. Utilize os seguintes valores de exemplo:

* **Nome WAN virtual:** myVirtualWAN
* **Grupo de recursos:** testRG
* **Localização:** Eua Ocidentais

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-vwan-include.md)]

## <a name="3-create-a-hub"></a><a name="hub"></a>3. Criar um hub

Criar o centro. Utilize os seguintes valores de exemplo:

* **Localização:** Eua Ocidentais
* **Nome:** westushub
* **Espaço de endereço privado hub:** 10.0.1.0/24

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-hub-include.md)]

## <a name="4-create-and-apply-a-hub-route-table"></a><a name="route"></a>4. Criar e aplicar uma tabela de rotas do hub

Atualize o hub com uma tabela de rota de hub. Utilize os seguintes valores de exemplo:

* **Espaços de endereçoS VNet:** (VNet1 e VNet2) 10.0.2.0/24 e 10.0.3.0/24
* **Endereço IP privado interface de rede DMZ NVA:** 10.0.4.5

1. Navegue para o seu WAN virtual.
2. Clique no centro para o qual deseja criar uma tabela de rotas.
3. Clique no **...**, e depois clique em **Editar o hub virtual**.
4. Na página **do hub virtual editar,** desloque-se para baixo e selecione a tabela de utilização da caixa de **verificação para encaminhamento**.
5. No **prefixo de destino Se o prefixo de destino for** coluna, adicione os espaços de endereço. Na coluna Enviar para a próxima coluna **de lúpulo,** adicione o endereço IP privado de interface de interface de rede DMZ NVA.

   > [!NOTE]
   > A rede DMZ NVA é aplicável ao centro local.
   
6. Clique **em Confirmar** para atualizar o recurso do hub com as definições da tabela de rotas.

## <a name="5-create-the-vnet-connections"></a><a name="connections"></a>5. Criar as ligações VNet

Crie uma ligação de rede virtual de cada VNet (VNet1 e VNet2) indireto para o hub. Estas ligações de rede virtuais são representadas pelas setas azuis na figura acima. Em seguida, crie uma ligação VNet do VNet NVA para o hub (seta preta na figura).

 Para este passo, pode utilizar os seguintes valores:

| Nome da rede virtual| Nome da ligação|
| --- | --- |
| VNet1 | ligação de teste1 |
| VNet2 | testconnection2 |
| NVAVNet | testconnection3 |

Repita o seguinte procedimento para cada rede virtual que pretende ligar.

1. Na página da WAN virtual, clique em **Ligações de rede virtual**.
2. Na página de ligação da rede virtual, clique em **+Add connection** (+Adicionar ligação).
3. Na página **Add connection** (Adicionar ligação), preencha os seguintes campos:

    * **Connection name** (Nome da ligação) - dê um nome à ligação.
    * **Hubs** - selecione o hub que pretende associar a esta ligação.
    * **Subscription** (Subscrição) - verifique a subscrição.
    * **Virtual network** (Rede virtual) - selecione a rede virtual que pretende ligar a este hub. A rede virtual não pode ter um gateway de rede virtual já existente.
4. Clique **em OK** para criar a ligação.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a WAN Virtual, veja a página [Virtual WAN Overview](virtual-wan-about.md) (Descrição Geral da WAN Virtual).
