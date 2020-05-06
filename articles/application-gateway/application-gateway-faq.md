---
title: Perguntas frequentes sobre o Portal de Aplicação Azure
description: Encontre respostas para perguntas frequentes sobre o Portal de Aplicação Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 05/05/2020
ms.author: victorh
ms.openlocfilehash: 92011495f5f746b18a7706ed2f9583548cc51286
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82836670"
---
# <a name="frequently-asked-questions-about-application-gateway"></a>Perguntas frequentes sobre o Gateway de Aplicação

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Seguem-se questões comuns sobre o Portal de Aplicação Azure.

## <a name="general"></a>Geral

### <a name="what-is-application-gateway"></a>O que é o Gateway de Aplicação?

O Portal de Aplicações Azure fornece um controlador de entrega de aplicações (ADC) como um serviço. Oferece várias capacidades de equilíbrio de carga de camada 7 para as suas aplicações. Este serviço é altamente disponível, escalável e totalmente gerido pelo Azure.

### <a name="what-features-does-application-gateway-support"></a>Quais as funcionalidades que o Application Gateway suporta?

Application Gateway suporta autoscalcificação, descarga de TLS e TLS de ponta a ponta, uma firewall de aplicação web (WAF), afinidade de sessão baseada em cookies, encaminhamento baseado em caminhos de URL, hospedagem multisite e outras funcionalidades. Para obter uma lista completa de funcionalidades suportadas, consulte [Introdução ao Gateway de Aplicação](application-gateway-introduction.md).

### <a name="how-do-application-gateway-and-azure-load-balancer-differ"></a>Como diferem o Gateway de Aplicação e o Equilíbrio de Carga Azure?

Application Gateway é um equilibrista de carga de camada 7, o que significa que funciona apenas com tráfego web (HTTP, HTTPS, WebSocket e HTTP/2). Suporta capacidades como a rescisão de TLS, a finidade de sessão baseada em cookies e robin redondo para tráfego de equilíbrio de carga. O balanço de carga equilibra o tráfego na camada 4 (TCP ou UDP).

### <a name="what-protocols-does-application-gateway-support"></a>Que protocolos suporta o Application Gateway?

O Gateway de aplicações suporta HTTP, HTTPS, HTTP/2 e WebSocket.

### <a name="how-does-application-gateway-support-http2"></a>Como é que o Application Gateway suporta HTTP/2?

Consulte [o suporte HTTP/2](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http2-support).

### <a name="what-resources-are-supported-as-part-of-a-backend-pool"></a>Que recursos são apoiados como parte de uma piscina de backend?

