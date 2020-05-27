---
title: Internet peering vs. Peering Service
titleSuffix: Azure
description: Internet peering vs. Peering Service
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: overview
ms.date: 5/22/2020
ms.author: derekol
ms.openlocfilehash: 3ffef24d459a2864ffd3f2271e6bca9e4bc638cc
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/26/2020
ms.locfileid: "83849387"
---
# <a name="internet-peering-vs-peering-service"></a>Internet peering vs. Peering Service

O peering da Internet refere-se a qualquer interligação entre a rede global da Microsoft (AS8075) e a rede Carriers ou Service Providers. Um Prestador de Serviços pode tornar-se um parceiro de Serviço de Peering implementando os requisitos de parceria do Peering Service explicados abaixo para fornecer conectividade pública fiável e de alto desempenho com o melhor encaminhamento do cliente para a rede Microsoft.

## <a name="about-peering-service"></a>Acerca do Peering Service
Peering Service é um programa de parceria com os principais prestadores de serviços para fornecer a melhor conectividade pública da Internet aos seus utilizadores empresariais. Os parceiros que fazem parte do programa terão ligações diretas, altamente disponíveis, georedundantes e encaminhamento otimizado para a Microsoft. O Peering Service é uma adição ao portfólio de conectividade da Microsoft:
*   Rota Expressa para conectividade privada com os recursos IaaS ou PaaS (suporte para espaço IP privado)
    *   Conectividade baseada em parceiros
    *   Conectividade direta de 100G à Microsoft
*   IPSEC através da Internet para conectividade VPN à nuvem
*   Conectividade SD-WAN a Azure através de Wan Virtual

O segmento-alvo do Serviço de Peering é a conectividade SaaS, os clientes SD-WAN dispostos a fazer a fuga de internet na sucursal e quaisquer clientes com uma estratégia dupla MPLS e Internet de nível empresarial.

O principal objetivo na ligação ao Microsoft Cloud deve ser minimizar a latência reduzindo o tempo de ida e volta (RTT) de um site de utilizador para a Microsoft Global Network, que é a espinha dorsal da rede pública da Microsoft que interliga todos os centros de dados da Microsoft e vários pontos de entrada de aplicações na nuvem. Ver [Obter a melhor conectividade e desempenho no Office 365](https://techcommunity.microsoft.com/t5/Office-365-Blog/Getting-the-best-connectivity-and-performance-in-Office-365/ba-p/124694).

> [!div class="mx-imgBorder"]
> ![Imagem de acesso distribuído](./media/distributed-access.png)

Na figura acima de cada filial de uma empresa global conecta-se à localização mais próxima possível da Microsoft através da rede do parceiro.

**Peering Service benefícios ao cliente:**
* Melhor encaminhamento público através da Internet para Microsoft Cloud Services para um desempenho e fiabilidade ótimos.
* Capacidade de selecionar o Fornecedor de Serviços preferido para ligar ao Microsoft Cloud.
* Informações de tráfego, tais como relatórios de latência e monitorização prefixo.
* Optimum Network Hops (AS Hops) do Microsoft Cloud.
* Aanálise de rotas e estatísticas - Eventos para Border Gateway Protocol[(BGP](https://en.wikipedia.org/wiki/Border_Gateway_Protocol)) rotas anomalias (deteção de fugas/sequestros) e encaminhamento sub-ideal.

## <a name="peering-service-partnership-requirements"></a>Requisitos de parceria de serviço de peering
* Conectividade com o Microsoft Cloud num local mais próximo do cliente. Um fornecedor de serviços parceiro irá direcionar o tráfego do utilizador para a borda da Microsoft mais próxima do utilizador. Da mesma forma, no tráfego para o utilizador, a Microsoft irá encaminhar o tráfego (utilizando a etiqueta BGP) para o local de borda mais próximo do utilizador e o Prestador de Serviços irá fornecer o tráfego ao utilizador.
* O parceiro manterá uma elevada conectividade disponível, alta e georedundante com a Microsoft Global Network.
* O parceiro pode utilizar o seu peering existente para apoiar o Serviço de Peering se cumprir o requisito.

## <a name="faq"></a>FAQ
Para perguntas frequentes, consulte [o Serviço de Peering - FAQ](service-faqs.md).

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre os benefícios do cliente com o [Serviço de Peering.](https://docs.microsoft.com/azure/peering-service/)
* Aprenda sobre passos para permitir um atendimento direto para o serviço de peering no [peering service partner walkthrough](walkthrough-peering-service-all.md).
