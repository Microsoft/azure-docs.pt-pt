---
title: Azure Front Door - Load Balanceing com a suite de entrega de aplicações do Azure / Microsoft Docs
description: Este artigo ajuda-o a aprender como a Azure recomenda o equilíbrio de carga com a sua suíte de entrega de aplicações
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
ms.openlocfilehash: 44af14a01e7b045b7abb6a84db89a67f3dd22445
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80875287"
---
# <a name="load-balancing-with-azures-application-delivery-suite"></a>Balanceamento de carga com o conjunto de entrega de aplicações do Azure

## <a name="introduction"></a>Introdução
A Microsoft Azure fornece múltiplos serviços globais e regionais para gerir a forma como o tráfego da sua rede é distribuído e a carga equilibrada: Gestor de Tráfego, Porta frontal, Gateway de aplicações e Balanceador de Carga.  Juntamente com as muitas regiões e arquitetura zonal da Azure, a utilização destes serviços em conjunto permite-lhe construir aplicações robustas e escaláveis de alto desempenho.

![Suíte de Entrega de Aplicações ][1]
 
Estes serviços são divididos em duas categorias:
1. **Serviços globais de equilíbrio de carga,** como o Traffic Manager e a Front Door, distribuem tráfego dos seus utilizadores finais através das suas costas regionais, através de nuvens ou até mesmo dos seus serviços híbridos no local. O equilíbrio global de cargas encaminha o seu tráfego para o backend de serviço mais próximo e reage a alterações na fiabilidade ou desempenho do serviço para manter um desempenho máximo e sempre ligado para os seus utilizadores. 
2. **Os serviços regionais de equilíbrio de carga,** tais como o Standard Load Balancer ou o Application Gateway, proporcionam a capacidade de distribuir tráfego dentro de redes virtuais (VNETs) através das suas máquinas virtuais (VMs) ou pontos finais de serviço zonais dentro de uma região.

A combinação de serviços globais e regionais na sua aplicação proporciona uma forma fiável, performante e segura de encaminhar o tráfego de e para os seus utilizadores para os seus serviços IaaS, PaaS ou no local. Na secção seguinte, descrevemos cada um destes serviços.

## <a name="global-load-balancing"></a>Equilíbrio global da carga
**O Traffic Manager** proporciona um equilíbrio global de carga de DNS. Olha para os pedidos de DNS que chegam e responde com um backend saudável, de acordo com a política de encaminhamento que o cliente selecionou. As opções para os métodos de encaminhamento são:
- Encaminhamento de desempenho para enviar o solicitador para o backend mais próximo em termos de latência.
- Encaminhamento prioritário para direcionar todo o tráfego para um backend, com outros backends como backup.
- Encaminhamento de robin redondo ponderado, que distribui o tráfego com base na ponderação atribuída a cada backend.
- O encaminhamento geográfico para garantir que os solicitadores localizados em regiões geográficas específicas sejam direcionados para os backends mapeados para essas regiões (por exemplo, todos os pedidos da Espanha devem ser dirigidos à região de France Central Azure)
- O encaminhamento de sub-redes que lhe permite mapear intervalos de endereços IP para backends de modo que os pedidos provenientes desses serão enviados para o backend especificado (por exemplo, todos os utilizadores que se conectem a partir da gama de endereços IP do seu QG corporativo devem obter diferentes conteúdos web do que os utilizadores gerais)

O cliente liga-se diretamente a esse backend. O Azure Traffic Manager deteta quando um backend não é saudável e, em seguida, redireciona os clientes para outro caso saudável. Consulte a documentação [do Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) para saber mais sobre o serviço.

**A Azure Front Door** fornece aceleração dinâmica do site (DSA) incluindo o equilíbrio global de carga HTTP.  Analisa as rotas de solicitação HTTP para o backend/região de serviço mais próximo para o nome de hospedeiro especificado, caminho URL e regras configuradas.  
Porta frontal termina pedidos HTTP na borda da rede da Microsoft e sondas ativamente para detetar alterações de saúde ou de saúde da infraestrutura ou de latência.  A Porta da Frente, em seguida, sempre encaminha o tráfego para o backend mais rápido e disponível (saudável). Consulte os detalhes da arquitetura de [encaminhamento](front-door-routing-architecture.md) da Porta Frontal e [os métodos de encaminhamento de tráfego](front-door-routing-methods.md) para saber mais sobre o serviço.

## <a name="regional-load-balancing"></a>Equilíbrio regional da carga
O Application Gateway fornece o controlador de entrega de aplicações (ADC) como um serviço, oferecendo várias capacidades de equilíbrio de carga da Camada 7 para a sua aplicação. Permite que os clientes otimizem a produtividade da web farm, descarregando a rescisão intensiva de TLS da CPU para o gateway de aplicações. Outras capacidades de encaminhamento da Camada 7 incluem distribuição de rodapé de tráfego de entrada, afinidade da sessão baseada em cookies, encaminhamento baseado em caminhos de URL e a capacidade de hospedar vários websites por trás de um único gateway de aplicação. O Gateway de Aplicações pode ser configurado como um portal virado para a Internet, um portal apenas interno, ou uma combinação de ambos. Application Gateway é totalmente gerido a Azure, escalável e altamente disponível. Proporciona um conjunto avançado de capacidades de registo e diagnóstico, para uma melhor capacidade de gestão.
O Balancer de Carga é parte integrante da stack Azure SDN, fornecendo serviços de equilíbrio de carga de alta performance e baixa latência Camada 4 para todos os protocolos UDP e TCP. Gere ligações de entrada e saída. Pode configurar pontos finais públicos e internos com balanceamento de carga e definir regras para mapear as ligações de entrada para destinos do conjunto de back-end, com opções de pesquisa de estado de funcionamento TCP e HTTP para gerir a disponibilidade do serviço.