Consulte [os recursos suportados.](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#backend-pools)

### <a name="in-what-regions-is-application-gateway-available"></a>Em que regiões está disponível o Application Gateway?

Application Gateway está disponível em todas as regiões do Azure global. Também está disponível no [Azure China 21Vianet](https://www.azure.cn/) e [Azure Government.](https://azure.microsoft.com/overview/clouds/government/)

### <a name="is-this-deployment-dedicated-for-my-subscription-or-is-it-shared-across-customers"></a>Esta implementação é dedicada à minha subscrição, ou é partilhada entre os clientes?

Application Gateway é uma implementação dedicada na sua rede virtual.

### <a name="does-application-gateway-support-http-to-https-redirection"></a>O Gateway de Aplicação suporta a reorientação http-to-HTTPS?

A reorientação é apoiada. Consulte a [visão geral do redirecionamento](application-gateway-redirect-overview.md)do Gateway de aplicações.

### <a name="in-what-order-are-listeners-processed"></a>Em que ordem os ouvintes são processados?

Consulte a [ordem de processamento do ouvinte](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-listeners).

### <a name="where-do-i-find-the-application-gateway-ip-and-dns"></a>Onde encontro o Gateway IP e o DNS?

Se estiver a utilizar um endereço IP público como ponto final, encontrará a informação IP e DNS no recurso de endereço IP público. Ou encontre-o no portal, na página geral da porta de aplicação. Se estiver a utilizar endereços IP internos, encontre as informações na página de visão geral.

Para o v2 SKU, abra o recurso IP público e **selecione Configuração**. O campo de etiqueta de **nome DNS (opcional)** está disponível para configurar o nome DNS.

### <a name="what-are-the-settings-for-keep-alive-timeout-and-tcp-idle-timeout"></a>Quais são as definições para o tempo limite de Keep-Alive e para o tempo de paragem do TCP?

*O tempo limite de tempo keep-Alive* rege o tempo que o Gateway de Aplicação irá esperar que um cliente envie outro pedido http numa ligação persistente antes de reutilizá-lo ou fechá-lo. *O tempo inativo da TCP* rege o tempo de duração de uma ligação TCP em caso de não atividade. 

O *tempo de tempo de Keep-Alive* no Gateway de aplicação v1 SKU é de 120 segundos e no V2 SKU são 75 segundos. O tempo de paragem inativa do *TCP* é um padrão de 4 minutos no IP virtual frontend (VIP) tanto v1 como v2 SKU do Application Gateway. Não pode mudar estes valores.

### <a name="does-the-ip-or-dns-name-change-over-the-lifetime-of-the-application-gateway"></a>O nome IP ou DNS muda ao longo da vida útil do gateway de aplicação?

No Gateway V1 SKU, o VIP pode alterar-se se parar e iniciar o gateway da aplicação. Mas o nome DNS associado à porta de entrada de aplicações não muda ao longo da vida útil do portal. Como o nome DNS não muda, deve utilizar um pseudónimo CNAME e apontá-lo para o endereço DNS do gateway da aplicação. No Gateway V2 SKU, pode definir o endereço IP como estático, para que o nome IP e DNS não se alterem ao longo da vida útil do gateway da aplicação. 

### <a name="does-application-gateway-support-static-ip"></a>O Gateway de Aplicação suporta IP estático?

Sim, o Gateway de aplicação v2 SKU suporta endereços IP públicos estáticos. O V1 SKU suporta iPs internos estáticos.

### <a name="does-application-gateway-support-multiple-public-ips-on-the-gateway"></a>O Application Gateway suporta vários IPs públicos na porta de entrada?

Um gateway de aplicação suporta apenas um endereço IP público.

### <a name="how-large-should-i-make-my-subnet-for-application-gateway"></a>Qual o tamanho que devo fazer a minha subnet para o Application Gateway?

Consulte considerações de tamanho da [subnet Gateway](https://docs.microsoft.com/azure/application-gateway/configuration-overview#size-of-the-subnet)de aplicação .

### <a name="can-i-deploy-more-than-one-application-gateway-resource-to-a-single-subnet"></a>Posso implantar mais do que um recurso de Gateway de aplicação para uma única sub-rede?

Sim. Além de múltiplos casos de uma determinada implementação do Application Gateway, pode fornecer outro recurso exclusivo do Application Gateway a uma subnet existente que contém um recurso gateway de aplicação diferente.

Uma única sub-rede não suporta tanto Standard_v2 como Gateway de Aplicação Padrão juntos.

### <a name="does-application-gateway-v2-support-user-defined-routes-udr"></a>O Gateway de aplicação v2 suporta rotas definidas pelo utilizador (UDR)?

Sim, mas apenas cenários específicos. Para mais informações, consulte a visão geral da [configuração do Gateway](configuration-overview.md#user-defined-routes-supported-on-the-application-gateway-subnet)de aplicação .

### <a name="does-application-gateway-support-x-forwarded-for-headers"></a>O Application Gateway suporta cabeçalhos x-forward-for?

Sim. Consulte [modificações num pedido](https://docs.microsoft.com/azure/application-gateway/how-application-gateway-works#modifications-to-the-request).

### <a name="how-long-does-it-take-to-deploy-an-application-gateway-will-my-application-gateway-work-while-its-being-updated"></a>Quanto tempo demora a implementar uma porta de aplicação? A minha candidatura funcionará enquanto está a ser atualizada?

As novas implementações do Application Gateway v1 SKU podem demorar até 20 minutos até ao fornecimento. As alterações ao tamanho ou contagem de casos não são disruptivas, e o gateway permanece ativo durante este tempo.

A maioria das implementações que usam o V2 SKU demoram cerca de 6 minutos a fornecer. No entanto, pode demorar mais tempo dependendo do tipo de implantação. Por exemplo, as implementações em várias Zonas de Disponibilidade com muitos casos podem demorar mais de 6 minutos. 

### <a name="can-i-use-exchange-server-as-a-backend-with-application-gateway"></a>Posso usar o Exchange Server como backend com o Application Gateway?

Não. Application Gateway não suporta protocolos de e-mail como SMTP, IMAP e POP3.

### <a name="is-there-guidance-available-to-migrate-from-the-v1-sku-to-the-v2-sku"></a>Existe orientação disponível para migrar do V1 SKU para o V2 SKU?

Sim. Para mais detalhes, [Migrate Azure Application Gateway e Web Application Firewall de v1 a v2](migrate-v1-v2.md).

### <a name="will-the-application-gateway-v1-sku-continue-to-be-supported"></a>O Gateway de aplicação v1 SKU continuará a ser apoiado?

Sim. O Gateway de aplicação v1 SKU continuará a ser suportado. No entanto, recomenda-se vivamente que se mude para v2 para tirar partido das atualizações de funcionalidades nesse SKU. Para mais informações, consulte [Autoscaling e Zone-redundantE Application Gateway v2](application-gateway-autoscaling-zone-redundant.md).

### <a name="does-application-gateway-v2-support-proxying-requests-with-ntlm-authentication"></a>O Application Gateway V2 suporta pedidos de procuração com autenticação NTLM?

Não. Aplicação Gateway V2 ainda não suporta pedidos de procuração com autenticação NTLM.

### <a name="does-application-gateway-affinity-cookie-support-samesite-attribute"></a>O cookie de afinidade Gateway application suporta o atributo sameSite?
Sim, a [atualização v80](https://chromiumdash.appspot.com/schedule) do [navegador Chromium](https://www.chromium.org/Home) introduziu um mandato em cookies HTTP sem atributo sameSite para ser tratado como SameSite=Lax. Isto significa que o cookie de afinidade Do Gateway da Aplicação não será enviado pelo navegador num contexto de terceiros. 

Para suportar este cenário, o Application Gateway injeta outro cookie chamado *ApplicationGatewayAffinityCORS* para além do cookie *ApplicationGatewayAffinity* existente.  Estes cookies são similares, mas o cookie *ApplicationGatewayAffinityCORS* tem mais dois atributos adicionados: *SameSite=None; Seguro.* Estes atributos mantêm sessões pegajosas mesmo para pedidos de origem cruzada. Consulte a secção de [afinidade baseada em cookies](configuration-overview.md#cookie-based-affinity) para obter mais informações.

## <a name="performance"></a>Desempenho

### <a name="how-does-application-gateway-support-high-availability-and-scalability"></a>Como é que o Application Gateway suporta alta disponibilidade e escalabilidade?

O Application Gateway v1 SKU suporta cenários de alta disponibilidade quando já implementou duas ou mais instâncias. O Azure distribui estas instâncias através de domínios de atualização e falhas para garantir que as instâncias não falham todas ao mesmo tempo. O V1 SKU suporta a escalabilidade adicionando múltiplas instâncias da mesma porta de entrada para partilhar a carga.

O V2 SKU garante automaticamente que novas instâncias são distribuídas por domínios de falhas e domínios de atualização. Se escolher a redundância da zona, as instâncias mais recentes também estão espalhadas por zonas de disponibilidade para oferecer resiliência de falha zonal.

### <a name="how-do-i-achieve-a-dr-scenario-across-datacenters-by-using-application-gateway"></a>Como posso alcançar um cenário de DR através dos datacenters utilizando o Application Gateway?

Utilize o Gestor de Tráfego para distribuir o tráfego através de vários gateways de aplicações em diferentes datacenters.

### <a name="does-application-gateway-support-autoscaling"></a>O Application Gateway suporta a autoscalcificação?

Sim, o Application Gateway v2 SKU suporta a autoscalcificação. Para mais informações, consulte [Autoscaling e Zone-redundantE Application Gateway](application-gateway-autoscaling-zone-redundant.md).

### <a name="does-manual-or-automatic-scale-up-or-scale-down-cause-downtime"></a>A escala manual ou automática para cima ou para baixo causa tempo de inatividade?

Não. As instâncias são distribuídas por domínios de upgrade e domínios de avaria.

### <a name="does-application-gateway-support-connection-draining"></a>O Suporte do Gateway de Aplicação é a drenagem da ligação?

Sim. Pode configurar a drenagem da ligação para alterar os membros dentro de uma piscina de backend sem interrupções. Para mais informações, consulte [a secção de drenagem de ligação do Gateway de Aplicação](features.md#connection-draining).

### <a name="can-i-change-instance-size-from-medium-to-large-without-disruption"></a>Posso mudar o tamanho da instância de médio para grande sem perturbações?

Sim.

## <a name="configuration"></a>Configuração

### <a name="is-application-gateway-always-deployed-in-a-virtual-network"></a>O Application Gateway está sempre implantado numa rede virtual?

Sim. O Application Gateway é sempre implantado numa subnet de rede virtual. Esta sub-rede só pode conter gateways de aplicação. Para obter mais informações, consulte os requisitos da [rede virtual e da sub-rede.](https://docs.microsoft.com/azure/application-gateway/configuration-overview#azure-virtual-network-and-dedicated-subnet)

### <a name="can-application-gateway-communicate-with-instances-outside-of-its-virtual-network-or-outside-of-its-subscription"></a>O Application Gateway pode comunicar com instâncias fora da sua rede virtual ou fora da sua subscrição?

Enquanto tiver conectividade IP, o Application Gateway pode comunicar com instâncias fora da rede virtual em que se está. O Application Gateway também pode comunicar com instâncias fora da subscrição em que se está. Se planeia utilizar os IPs internos como membros do pool backend, utilize [o peering de rede virtual](../virtual-network/virtual-network-peering-overview.md) ou o [Azure VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="can-i-deploy-anything-else-in-the-application-gateway-subnet"></a>Posso colocar mais alguma coisa na sub-rede de porta de aplicação?

Não. Mas pode implantar outros gateways de aplicação na sub-rede.

### <a name="are-network-security-groups-supported-on-the-application-gateway-subnet"></a>Os grupos de segurança da rede são apoiados na subnet de gateway de aplicação?

Consulte [os grupos de segurança da rede na subnet Application Gateway](https://docs.microsoft.com/azure/application-gateway/configuration-overview#network-security-groups-on-the-application-gateway-subnet).

### <a name="does-the-application-gateway-subnet-support-user-defined-routes"></a>A sub-rede de gateway da aplicação suporta rotas definidas pelo utilizador?

Consulte [as rotas definidas pelo utilizador suportadas na subnet Gateway da aplicação](https://docs.microsoft.com/azure/application-gateway/configuration-overview#user-defined-routes-supported-on-the-application-gateway-subnet).

### <a name="what-are-the-limits-on-application-gateway-can-i-increase-these-limits"></a>Quais são os limites para o Gateway de Aplicação? Posso aumentar estes limites?

Ver limites de Gateway de [Aplicação](../azure-resource-manager/management/azure-subscription-service-limits.md#application-gateway-limits).

### <a name="can-i-simultaneously-use-application-gateway-for-both-external-and-internal-traffic"></a>Posso simultaneamente utilizar o Application Gateway para tráfego externo e interno?

Sim. O Gateway de aplicação suporta um IP interno e um IP externo por gateway de aplicação.

### <a name="does-application-gateway-support-virtual-network-peering"></a>O Application Gateway suporta o peering virtual da rede?

Sim. O peering de rede virtual ajuda a equilibrar o tráfego em outras redes virtuais.

### <a name="can-i-talk-to-on-premises-servers-when-theyre-connected-by-expressroute-or-vpn-tunnels"></a>Posso falar com servidores no local quando estiverem ligados por túneis ExpressRoute ou VPN?

Sim, desde que o trânsito seja permitido.

### <a name="can-one-backend-pool-serve-many-applications-on-different-ports"></a>Pode uma piscina de backend servir muitas aplicações em diferentes portas?

A arquitetura de microserviços é apoiada. Para sondar em diferentes portas, é necessário configurar várias definições de HTTP.

### <a name="do-custom-probes-support-wildcards-or-regex-on-response-data"></a>As sondas personalizadas suportam wildcards ou regex em dados de resposta?

Não. 

### <a name="how-are-routing-rules-processed-in-application-gateway"></a>Como são processadas as regras de encaminhamento no Gateway de Aplicação?

Consulte [as regras de processamento da Ordem.](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-rules)

### <a name="for-custom-probes-what-does-the-host-field-signify"></a>Para sondas personalizadas, o que significa o campo host?

O campo anfitrião especifica o nome para enviar a sonda quando configurar o multisite no Gateway da Aplicação. Caso contrário, utilize '127.0.0.1'. Este valor é diferente do nome de anfitrião da máquina virtual. O seu \<\>formato\>\<é\>\<\>protocolo ://\<anfitrião : caminho portuário .

### <a name="can-i-allow-application-gateway-access-to-only-a-few-source-ip-addresses"></a>Posso permitir o acesso do Application Gateway a apenas alguns endereços IP de origem?

Sim. Consulte [restringir o acesso a IPs de origem específica](https://docs.microsoft.com/azure/application-gateway/configuration-overview#allow-application-gateway-access-to-a-few-source-ips).

### <a name="can-i-use-the-same-port-for-both-public-facing-and-private-facing-listeners"></a>Posso usar o mesmo porto para ouvintes virados para o público e para os privados?

Não.

### <a name="does-application-gateway-support-ipv6"></a>O Gateway de Aplicação suporta o IPv6?

O Gateway v2 de aplicação não suporta atualmente o IPv6. Pode funcionar numa vNet de dupla pilha usando apenas IPv4, mas a subnet gateway deve ser apenas IPv4. Application Gateway v1 não suporta VNets de dupla pilha. 

### <a name="how-do-i-use-application-gateway-v2-with-only-private-frontend-ip-address"></a>Como uso o Application Gateway V2 com apenas endereço IP frontend privado?

O Gateway V2 da aplicação não suporta atualmente apenas o modo IP privado. Suporta as seguintes combinações
* IP privado e IP público
* IP público apenas

Mas se quiser utilizar o Application Gateway V2 apenas com IP privado, pode seguir o processo abaixo:
1. Criar um Gateway de Aplicação com endereço IP frontend público e privado
2. Não forneça os seus ouvintes para o endereço IP frontal público. Application Gateway não ouvirá qualquer tráfego no endereço IP público se não forem criados ouvintes para o mesmo.
3. Criar e anexar um Grupo de Segurança de [Rede](https://docs.microsoft.com/azure/virtual-network/security-overview) para a sub-rede gateway de aplicação com a seguinte configuração na ordem de prioridade:
    
    a. Permita o tráfego da Source como etiqueta de serviço **GatewayManager** e Destino como porta **De qualquer** e Destino como **65200-65535**. Esta gama portuária é necessária para a comunicação da infraestrutura Azure. Estas portas estão protegidas (bloqueadas) por autenticação de certificado. Entidades externas, incluindo os administradores de utilizadores gateway, não podem iniciar alterações nesses pontos finais sem certificados apropriados em vigor
    
    b. Permitir o tráfego da Source como etiqueta de serviço **AzureLoadBalancer** e destino e porta de destino como **qualquer**
    
    c. Negue todo o tráfego de entrada da Fonte como etiqueta de serviço de **Internet** e destino e porta de destino como **Qualquer**. Dar a esta regra a *menor prioridade* nas regras de entrada
    
    d. Mantenha as regras padrão como permitir a entrada da VirtualNetwork para que o acesso no endereço IP privado não seja bloqueado
    
    e. A conectividade da Internet não pode ser bloqueada. Caso contrário, enfrentará problemas com a exploração madeireira, métricas, etc.

Configuração DE NSG de ![amostra para acesso IP privado apenas: Configuração NSG do Gateway de aplicação para acesso IP privado apenas](./media/application-gateway-faq/appgw-privip-nsg.png)

## <a name="configuration---tls"></a>Configuração - TLS

### <a name="what-certificates-does-application-gateway-support"></a>Que certificados suporta o Application Gateway?

Application Gateway suporta certificados auto-assinados, certificados de autoridade de certificados (CA), certificados de Validação Estendida (EV) e certificados wildcard.

### <a name="what-cipher-suites-does-application-gateway-support"></a>Que suítes cifra saem as suites application Gateway?

Application Gateway suporta as seguintes suítes cifra. 

- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_CBC_SHA
- TLS_DHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_3DES_EDE_CBC_SHA
- TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA

Para obter informações sobre como personalizar as opções tLS, consulte [versões políticas De Configuração TLS e suítes cifra no Gateway](application-gateway-configure-ssl-policy-powershell.md)de Aplicação .

### <a name="does-application-gateway-support-reencryption-of-traffic-to-the-backend"></a>O Application Gateway suporta a reencriptação do tráfego até ao fim?

Sim. Application Gateway suporta a descarga tLS e tLS de ponta a ponta, que reencriptam o tráfego para o backend.

### <a name="can-i-configure-tls-policy-to-control-tls-protocol-versions"></a>Posso configurar a política do TLS para controlar as versões de protocolo TLS?

Sim. Pode configurar o Gateway de Aplicação para negar TLS1.0, TLS1.1 e TLS1.2. Por predefinição, sSL 2.0 e 3.0 já estão desativados e não são configuráveis.

### <a name="can-i-configure-cipher-suites-and-policy-order"></a>Posso configurar suítes cifrae e ordem política?

Sim. No Gateway da Aplicação, pode [configurar suites de cifra.](application-gateway-ssl-policy-overview.md) Para definir uma política personalizada, ative pelo menos uma das seguintes suítes cifra. 

* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_256_CBC_SHA256
* TLS_RSA_WITH_AES_128_CBC_SHA256

Application Gateway usa SHA256 para gestão de backend.

### <a name="how-many-tlsssl-certificates-does-application-gateway-support"></a>Quantos certificados TLS/SSL suportam o Application Gateway?

O Gateway de aplicação suporta até 100 certificados TLS/SSL.

### <a name="how-many-authentication-certificates-for-backend-reencryption-does-application-gateway-support"></a>Quantos certificados de autenticação para reencriptação de backend suporta o Application Gateway?

O Gateway de aplicação suporta até 100 certificados de autenticação.

### <a name="does-application-gateway-natively-integrate-with-azure-key-vault"></a>O Application Gateway integra-se de forma nativa com o Cofre chave Azure?

Sim, o Gateway de aplicação v2 SKU suporta o Cofre chave. Para mais informações, consulte a [rescisão de TLS com certificados key vault](key-vault-certs.md).

### <a name="how-do-i-configure-https-listeners-for-com-and-net-sites"></a>Como configurar os ouvintes HTTPS para sites .com e .net? 

Para vários encaminhamentobaseados em domínio (baseados em hospedeiros), pode criar ouvintes multilocais, configurar ouvintes que usam HTTPS como protocolo, e associar os ouvintes às regras de encaminhamento. Para mais informações, consulte [hospedar vários sites utilizando o Application Gateway](https://docs.microsoft.com/azure/application-gateway/multiple-site-overview).

### <a name="can-i-use-special-characters-in-my-pfx-file-password"></a>Posso usar caracteres especiais na minha senha de ficheiro .pfx?

Não, utilize apenas caracteres alfanuméricos na sua senha de ficheiro .pfx.

## <a name="configuration---ingress-controller-for-aks"></a>Configuração - controlador de ingresso para AKS

### <a name="what-is-an-ingress-controller"></a>O que é um Controlador de Ingress?

Kubernetes permite `deployment` a `service` criação e recurso para expor um grupo de cápsulas internamente no cluster. Para expor o mesmo serviço [`Ingress`](https://kubernetes.io/docs/concepts/services-networking/ingress/) externamente, é definido um recurso que proporciona o equilíbrio de carga, a rescisão de TLS e o alojamento virtual baseado em nomes.
Para satisfazer `Ingress` este recurso, é necessário um Controlador De `Ingress` Ingress que ouça quaisquer alterações aos recursos e configura as políticas de equilíbrio de carga.

O controlador de entrada de entrada de gateway de aplicação permite que o Portal de [Aplicações Azure](https://azure.microsoft.com/services/application-gateway/) seja usado como entrada para um [Serviço Azure Kubernetes](https://azure.microsoft.com/services/kubernetes-service/) também conhecido como cluster AKS.

### <a name="can-a-single-ingress-controller-instance-manage-multiple-application-gateways"></a>Um único controlador de ingresso pode gerir vários Gateways de Aplicação?

Atualmente, um caso de Controlador Ingress só pode ser associado a um Gateway de aplicação.

## <a name="diagnostics-and-logging"></a>Diagnóstico e registos

### <a name="what-types-of-logs-does-application-gateway-provide"></a>Que tipo de registos o Gateway aplicação fornece?

O Gateway de Aplicação fornece três registos: 

* **ApplicationGatewayAccessLog**: O registo de acesso contém cada pedido submetido ao frontend do gateway da aplicação. Os dados incluem o IP do chamador, URL solicitado, latência de resposta, código de devolução e bytes dentro e fora. Contém um registo por porta de aplicação.
* **ApplicationGatewayPerformanceLog**: O registo de desempenho capta informações de desempenho para cada gateway de aplicação. A informação inclui a entrada em bytes, pedidos totais servidos, contagem de pedidos falhada, e contagem de instâncias de backend saudável e pouco saudável.
* **ApplicationGatewayFirewallLog**: Para os gateways de aplicação que configura com WAF, o registo de firewall contém pedidos que são registados através do modo de deteção ou do modo de prevenção.

Todos os registos são recolhidos a cada 60 segundos. Para mais informações, consulte [backend saúde, registos de diagnóstico e métricas para Application Gateway](application-gateway-diagnostics.md).

### <a name="how-do-i-know-if-my-backend-pool-members-are-healthy"></a>Como sei se os meus membros da piscina são saudáveis?

Verifique a saúde utilizando o `Get-AzApplicationGatewayBackendHealth` cmdlet PowerShell ou o portal. Para mais informações, consulte [diagnósticos de Gateway](application-gateway-diagnostics.md)de aplicação .

### <a name="whats-the-retention-policy-for-the-diagnostic-logs"></a>Qual é a política de retenção dos registos de diagnóstico?

Os registos de diagnóstico fluem para a conta de armazenamento do cliente. Os clientes podem definir a política de retenção com base na sua preferência. Os registos de diagnóstico também podem ser enviados para um centro de eventos ou registos do Monitor Azure. Para mais informações, consulte [diagnósticos de Gateway](application-gateway-diagnostics.md)de aplicação .

### <a name="how-do-i-get-audit-logs-for-application-gateway"></a>Como recebo registos de auditoria para o Gateway de Aplicação?

No portal, na lâmina do menu de um gateway de aplicação, selecione **Registo de Atividades** para aceder ao registo de auditoria. 

### <a name="can-i-set-alerts-with-application-gateway"></a>Posso definir alertas com o Gateway de Aplicação?

Sim. No Gateway da Aplicação, os alertas são configurados nas métricas. Para mais informações, consulte [as métricas do Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics) e receba [notificações](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)de alerta .

### <a name="how-do-i-analyze-traffic-statistics-for-application-gateway"></a>Como analiso as estatísticas de tráfego para o Gateway de Aplicação?

Pode ver e analisar os registos de acesso de várias maneiras. Utilize registos do Monitor Azure, Excel, Power BI, e assim por diante.

Também pode usar um modelo de Gestor de Recursos que instala e executa o popular analisador de log [GoAccess](https://goaccess.io/) para registos de acesso gateway de aplicação. O GoAccess fornece estatísticas de tráfego http valiosas, tais como visitantes únicos, ficheiros solicitados, anfitriões, sistemas operativos, navegadores e códigos de estado HTTP. Para mais informações, no GitHub, consulte o [ficheiro Readme na pasta do modelo Do Gestor de Recursos](https://aka.ms/appgwgoaccessreadme).

### <a name="what-could-cause-backend-health-to-return-an-unknown-status"></a>O que poderia fazer com que a saúde de retribua para devolver um estatuto desconhecido?

Normalmente, vê-se um estado desconhecido quando o acesso ao backend é bloqueado por um grupo de segurança de rede (NSG), DNS personalizado ou encaminhamento definido pelo utilizador (UDR) na subnet de gateway da aplicação. Para mais informações, consulte [Backend health, diagnósticos de registo e métricas para Application Gateway](application-gateway-diagnostics.md).

### <a name="is-there-any-case-where-nsg-flow-logs-wont-show-allowed-traffic"></a>Há algum caso em que os registos de fluxo da NSG não apareçam no tráfego permitido?

Sim. Se a sua configuração corresponder ao cenário seguinte, não verá o tráfego permitido nos seus registos de fluxo NSG:
- Implementou o Application Gateway v2
- Tem um NSG na subnet de gateway de aplicação
- Você ativou registos de fluxo NSG em que NSG

### <a name="how-do-i-use-application-gateway-v2-with-only-private-frontend-ip-address"></a>Como uso o Application Gateway V2 com apenas endereço IP frontend privado?

O Gateway V2 da aplicação não suporta atualmente apenas o modo IP privado. Suporta as seguintes combinações
* IP privado e IP público
* IP público apenas

Mas se quiser utilizar o Application Gateway V2 apenas com IP privado, pode seguir o processo abaixo:
1. Criar um Gateway de Aplicação com endereço IP frontend público e privado
2. Não crie nenhum ouvinte para o endereço IP frontal público. Application Gateway não ouvirá qualquer tráfego no endereço IP público se não forem criados ouvintes para o mesmo.
3. Criar e anexar um Grupo de Segurança de [Rede](https://docs.microsoft.com/azure/virtual-network/security-overview) para a sub-rede gateway de aplicação com a seguinte configuração na ordem de prioridade:
    
    a. Permita o tráfego da Source como etiqueta de serviço **GatewayManager** e Destino como porta **De qualquer** e Destino como **65200-65535**. Esta gama portuária é necessária para a comunicação da infraestrutura Azure. Estas portas estão protegidas (bloqueadas) por autenticação de certificado. Entidades externas, incluindo os administradores de utilizadores gateway, não podem iniciar alterações nesses pontos finais sem certificados apropriados em vigor
    
    b. Permitir o tráfego da Source como etiqueta de serviço **AzureLoadBalancer** e porta de destino como **Qualquer**
    
    c. Negue todo o tráfego de entrada da Fonte como etiqueta de serviço de **Internet** e porta de destino como **Qualquer**. Dar a esta regra a *menor prioridade* nas regras de entrada
    
    d. Mantenha as regras padrão como permitir a entrada da VirtualNetwork para que o acesso no endereço IP privado não seja bloqueado
    
    e. A conectividade da Internet não pode ser bloqueada. Caso contrário, enfrentará problemas com a exploração madeireira, métricas, e assim por diante.

Configuração DE NSG de ![amostra para acesso IP privado apenas: Configuração NSG do Gateway de aplicação para acesso IP privado apenas](./media/application-gateway-faq/appgw-privip-nsg.png)

### <a name="does-application-gateway-affinity-cookie-support-samesite-attribute"></a>O cookie de afinidade Gateway application suporta o atributo sameSite?
Sim, a [atualização v80](https://chromiumdash.appspot.com/schedule) do [navegador Chromium](https://www.chromium.org/Home) introduziu um mandato em cookies HTTP sem atributo sameSite para ser tratado como SameSite=Lax. Isto significa que o cookie de afinidade Do Gateway da Aplicação não será enviado pelo navegador num contexto de terceiros. Para suportar este cenário, o Application Gateway injeta outro cookie chamado *ApplicationGatewayAffinityCORS* para além do cookie *ApplicationGatewayAffinity* existente.  Estes cookies são similares, mas o cookie *ApplicationGatewayAffinityCORS* tem mais dois atributos adicionados: *SameSite=None; Seguro.* Estes atributos mantêm sessões pegajosas mesmo para pedidos de origem cruzada. Consulte a secção de [afinidade baseada em cookies](configuration-overview.md#cookie-based-affinity) para obter mais informações.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o Gateway de Aplicação, veja o que é o Portal de [Aplicação Azure?](overview.md)
