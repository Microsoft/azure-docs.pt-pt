---
title: Porta frontal Azure - Balanceamento de carga com a suíte de entrega de candidaturas do Azure Microsoft Docs
description: Este artigo ajuda-o a aprender como o Azure recomenda o equilíbrio de carga com a sua suíte de entrega de aplicações
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 9f8d1959549eaddfb4a2c9ea271094db0073c788
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471715"
---
# <a name="load-balancing-with-azures-application-delivery-suite"></a>Balanceamento de carga com o conjunto de entrega de aplicações do Azure

## <a name="introduction"></a>Introdução
O Microsoft Azure fornece vários serviços globais e regionais para gerir a forma como o tráfego de rede é distribuído e equilibrado: Traffic Manager, Front Door, Application Gateway e Load Balancer.  Juntamente com as muitas regiões e arquitetura zonal da Azure, a utilização destes serviços em conjunto permite-lhe construir aplicações robustas e escaláveis de alto desempenho.

![Suíte de entrega de aplicações ][1]
 
Estes serviços são divididos em duas categorias:
1. **Serviços globais de equilíbrio de carga,** como Traffic Manager e Front Door distribuem tráfego dos seus utilizadores finais através dos seus backends regionais, através de nuvens ou até mesmo dos seus serviços híbridos no local. O equilíbrio global de carga sabota o seu tráfego para o seu backend de serviço mais próximo e reage a alterações na fiabilidade do serviço ou desempenho para manter o desempenho sempre ligado e máximo para os seus utilizadores. 
2. **Serviços regionais de equilíbrio de carga,** como standard load balancer ou Application Gateway fornecem a capacidade de distribuir tráfego dentro de redes virtuais (VNETs) através das suas máquinas virtuais (VMs) ou pontos finais de serviço zonais dentro de uma região.

Combinar serviços globais e regionais na sua aplicação fornece uma forma fiável, performativa e segura de encaminhar o tráfego de e para os seus utilizadores para os seus serviços IaaS, PaaS ou no local. Na secção seguinte, descrevemos cada um destes serviços.

## <a name="global-load-balancing"></a>Equilíbrio global de carga
**O Traffic Manager** fornece um equilíbrio global de carga dNS. Analisa os pedidos de DNS e responde com um backend saudável, de acordo com a política de encaminhamento que o cliente selecionou. As opções para métodos de encaminhamento são:
- Encaminhamento de desempenho para enviar o solicitador para o backend mais próximo em termos de latência.
- Encaminhamento prioritário para direcionar todo o tráfego para um backend, com outros backends como backup.
- Encaminhamento de robin redondo ponderado, que distribui tráfego com base na ponderação que é atribuída a cada backend.
- Encaminhamento geográfico para garantir que os solicitores localizados em regiões geográficas específicas sejam direcionados para as extremidades traseiras mapeadas para essas regiões (por exemplo, todos os pedidos de Espanha devem ser dirigidos à região do Azure Central de França)
- O encaminhamento de subnet que lhe permite mapear intervalos de endereçoip para backends para que os pedidos provenientes desses serão enviados para o backend especificado (por exemplo, todos os utilizadores que se ligam a partir da gama de endereços IP do seu QG corporativo devem obter conteúdo web diferente do geral utilizadores)

O cliente liga-se diretamente ao backend. O Azure Traffic Manager deteta quando um backend não é saudável e depois redireciona os clientes para outra instância saudável. Consulte a documentação do Gestor de [Tráfego do Azure](../traffic-manager/traffic-manager-overview.md) para saber mais sobre o serviço.

**A Porta Frontal Azure** fornece uma aceleração dinâmica do website (DSA), incluindo o equilíbrio global de carga HTTP.  Analisa as rotas de solicitação do HTTP para o backend/região de serviço mais próximo para o nome de anfitrião especificado, caminho URL e regras configuradas.  
A Porta Frontal encerra os pedidos http na borda da rede da Microsoft e sonda ativamente para detetar alterações de saúde ou latência de aplicações ou infraestruturas.  Porta da Frente então sempre encaminha o tráfego para o backend mais rápido e disponível (saudável). Consulte os detalhes da arquitetura de [encaminhamento](front-door-routing-architecture.md) da Porta Frontal e os métodos de [encaminhamento](front-door-routing-methods.md) de tráfego para saber mais sobre o serviço.

## <a name="regional-load-balancing"></a>Equilíbrio regional de carga
O Application Gateway fornece o controlador de entrega de aplicações (ADC) como um serviço, oferecendo várias capacidades de equilíbrio de carga da Camada 7 para a sua aplicação. Permite que os clientes otimizem a produtividade da quinta web descarregando a rescisão intensiva de SSL por CPU para o gateway da aplicação. Outras capacidades de encaminhamento da Camada 7 incluem distribuição de rodada-robin do tráfego de entrada, afinidade de sessão baseada em cookies, encaminhamento baseado em caminhos de URL, e a capacidade de hospedar vários websites atrás de um único gateway de aplicação. O Gateway de aplicações pode ser configurado como um gateway virado para a Internet, um gateway apenas interno, ou uma combinação de ambos. Application Gateway é totalmente gerido, escalável e altamente disponível. Proporciona um conjunto avançado de capacidades de registo e diagnóstico, para uma melhor capacidade de gestão.
Load Balancer é parte integrante da pilha Azure SDN, fornecendo serviços de equilíbrio de carga de baixa latência e de baixa latência para todos os protocolos UDP e TCP. Gere as ligações de entrada e saída. Pode configurar pontos finais públicos e internos com balanceamento de carga e definir regras para mapear as ligações de entrada para destinos do conjunto de back-end, com opções de pesquisa de estado de funcionamento TCP e HTTP para gerir a disponibilidade do serviço.


