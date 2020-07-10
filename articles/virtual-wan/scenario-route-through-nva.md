---
title: 'Cenário: Encaminhar o tráfego através de uma NVA'
titleSuffix: Azure Virtual WAN
description: Encaminhar o tráfego através de uma NVA
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: ed64b9d281cfbbf8202a99335ea2759b27a6fc42
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86142963"
---
# <a name="scenario-route-traffic-through-an-nva"></a>Cenário: Encaminhar o tráfego através de uma NVA

Ao trabalhar com o encaminhamento virtual do hub virtual WAN, existem alguns cenários disponíveis. Neste cenário NVA, o objetivo é encaminhar o tráfego através de um NVA (Network Virtual Appliance) para ramo para VNet e VNet para ramificar. Para obter informações sobre o encaminhamento de hubs virtuais, consulte [sobre o encaminhamento do hub virtual](about-virtual-hub-routing.md).

> [!NOTE]
> Algumas das capacidades de encaminhamento ainda podem estar a ser lançadas. Se o lançamento ainda não tiver acontecido na sua região, por favor, use os passos nestas versões dos artigos entretanto:
>* [Artigo do portal Azure](virtual-wan-route-table-nva-portal.md)
>* [Artigo PowerShell](virtual-wan-route-table-nva.md)
>

## <a name="scenario-architecture"></a><a name="architecture"></a>Arquitetura do cenário

Na **Figura 1**, existem dois hubs; **Hub 1** e **Hub 2**.

* **O Hub 1** e **o Hub 2** estão diretamente ligados aos VNET 2 e **VNET 4**da NVA **VNET 2** .

* **VNET 5** e **VNET 6** são espreitados com **VNET 2**.

* **VNET 7** e **VNET 8** são espreitados com **VNET 4**.

* **Os VNETs 5,6,7,8** são porta-vozes indiretos, não diretamente ligados a um centro virtual.

**Figura 1**

:::image type="content" source="./media/routing-scenarios/nva/nva.png" alt-text="Figura 1":::

## <a name="scenario-workflow"></a><a name="workflow"></a>Fluxo de trabalho de cenário

Para configurar o encaminhamento via NVA, aqui estão os passos a considerar:

1. Identifique a ligação VNet falada pela NVA. Na **Figura 1**, são **ligações VNET 2 (eastusconn)** e **VNET 4 (weconn)**.

   Certifique-se de que existem UDRs configurados:
   * De VNET 5 e 6 a VNET 2 NVA IP
   * De VNET 7 e 8 a VNET 4 NVA IP 
   
   Não é necessário ligar o VNET 5,6,7,8 diretamente aos centros virtuais. Certifique-se de que os NSGs em VNETs 5,6,7,8 permitem o tráfego de sucursais (VPN/ER/P2S) ou VNETs ligados aos seus VNETs remotos. Por exemplo, o VNET 5,6 deve garantir que os NSGs permitem o tráfego para prefixos de endereço no local e VNETs 7,8 que estão ligados ao hub remoto 2. 

2. Adicione uma entrada estática agregada para VNETs 2,5,6 à tabela de rota padrão do Hub 1. 

   :::image type="content" source="./media/routing-scenarios/nva/nva-static-expand.png" alt-text="Exemplo":::

3. Configure uma rota estática para VNETs 5,6 na ligação virtual da rede VNET 2. Para configurar a configuração de encaminhamento para uma ligação de rede virtual, consulte [o encaminhamento do hub virtual](how-to-virtual-hub-routing.md#routing-configuration).

4. Adicione uma entrada estática agregada para VNETs 4,7,8 à tabela de rota padrão do Hub 1.

5. Repita os passos 2, 3 e 4 para a tabela de rota padrão do Hub 2.

Isto resultará em alterações na configuração do encaminhamento, como visto na figura abaixo

   :::image type="content" source="./media/routing-scenarios/nva/nva-result.png" alt-text="Result":::

## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre o VIRTUAL WAN, consulte as [FAQ.](virtual-wan-faq.md)
* Para obter mais informações sobre o encaminhamento de hubs virtuais, consulte [sobre o encaminhamento do hub virtual](about-virtual-hub-routing.md).
