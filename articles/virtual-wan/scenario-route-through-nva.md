---
title: 'Cenário: Encaminhar o tráfego através de um aparelho virtual de rede (NVA)'
titleSuffix: Azure Virtual WAN
description: Encaminhar o tráfego através de uma NVA
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: 24671a34214864e253d96c356dc8b2853bf6d560
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100519801"
---
# <a name="scenario-route-traffic-through-an-nva"></a>Cenário: Encaminhar o tráfego através de uma NVA

Ao trabalhar com o encaminhamento virtual do hub virtual WAN, existem alguns cenários disponíveis. Neste cenário NVA, o objetivo é encaminhar o tráfego através de um NVA (Network Virtual Appliance) para ramo para VNet e VNet para ramificar. Para obter informações sobre o encaminhamento de hubs virtuais, consulte [sobre o encaminhamento do hub virtual](about-virtual-hub-routing.md).

> [!NOTE]
> Se já tem uma configuração com rotas que são anteriores às novas capacidades Como configurar o [encaminhamento de hub virtual](how-to-virtual-hub-routing.md) que está disponível, por favor, use os passos nestas versões dos artigos:
>* [Artigo do portal Azure](virtual-wan-route-table-nva-portal.md)
>* [Artigo PowerShell](virtual-wan-route-table-nva.md)
>

## <a name="design"></a><a name="design"></a>Design

Neste cenário, utilizaremos a convenção de nomeação:

* "NVA VNets" para redes virtuais onde os utilizadores tenham implementado um NVA e tenham conectado outras redes virtuais como porta-vozes (VNet 2 e VNet 4 na **Figura 2** mais abaixo no artigo).
* "NVA Spokes" para redes virtuais ligadas a um VNet NVA (VNet 5, VNet 6, VNet 7 e VNet 8 na **Figura 2** mais abaixo no artigo).
* "VNets não-NVA" para redes virtuais ligadas a WAN virtuais que não tenham um NVA ou outros VNets com eles (VNet 1 e VNet 3 na **Figura 2** mais abaixo no artigo).
* "Hubs" para Hubs VIRTUAIS WAN geridos pela Microsoft, onde os VNets NVA estão ligados. Os VNets de NVA não precisam de ser ligados aos hubs VIRTUAIS WAN, apenas aos VNets NVA.

A seguinte matriz de conectividade, resume os fluxos suportados neste cenário:

**Matriz de conectividade**

| De             | Para:|   *Porta-vozes da NVA*|*NVA VNets*|*VNets não-NVA*|*Ramos*|
|---|---|---|---|---|---|
| **Porta-vozes da NVA**   | &#8594; | Sobre nVA VNet | Peering | Sobre nVA VNet | Sobre nVA VNet |
| **NVA VNets**    | &#8594; | Peering | Direct | Direct | Direct |
| **VNets não-NVA**| &#8594; | Sobre nVA VNet | Direct | Direct | Direct |
| **Ramos**     | &#8594; | Sobre nVA VNet | Direct | Direct | Direct |

Cada uma das células da matriz de conectividade descreve como um VNet ou ramo (o lado "De" do fluxo, os cabeçalhos de linha na tabela) comunica com um destino VNet ou ramo (o lado "To" do fluxo, os cabeçalhos da coluna em itálico na tabela). "Direto" significa que a conectividade é fornecida de forma nativa por PARTE Virtual, "Peering" significa que a conectividade é fornecida por uma Rota User-Defined no VNet, "Over NVA VNet" significa que a conectividade atravessa a NVA implantada no VNet NVA. Considere o seguinte:

* Os porta-vozes da NVA não são geridos pela Virtual WAN. Como resultado, os mecanismos com os quais comunicarão a outros VNets ou ramos são mantidos pelo utilizador. A conectividade com o VNet NVA é fornecida por um peering VNet, e uma rota padrão para 0.0.0.0/0 apontando para o NVA como o próximo salto deve cobrir conectividade com a Internet, com outros raios, e para sucursais
* Os VNets NVA saberão dos seus próprios porta-vozes da NVA, mas não sobre os porta-vozes da NVA ligados a outros VNets NVA. Por exemplo, na Figura 2 mais abaixo neste artigo, o VNet 2 sabe sobre o VNet 5 e o VNet 6, mas não sobre outros porta-vozes como vNet 7 e VNet 8. É necessária uma rota estática para injetar os prefixos de outros porta-vozes em VNets NVA
* Da mesma forma, os balcões e os VNets não-NVA não saberão de nenhum NVA falado, uma vez que os porta-vozes da NVA não estão ligados aos hubs virtuais do WAN. Como resultado, também aqui serão necessárias rotas estáticas.

Tendo em conta que os porta-vozes da NVA não são geridos pela VIRTUAL WAN, todas as outras linhas apresentam o mesmo padrão de conectividade. Como resultado, uma única tabela de rotas (a predefinida) fará:

* Redes virtuais (VNets não-hub e VNets de utilizadores- hub):
  * Tabela de rotas associada: **Padrão**
  * Propagação para tabelas de rotas: **Predefinido**
* Ramos:
  * Tabela de rotas associada: **Padrão**
  * Propagação para tabelas de rotas: **Predefinido**