## <a name="choosing-a-global-load-balancer"></a>Escolher um equilibrador de carga global
Ao escolher um equilibrador de carga global entre o Traffic Manager e o Azure Front Door para o encaminhamento global, deve considerar o que é semelhante e o que é diferente nos dois serviços.   Ambos os serviços fornecem
- **Redundância multi-geo:** Se uma região descer, o tráfego percorre perfeitamente a região mais próxima sem qualquer intervenção do proprietário da aplicação.
- **Encaminhamento da região mais próxima:** O tráfego é automaticamente encaminhado para a região mais próxima

</br>A tabela seguinte descreve as diferenças entre o Gestor de Tráfego e a Porta Frontal Azure:</br>

| Gestor de Tráfego | Azure Front Door |
| --------------- | ------------------------ |
|**Qualquer protocolo:** Como o Gestor de Tráfego trabalha na camada DNS, pode encaminhar qualquer tipo de tráfego de rede; HTTP, TCP, UDP, etc. | **Aceleração http:** Com o tráfego front door está à beira da rede da Microsoft.  Por isso, os pedidos http(S) vêem a latência e as melhorias de resultados reduzindo a latência para a negociação do SSL e usando ligações quentes da AFD para a sua aplicação.|
|**Encaminhamento no local:** Com o encaminhamento numa camada DNS, o tráfego vai sempre de ponto a ponto.  O encaminhamento da sua filial para o seu centro de dados no local pode seguir um caminho direto; mesmo na sua própria rede usando traffic manager. | **Escalabilidade independente:** Como a Porta Da Frente trabalha com o pedido http, os pedidos para diferentes caminhos de URL podem ser encaminhados para diferentes piscinas de serviços backend/regional (microserviços) com base nas regras e na saúde de cada microserviço de aplicações.|
|**Formato de faturação:** Escalas de faturação baseadas em DNS com os seus utilizadores e para serviços com mais utilizadores, planaltos para reduzir custos a uma utilização mais elevada. |**Segurança inline:** A Porta da Frente permite que regras como a limitação da taxa e o IP ACL-ing lhe permitam proteger as suas costas antes que o tráfego chegue à sua aplicação. 

</br>Devido ao desempenho, à operabilidade e aos benefícios de segurança para as cargas de trabalho http com porta da frente, recomendamos que os clientes utilizem a Porta Da Frente para as suas cargas de trabalho HTTP.    O Gestor de Tráfego e a Porta da Frente podem ser utilizados paralelamente para servir todo o tráfego para a sua aplicação. 

## <a name="building-with-azures-application-delivery-suite"></a>Edifício com suíte de entrega de candidaturas do Azure 
Recomendamos que todos os websites, APIs, os serviços sejam geograficamente redundantes e entreguem tráfego aos seus utilizadores a partir da localização mais próxima (mais baixa) para eles sempre que possível.  Combinar serviços do Traffic Manager, Front Door, Application Gateway e Load Balancer permite-lhe construir geograficamente e zonalmente redundante para maximizar a fiabilidade, escala e desempenho.

No diagrama seguinte, descrevemos um serviço de exemplo que usa uma combinação de todos estes serviços para construir um serviço web global.   Neste caso, o arquiteto usou o Traffic Manager para encaminhar para backends globais para entrega de ficheiros e objetos, enquanto utilizava a Porta da Frente para rotas globais de URL que correspondem ao padrão /loja/* ao serviço que migraram para o App Service enquanto encaminham todos os outros pedidos para gateways de aplicação regionais.

Na região, para o seu serviço IaaS, o desenvolvedor de aplicações decidiu que quaisquer URLs que correspondam ao padrão/imagens/* são servidos a partir de um conjunto dedicado de VMs que são diferentes do resto da quinta web.

Além disso, o pool de VM padrão que serve o conteúdo dinâmico precisa de falar com uma base de dados de back-end que está alojada num cluster de alta disponibilidade. Toda a implantação é criada através do Gestor de Recursos Azure.

O diagrama seguinte mostra a arquitetura deste cenário:

![Suíte de entrega de aplicações Arquitetura Detalhada][2] 

> [!NOTE]
> Este exemplo é apenas uma das muitas configurações possíveis dos serviços de equilíbrio de carga que o Azure oferece. O Gestor de Tráfego, a Porta da Frente, o Gateway da Aplicação e o Balancer de Carga podem ser misturados e compatíveis com as suas necessidades de equilíbrio de carga. Por exemplo, se não for necessário descarregar sSL ou processar a Camada 7, o Balancer de carga pode ser utilizado no lugar do Gateway da Aplicação.


## <a name="next-steps"></a>Passos Seguintes

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como funciona o Front Door](front-door-routing-architecture.md).

<!--Image references-->
[1]: ./media/front-door-lb-with-azure-app-delivery-suite/application-delivery-figure1.png
[2]: ./media/front-door-lb-with-azure-app-delivery-suite/application-delivery-figure2.png
