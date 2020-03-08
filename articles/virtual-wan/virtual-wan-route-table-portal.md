---
title: 'Wan virtual: Criar tabela de rota de hub virtual para NVA: Portal Azure'
description: Mesa virtual de rota do hub virtual WAN para orientar o tráfego para um aparelho virtual de rede usando o portal.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/05/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to create a route table using the portal.
ms.openlocfilehash: 0807b535adc45093b439dba5ab8a0ea26b2a0721
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78402936"
---
# <a name="create-a-virtual-wan-hub-route-table-for-nvas-azure-portal"></a>Crie uma tabela de rota virtual wan hub para NVAs: Portal Azure

Este artigo mostra-lhe como orientar o tráfego de um ramo (site no local) ligado ao centro virtual WAN a uma rede virtual (VNet) através de um Eletrodoméstico Virtual de Rede (NVA).

![Diagrama da WAN Virtual](./media/virtual-wan-route-table/vwanroute.png)

## <a name="before-you-begin"></a>Antes de começar

Verifique se preencheu os seguintes critérios:

*  Tem um Aparelho Virtual rede (NVA). Um Aparelho Virtual de Rede é um software de terceiros à sua escolha que é normalmente abastecido a partir do Azure Marketplace numa rede virtual.

    * Um endereço IP privado deve ser atribuído à interface de rede NVA.

    * A NVA não está implantada no centro virtual. Deve ser implantado numa rede virtual separada.

    *  A rede virtual NVA pode ter uma ou muitas redes virtuais ligadas a ela. Neste artigo, referimo-nos à rede virtual NVA como um "VNet de voz indireta". Estas redes virtuais podem ser ligadas ao VNet NVA utilizando o peering VNet. As ligações VNet Peering são representadas por setas pretas na figura acima entre VNet 1, VNet 2 e NVA VNet.
*  Criou duas redes virtuais. Serão usados como VNets falados.

    * Os espaços de endereço saldama VNet são: VNet1: 10.0.2.0/24 e VNet2: 10.0.3.0/24. Se precisar de informações sobre como criar uma rede virtual, consulte [Criar uma rede virtual](../virtual-network/quick-create-portal.md).

    * Certifique-se de que não existem gateways de rede virtuais em nenhum dos VNets.

    * Os VNets não requerem uma sub-rede de gateway.

## <a name="signin"></a>1. Iniciar sessão

Num browser, navegue para o [Portal do Azure](https://portal.azure.com) e inicie sessão com a sua conta do Azure.

## <a name="vwan"></a>2. Criar um WAN virtual

Crie um WAN virtual. Utilize os seguintes valores exemplo:

* **Nome wan virtual:** myVirtualWAN
* **Grupo de recursos:** testRG
* **Localização:** Oeste dos EUA

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-vwan-include.md)]

## <a name="hub"></a>3. Criar um hub

Crie o centro. Utilize os seguintes valores exemplo:

* **Localização:** Oeste dos EUA
* **Nome:** westushub
* **Espaço de endereço seletivo:** 10.0.1.0/24

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-hub-include.md)]

## <a name="route"></a>4. Criar e aplicar uma tabela de rota de hub

Atualize o hub com uma mesa de rota do hub. Utilize os seguintes valores exemplo:

* Espaços de **endereço SVNet:** (VNet1 e VNet2) 10.0.2.0/24 e 10.0.3.0/24
* Endereço IP privado de interface de **rede DMZ NVA:** 10.0.4.5

1. Navegue para o seu WAN virtual.
2. Clique no centro para o qual pretende criar uma tabela de rotas.
3. Clique no **...** , e, em seguida, clique em **Editar o centro virtual**.
4. Na página do **hub virtual Editar,** desloque-se e selecione a tabela de utilização da caixa de verificação **para o encaminhamento**.
5. No **prefixo de destino Se for** coluna, adicione os espaços de endereço. Na coluna Enviar para a próxima coluna **de lúpulo,** adicione o endereço IP privado da interface de rede DMZ NVA.
6. Clique **em Confirmar** para atualizar o recurso do hub com as definições da tabela de rotas.

## <a name="connections"></a>5. Criar as ligações VNet

Crie uma ligação de rede virtual de cada VNet de voz indireta (VNet1 e VNet2) ao centro. Estas ligações de rede virtuais são representadas pelas setas azuis na figura acima. Em seguida, crie uma ligação VNet do VNet NVA para o centro (seta preta na figura).

 Para este passo, pode utilizar os seguintes valores:

| Nome de rede virtual| Nome da ligação|
| --- | --- |
| VNet1 | testconnection1 |
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
