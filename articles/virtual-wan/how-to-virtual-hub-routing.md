---
title: Como configurar o encaminhamento do hub virtual
titleSuffix: Azure Virtual WAN
description: Este artigo descreve como configurar o encaminhamento de hub virtual
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: cae74a5f4859d208765c9a7e5cde05ff3c0c3096
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91313693"
---
# <a name="how-to-configure-virtual-hub-routing"></a>Como configurar o encaminhamento do hub virtual

Um hub virtual pode conter vários gateways, tais como um gateway VPN local-para-local, gateway ExpressRoute, gateway ponto-a-local e Azure Firewall. As capacidades de encaminhamento no centro virtual são fornecidas por um router que gere todo o encaminhamento, incluindo o encaminhamento de trânsito, entre os gateways usando o Border Gateway Protocol (BGP). Este router também fornece conectividade de trânsito entre redes virtuais que se conectam a um hub virtual e podem suportar até um rendimento agregado de 50 Gbps. Estas capacidades de encaminhamento aplicam-se aos clientes Standard Virtual WAN.

Para obter mais informações, consulte [sobre o encaminhamento do hub virtual](about-virtual-hub-routing.md).

## <a name="create-a-route-table"></a><a name="create-table"></a>Criar uma tabela de rotas

1. No portal Azure, navegue para o centro virtual.
2. Em **Conectividade**, selecione **Encaminhamento**. Na página 'Encaminhamento', vê as tabelas de rota **Padrão** e **Nenhuma.**

   :::image type="content" source="./media/how-to-virtual-hub-routing/routing.png" alt-text="Página de encaminhamento":::
3. Selecione **+Criar tabela de rotas** para abrir a página **'Tabela de Rotas' Criar.**
4. No separador 'Quadro de Rota' **'Criar',** preencha os seguintes campos.

   :::image type="content" source="./media/how-to-virtual-hub-routing/basics.png" alt-text="Separador básico":::

   * **Nome**
   * **Rotas**
   * **Nome da rota**
   * **Tipo de destino**
   * **Prefixo de destino**: Pode agregar prefixos. Por exemplo: VNet 1: 10.1.0.0/24 e VNet 2: 10.1.1.0/24 podem ser agregados como 10.1.0.0/16. As rotas **de ramificação** aplicam-se a todos os sites VPN conectados, circuitos ExpressRoute e ligações VPN do utilizador.
   * **Próximo salto**: Uma lista de ligações de rede virtuais, ou Azure Firewall.

     Se selecionar uma ligação de rede virtual, verá **rotas estáticas de configuração**. Esta é uma configuração opcional. Para obter mais informações, consulte [as rotas estáticas de configuração.](about-virtual-hub-routing.md#static)

      :::image type="content" source="./media/how-to-virtual-hub-routing/next-hop.png" alt-text="Próximo salto":::

5. Selecione o **separador Etiquetas** para configurar os nomes das etiquetas. As etiquetas fornecem um mecanismo para agrupar logicamente as tabelas de rotas.

    :::image type="content" source="./media/how-to-virtual-hub-routing/labels.png" alt-text="Configurar nomes de etiquetas":::

6. Selecione o separador **Associações** para associar ligações à tabela de rotas.
Verá **Os Balcões,** **Redes Virtuais** e as **definições de Corrente** das ligações.

    :::image type="content" source="./media/how-to-virtual-hub-routing/associations.png" alt-text="Ligações de associação à tabela de rotas":::

7. Selecione o **separador Propagações** para propagar rotas das ligações à tabela de rotas.

    :::image type="content" source="./media/how-to-virtual-hub-routing/propagations.png" alt-text="Rotas de propagação":::

8. Selecione **Criar** para criar a tabela de rotas.

## <a name="to-edit-a-route-table"></a><a name="edit-table"></a>Para editar uma tabela de rotas

No portal Azure, localize a tabela de rotas do seu hub virtual. Selecione a tabela de rotas para editar qualquer informação.

## <a name="to-delete-a-route-table"></a><a name="delete-table"></a>Para apagar uma tabela de rotas

No portal Azure, localize a tabela de rotas do seu hub virtual. Não é possível eliminar uma tabela de rota padrão ou nenhuma. No entanto, pode eliminar todas as tabelas de rotas personalizadas. Clique **em "..."** e, em seguida, selecione **Delete**.

## <a name="to-view-effective-routes"></a><a name="view-routes"></a>Para ver rotas eficazes

No portal Azure, localize a tabela de rotas do seu hub virtual. Clique **em "..."** e selecione **Rotas Eficazes** para visualizar rotas aprendidas pela tabela de rotas selecionada. As rotas propagadas da ligação à tabela de rotas são automaticamente povoadas em **Rotas Eficazes** do quadro de rotas. Para mais informações, consulte [Rotas eficazes.](effective-routes-virtual-hub.md)

:::image type="content" source="./media/how-to-virtual-hub-routing/effective.png" alt-text="Ver Rotas Eficazes" lightbox="./media/how-to-virtual-hub-routing/effective-expand.png":::

## <a name="to-set-up-routing-configuration-for-a-virtual-network-connection"></a><a name="routing-configuration"></a>Para configurar a configuração de encaminhamento para uma ligação de rede virtual

1. No portal Azure, navegue para o seu WAN virtual e, em **conectividade,** selecione **Conexões de Rede Virtual**.
1. Selecione **+Adicionar ligação**.
1. Selecione a rede virtual a partir do dropdown.
1. Configurar a configuração de encaminhamento para associar a uma tabela de rotas. Para **tabela de rotas associada,** selecione a tabela de rotas a partir do dropdown.
1. Configurar a configuração de encaminhamento para propagar para uma ou muitas tabelas de rota. Para **propagar para tabela de rota,** selecione a partir do dropdown.
1. Para **rotas estáticas,** configure rotas estáticas para aparelhos virtuais de rede (se aplicável). O WAN virtual suporta um único IP de lúpulo próximo para uma rota estática numa ligação de rede virtual. Por exemplo, se tiver um aparelho virtual separado para fluxos de tráfego de entrada e saída, seria melhor ter os aparelhos virtuais em VNETs separados e anexar os VNETs ao centro virtual.


:::image type="content" source="./media/how-to-virtual-hub-routing/routing-configuration.png" alt-text="Configurar configuração de encaminhamento" lightbox="./media/how-to-virtual-hub-routing/routing-configuration-expand.png":::

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o encaminhamento de hubs virtuais, consulte [sobre o encaminhamento do hub virtual](about-virtual-hub-routing.md).
Para obter mais informações sobre o VIRTUAL WAN, consulte as [FAQ.](virtual-wan-faq.md)
