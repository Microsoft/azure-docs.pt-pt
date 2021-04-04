---
title: 'Cenário: Isolamento personalizado para VNets'
titleSuffix: Azure Virtual WAN
description: Cenários para o encaminhamento - evite que os VNets selecionados possam chegar uns aos outros
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: ca1ee8418bc08d70a031d81a15dc1b4ace2f1a3a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92461826"
---
# <a name="scenario-custom-isolation-for-vnets"></a>Cenário: Isolamento personalizado para VNets

Ao trabalhar com o encaminhamento virtual do hub virtual WAN, existem alguns cenários disponíveis. Num cenário de isolamento personalizado para os VNets, o objetivo é evitar que um conjunto específico de VNets seja capaz de alcançar outro conjunto específico de VNets. No entanto, os VNets são necessários para chegar a todos os ramos (VPN/ER/User VPN). Para obter mais informações sobre o encaminhamento de hubs virtuais, consulte [sobre o encaminhamento do hub virtual](about-virtual-hub-routing.md).

## <a name="design"></a><a name="design"></a>Design

Para descobrir quantas tabelas de rotas serão necessárias, você pode construir uma matriz de conectividade. Para este cenário será o seguinte, onde cada célula representa se uma fonte (linha) pode comunicar a um destino (coluna):

| De | Para:| *VNets Azuis* | *VNets vermelhos* | *Ramos*|
|---|---|---|---|---|
| **VNets Azuis** |   &#8594;|   Direct     |           |  Direct |
| **VNets vermelhos**  |   &#8594;|              |   Direct  |  Direct |
| **Ramos**   |   &#8594;|   Direct     |   Direct  |  Direct |

Cada uma das células da tabela anterior descreve se uma ligação WAN virtual (o lado "From" do fluxo, os cabeçalhos de linha) comunica com um destino (o lado "To" do fluxo, os cabeçalhos da coluna em itálico). Neste cenário não existem firewalls ou Aparelhos Virtuais de Rede, pelo que as comunicações fluem diretamente sobre o WAN Virtual (daí a palavra "Direto" na tabela).

O número de diferentes padrões de linha será o número de tabelas de rotas que vamos precisar neste cenário. Neste caso, três tabelas de rota que chamaremos **RT_BLUE** e **RT_RED** para as redes virtuais, e **Padrão** para os balcões. Lembre-se, os ramos têm sempre de estar associados à tabela de encaminhamento padrão.

Os ramos terão de aprender os prefixos tanto dos VNets vermelhos como dos azuis, pelo que todos os VNets terão de se propagar ao Padrão (adicionalmente para **RT_BLUE** ou **RT_RED).** Os VNets azuis e vermelhos terão de aprender os prefixos dos ramos, para que os ramos se propaguem para ambas as tabelas de rota **RT_BLUE** e **RT_RED** também. Como resultado, este é o design final:

* Redes virtuais azuis:
  * Tabela de rotas associada: **RT_BLUE**
  * Propagação para tabelas de rotas: **RT_BLUE** e **Padrão**
* Redes virtuais vermelhas:
  * Tabela de rotas associada: **RT_RED**
  * Propagação para tabelas de rotas: **RT_RED** e **Padrão**
* Ramos:
  * Tabela de rotas associada: **Padrão**
  * Propagação para tabelas de rotas: **RT_BLUE,** **RT_RED** e **Padrão**

> [!NOTE]
> Uma vez que todos os ramos precisam de ser associados à tabela de rotas predefinida, bem como para se propagarem ao mesmo conjunto de tabelas de encaminhamento, todos os ramos terão o mesmo perfil de conectividade. Por outras palavras, o conceito Vermelho/Azul para VNets não pode ser aplicado aos ramos.

> [!NOTE]
> Se o seu WAN Virtual estiver implantado em várias regiões, terá de criar as tabelas de rotas **RT_BLUE** e **RT_RED** em todos os hubs, e as rotas de cada ligação VNet precisam de ser propagadas às tabelas de rotas em todos os centros virtuais utilizando etiquetas de propagação.

Para obter mais informações sobre o encaminhamento de hubs virtuais, consulte [sobre o encaminhamento do hub virtual](about-virtual-hub-routing.md).

## <a name="workflow"></a><a name="architecture"></a>Fluxo de trabalho

Na **Figura 1,** existem ligações VNet Azul e Vermelha.

* Os VNets ligados a azul podem alcançar-se entre si, bem como alcançar todas as ligações de ramos (VPN/ER/P2S).
* Os VNets vermelhos podem chegar uns aos outros, assim como alcançar todas as ligações de ramos (VPN/ER/P2S).

Considere os seguintes passos ao configurar o encaminhamento.

1. Crie duas tabelas de rota personalizadas no portal Azure, **RT_BLUE** e **RT_RED**.
2. Para a tabela **de rotas RT_BLUE,** para as seguintes definições:
   * **Associação**: Selecione todos os VNets Azuis.
   * **Propagação**: Para os balcões, selecione a opção para os balcões, implicando ligações de ramo (VPN/ER/P2S) que propagarão rotas para este quadro de rotas.
3. Repita os mesmos passos para **RT_RED** tabela de rotas para VNets e ramos vermelhos (VPN/ER/P2S).

Isto resultará em alterações na configuração do encaminhamento, como visto na figura abaixo

**Figura 1**

:::image type="content" source="./media/routing-scenarios/custom-isolation/custom.png" alt-text="figura 1":::

## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre o VIRTUAL WAN, consulte as [FAQ.](virtual-wan-faq.md)
* Para obter mais informações sobre o encaminhamento de hubs virtuais, consulte [sobre o encaminhamento do hub virtual](about-virtual-hub-routing.md).
