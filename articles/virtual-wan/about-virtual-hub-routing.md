---
title: Acerca do encaminhamento do hub virtual
titleSuffix: Azure Virtual WAN
description: Este artigo descreve o encaminhamento virtual do hub
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: c1a2a54bf2d4c5de3e6cfca66256f60592fc1f3e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101737516"
---
# <a name="about-virtual-hub-routing"></a>Acerca do encaminhamento do hub virtual

As capacidades de encaminhamento num hub virtual são fornecidas por um router que gere todo o encaminhamento entre gateways usando o Border Gateway Protocol (BGP). Um hub virtual pode conter vários gateways, tais como um gateway VPN local-para-local, gateway ExpressRoute, gateway ponto-a-local, Azure Firewall. Este router também fornece conectividade de trânsito entre redes virtuais que se conectam a um hub virtual e podem suportar até um rendimento agregado de 50 Gbps. Estas capacidades de encaminhamento aplicam-se aos clientes Standard Virtual WAN.

Para configurar o encaminhamento, consulte [Como configurar o encaminhamento do hub virtual](how-to-virtual-hub-routing.md).

## <a name="routing-concepts"></a><a name="concepts"></a>Conceitos de encaminhamento

As secções seguintes descrevem os conceitos-chave no encaminhamento de hubs virtuais.

### <a name="hub-route-table"></a><a name="hub-route"></a>Tabela de rota do hub

Uma tabela de rota de hub virtual pode conter uma ou mais rotas. Uma rota inclui o seu nome, uma etiqueta, um tipo de destino, uma lista de prefixos de destino, e próxima informação de lúpulo para um pacote a ser encaminhado. Uma **Ligação** normalmente terá uma configuração de encaminhamento que associou ou se propaga a uma tabela de rotas.

### <a name="connections"></a><a name="connection"></a>Ligações

As ligações são recursos do Gestor de Recursos que têm uma configuração de encaminhamento. Os quatro tipos de ligações são:

* **Ligação VPN**: Liga um site VPN a um gateway VPN de hub virtual.
* **Ligação ExpressRoute**: Liga um circuito ExpressRoute a um gateway expressRoute do hub virtual.
* **Ligação de configuração P2S**: Liga uma configuração VPN do utilizador (ponto a local) a um gateway de utilizador de hub virtual VPN (ponto a local).
* **Ligação de rede virtual do hub**: Conecta redes virtuais a um hub virtual.

Pode configurar a configuração de encaminhamento para uma ligação de rede virtual durante a configuração. Por predefinição, todas as ligações associam e propagam-se à tabela de rotas Predefinido.

### <a name="association"></a><a name="association"></a>Associação

Cada ligação está associada a uma tabela de rotas. Associar uma ligação a uma tabela de rotas permite que o tráfego seja enviado para o destino indicado como rotas na tabela de rotas. A configuração de encaminhamento da ligação mostrará a tabela de rotas associada.  Várias ligações podem ser associadas à mesma tabela de rotas. Todas as ligações VPN, ExpressRoute e User VPN estão associadas à mesma tabela de rotas (predefinida).

Por predefinição, todas as ligações estão associadas a uma **tabela de rota padrão** num hub virtual. Cada hub virtual tem a sua própria tabela de rotas Padrão, que pode ser editada para adicionar uma rota estática.s. As rotas adicionadas estáticamente têm precedência sobre rotas dinamicamente aprendidas para os mesmos prefixos.

:::image type="content" source="./media/about-virtual-hub-routing/concepts-association.png" alt-text="Associação":::

### <a name="propagation"></a><a name="propagation"></a>Propagação

As ligações propagam dinamicamente as rotas para uma tabela de rotas. Com uma ligação VPN, ligação ExpressRoute ou ligação de configuração P2S, as rotas são propagadas do hub virtual para o router no local utilizando BGP. As rotas podem ser propagadas a uma ou múltiplas tabelas de rotas.

Uma **tabela de rotas Zero** também está disponível para cada hub virtual. A propagação para o quadro de rotas Zero implica que não são necessárias rotas para serem propagadas a partir da ligação. As ligações VPN, ExpressRoute e User VPN propagam rotas para o mesmo conjunto de tabelas de rotas.

:::image type="content" source="./media/about-virtual-hub-routing/concepts-propagation.png" alt-text="Propagação":::

