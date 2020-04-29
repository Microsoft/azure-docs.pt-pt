---
title: Porta da frente azure - Perguntas frequentes
description: Esta página fornece respostas a perguntas frequentes sobre a Porta da Frente Azure
services: frontdoor
documentationcenter: ''
author: sohamnchatterjee
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/13/2020
ms.author: sohamnc
ms.openlocfilehash: e2785baab27f5bfc996b57607816062195a19b2b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81313760"
---
# <a name="frequently-asked-questions-for-azure-front-door"></a>Perguntas frequentes para a Porta da Frente Azure

Este artigo responde a perguntas comuns sobre funcionalidades e funcionalidades da Porta Frontal do Azure. Se não vir a resposta à sua pergunta, pode contactar-nos através dos seguintes canais (por ordem crescente):

1. A secção de comentários deste artigo.
2. [Voz do utilizador da porta frontal Azure.](https://feedback.azure.com/forums/217313-networking?category_id=345025)
3. **Suporte da Microsoft:** Para criar um novo pedido de suporte, no portal Azure, no separador **Ajuda,** selecione o botão **de suporte Ajuda +** e, em seguida, selecione Novo pedido de **suporte**.

## <a name="general"></a>Geral

### <a name="what-is-azure-front-door"></a>O que é o Azure Front Door?

A Porta Frontal Azure é uma Rede de Entrega de Aplicações (ADN) como um serviço, oferecendo várias capacidades de equilíbrio de carga de camada 7 para as suas aplicações. Proporciona uma aceleração dinâmica do local (DSA) juntamente com o equilíbrio global da carga com quase falha em tempo real. É um serviço altamente disponível e escalável, que é totalmente gerido pelo Azure.

### <a name="what-features-does-azure-front-door-support"></a>Quais as funcionalidades que o Azure Front Door suporta?

A Porta Frontal Azure suporta a aceleração dinâmica do site (DSA), o descarregamento tLS/SSL e o fim do TLS final, firewall de aplicação web, afinidade de sessão baseada em cookies, encaminhamento baseado em caminhos de url, certificados gratuitos e gestão de domínios múltiplos, entre outros. Para obter uma lista completa de funcionalidades suportadas, consulte [a visão geral da Porta frontal do Azure.](front-door-overview.md)

### <a name="what-is-the-difference-between-azure-front-door-and-azure-application-gateway"></a>Qual é a diferença entre a Porta frontal azure e o Portal de Aplicação Azure?

Embora tanto a Porta Frontal como o Gateway de Aplicação sejam os equilibradores de carga da camada 7 (HTTP/HTTPS), a principal diferença é que a Porta Da Frente é um serviço global, enquanto o Application Gateway é um serviço regional. Enquanto a Porta Frontal pode carregar o equilíbrio entre as suas diferentes unidades/clusters/unidades de selos entre regiões, o Gateway de Aplicação permite-lhe carregar o equilíbrio entre os seus VMs/contentores, etc. que está dentro da unidade de escala.

### <a name="when-should-we-deploy-an-application-gateway-behind-front-door"></a>Quando devemos colocar um Gateway de aplicação atrás da porta da frente?

Os cenários-chave para utilizar o Gateway de aplicação atrás da porta da frente são:

- A Porta Frontal só pode realizar o equilíbrio de carga baseado na trajetória a nível global, mas se alguém quiser carregar o tráfego de equilíbrio ainda mais dentro da sua rede virtual (VNET), então deve utilizar o Application Gateway.
- Uma vez que a Porta da Frente não funciona a nível vM/contentor, por isso não pode fazer a Drenagem de Ligação. No entanto, o Gateway de Aplicação permite-lhe fazer a Drenagem de Ligação. 
- Com um Gateway de Aplicação atrás da AFD, pode-se obter 100% de descarga TLS/SSL e encaminhar apenas pedidos HTTP dentro da sua rede virtual (VNET).
- Porta da Frente e Gateway de aplicação ambos afinidade de sessão de apoio. Enquanto a Porta Frontal pode direcionar o tráfego subsequente de uma sessão de utilizador para o mesmo cluster ou backend numa determinada região, o Application Gateway pode direcionar o tráfego para o mesmo servidor dentro do cluster.  

### <a name="can-we-deploy-azure-load-balancer-behind-front-door"></a>Podemos colocar o Azure Load Balancer atrás da Porta da Frente?

A Porta Frontal Azure precisa de um VIP público ou um nome DNS disponível publicamente para encaminhar o tráfego para. A colocação de um balancer de carga Azure atrás da Porta da Frente é um caso de uso comum.

### <a name="what-protocols-does-azure-front-door-support"></a>Que protocolos suporta a Porta da Frente Azure?

A Porta Frontal Azure suporta HTTP, HTTPS e HTTP/2.

### <a name="how-does-azure-front-door-support-http2"></a>Como é que a Porta Frontal Azure suporta http/2?

O suporte ao protocolo HTTP/2 está disponível apenas para clientes que se ligam à Porta Frontal Azure. A comunicação para backends na piscina de backend é superior a HTTP/1.1. O suporte HTTP/2 é ativado por defeito.

### <a name="what-resources-are-supported-today-as-part-of-backend-pool"></a>Que recursos são apoiados hoje como parte da reserva de backend?

As piscinas backend podem ser compostas por casos de Armazenamento, Web App, Kubernetes ou qualquer outro nome de anfitrião personalizado que tenha conectividade pública. A Porta Frontal Azure exige que os backends sejam definidos através de um IP público ou de um nome de anfitrião dNS publicamente resolvível. Os membros de piscinas de backend podem estar em zonas, regiões ou mesmo fora de Azure, desde que tenham conectividade pública.

### <a name="what-regions-is-the-service-available-in"></a>Em que regiões está disponível o serviço?

A Porta Frontal Azure é um serviço global e não está ligada a nenhuma região específica do Azure. A única localização que precisa especificar ao criar uma Porta Frontal é a localização do grupo de recursos, que está basicamente a especificar onde serão armazenados os metadados para o grupo de recursos. O próprio recurso Front Door é criado como um recurso global e a configuração é implantada globalmente para todos os POPs (Ponto de Presença). 

### <a name="what-are-the-pop-locations-for-azure-front-door"></a>Quais são as localizações pop para a Porta Da Frente Azure?

A Porta Frontal Azure tem a mesma lista de localizações pop (Ponto de Presença) que o Azure CDN da Microsoft. Para obter a lista completa dos nossos POPs, consulte as [localizações do Azure CDN POP da Microsoft](https://docs.microsoft.com/azure/cdn/cdn-pop-locations).

### <a name="is-azure-front-door-a-dedicated-deployment-for-my-application-or-is-it-shared-across-customers"></a>A Azure Front Door é uma implementação dedicada para a minha aplicação ou é partilhada entre os clientes?

Azure Front Door é um serviço multi-inquilino distribuído globalmente. Assim, a infraestrutura para a Porta da Frente é partilhada em todos os seus clientes. No entanto, ao criar um perfil porta da frente, define a configuração específica necessária para a sua aplicação e nenhuma alteração feita na porta da frente impacta outras configurações da Porta Da Frente.

### <a name="is-http-https-redirection-supported"></a>A reorientação http->HTTPS é suportada?

Sim. De facto, a Porta Frontal Azure suporta a redirecção de cordas de anfitrião, caminho e consulta, bem como parte da redirecção de URL. Saiba mais sobre [a reorientação do URL.](front-door-url-redirect.md) 

### <a name="in-what-order-are-routing-rules-processed"></a>Em que ordem são processadas as regras de encaminhamento?

As rotas para a sua Porta Da Frente não são encomendadas e uma rota específica é selecionada com base na melhor correspondência. Saiba mais sobre como a [Porta da Frente corresponde aos pedidos de uma regra de encaminhamento](front-door-route-matching.md).

### <a name="how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door"></a>Como fecho o acesso ao meu backend só para a Porta da Frente Azure?

Para bloquear a sua aplicação para aceitar o tráfego apenas a partir da sua porta frontal específica, terá de configurar OS ACLs IP para o seu backend e, em seguida, restringir o tráfego no seu backend ao valor específico do cabeçalho 'X-Azure-FDID' enviado pela Porta da Frente. Estes passos são detalhados como abaixo:

- Configure o IP ACLing para os seus backends para aceitar o tráfego do espaço de endereço IP backend da Azure Front Door e apenas os serviços de infraestrutura do Azure. Consulte os detalhes ip abaixo para AACLing o seu backend:
 
    - Consulte a secção *AzureFrontDoor.Backend* em [Gamas E Etiquetas](https://www.microsoft.com/download/details.aspx?id=56519) de Serviço Azure IP para o intervalo de endereçoIP iPv4 da Porta Frontal ou também pode utilizar a etiqueta de serviço *AzureFrontDoor.Backend* nos seus [grupos](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) de segurança de rede ou com [firewall Azure](https://docs.microsoft.com/azure/firewall/service-tags).
    - O espaço IP **iPv6** da Porta Frontal enquanto está coberto na etiqueta de serviço, não está listado no ficheiro JSON das gamas Azure IP. Se procura um intervalo de endereços IPv6 explícito, está atualmente limitado a`2a01:111:2050::/44`
    - Serviços básicos de [infraestrutura](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations) da Azure através de endereços IP de acolhimento virtualizados: `168.63.129.16` e`169.254.169.254`

    > [!WARNING]
    > O espaço IP backend da Front Door pode mudar mais tarde, no entanto, vamos garantir que antes disso acontecer, que teríamos integrado com [gamas ip azure e etiquetas](https://www.microsoft.com/download/details.aspx?id=56519)de serviço. Recomendamos que subscreva as [gamas e etiquetas](https://www.microsoft.com/download/details.aspx?id=56519) de serviço Do Azure IP para quaisquer alterações ou atualizações.

-    Execute uma operação GET na sua Porta `2020-01-01` Frontal com a versão API ou superior. Na chamada da API, procure `frontdoorID` campo. Filtre no cabeçalho de entrada '**X-Azure-FDID**' enviado pela Porta da `frontdoorID`Frente para o seu backend com o valor como o do campo . 

### <a name="can-the-anycast-ip-change-over-the-lifetime-of-my-front-door"></a>O IP pode mudar ao longo da vida da minha porta da frente?

O IP frontal para a sua Porta Frontal normalmente não deve mudar e pode permanecer estático durante toda a vida útil da Porta da Frente. No entanto, não há **garantias** para o mesmo. Por favor, não tome nenhuma dependência direta no PERÍODO.

### <a name="does-azure-front-door-support-static-or-dedicated-ips"></a>A Porta Frontal Azure suporta IPs estáticos ou dedicados?

Não, a Porta Frontal Azure não suporta atualmente iPs de fachada estática ou dedicada. 

### <a name="does-azure-front-door-support-x-forwarded-for-headers"></a>A Porta da Frente Azure suporta cabeçalhos x-forward-for?

Sim, a Porta frontal Azure suporta os cabeçalhos X-Forwarded-For, X-Forward-Host e X-Forward-Proto. Para X-Forward-For se o cabeçalho já estava presente, então a Porta Da Frente anexa o IP da tomada do cliente. Caso contrário, adiciona o cabeçalho com o IP da tomada do cliente como o valor. Para x-forward-host e X-Forwarded-Proto, o valor é ultrapassado.

Saiba mais sobre os [cabeçalhos HTTP suportados](front-door-http-headers-protocol.md)pela Porta Da Frente .  

### <a name="how-long-does-it-take-to-deploy-an-azure-front-door-does-my-front-door-still-work-when-being-updated"></a>Quanto tempo demora a implantar uma porta da frente Azure? A minha porta da frente ainda funciona quando está a ser atualizada?

Uma nova criação da Porta Da Frente ou quaisquer atualizações para uma porta da frente existente leva cerca de 3 a 5 minutos para a implantação global. Isto significa que em cerca de 3 a 5 minutos, a configuração da Porta Da Frente será implantada em todos os nossos POPs a nível global.

Nota - As atualizações de certificados Custom TLS/SSL demoram cerca de 30 minutos a serem implementadas globalmente.

Quaisquer atualizações de rotas ou piscinas de backend, etc. são perfeitas e causarão zero tempo de inatividade (se a nova configuração estiver correta). As atualizações de certificados também são atómicas e não causarão qualquer interrupção, a menos que a mudança de 'AFD Managed' para 'Use o seu próprio cert' ou vice-versa.


## <a name="configuration"></a>Configuração

### <a name="can-azure-front-door-load-balance-or-route-traffic-within-a-virtual-network"></a>O equilíbrio de carga da Porta Frontal Azure ou o tráfego de rotas dentro de uma rede virtual?

A Porta Frontal Azure (AFD) requer um IP público ou um nome dNS publicamente resolável para o tráfego de rotas. Assim, a resposta é que nenhum AFD não pode encaminhar diretamente dentro de uma rede virtual, mas usar um Gateway de aplicação ou um Equilíbrio de Carga Azure no meio irá resolver este cenário.

### <a name="what-are-the-various-timeouts-and-limits-for-azure-front-door"></a>Quais são os vários intervalos e limites para a Porta da Frente Azure?

Conheça todos os [intervalos e limites documentados para a Porta da Frente Azure.](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-front-door-service-limits)

## <a name="performance"></a>Desempenho

### <a name="how-does-azure-front-door-support-high-availability-and-scalability"></a>Como é que a Porta Frontal azure suporta alta disponibilidade e escalabilidade?

A Azure Front Door é uma plataforma multiarrendatária distribuída globalmente com enormes volumes de capacidade para atender às necessidades de escalabilidade da sua aplicação. Entregue a partir da borda da rede global da Microsoft, a Front Door fornece uma capacidade global de equilíbrio de carga que lhe permite falhar em toda a sua aplicação ou mesmo microserviços individuais em regiões ou nuvens diferentes.

## <a name="tls-configuration"></a>Configuração TLS

### <a name="what-tls-versions-are-supported-by-azure-front-door"></a>Que versões TLS são suportadas pela Porta Frontal Azure?

Todos os perfis front door criados após setembro de 2019 usam TLS 1.2 como o mínimo padrão.

Porta frontal suporta versões TLS 1.0, 1.1 e 1.2. TLS 1.3 ainda não é suportado.

### <a name="what-certificates-are-supported-on-azure-front-door"></a>Que certificados são suportados na Porta da Frente Azure?

Para ativar o protocolo HTTPS para entregar conteúdo de forma segura num domínio personalizado porta frontal, pode optar por utilizar um certificado que seja gerido pela Porta Frontal Azure ou utilizar o seu próprio certificado.
A opção gerida pela Porta Frontal prevê um certificado TLS/SSL padrão via Digicert e armazenado no Cofre chave da Porta Frontal. Se optar por usar o seu próprio certificado, pode embarcar num certificado de uma CA suportada e pode ser um TLS padrão, certificado de validação alargado ou até mesmo um certificado wildcard. Os certificados auto-assinados não são suportados. Saiba [como ativar HTTPS para um domínio personalizado](https://aka.ms/FrontDoorCustomDomainHTTPS).

### <a name="does-front-door-support-autorotation-of-certificates"></a>A Porta da Frente suporta a rotação automática de certificados?

Para a opção de certificado gerido pela Porta Da Frente, os certificados são rodados automaticamente pela Porta Da Frente. Se estiver a utilizar um certificado gerido pela Porta Da Frente e ver se a data de validade do certificado está a menos de 60 dias de distância, preencha um bilhete de apoio.
</br>Para o seu próprio certificado personalizado TLS/SSL, a rotação automática não é suportada. À semelhança de como foi configurado pela primeira vez para um determinado domínio personalizado, terá de apontar a Porta da Frente para a versão de certificado certo no seu Cofre de Chaves e garantir que o diretor de serviço da Porta Frontal ainda tem acesso ao Cofre chave. Esta operação atualizada de lançamento de certificados pela Porta Frontal é atómica e não causa qualquer impacto de produção desde que o nome do assunto ou SAN para o certificado não se alterem.

### <a name="what-are-the-current-cipher-suites-supported-by-azure-front-door"></a>Quais são as atuais suítes cifra suportadas pela Porta da Frente Azure?

Para TLS1.2 são suportadas as seguintes suítes cifras

TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 TLS_DHE_RSA_WITH_AES_256_GCM_SHA384 TLS_DHE_RSA_WITH_AES_128_GCM_SHA256

Quando utilizar domínios personalizados com TLS1.0/1.1 habilitados são suportadas as seguintes suites de cifra:

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
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384

### <a name="can-i-configure-tls-policy-to-control-tls-protocol-versions"></a>Posso configurar a política do TLS para controlar as versões do Protocolo TLS?

Pode configurar uma versão TLS mínima em Porta Frontal Azure nas definições de domínio personalizado HTTPS através do portal Azure ou da [API Azure REST](https://docs.microsoft.com/rest/api/frontdoorservice/frontdoor/frontdoors/createorupdate#minimumtlsversion). Atualmente, pode escolher entre 1.0 e 1.2.

### <a name="can-i-configure-front-door-to-only-support-specific-cipher-suites"></a>Posso configurar a Porta da Frente para apenas apoiar suites específicas de cifra?

Não, configurar a Porta da Frente para suites específicas de cifra não é suportado. No entanto, pode obter o seu próprio certificado TLS/SSL personalizado da sua Autoridade de Certificado (por exemplo Verisign, Entrust ou Digicert) e ter suites de cifra específicas marcadas no certificado quando o tiver gerado. 

### <a name="does-front-door-support-ocsp-stapling"></a>A Porta da Frente suporta a agrafamento de OCSP?

Sim, a estação OCSP é suportada por padrão pela Porta Da Frente e não é necessária configuração.

### <a name="does-azure-front-door-also-support-re-encryption-of-traffic-to-the-backend"></a>A Porta frontal do Azure também suporta a reencriptação do tráfego até ao backend?

Sim, a Porta Frontal Azure suporta a descarga TLS/SSL e termina com o TLS, que encripta o tráfego até ao backend. De facto, uma vez que as ligações ao backend acontecem ao longo do seu IP público, recomenda-se que configure a porta da frente para utilizar https como protocolo de reencaminhamento.

### <a name="does-front-door-support-self-signed-certificates-on-the-backend-for-https-connection"></a>A Porta da Frente suporta certificados auto-assinados no backend para ligação HTTPS?

Não, os certificados auto-assinados não são suportados na Porta da Frente e a restrição aplica-se a ambos:

1. **Backends**: Não pode utilizar certificados auto-assinados quando estiver a encaminhar o tráfego como sondas de saúde HTTPS ou HTTPS ou a preencher a cache de origem para regras de encaminhamento com enrdação ativada.
2. **Frontend**: Não pode utilizar certificados auto-assinados quando utilizar o seu próprio certificado TLS/SSL personalizado para ativar HTTPS no seu domínio personalizado.

### <a name="why-is-https-traffic-to-my-backend-failing"></a>Porque é que o tráfego https para o meu backend está a falhar?

Por ter ligações HTTPS bem sucedidas ao seu backend, quer para sondas de saúde quer para pedidos de reencaminhamento, pode haver duas razões pelas quais o tráfego HTTPS pode falhar:

1. **Desajuste do nome**do certificado : Para ligações HTTPS, a Porta Frontal espera que o seu backend apresente certificado de um CA válido com nome ou nome ou nome de assunto correspondente ao nome de anfitrião do backend. Como exemplo, se o seu nome `myapp-centralus.contosonews.net` de anfitrião de backend for definido e o `myapp-centralus.contosonews.net` `*myapp-centralus*.contosonews.net` certificado que o seu backend apresenta durante o aperto de mão TLS não tem nem no nome do assunto, a Porta da Frente recusará a ligação e resultará num erro. 
    1. **Solução**: Embora não seja recomendado do ponto de vista da conformidade, pode contornar este erro desativando a verificação do nome do certificado para a sua Porta Frontal. Isto está presente em Definições no portal Azure e em BackendPoolsSettings na API.
2. **Certificado de hospedagem backend de CA inválido**: Apenas os certificados de [Ca válidos](/azure/frontdoor/front-door-troubleshoot-allowed-ca) podem ser usados no backend com porta da frente. Não são permitidos certificados provenientes de AC internos ou certificados auto-assinados.

## <a name="diagnostics-and-logging"></a>Diagnóstico e registos

### <a name="what-types-of-metrics-and-logs-are-available-with-azure-front-door"></a>Que tipos de métricas e registos estão disponíveis com a Porta da Frente Azure?

Para obter informações sobre registos e outras capacidades de diagnóstico, consulte [métricas de monitorização e registos para a Porta Da Frente](front-door-diagnostics.md).

### <a name="what-is-the-retention-policy-on-the-diagnostics-logs"></a>Qual é a política de retenção nos registos de diagnóstico?

Os registos de diagnóstico fluem para a conta de armazenamento dos clientes e os clientes podem definir a política de retenção com base na sua preferência. Os registos de diagnóstico também podem ser enviados para um Hub de Eventos ou registos do Monitor Azure. Para mais informações, consulte O Diagnóstico da [Porta Da Frente Azure](front-door-diagnostics.md).

### <a name="how-do-i-get-audit-logs-for-azure-front-door"></a>Como recebo registos de auditoria para a Porta da Frente Azure?

Os registos de auditoria estão disponíveis para a Porta da Frente Azure. No portal, clique em **'Iniciar sessão de atividades'** na lâmina do menu da sua Porta Frontal para aceder ao registo de auditoria. 

### <a name="can-i-set-alerts-with-azure-front-door"></a>Posso colocar alertas com a Porta da Frente Azure?

Sim, a Porta da Frente Azure suporta alertas. Os alertas estão configurados nas métricas. 

## <a name="next-steps"></a>Passos seguintes

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como funciona o Front Door](front-door-routing-architecture.md).
