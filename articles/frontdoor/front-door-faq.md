---
title: Serviço de porta de entrada do Azure - perguntas mais frequentes sobre desde início | Documentos da Microsoft
description: Esta página fornece respostas às perguntas mais frequentes sobre o serviço de porta de entrada do Azure
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/08/2019
ms.author: sharadag
ms.openlocfilehash: 256435dfd016ebbd86dbbe49f4abbb346fb1cd19
ms.sourcegitcommit: 280d9348b53b16e068cf8615a15b958fccad366a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58407756"
---
# <a name="frequently-asked-questions-for-azure-front-door-service"></a>Perguntas mais frequentes sobre a porta de entrada do serviço do Azure

Este artigo responde a perguntas comuns sobre as funcionalidades de serviço de porta de entrada do Azure. Se não vir a resposta à sua pergunta, pode contactar-nos através dos canais seguintes (em ordem de cada vez maiores):

1. A secção de comentários deste artigo.
2. [UserVoice do serviço de porta de entrada do Azure](https://feedback.azure.com/forums/217313-networking?category_id=345025).
3. **Suporte da Microsoft:** Para criar um novo pedido de suporte, no portal do Azure, no **ajudar** separador, selecione a **ajuda + suporte** botão e, em seguida, selecione **novo pedido de suporte**.

## <a name="general"></a>Geral

### <a name="what-is-azure-front-door-service"></a>O que é o Azure Front Door Service?

O serviço de porta de entrada do Azure é uma rede de entrega de aplicativo (ADN) como um serviço, oferecendo vários grupos de recursos de balanceamento de carga de camada 7 para as suas aplicações. Ele fornece a aceleração de sites dinâmicos (DSA), juntamente com balanceamento de carga global com perto de ativação pós-falha em tempo real. É um serviço altamente disponível e dimensionável, que é totalmente gerido pelo Azure.

### <a name="what-features-does-azure-front-door-service-support"></a>Quais recursos suporta o serviço de porta de entrada do Azure?

Serviço de porta de entrada do Azure suporta a aceleração de sites dinâmicos (DSA), descarga de SSL e SSL ponto a ponto, Firewall de aplicações Web, afinidade por sessões com base no cookie, encaminhamento baseado no caminho url, certificados gratuitos e gestão de domínios de múltiplas e outros. Para obter uma lista completa das funcionalidades suportadas, consulte [descrição geral do serviço de porta de entrada do Azure](front-door-overview.md).

### <a name="what-is-the-difference-between-azure-front-door-service-and-azure-application-gateway"></a>O que é a diferença entre o serviço de porta de entrada do Azure e o Gateway de aplicação do Azure?

Embora a porta de entrada e o Gateway de aplicação são de camada 7 balanceadores de carga (HTTP/HTTPS), a principal diferença é a porta de entrada é um serviço global, ao passo que o Gateway de aplicação é um serviço regional. Enquanto a porta de entrada pode carregar o equilíbrio entre as unidades de unidades/clusters/carimbo de escala diferentes em várias regiões, o Gateway de aplicação permite-lhe carregar o equilíbrio entre seu etc. de VMs/contentores que está dentro da unidade de escala.

### <a name="when-should-we-deploy-an-application-gateway-behind-front-door"></a>Quando deve podemos implementar um Gateway de aplicação por trás da porta da frente?

Os cenários principais devem ser usados por que o Gateway de aplicação por trás da porta de entrada são:

- Porta de entrada pode executar apenas a nível global, mas se quer balancear carga de tráfego ainda mais nas suas redes virtuais (VNET), em seguida, devem utilizar o Gateway de aplicação de balanceamento de carga baseado no caminho.
- Uma vez que a porta de entrada não funciona num nível VM/contentor, por isso, ele não pode fazer drenagem de ligação. No entanto, o Gateway de aplicação permite que faça a drenagem da ligação. 
- Com um Gateway de aplicação por trás do AFD, é possível obter a 100% a descarga de SSL e encaminhar os pedidos HTTP apenas dentro da sua rede virtual (VNET).
- Porta de entrada e o Gateway de aplicação suportam a afinidade de sessão. Enquanto a porta de entrada pode direcionar tráfego subsequente de uma sessão de utilizador no mesmo cluster ou back-end numa determinada região, o Gateway de aplicação direto pode permitir o tráfego para o mesmo servidor no cluster.  

### <a name="can-we-deploy-azure-load-balancer-behind-front-door"></a>Podemos implementar o Balanceador de carga do Azure por trás da porta da frente?

O serviço de porta de entrada do Azure tem um VIP público ou um nome DNS disponível publicamente para encaminhar o tráfego para. Implementar um balanceador de carga do Azure por trás da porta de entrada é o caso de utilização de uma comum.

### <a name="what-protocols-does-azure-front-door-service-support"></a>Quais protocolos suporta o serviço de porta de entrada do Azure?

O serviço de porta de entrada do Azure oferece suporte a HTTP, HTTPS e HTTP/2.

### <a name="how-does-azure-front-door-service-support-http2"></a>Como é que o serviço de porta de entrada do Azure suporta HTTP/2?

Suporte de protocolo HTTP/2 está disponível para os clientes ligar apenas a porta de entrada no serviço do Azure. A comunicação para back-ends no conjunto de back-end é através de HTTP/1.1. Suporte HTTP/2 está ativado por predefinição.

### <a name="what-resources-are-supported-today-as-part-of-backend-pool"></a>Os recursos que são atualmente suportados como parte do conjunto de back-end?

Conjuntos de back-end podem ser compostos por armazenamento, aplicação Web, instâncias de Kubernetes ou qualquer outro nome de anfitrião personalizado que tenha conectividade pública. O serviço de porta de entrada do Azure requer que o back-ends são definidos por meio de um IP público ou um nome de anfitrião DNS resolvível publicamente. Membros dos agrupamentos de back-end podem ser entre zonas, regiões, ou até mesmo fora do Azure, desde que eles têm conectividade pública.

### <a name="what-regions-is-the-service-available-in"></a>Que regiões o serviço está disponível no?

O serviço de porta de entrada do Azure é um serviço global e não está associado a qualquer região do Azure específica. A única localização que tem de especificar durante a criação de uma porta de entrada é a localização do grupo de recursos, que basicamente é especificar onde serão armazenados os metadados para o grupo de recursos. Recurso de porta de entrada em si é criado como um recurso global e a configuração está implementada globalmente para todos os POPs (pontos de presença). 

### <a name="what-are-the-pop-locations-for-azure-front-door-service"></a>Quais são as localizações de POP desde início do serviço do Azure?

O serviço de porta de entrada do Azure tem a mesma lista de localizações POP (pontos de presença) como a CDN do Azure da Microsoft. Para obter a lista completa de nossos POPs, consulte por gentileza [localizações POP do CDN do Azure da Microsoft](https://docs.microsoft.com/azure/cdn/cdn-pop-locations).

### <a name="is-azure-front-door-service-a-dedicated-deployment-for-my-application-or-is-it-shared-across-customers"></a>É uma implementação de dedicado para a minha aplicação de serviço de porta de entrada do Azure ou é partilhado entre os clientes?

O serviço de porta de entrada do Azure é um serviço de multi-inquilino e distribuído globalmente. Por isso, a infraestrutura para a porta de entrada é compartilhada entre todos os seus clientes. No entanto, através da criação de uma porta de entrada define a configuração específica necessária para a sua aplicação e 

### <a name="is-http-https-redirection-supported"></a>É HTTP -> redirecionamento a HTTPS suportado?

Porta de entrada atualmente não suporta redirecionamento de URL.

### <a name="in-what-order-are-routing-rules-processed"></a>Ordem pela qual são regras de encaminhamento processadas?

As rotas para sua porta de entrada não são ordenadas e uma rota específica está selecionada com base na melhor correspondência. Saiba mais sobre [como porta de entrada corresponde aos pedidos para uma regra de roteamento](front-door-route-matching.md).

### <a name="how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door-service"></a>Como posso bloquear o acesso ao meu back-end apenas o serviço de porta de entrada do Azure?

Pode configurar a ACL de IP da sua back-ends para aceitar apenas o tráfego do serviço de porta de entrada do Azure. Pode restringir o seu aplicativo para aceitar ligações de entrada apenas a partir do espaço IP de back-end do serviço de porta de entrada do Azure. Estamos a trabalhar em direção à integração com o [intervalos de IP do Azure e as etiquetas de serviço](https://www.microsoft.com/download/details.aspx?id=56519) mas, por agora, pode consultar os intervalos IP, conforme mostrado a seguir:
 
- **IPv4** - `147.243.0.0/16`
- **IPv6** - `2a01:111:2050::/44`

> [!WARNING]
> Nosso espaço IP de back-end pode alterar mais tarde, no entanto, asseguraremos que que isso acontece, podemos seria tiver integrado com o [intervalos de IP do Azure e as etiquetas de serviço](https://www.microsoft.com/download/details.aspx?id=56519). Recomendamos que assine [intervalos de IP do Azure e as etiquetas de serviço](https://www.microsoft.com/download/details.aspx?id=56519) para quaisquer alterações ou atualizações. 

### <a name="can-the-anycast-ip-change-over-the-lifetime-of-my-front-door"></a>Pode alterar o IP do anycast durante a duração da minha porta da frente?

O IP de front-end anycast para sua porta de entrada, normalmente, não deve alterar a e pode permanecer estático para a vida útil de desde o início. No entanto, existem **sem garantias** para a mesma. Por gentileza não têm dependências diretas no IP.  

### <a name="does-azure-front-door-service-support-static-or-dedicated-ips"></a>O serviço de porta de entrada do Azure suporta IPs estáticos ou dedicado?

Não, serviço de porta de entrada do Azure atualmente não suporta anycast de front-end estática ou dedicado IPs. 

### <a name="does-azure-front-door-service-support-x-forwarded-for-headers"></a>O serviço de porta de entrada do Azure suporta cabeçalhos x-reencaminhados-para?

Sim, o serviço de porta de entrada do Azure suporta os cabeçalhos X-reencaminhados-para X-reencaminhados-anfitrião e Proto X reencaminhados. Para X-reencaminhados-para se o cabeçalho já estava presente depois porta da frente acrescenta o IP de soquete do cliente, ao mesmo. Caso contrário, ele adiciona o cabeçalho com o IP de soquete do cliente como o valor. Para X-reencaminhados-anfitrião e Proto X reencaminhados, o valor é substituído.

Saiba mais sobre o [porta de entrada suportado cabeçalhos HTTP](front-door-http-headers-protocol.md).  

### <a name="how-long-does-it-take-to-deploy-an-azure-front-door-service-does-my-front-door-still-work-when-being-updated"></a>Quanto tempo demora a implementar um serviço de porta de entrada do Azure? Meu porta da frente ainda funciona quando a ser atualizado?

Uma nova criação de porta de entrada ou de quaisquer atualizações para uma porta de entrada existentes demora cerca de 3 a 5 minutos para implantação global. Isso significa que em cerca de 3 a 5 minutos, a configuração de porta de entrada vai ser implementada em todos os nossos POPs globalmente.

Tenha em atenção - personalizado atualizações ao certificado SSL demorar cerca de 30 minutos para ser implementado globalmente.

## <a name="configuration"></a>Configuração

### <a name="can-azure-front-door-load-balance-or-route-traffic-within-a-virtual-network"></a>Pode balanceamento de carga de porta de entrada do Azure ou encaminhar tráfego numa rede virtual?

Porta de entrada do Azure (AFD) requer um IP público ou o nome DNS resolvível publicamente para encaminhar o tráfego. Por isso, a resposta é não AFD não é possível encaminhar diretamente dentro de uma rede virtual, mas entre a utilizar um Gateway de aplicação ou Balanceador de carga do Azure irá resolver este cenário.

### <a name="what-are-the-various-timeouts-and-limits-for-azure-front-door-service"></a>Quais são os vários tempos limite e limites de porta de entrada do serviço do Azure?

Saiba tudo sobre o documento [tempos limite e os limites de serviço do Azure desde início](https://docs.microsoft.com/azure/azure-subscription-service-limits#azure-front-door-service-limits).

## <a name="performance"></a>Desempenho

### <a name="how-does-azure-front-door-service-support-high-availability-and-scalability"></a>Como o serviço de porta de entrada do Azure suporta elevada disponibilidade e escalabilidade?

O serviço de porta de entrada do Azure é uma plataforma de multi-inquilino e distribuída globalmente com enormes volumes de capacidade para se adaptar às necessidades de escalabilidade da sua aplicação. Entregue do limite da rede global da Microsoft, desde início fornece o recurso global balanceamento de carga que permite-lhe fazer a ativação pós-falha seu aplicativo inteiro ou os microsserviços individuais entre regiões ou nuvens diferentes.

## <a name="ssl-configuration"></a>Configuração de SSL

### <a name="what-tls-versions-are-supported-by-azure-front-door-service"></a>Que versões do TLS são suportadas pelo serviço de porta de entrada do Azure?

Porta de entrada suporta o TLS 1.0, 1.1 e 1.2 de versões. TLS 1.3 ainda não é suportado.

### <a name="what-certificates-are-supported-on-azure-front-door-service"></a>Quais certificados são suportados no serviço de porta de entrada do Azure?

Para ativar o protocolo HTTPS para o fornecimento com segurança o conteúdo num domínio personalizado da porta de entrada, pode optar por utilizar um certificado que é gerido pelo serviço de porta de entrada do Azure ou utilizar o seu próprio certificado.
Desde o início geridos provisões de opção um certificado SSL padrão por meio da Digicert e armazenados na frente Key Vault da porta. Se optar por utilizar o seu próprio certificado, pode carregar um certificado de uma AC suportado e pode ser um SSL padrão, o certificado de validação estendida ou até mesmo um certificado de caráter universal. Certificados autoassinados não são suportados. Saiba mais [como ativar o HTTPS num domínio personalizado](https://aka.ms/FrontDoorCustomDomainHTTPS).

### <a name="what-are-the-current-cipher-suites-supported-by-azure-front-door-service"></a>Quais são os conjuntos de cifras atual suportados pelo serviço de porta de entrada do Azure?

Seguem-se a atual conjuntos de cifras suportados pelo serviço de porta de entrada do Azure:

- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA

### <a name="does-azure-front-door-service-also-support-re-encryption-of-traffic-to-the-backend"></a>O serviço de porta de entrada do Azure também oferece suporte a reencriptação do tráfego para o back-end?

Sim, o serviço de porta de entrada do Azure suporta a descarga de SSL e o SSL de ponto a ponto, o que reencripta o tráfego para o back-end. Na verdade, uma vez que as ligações para o back-end acontecem ao longo do é IP público, recomenda-se que configure a porta de entrada para utilizar HTTPS como o protocolo de encaminhamento.

### <a name="can-i-configure-ssl-policy-to-control-ssl-protocol-versions"></a>Posso configurar a política SSL para controlar as versões de protocolo SSL?

Não, atualmente não suporta a porta de entrada para negar versões específicas do TLS, nem pode definir as versões do TLS mínimas. 

### <a name="can-i-configure-front-door-to-only-support-specific-cipher-suites"></a>Posso configurar a porta de entrada para suportar apenas conjuntos de cifras específico?

Não, a configuração desde início para conjuntos de cifras específico não é suportada. 

## <a name="diagnostics-and-logging"></a>Diagnóstico e registos

### <a name="what-types-of-metrics-and-logs-are-available-with-azure-front-door-service"></a>Que tipos de métricas e registos estão disponíveis com o serviço de porta de entrada do Azure?

Para obter informações sobre os registos e outros recursos de diagnóstico, consulte [monitorizar métricas e registos para a porta da frente](front-door-diagnostics.md).

### <a name="what-is-the-retention-policy-on-the-diagnostics-logs"></a>O que é a política de retenção de registos de diagnóstico?

Fluxo de registos de diagnóstico para a conta de armazenamento de clientes e os clientes podem definir a política de retenção com base nas suas preferências. Os registos de diagnóstico também podem ser enviados para um Hub de eventos ou do Azure Monitor registos. Para obter mais informações, consulte [diagnóstico do serviço do Azure desde início](front-door-diagnostics.md).

### <a name="how-do-i-get-audit-logs-for-azure-front-door-service"></a>Como posso obter registos de auditoria para o serviço de porta de entrada do Azure?

Registos de auditoria estão disponíveis desde início do serviço do Azure. No portal, clique em **registo de atividades** no painel do menu da sua porta de entrada para aceder ao registo de auditoria. 

### <a name="can-i-set-alerts-with-azure-front-door-service"></a>Pode definir alertas com o serviço de porta de entrada do Azure?

Sim, o serviço de porta de entrada do Azure suportam alertas. Os alertas são configurados em métricas. 

## <a name="next-steps"></a>Passos Seguintes

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como funciona o Front Door](front-door-routing-architecture.md).