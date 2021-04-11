---
title: Porta frontal Azure - Load Balanceing com a suíte de entrega de aplicações da Azure | Microsoft Docs
description: Este artigo ajuda-o a aprender como a Azure recomenda o equilíbrio de carga com a sua suíte de entrega de aplicações
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/06/2021
ms.author: duau
ms.openlocfilehash: 0a7e81c57552fdc24262522343a08fdabba71bfd
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "106552585"
---
# <a name="load-balancing-with-azures-application-delivery-suite"></a>Balanceamento de carga com o conjunto de entrega de aplicações do Azure

## <a name="introduction"></a>Introdução
A Microsoft Azure fornece vários serviços globais e regionais para gerir a forma como o tráfego da sua rede é distribuído e a carga equilibrada: 

* Gateway de Aplicação
* Front Door 
* Balanceador de Carga  
* Gestor de Tráfego

Juntamente com as muitas regiões e arquitetura zonal da Azure, a utilização destes serviços em conjunto pode permitir-lhe construir aplicações robustas, escaláveis e de alto desempenho.

:::image type="content" source="./media/front-door-lb-with-azure-app-delivery-suite/application-delivery-figure1.png" alt-text="Suíte de Entrega de Aplicações":::
 
Estes serviços são divididos em duas categorias:
1. **Serviços globais de equilíbrio de carga,** como o Traffic Manager e a Front Door, distribuem tráfego dos seus utilizadores finais através das suas costas regionais, através das nuvens e até dos seus serviços híbridos no local. O equilíbrio global de cargas encaminha o seu tráfego para o backend de serviço mais próximo e reage a alterações na fiabilidade do serviço para manter a disponibilidade e o alto desempenho para os seus utilizadores. 
1. **Os serviços regionais de equilíbrio de carga,** tais como Balanceadores de Carga e Gateways de Aplicação, proporcionam a capacidade de distribuir tráfego a máquinas virtuais (VMs) dentro de uma rede virtual (VNETs) ou pontos finais de serviço dentro de uma região.

Ao combinar estes serviços globais e regionais, a sua aplicação beneficiará de tráfego fiável e seguro que é enviado dos seus utilizadores finais para os seus serviços IaaS, PaaS ou no local. Na secção seguinte, descrevemos cada um destes serviços.

## <a name="global-load-balancing"></a>Equilíbrio global da carga
**O Traffic Manager** proporciona um equilíbrio global de carga de DNS. Olha para os pedidos de DNS que chegam e responde com um backend saudável, seguindo a política de encaminhamento que o cliente selecionou. As opções para os métodos de encaminhamento são:
- **O encaminhamento de** desempenho envia pedidos para o backend mais próximo com a menor latência.
- **O encaminhamento prioritário** direciona todo o tráfego para um backend, com outros backends como backup.
- **O encaminhamento de robin redondo ponderado** distribui o tráfego com base na ponderação atribuída a cada backend.
- **O encaminhamento geográfico** garante que os pedidos provenientes de regiões geográficas específicas são tratados por backends mapeados para essas regiões. (Por exemplo, todos os pedidos da Espanha devem ser dirigidos à região de France Central Azure)
- **O encaminhamento de sub-redes** permite mapear intervalos de endereços IP para backends, de modo que os pedidos de entrada para esses IPs serão enviados para o backend específico. (Por exemplo, qualquer utilizadores que se conectem a partir da gama de endereços IP do seu QG corporativo devem obter conteúdos web diferentes dos utilizadores gerais)

O cliente liga-se diretamente a esse backend. O Azure Traffic Manager deteta quando um backend não é saudável e, em seguida, redireciona os clientes para outro caso saudável. Consulte a documentação [do Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) para saber mais sobre o serviço.

**A Azure Front Door** fornece aceleração dinâmica do site (DSA) incluindo o equilíbrio global de carga HTTP.  Analisa as rotas de solicitação HTTP para o backend/região de serviço mais próximo para o nome de hospedeiro especificado, caminho URL e regras configuradas.  
Porta frontal termina pedidos HTTP na borda da rede da Microsoft e sondas ativamente para detetar alterações de saúde ou de saúde da infraestrutura ou de latência.  A Porta da Frente, em seguida, sempre encaminha o tráfego para o backend mais rápido e disponível (saudável). Consulte os detalhes da arquitetura de [encaminhamento](front-door-routing-architecture.md) da Porta Frontal e [os métodos de encaminhamento de tráfego](front-door-routing-methods.md) para saber mais sobre o serviço.

## <a name="regional-load-balancing"></a>Equilíbrio regional da carga
O Application Gateway fornece o controlador de entrega de aplicações (ADC) como um serviço, oferecendo várias capacidades de equilíbrio de carga da Camada 7 para a sua aplicação. Permite que os clientes otimizem a produtividade da web farm, descarregando a rescisão intensiva de TLS da CPU para o gateway de aplicações. Outras capacidades adicionais de encaminhamento da Camada 7 também incluem distribuição de rodada-robin do tráfego de entrada, afinidade da sessão baseada em cookies, encaminhamento baseado em caminhos url, e a capacidade de hospedar vários websites por trás de um único gateway de aplicação. O Gateway de Aplicação pode ser configurado como um ponto final virado para a Internet, um ponto final apenas interno, ou uma combinação de ambos. O Application Gateway é totalmente gerido pela Azure, proporcionando-lhe escalabilidade e alta disponibilidade. Proporciona um conjunto avançado de capacidades de registo e diagnóstico, para uma melhor capacidade de gestão.