No entanto, neste cenário, temos de pensar em que rotas estáticas para configurar. Cada rota estática terá dois componentes, uma parte no hub VIRTUAL WAN, dizendo aos componentes Virtuais WAN que ligação a utilizar para cada um dos raios, e outra nessa ligação específica que aponta para o endereço IP de betão atribuído ao NVA (ou a um balançador de carga em frente a vários NVAs), como a **Figura 1** mostra:

**Figura 1**

:::image type="content" source="media/routing-scenarios/nva/nva-static-concept.png" alt-text="Figura 1":::

Com isso, as rotas estáticas de que precisamos na tabela Predefinido para enviar tráfego para os porta-vozes da NVA por trás do VNet NVA são as seguintes:

| Description | Tabela de rota | Rota estática              |
| ----------- | ----------- | ------------------------- |
| VNet 2       | Predefinição     | 10.2.0.0/16 -> eastusconn |
| VNet 4       | Predefinição     | 10.4.0.0/16 -> weconn     |

Agora a WAN virtual sabe para que ligação enviar os pacotes, mas a ligação precisa de saber o que fazer ao receber esses pacotes: É aqui que são utilizadas as tabelas de rota de ligação. Aqui utilizaremos os prefixos mais curtos (/24 em vez dos mais longos /16), para garantir que estas rotas têm preferência sobre as rotas que são importadas a partir dos VNet 2 e VNet 4):

| Description | Ligação | Rota estática            |
| ----------- | ---------- | ----------------------- |
| VNet 5       | eastusconn | 10.2.1.0/24 -> 10.2.0.5 |
| VNet 6       | eastusconn | 10.2.2.0/24 -> 10.2.0.5 |
| VNet 7       | weconn     | 10.4.1.0/24 -> 10.4.0.5 |
| VNet 8       | weconn     | 10.4.2.0/24 -> 10.4.0.5 |

Agora NVA VNets, VNets não-NVA, e ramos sabem como chegar a todos os porta-vozes da NVA. Para obter mais informações sobre o encaminhamento de hubs virtuais, consulte [sobre o encaminhamento do hub virtual](about-virtual-hub-routing.md).

## <a name="architecture"></a><a name="architecture"></a>Arquitetura

Na **Figura 2**, existem dois hubs; **Hub1** e **Hub2.**

* **Hub1** e **Hub2** estão diretamente ligados aos VNet **2** e **VNet 4** da NVA VNet .

* **VNet 5** e **VNet 6** são espreitados com **VNet 2**.

* **VNet 7** e **VNet 8** são espreitados com **VNet 4**.

* **Os VNets 5,6,7,8** são porta-vozes indiretos, não diretamente ligados a um hub virtual.

**Figura 2**

:::image type="content" source="./media/routing-scenarios/nva/nva.png" alt-text="Figura 2" lightbox="./media/routing-scenarios/nva/nva.png":::

## <a name="scenario-workflow"></a><a name="workflow"></a>Fluxo de trabalho de cenário

Para configurar o encaminhamento via NVA, aqui estão os passos a considerar:

1. Identifique a ligação VNet falada pela NVA. Na **Figura 2**, são **ligações VNet 2 (eastusconn)** e **VNet 4 (weconn)**.

   Certifique-se de que existem UDRs configurados:
   * De VNet 5 e VNet 6 a VNet 2 NVA IP
   * De VNet 7 e VNet 8 a VNet 4 NVA IP 
   
   Não é necessário ligar os VNets 5,6,7,8 diretamente aos centros virtuais. Certifique-se de que os NSGs em VNets 5,6,7,8 permitem o tráfego de sucursais (VPN/ER/P2S) ou VNets ligados aos seus VNets remotos. Por exemplo, os VNets 5.6 devem garantir que os NSGs permitem o tráfego para prefixos de endereço no local e VNets 7,8 que estão ligados ao hub remoto 2.

O WAN virtual não suporta um cenário em que os VNets 5,6 se conectem ao hub virtual e comuniquem através do VNet 2 NVA IP; portanto, a necessidade de ligar vNets 5,6 ao VNet2 e similarmente VNet 7,8 ao VNet 4.

2. Adicione uma entrada de rota estática agregada para VNets 2,5,6 à tabela de rota padrão do Hub 1.

   :::image type="content" source="./media/routing-scenarios/nva/nva-static-expand.png" alt-text="Exemplo":::

3. Configurar uma rota estática para VNets 5,6 na ligação de rede virtual do VNet 2. Para configurar a configuração de encaminhamento para uma ligação de rede virtual, consulte [o encaminhamento do hub virtual](how-to-virtual-hub-routing.md#routing-configuration).

4. Adicione uma entrada de rota estática agregada para VNets 4,7,8 à tabela de rota padrão do Hub 1.

5. Repita os passos 2, 3 e 4 para a tabela de rota padrão do Hub 2.

Isto resultará em alterações na configuração do encaminhamento, como mostra a **Figura 3**, abaixo.

**Figura 3**

   :::image type="content" source="./media/routing-scenarios/nva/nva-result.png" alt-text="Figura 3" lightbox="./media/routing-scenarios/nva/nva-result.png":::

## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre o VIRTUAL WAN, consulte as [FAQ.](virtual-wan-faq.md)
* Para obter mais informações sobre o encaminhamento de hubs virtuais, consulte [sobre o encaminhamento do hub virtual](about-virtual-hub-routing.md).