### <a name="labels"></a><a name="labels"></a>Etiquetas

As etiquetas fornecem um mecanismo para agrupar logicamente as tabelas de rotas. Isto é especialmente útil durante a propagação de rotas de ligações a várias tabelas de rotas. Por exemplo, a **Tabela de Rota Padrão** tem uma etiqueta incorporada chamada "Padrão". Quando os utilizadores propagam as rotas de ligação para a etiqueta 'Predefinido', aplica-se automaticamente a todas as tabelas de rota padrão em todos os hubs do WAN Virtual.

### <a name="configuring-static-routes-in-a-virtual-network-connection"></a><a name="static"></a>Configurar rotas estáticas numa ligação de rede virtual

Configurar as rotas estáticas fornece um mecanismo para orientar o tráfego através de um próximo IP de lúpulo, que poderia ser de um Aparelho Virtual de Rede (NVA) a provisionado num VNet Spoke ligado a um hub virtual. A rota estática é composta por um nome de rota, lista de prefixos de destino e um próximo IP de lúpulo.

## <a name="route-tables-for-pre-existing-routes"></a><a name="route"></a>Tabelas de rotas para rotas pré-existentes

As tabelas de rotas têm agora características de associação e propagação. Uma tabela de rotas pré-existente é uma tabela de rotas que não tem estas funcionalidades. Se tiver rotas pré-existentes no encaminhamento do hub e quiser utilizar as novas capacidades, considere o seguinte:

* **Clientes STANDARD VIRTUAL WAN com rotas pré-existentes no centro virtual:**

   Se tiver rotas pré-existentes na secção de encaminhamento para o hub no portal Azure, terá de as eliminar primeiro e, em seguida, tentar criar novas tabelas de rotas (disponíveis na secção Tabelas de Rota para o hub no portal Azure).

* **Clientes Básicos DE WAN Virtuais com rotas pré-existentes no centro virtual:**

   Se tiver rotas pré-existentes na secção de encaminhamento para o hub no portal Azure, terá de as eliminar primeiro e, em seguida, **atualize** o seu WAN Virtual Básico para O WAN Virtual Padrão. Consulte [a Atualização de um WAN virtual de Basic para Standard](upgrade-virtual-wan.md).

## <a name="hub-reset"></a><a name="reset"></a>Reset do hub

O **reset** do hub virtual está disponível apenas no portal Azure. A reposição fornece-lhe uma forma de trazer quaisquer recursos falhados, tais como tabelas de rotas, router de hub ou o próprio recurso do hub virtual de volta ao seu estado de provisão legítimo. Considere reiniciar o hub antes de contactar a Microsoft para obter suporte. Esta operação não repõe nenhum dos gateways num centro virtual.

## <a name="additional-considerations"></a><a name="considerations"></a>Considerações adicionais

Considere o seguinte ao configurar o encaminhamento Virtual WAN:

* Todas as ligações de ramo (ponto a local, site-to-site e ExpressRoute) devem ser associadas à tabela de rotas predefinida. Assim, todos os ramos aprenderão os mesmos prefixos.
* Todas as ligações de ramo precisam de propagar as suas rotas para o mesmo conjunto de tabelas de rotas. Por exemplo, se decidir que os ramos devem propagar-se à tabela de rotas Predefinidos, esta configuração deve ser consistente em todos os ramos. Como resultado, todas as ligações associadas à tabela de rotas Predefinidas poderão chegar a todos os ramos.
* O ramo-a-ramo via Azure Firewall não é atualmente suportado.
* Ao utilizar o Azure Firewall em várias regiões, todas as redes virtuais faladas devem estar associadas à mesma tabela de rotas. Por exemplo, não é possível ter um subconjunto dos VNets a passar pela Firewall do Azure, enquanto outros VNets contornam o Azure Firewall no mesmo hub virtual.
* Um único IP de lúpulo seguinte pode ser configurado por ligação VNet.

## <a name="next-steps"></a>Passos seguintes

* Para configurar o encaminhamento, consulte [Como configurar o encaminhamento do hub virtual](how-to-virtual-hub-routing.md).
* Para obter mais informações sobre o VIRTUAL WAN, consulte as [FAQ.](virtual-wan-faq.md)