Os Balanceadores de Carga são parte integrante da stack Azure SDN, que lhe fornece serviços de equilíbrio de carga de alta performance e baixa latência Camada 4 para todos os protocolos UDP e TCP. Pode configurar pontos finais públicos ou internos equilibrados em carga, definindo regras que mapeiam as ligações de entrada para piscinas traseiras. Com a monitorização da sondagem sanitária utilizando TCP ou HTTPS, pode ajudá-lo a gerir a disponibilidade do seu serviço.

## <a name="choosing-a-global-load-balancer"></a>Escolher um equilibrador de carga global
Ao escolher um equilibrador de carga global entre o Traffic Manager e o Azure Front Door para o encaminhamento global, deve considerar o que é semelhante e o que é diferente sobre os dois serviços.   Ambos os serviços fornecem
- **Redundância multi-geo:** Se uma região ficar fora de serviço, o tráfego vai perfeitamente para a região mais próxima sem qualquer intervenção do proprietário da aplicação.
- **Encaminhamento da região mais próximo:** O tráfego é automaticamente encaminhado para a região mais próxima

</br>A tabela a seguir descreve as diferenças entre o Gestor de Tráfego e a Porta frontal Azure:</br>

| Gestor de Tráfego | Azure Front Door |
| --------------- | ------------------------ |
|**Qualquer protocolo:** Uma vez que o Gestor de Tráfego trabalha na camada DE DNS, pode encaminhar qualquer tipo de tráfego de rede; HTTP, TCP, UDP, e assim por diante. | **Aceleração HTTP:** Com a Porta Frontal, o tráfego é proxied na borda da rede Microsoft. Os pedidos HTTP/S verão melhorias de latência e de produção, que reduzem a latência para a negociação do TLS.|
|**Encaminhamento no local:** Com o encaminhamento numa camada de DNS, o tráfego vai sempre de ponto a ponta.  O encaminhamento da sua sucursal para o seu datacenter no local pode seguir um caminho direto; mesmo na sua própria rede usando o Traffic Manager. | **Escalabilidade independente:** Uma vez que a Porta Frontal funciona com o pedido HTTP, os pedidos para diferentes caminhos URL podem ser encaminhados para diferentes piscinas de backend/serviço regional (microserviços) com base nas regras e na saúde de cada microserviço de aplicação.|
|**Formato de faturação:** Escalas de faturação baseadas em DNS com os seus utilizadores e para serviços com mais utilizadores, planaltos para reduzir custos a uma utilização mais elevada. |**Segurança em linha:** A Porta frontal permite que regras como a limitação de tarifas e o IP ACL-ing lhe permitam proteger os seus backends antes que o tráfego chegue à sua aplicação. 

</br>Recomendamos que os clientes utilizem a Porta Frontal para a sua carga de trabalho HTTP devido ao desempenho, à operabilidade e aos benefícios de segurança que http trabalha com a Porta da Frente. O Gestor de Trânsito e a Porta frontal podem ser utilizados em paralelo para servir todo o tráfego para a sua aplicação. 

## <a name="building-with-azures-application-delivery-suite"></a>Edifício com a suíte de entrega de aplicações da Azure 
Recomendamos que todos os websites, APIs, serviços sejam geograficamente redundantes para que possa entregar tráfego aos seus utilizadores a partir do local mais próximo sempre que possível.  A combinação de múltiplos serviços de equilíbrio de carga permite-lhe construir redundância geográfica e zonal para maximizar a fiabilidade e desempenho.

No diagrama seguinte, descrevemos uma arquitetura de exemplo que usa uma combinação de todos estes serviços para construir um serviço web global. O arquiteto usou o Traffic Manager para encaminhar o tráfego para backends globais para entrega de ficheiros e objetos. Ao usar a Porta Frontal, para rotas globais caminhos URL que correspondam ao padrão /loja/* ao serviço que migraram para o Serviço de Aplicações. Por último, encaminhar todos os outros pedidos para os Gateways regionais de aplicações.

Em cada região do serviço IaaS, o desenvolvedor de aplicações decidiu que quaisquer URLs que correspondam ao padrão /imagens/* são servidos a partir de um pool dedicado de VMs. Esta piscina de VMs é diferente do resto da fazenda web.

Além disso, o pool VM padrão que serve o conteúdo dinâmico precisa de falar com uma base de dados back-end que está hospedada num cluster de alta disponibilidade. Toda a implementação é configurada através do Azure Resource Manager.

O seguinte diagrama mostra a arquitetura deste cenário:

:::image type="content" source="./media/front-door-lb-with-azure-app-delivery-suite/application-delivery-figure2.png" alt-text="Suíte de entrega de aplicações Arquitetura detalhada":::

> [!NOTE]
> Este exemplo é apenas uma das muitas configurações possíveis dos serviços de equilíbrio de carga que a Azure oferece. O Gestor de Tráfego, Porta Frontal, Gateway de Aplicação e Balancer de Carga podem ser misturados e combinados de melhor forma às suas necessidades de equilíbrio de carga. Por exemplo, se não for necessária a descarga TLS/SSL ou o processamento da Camada 7, o Balanceador de Carga pode ser utilizado no lugar do Gateway de Aplicações.

## <a name="next-steps"></a>Passos Seguintes

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como funciona o Front Door](front-door-routing-architecture.md).
