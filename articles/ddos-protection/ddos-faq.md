---
title: Azure DDoS Protection Standard perguntas frequentes
description: Perguntas frequentes sobre o Azure DDoS Protection Standard, que ajuda a fornecer defesa contra ataques DDoS.
services: virtual-network
documentationcenter: na
author: yitoh
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/28/2020
ms.author: yitoh
ms.openlocfilehash: 871ededce1db5e4c3179c187fc46a828cd157456
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2020
ms.locfileid: "94886282"
---
# <a name="azure-ddos-protection-standard-frequent-asked-questions"></a>Azure DDoS Protection Standard perguntas frequentes

Este artigo responde a perguntas comuns sobre a Norma de Proteção DDoS Azure. 

## <a name="what-is-a-distributed-denial-of-service-ddos-attack"></a>O que é um ataque de Negação de Serviço Distribuído (DDoS) ?
A negação de serviço distribuída, ou DDoS, é um tipo de ataque em que um intruso envia mais pedidos para uma aplicação do que a aplicação é capaz de lidar. O efeito resultante é o esgotamento dos recursos, afetando a disponibilidade e a capacidade da aplicação de servir os seus clientes. Nos últimos anos, a indústria tem assistido a um aumento acentuado dos ataques, com os ataques a tornarem-se mais sofisticados e maiores de magnitude. Os ataques DDoS podem ser direcionados para qualquer ponto final que seja acessível publicamente através da Internet.

## <a name="what-is-azure-ddos-protection-standard-service"></a>O que é o serviço Azure DDoS Protection Standard?
A Azure DDoS Protection Standard, combinada com as melhores práticas de design de aplicações, fornece funcionalidades de mitigação de DDoS melhoradas para se defender contra ataques de DDoS. É automaticamente sintonizado para ajudar a proteger os seus recursos Azure específicos numa rede virtual. A proteção é simples de permitir qualquer rede virtual nova ou existente, e não requer nenhuma aplicação ou alterações de recursos. Tem várias vantagens sobre o serviço básico, incluindo registo, alerta e telemetria. Consulte [a visão geral da Norma de Proteção DDoS do Azure](ddos-protection-overview.md) para obter mais detalhes. 

## <a name="what-about-protection-at-the-service-layer-layer-7"></a>E a proteção na camada de serviço (camada 7)?
Os clientes podem utilizar o serviço de proteção Azure DDoS em combinação com [o Gateway de Aplicação WAF SKU](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview) para proteção tanto na camada de rede (Camada 3 e 4, oferecida pelo Serviço de Proteção DDoS Azure) como na camada de aplicação (Camada 7, oferecida pela Aplicação Gateway WAF SKU).

## <a name="are-services-unsafe-in-azure-without-the-service"></a>Os serviços não são seguros em Azure sem o serviço?
Os serviços em funcionamento em Azure estão inerentemente protegidos pela Azure DDoS Protection Basic que está em vigor para proteger a infraestrutura do Azure. No entanto, a proteção que salvaguarda a infraestrutura tem um limiar muito mais elevado do que a maioria das aplicações têm capacidade de manusear, e não fornece telemetria ou alerta, pelo que, embora um volume de tráfego possa ser considerado inofensivo pela plataforma, pode ser devastador para a aplicação que a recebe. 

Ao embarcar no Serviço Standard de Proteção DDoS do Azure, a aplicação recebe monitorização dedicada para detetar ataques e limites específicos de aplicação. Um serviço será protegido com um perfil que está sintonizado com o seu volume de tráfego esperado, proporcionando uma defesa muito mais apertada contra os ataques de DDoS.

## <a name="what-are-the-supported-protected-resource-types"></a>Quais são os tipos de recursos protegidos suportados?
Os IPs públicos em VNETs baseados em ARM são atualmente o único tipo de recurso protegido. Os serviços PaaS (multitenant) não são suportados na apresentação. Consulte [as arquiteturas de referência standard de proteção Azure DDoS](ddos-protection-reference-architectures.md) para obter mais detalhes.

## <a name="are-classicrdfe-protected-resources-supported"></a>Os recursos clássicos/RDFE estão protegidos?
Apenas os recursos protegidos baseados na ARM são suportados na pré-visualização. As VMs nas implementações Classic/RDFE não são suportadas. O apoio não está atualmente previsto para os recursos Clássicos/RDFE. Consulte [as arquiteturas de referência standard de proteção Azure DDoS](ddos-protection-reference-architectures.md) para obter mais detalhes.

## <a name="can-i-protect-my-on-premise-resources-using-ddos-protection"></a>Posso proteger os meus recursos no local usando a Proteção DDoS?
É necessário que os pontos finais públicos do seu serviço estejam associados a um VNet em Azure para poder ser protegido por DDoS. Os desenhos de exemplo incluem:
- Web sites (IaaS) em Azure e bases de dados de backend no centro de dados on-prem. 
- Gateway de aplicações em Azure (proteção DDoS ativada no App Gateway/WAF) e websites em datacenters on-prem.

Consulte [as arquiteturas de referência standard de proteção Azure DDoS](ddos-protection-reference-architectures.md) para obter mais detalhes.

## <a name="can-i-register-a-domain-outside-of-azure-and-associate-that-to-a-protected-resource-like-vm-or-elb"></a>Posso registar um domínio fora de Azure e associar isso a um recurso protegido como VM ou ELB?
Para os cenários IP públicos, o serviço de Proteção DDoS irá suportar qualquer aplicação, independentemente do local onde o domínio associado esteja registado ou hospedado desde que o IP público associado esteja hospedado no Azure. 

## <a name="can-i-manually-configure-the-ddos-policy-applied-to-the-vnetspublic-ips"></a>Posso configurar manualmente a política DDoS aplicada aos VNets/IPs públicos?
Não, infelizmente, a personalização das políticas não está disponível neste momento.

## <a name="can-i-allowlistblocklist-specific-ip-addresses"></a>Posso permitir endereços IP específicos de lista/blocklist?
Não, infelizmente a configuração manual não está disponível neste momento.

## <a name="how-can-i-test-ddos-protection"></a>Como posso testar a Proteção DDoS?
Ver [testes através de simulações](test-through-simulations.md).

## <a name="how-long-does-it-take-for-the-metrics-to-load-on-portal"></a>Quanto tempo demora as métricas a carregar no portal?
As métricas devem ser visíveis no portal dentro de 5 minutos. Se o seu recurso estiver sob ataque, outras métricas começarão a aparecer no portal dentro de 5-7 minutos. 
    



