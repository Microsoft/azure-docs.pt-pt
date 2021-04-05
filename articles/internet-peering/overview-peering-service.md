---
title: Peering da Internet vs. Peering Service
titleSuffix: Azure
description: Peering da Internet vs. Peering Service
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: overview
ms.date: 5/22/2020
ms.author: derekol
ms.openlocfilehash: 717a8c87c6a1c22d3f75cd4e3054bfdf0c4b5f9a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "95023450"
---
# <a name="internet-peering-vs-peering-service"></a>Peering da Internet vs. Peering Service

O estomo na Internet refere-se a qualquer interligação entre a rede global da Microsoft (AS8075) e a rede Carriers ou Service Providers. Um Fornecedor de Serviços pode tornar-se um parceiro de Serviço de Peering implementando os requisitos de parceria do Serviço Peering explicados abaixo para fornecer conectividade pública fiável e de alto desempenho com o encaminhamento ideal do cliente para a rede Microsoft.

## <a name="about-peering-service"></a>Acerca do Peering Service
O Peering Service é um programa de parceria com os principais fornecedores de serviços para fornecer a melhor conectividade pública da Internet aos seus utilizadores empresariais. Os parceiros que fazem parte do programa terão ligações diretas, altamente disponíveis, geo-redundantes e encaminhamento otimizado para a Microsoft. O Peering Service é uma adição ao portfólio de conectividade da Microsoft:
*   ExpressRoute para conectividade privada aos recursos IaaS ou PaaS (suporte para espaço IP privado)
    *   Conectividade baseada em parceiros
    *   Conectividade direta 100G à Microsoft
*   IPSEC através da Internet para conectividade VPN à nuvem
*   Conectividade SD-WAN para Azure através do VIRTUAL WAN

O segmento alvo para o Serviço de Peering é a conectividade SaaS, clientes SD-WAN dispostos a fazer a fuga de internet na sucursal e quaisquer clientes com uma estratégia dupla MPLS e Internet de nível empresarial.

O objetivo principal ao ligar-se ao Microsoft Cloud deve ser minimizar a latência reduzindo o tempo de ida e volta (RTT) de um site de utilizador para a Microsoft Global Network, que é a espinha dorsal da rede pública da Microsoft que interliga todos os datacenters da Microsoft e vários pontos de entrada de aplicações em nuvem. Ver [obter a melhor conectividade e desempenho no Office 365](https://techcommunity.microsoft.com/t5/Office-365-Blog/Getting-the-best-connectivity-and-performance-in-Office-365/ba-p/124694).

> [!div class="mx-imgBorder"]
> ![Imagem de acesso distribuído](./media/distributed-access.png)

Na figura acima de cada sucursal de uma empresa global conecta-se à localização de borda da Microsoft mais próxima possível através da rede do parceiro.

**Benefícios do cliente do Serviço de Observação:**
* Melhor encaminhamento público através da Internet para o Microsoft Cloud Services para obter um desempenho e fiabilidade ideais.
* Capacidade de selecionar o Fornecedor de Serviços preferido para ligar ao Microsoft Cloud.
* Insights de tráfego, tais como relatórios de latência e monitorização de prefixos.
* Optimum Network Hops (AS Hops) da Microsoft Cloud.
* Análise de rotas e estatísticas - Eventos para protocolo de gateway de fronteira[(BGP)](https://en.wikipedia.org/wiki/Border_Gateway_Protocol)anomalias de rota (deteção de fugas/sequestros) e encaminhamento subóptoal.

## <a name="peering-service-partnership-requirements"></a>Requisitos de parceria do Serviço de Espreitamento
* Conectividade com o Microsoft Cloud num local mais próximo do cliente. Um fornecedor de serviços de parceiros irá encaminhar o tráfego do utilizador para a borda da Microsoft mais próxima do utilizador. Da mesma forma, no tráfego para o utilizador, a Microsoft irá encaminhar o tráfego (utilizando a etiqueta BGP) para a localização de borda mais próxima do utilizador e o Fornecedor de Serviços irá entregar o tráfego ao utilizador.
* O parceiro manterá alta produção disponível, alta produção e conectividade geo-redundante com a Microsoft Global Network.
* O parceiro pode utilizar o seu espreitante existente para apoiar o Serviço de Peering se cumprir o requisito.

## <a name="faq"></a>FAQ
Para perguntas frequentes, consulte [o Serviço de Peering - FAQ](service-faqs.md).

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre os benefícios do cliente com [o Serviço de Espreitamento.](../peering-service/index.yml)
* Saiba mais sobre os passos para permitir um persimento direto para o serviço de peering em [peering service parceiro walkthrough](walkthrough-peering-service-all.md).