## <a name="choosing-a-global-load-balancer"></a>Escolher um equilibrador de carga global
Ao escolher um equilibrador de carga global entre o Traffic Manager e o Azure Front Door para o encaminhamento global, deve considerar o que é semelhante e o que é diferente sobre os dois serviços.   Ambos os serviços fornecem
- **Redundância multi-geo:** Se uma região se desabrandar, o tráfego faz rotas perfeitamente para a região mais próxima sem qualquer intervenção do proprietário da aplicação.
- **Encaminhamento da região mais próximo:** O tráfego é automaticamente encaminhado para a região mais próxima

</br>A tabela a seguir descreve as diferenças entre o Gestor de Tráfego e a Porta frontal Azure:</br>

| Gestor de Tráfego | Azure Front Door |
| --------------- | ------------------------ |
|**Qualquer protocolo:** Como o Gestor de Tráfego trabalha na camada de DNS, pode encaminhar qualquer tipo de tráfego de rede; HTTP, TCP, UDP, etc. | **Aceleração HTTP:** Com o tráfego da Porta Frontal está proxiitado na borda da rede da Microsoft.  Por isso, os pedidos http(S) vêem a latência e a melhoria da produção reduzindo a latência para a negociação do TLS e utilizando ligações quentes da AFD para a sua aplicação.|
|**Encaminhamento no local:** Com o encaminhamento numa camada de DNS, o tráfego vai sempre de ponto a ponta.  O encaminhamento da sua sucursal para o seu datacenter no local pode seguir um caminho direto; mesmo na sua própria rede usando o Traffic Manager. | **Escalabilidade independente:** Como a Porta Frontal funciona com o pedido HTTP, os pedidos para diferentes caminhos DE URL podem ser encaminhados para diferentes piscinas de backend/serviço regional (microserviços) com base nas regras e na saúde de cada microserviço de aplicação.|
|**Formato de faturação:** Escalas de faturação baseadas em DNS com os seus utilizadores e para serviços com mais utilizadores, planaltos para reduzir custos a uma utilização mais elevada. |**Segurança em linha:** A Porta frontal permite que regras como a limitação de tarifas e o IP ACL-ing lhe permitam proteger os seus backends antes que o tráfego chegue à sua aplicação. 

</br>Devido ao desempenho, à operabilidade e benefícios de segurança às cargas de trabalho HTTP com porta da frente, recomendamos que os clientes utilizem a Porta frontal para as suas cargas de trabalho HTTP.    O Gestor de Trânsito e a Porta frontal podem ser utilizados em paralelo para servir todo o tráfego para a sua aplicação. 

## <a name="building-with-azures-application-delivery-suite"></a>Edifício com a suíte de entrega de aplicações da Azure 
Recomendamos que todos os websites, APIs, serviços sejam geograficamente redundantes e entreguem tráfego aos seus utilizadores desde o local mais próximo (latência mais baixa) para eles sempre que possível.  A combinação de serviços de Gestor de Tráfego, Porta Frontal, Gateway de Aplicação e Balanceador de Carga permite-lhe construir geograficamente e zonalmente redundante para maximizar a fiabilidade, escala e desempenho.

No diagrama seguinte, descrevemos um serviço de exemplo que utiliza uma combinação de todos estes serviços para construir um serviço web global.   Neste caso, o arquiteto usou o Traffic Manager para encaminhar para backends globais para entrega de ficheiros e objetos, enquanto utilizava a Porta Frontal para rotas globais de caminhos URL que correspondem ao padrão /loja/* ao serviço que migraram para o Serviço de Aplicações enquanto encaminham todos os outros pedidos para os Gateways de Aplicações regionais.

Na região, para o seu serviço IaaS, o desenvolvedor de aplicações decidiu que quaisquer URLs que correspondam ao padrão /imagens/* são servidos a partir de um conjunto dedicado de VMs que são diferentes do resto da fazenda web.

Além disso, o pool VM padrão que serve o conteúdo dinâmico precisa de falar com uma base de dados back-end que está hospedada num cluster de alta disponibilidade. Toda a implementação é configurada através do Azure Resource Manager.

O seguinte diagrama mostra a arquitetura deste cenário:

![Suíte de entrega de aplicações Arquitetura detalhada][2] 

> [!NOTE]
> Este exemplo é apenas uma das muitas configurações possíveis dos serviços de equilíbrio de carga que a Azure oferece. O Gestor de Tráfego, Porta Frontal, Gateway de Aplicação e Balancer de Carga podem ser misturados e combinados de melhor forma às suas necessidades de equilíbrio de carga. Por exemplo, se não for necessária a descarga TLS/SSL ou o processamento da Camada 7, o Balanceador de Carga pode ser utilizado no lugar do Gateway de Aplicações.


## <a name="next-steps"></a>Passos Seguintes

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como funciona o Front Door](front-door-routing-architecture.md).

<!--Image references-->
[1]: ./media/front-door-lb-with-azure-app-delivery-suite/application-delivery-figure1.png
[2]: ./media/front-door-lb-with-azure-app-delivery-suite/application-delivery-figure2.png
