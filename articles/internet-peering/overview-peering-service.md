---
title: Emparelhamento de Internet versus serviço de emparelhamento
titleSuffix: Azure
description: Emparelhamento de Internet versus serviço de emparelhamento
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: overview
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 6c1205fcacd3d7228f1aecf1e603b66d9e1fcee5
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775706"
---
# <a name="internet-peering-vs-peering-service"></a>Emparelhamento de Internet versus serviço de emparelhamento

O emparelhamento da Internet refere-se a qualquer interconexão entre a rede global da Microsoft (AS8075) e as operadoras ou de provedores de serviço. Um provedor de serviços pode se tornar um parceiro de serviço de emparelhamento implementando os requisitos de parceria de serviço de emparelhamento explicados abaixo para fornecer conectividade pública confiável e de alto desempenho com roteamento ideal do cliente para a rede da Microsoft.

## <a name="about-peering-service"></a>Acerca do Peering Service
O serviço de emparelhamento é um programa de parceria com os principais provedores de serviços para fornecer a melhor conectividade de Internet pública da categoria aos usuários empresariais. Os parceiros que fazem parte do programa terão conexões diretas, altamente disponíveis e com redundância geográfica e roteamento otimizado para a Microsoft. O serviço de emparelhamento é uma adição ao portfólio de conectividade da Microsoft:
*   ExpressRoute para conectividade privada com recursos de IaaS ou PaaS (suporte para o espaço IP privado)
    *   Conectividade baseada em parceiro
    *   Conectividade Direct 100G à Microsoft
*   IPSEC pela Internet para conectividade de VPN para a nuvem
*   Conectividade SD-WAN com o Azure por meio da WAN virtual

O segmento de destino para o serviço de emparelhamento é a conectividade SaaS, clientes SD-WAN que desejam fazer a interrupções na Internet na ramificação e qualquer cliente com MPLS de estratégia dupla e Internet de nível empresarial.

A meta principal ao se conectar a Microsoft Cloud deve ser minimizar a latência, reduzindo o tempo de ida e volta (RTT) de um site de usuário para a rede global da Microsoft, que é o backbone de rede pública da Microsoft que interconecta todos os data centers da Microsoft e vários pontos de entrada de aplicativo em nuvem. Consulte [obtendo a melhor conectividade e desempenho no Office 365](https://techcommunity.microsoft.com/t5/Office-365-Blog/Getting-the-best-connectivity-and-performance-in-Office-365/ba-p/124694).

> [!div class="mx-imgBorder"]
> ![imagem de](./media/distributed-access.png) de acesso distribuído

Na figura acima, cada filial de uma empresa global conecta-se ao local do Microsoft Edge mais próximo possível por meio da rede do parceiro.

**Benefícios do cliente do serviço de emparelhamento:**
* Melhor roteamento público pela Internet para Microsoft Cloud serviços para obter desempenho e confiabilidade ideais.
* Capacidade de selecionar o SP preferencial para se conectar ao Microsoft Cloud.
* Informações de tráfego, como relatórios de latência e monitoramento de prefixo.
* Saltos de rede ideais (como saltos) de Microsoft Cloud.
* Análise de rota e estatísticas – eventos para anomalias de rota de Border Gateway Protocol ([BGP](https://en.wikipedia.org/wiki/Border_Gateway_Protocol)) (detecção de vazamento/seqüestro) e roteamento abaixo do ideal.

## <a name="peering-service-partnership-requirements"></a>Requisitos de parceria de serviço de emparelhamento
* Conectividade com Microsoft Cloud em um local mais próximo do cliente. Um provedor de serviços de parceiro encaminhará o tráfego do usuário para o Microsoft Edge mais próximo ao usuário. Da mesma forma, no tráfego para o usuário, a Microsoft encaminhará o tráfego (usando a marca BGP) para o local de borda mais próximo do usuário e o SP fornecerá o tráfego para o usuário.
* O parceiro manterá alta disponibilidade, alta taxa de transferência e conectividade com redundância geográfica com a rede global da Microsoft.
* O parceiro pode utilizar seu emparelhamento existente para dar suporte ao serviço de emparelhamento se ele atender ao requisito

## <a name="faq"></a>FAQ
Para perguntas frequentes, consulte [serviço de emparelhamento-perguntas frequentes](service-faqs.md).

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre os benefícios do cliente com o [serviço de emparelhamento](https://docs.microsoft.com/azure/peering-service/).
* Saiba mais sobre as etapas para habilitar um emparelhamento direto para o serviço de emparelhamento em [instruções do parceiro de serviço de emparelhamento](walkthrough-peering-service-all.md).
