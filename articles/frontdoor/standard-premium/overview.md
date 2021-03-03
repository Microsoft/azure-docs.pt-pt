---
title: Azure Front Door Standard/Premium| Microsoft Docs
description: Este artigo fornece uma visão geral do Azure Front Door Standard/Premium.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: overview
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: bcfff1a2c8490a05f4b96a8e2ff68186348f596f
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101742369"
---
# <a name="what-is-azure-front-door-standardpremium-preview"></a>O que é Azure Front Door Standard/Premium (Preview)?

> [!IMPORTANT]
> Esta documentação destina-se ao Azure Front Door Standard/Premium (Preview). À procura de informações sobre a Porta da Frente Azure? Ver [Azure Front Door Docs](../front-door-overview.md).

O Azure Front Door Standard/Premium é um CDN de nuvem moderna rápido, fiável e seguro que utiliza a rede global de arestas da Microsoft e se integra com uma proteção inteligente de ameaças. Combina as capacidades da Porta Frontal Azure, padrão da Rede de Entrega de Conteúdos Azure (CDN) e firewall de aplicação web Azure (WAF) numa única plataforma segura de CDN em nuvem.

Com o Azure Front Door Standard/Premium, pode transformar as suas aplicações globais de consumidores e empresas em aplicações modernas personalizadas seguras e de alto desempenho com conteúdos que atingem um público global na borda da rede perto do utilizador. Também permite que a sua aplicação se escalone sem aquecimento, beneficiando do equilíbrio global da carga HTTP com o failover instantâneo.

   :::image type="content" source="../media/overview/front-door-overview.png" alt-text="Arquitetura Standard/Premium da Porta da Frente azul" lightbox="../media/overview/front-door-overview-expanded.png":::

O Azure Front Door Standard/Premium funciona na camada 7 (camada HTTP/HTTPS) utilizando qualquer protocolo decast com TCP dividido e a rede global da Microsoft para melhorar a conectividade global. Com base no seu método de encaminhamento personalizado usando regras definidas, pode garantir que a Porta Frontal Azure irá encaminhar os seus pedidos de cliente para a origem mais rápida e disponível. Uma origem de aplicação é qualquer serviço virado para a Internet hospedado dentro ou fora de Azure. O Azure Front Door Standard/Premium fornece uma gama de métodos de encaminhamento de tráfego e opções de monitorização da saúde de origem para atender às diferentes necessidades de aplicação e cenários automáticos de failover. À semelhança do Traffic Manager, a Porta frontal é resiliente a falhas, incluindo falhas em toda uma região do Azure.

A Azure Front Door também protege a sua aplicação nas bordas com proteção integrada de Firewall de Aplicação Web, Proteção de Bot, e proteção de lei 3/camada 4 distribuída de negação de serviço (DDoS). Também protege as suas back-ends privadas com o serviço de ligação privada. A Azure Front Door dá-lhe a melhor segurança em prática da Microsoft à escala global.  

>[!NOTE]
> O Azure oferece um conjunto de soluções de balanceamento de carga totalmente geridas para os seus cenários.
>
> * Se estiver a procurar fazer o encaminhamento global baseado em DNS e **não** tiver requisitos para a rescisão do protocolo de Segurança da Camada de Transporte (TLS) ("descarga SSL"), por pedido HTTP/HTTPS ou processamento de camadas de aplicação, reveja [o Gestor de Tráfego.](../../traffic-manager/traffic-manager-overview.md)
> * Se pretender carregar o equilíbrio entre os seus servidores numa região na camada de aplicação, reveja o [Gateway de Aplicações](../../application-gateway/overview.md)
> * Para equilibrar a carga da camada de rede, reveja [o Balanceador de Carga](../../load-balancer/load-balancer-overview.md).
>
> Os seus cenários de ponta a ponta podem beneficiar de combinar estas soluções conforme necessário.
> Para uma comparação de opções de equilíbrio de carga Azure, consulte [a visão geral das opções de equilíbrio de carga em Azure](/azure/architecture/guide/technology-choices/load-balancing-overview).

> [!IMPORTANT]
> Azure Front Door Standard/Premium está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="why-use-azure-front-door-standardpremium-preview"></a>Porquê utilizar o Azure Front Door Standard/Premium (Preview)?

O Azure Front Door Standard/Premium fornece uma única plataforma unificada que atende à aceleração dinâmica e estática com integração de segurança chave na mão, e um modelo de preços simples e previsíveis. A Porta frontal também permite definir, gerir e monitorizar o encaminhamento global para a sua aplicação.

Principais funcionalidades incluídas com Azure Front Door Standard/Premium (Pré-visualização):

- Desempenho acelerado da aplicação utilizando o protocolo de qualquercast **[baseado em TCP dividido.](../front-door-routing-architecture#splittcp.md)**

- Monitorização inteligente da sonda de **[saúde](concept-health-probes.md)** e equilíbrio de carga entre **[origens](concept-origin.md)**.

- Defina o seu próprio **[domínio personalizado](how-to-add-custom-domain.md)** com validação flexível do domínio.

- Segurança da aplicação com firewall integrada **[de aplicações web (WAF)](../../web-application-firewall/afds/afds-overview.md)**.

- SSL descarregamento e gestão integrada de **[certificados.](how-to-configure-https-custom-domain.md)**

- Proteja as suas origens com **[o Private Link](concept-private-link.md)**.  

- Encaminhamento e otimização de tráfego personalizáveis através do **[Conjunto de Regras](concept-rule-set.md)**.

- **[Relatórios incorporados](how-to-reports.md)** com painel all-in-one para porta da frente e padrões de segurança.

- **[Monitorização em tempo real](how-to-monitor-metrics.md)** e alertas que se integram com a Monitorização Azure.

- **[Registação](how-to-logs.md)** para cada pedido da Porta frontal e sondas de saúde falhadas.

- Suporte nativo da conectividade IPv6 e protocolo HTTP/2.

## <a name="pricing"></a>Preços

Azure Front Door Standard/Premium tem dois SKUs, Standard e Premium. Ver [Comparação de nível](tier-comparison.md). Para obter informações sobre preços, veja [Preços do Front Door](https://azure.microsoft.com/pricing/details/frontdoor/). 

## <a name="whats-new"></a>Novidades?

Subscreva o feed RSS e veja as últimas atualizações da funcionalidade Azure Front Door na página [Azure Updates.](https://azure.microsoft.com/updates/?category=networking&query=Azure%20Front%20Door)

## <a name="next-steps"></a>Passos seguintes

* Saiba como [criar um Front Door](create-front-door-portal.md).
