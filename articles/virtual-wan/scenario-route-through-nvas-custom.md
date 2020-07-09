---
title: 'Cenário: Encaminhar o tráfego através de NVAs usando configurações personalizadas'
titleSuffix: Azure Virtual WAN
description: Este cenário ajuda-o a passar o tráfego através de NVAs usando um NVA diferente para tráfego ligado à Internet.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: 3fcad59bcfb75b8de6af14eae7e0bcb4962b17eb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85569268"
---
# <a name="scenario-route-traffic-through-nvas---custom-preview"></a>Cenário: Tráfego de rotas através de NVAs - personalizado (Pré-visualização)

Ao trabalhar com o encaminhamento virtual do hub virtual WAN, existem alguns cenários disponíveis. Neste cenário NVA (Network Virtual Appliance), o objetivo é encaminhar o tráfego através de um NVA para VNet <-> Branch, e usar um NVA diferente para tráfego ligado à Internet. Para obter informações sobre o encaminhamento de hubs virtuais, consulte [sobre o encaminhamento do hub virtual](about-virtual-hub-routing.md).

## <a name="scenario-architecture"></a><a name="architecture"></a>Arquitetura do cenário

Na **Figura 1,** há um hub, **Hub 1**.

* **O Hub 1** está diretamente ligado aos VNETs **NVA VNET 4** e **VNET 5**.

* O tráfego entre os VNETs 1, 2 e 3 e os Balcões (VPN/ER/P2S) deverá passar pelo **VNET 4 NVA** 10.4.0.5.

* Espera-se que todo o tráfego ligado à Internet dos VNETs 1, 2 e 3 seja proveniente do **VNET 5 NVA** 10.5.0.5.

**Figura 1**

:::image type="content" source="./media/routing-scenarios/nva-custom/figure-1.png" alt-text="Figura 1":::

## <a name="scenario-workflow"></a><a name="workflow"></a>Fluxo de trabalho de cenário

Para configurar o encaminhamento via NVA, aqui estão os passos a considerar:

1. Para que o tráfego ligado à Internet vá via VNET5, você precisa de VNETs 1, 2 e 3 para ligar diretamente via VNet olhando para VNET 5. Também precisa de um UDR configurado nos VNets para 0.0.0.0/0 e o próximo salto 10.5.0.5. Atualmente, o Virtual WAN não permite um próximo LNVA de lúpulo no centro virtual para 0.0.0.0/0.

1. No portal Azure, navegue até ao seu hub virtual e crie uma tabela de rotas personalizada **RT_Shared** que aprenderá rotas através da propagação de todas as ligações VNets e Branch. Na **Figura 2**, esta é representada como uma tabela de rota personalizada vazia **RT_Shared**.

   * **Rotas:** Não é necessário adicionar rotas estáticas.

   * **Associação:** Selecione VNETs 4 e 5, o que significa que as ligações VNETs 4 e 5 associam à tabela de rotas **RT_Shared**.

   * **Propagação:** Uma vez que pretende que todos os ramos e ligações VNet propaguem as suas rotas dinamicamente para esta tabela de rotas, selecione os balcões e todos os VNets.

1. Crie uma tabela de rotas personalizada **RT_V2B** para direcionar o tráfego dos VNETs 1, 2 e 3 para os balcões.

   * **Rotas:** Adicione uma entrada estática agregada para ramos (VPN/ER/P2S) (10.2.0.0/16 na **Figura 2**) com o próximo salto como ligação VNET 4. Também é necessário configurar uma rota estática na ligação do VNET 4 para prefixos de ramificação, e indicar que o próximo lúpulo será o IP específico da NVA em VNET 4.

   * **Associação:** Selecione todos os VNETs 1, 2 e 3. Isto implica que as ligações VNet 1, 2 e 3 associarão a esta tabela de rotas e serão capazes de aprender rotas (estáticas e dinâmicas através da propagação) nesta tabela de rotas.

   * **Propagação:** As ligações propagam rotas para as tabelas de rotas. A seleção dos VNETs 1, 2 e 3 permitirá a propagação das rotas de propagação dos VNETs 1, 2 e 3 para esta tabela de rotas. Não há necessidade de propagar rotas das ligações de filiais para RT_V2B, uma vez que o tráfego de ramal VNet passa pela NVA em VNET4.
  
1. Editar a tabela de rotas padrão **DefaultRouteTable**.

   Todas as ligações VPN, ExpressRoute e User VPN estão associadas à tabela de rotas predefinida. Todas as ligações VPN, ExpressRoute e User VPN propagam rotas para o mesmo conjunto de tabelas de rotas.

   * **Rotas:** Adicione uma entrada estática agregada para os VNETs 1, 2 e 3 (10.1.0.0/16 na **Figura 2**) com o próximo salto como ligação VNET 4. Também é necessário configurar uma rota estática na ligação do VNET 4 para prefixos agregados VNET 1, 2 e 3, e indicar que o próximo lúpulo será o IP específico da NVA em VNET 4.

   * **Associação:** Certifique-se de que a opção para os balcões (VPN/ER/P2S) está selecionada, garantindo que as ligações de sucursais no local estão associadas ao *padrão rotautetable*.

   * **Propagação a partir de:** Certifique-se de que a opção para os balcões (VPN/ER/P2S) está selecionada, garantindo que as ligações no local são rotas de propagação para o *padrão rotatável*.

**Figura 2**

:::image type="content" source="./media/routing-scenarios/nva-custom/figure-2.png" alt-text="Figura 2":::

## <a name="next-steps"></a>Próximos passos

* Para obter mais informações sobre o VIRTUAL WAN, consulte as [FAQ.](virtual-wan-faq.md)
* Para obter mais informações sobre o encaminhamento de hubs virtuais, consulte [sobre o encaminhamento do hub virtual](about-virtual-hub-routing.md).
