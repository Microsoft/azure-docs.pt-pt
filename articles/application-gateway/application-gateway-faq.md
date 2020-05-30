---
title: Perguntas frequentes sobre gateway de aplicação Azure
description: Encontre respostas para perguntas frequentes sobre O Gateway de Aplicações Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 05/26/2020
ms.author: victorh
ms.custom: references_regions
ms.openlocfilehash: e61ce629e723f56524ee22d8b127243f9568a835
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/29/2020
ms.locfileid: "84196490"
---
# <a name="frequently-asked-questions-about-application-gateway"></a>Perguntas frequentes sobre Gateway de Aplicação

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Seguem-se perguntas comuns sobre o Gateway de Aplicação Azure.

## <a name="general"></a>Geral

### <a name="what-is-application-gateway"></a>O que é o Gateway de Aplicação?

O Azure Application Gateway fornece um controlador de entrega de aplicações (ADC) como um serviço. Oferece várias capacidades de equilíbrio de carga de camada 7 para as suas aplicações. Este serviço é altamente disponível, escalável e totalmente gerido pela Azure.

### <a name="what-features-does-application-gateway-support"></a>Quais as funcionalidades que o Application Gateway suporta?

O Application Gateway suporta a autoscalagem, a descarga de TLS e o TLS de ponta a ponta, uma firewall de aplicação web (WAF), afinidade de sessão baseada em cookies, encaminhamento baseado em caminhos de URL, hospedagem multissíora e outras funcionalidades. Para obter uma lista completa de funcionalidades suportadas, consulte [Introdução ao Gateway de Aplicações](application-gateway-introduction.md).

### <a name="how-do-application-gateway-and-azure-load-balancer-differ"></a>Como diferem o Gateway de Aplicação e o Balançador de Carga Azure?

Application Gateway é um equilibrador de carga de camada 7, o que significa que funciona apenas com tráfego web (HTTP, HTTPS, WebSocket e HTTP/2). Suporta capacidades como a rescisão de TLS, afinidade da sessão baseada em cookies e robin redondo para o tráfego de equilíbrio de carga. Carga Balanceador balance o tráfego na camada 4 (TCP ou UDP).

### <a name="what-protocols-does-application-gateway-support"></a>Que protocolos suporta o Application Gateway?

O Application Gateway suporta HTTP, HTTPS, HTTP/2 e WebSocket.

### <a name="how-does-application-gateway-support-http2"></a>Como suporta o Gateway de Aplicação HTTP/2?

Consulte [o suporte HTTP/2](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http2-support).

### <a name="what-resources-are-supported-as-part-of-a-backend-pool"></a>Que recursos são suportados como parte de uma piscina de backend?

Consulte [os recursos de backend suportados.](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#backend-pools)

### <a name="in-what-regions-is-application-gateway-available"></a>Em que regiões está disponível o Application Gateway?

Application Gateway está disponível em todas as regiões do Azure global. Também está disponível no [Azure China 21Vianet](https://www.azure.cn/) e [no Governo Azure.](https://azure.microsoft.com/overview/clouds/government/)

### <a name="is-this-deployment-dedicated-for-my-subscription-or-is-it-shared-across-customers"></a>Esta implementação é dedicada à minha subscrição, ou é partilhada entre os clientes?

Application Gateway é uma implementação dedicada na sua rede virtual.

### <a name="does-application-gateway-support-http-to-https-redirection"></a>O Application Gateway suporta a reorientação HTTP-to-HTTPS?

A reorientação é suportada. Consulte [a visão geral do gateway de aplicações](application-gateway-redirect-overview.md).

### <a name="in-what-order-are-listeners-processed"></a>Em que ordem são processados os ouvintes?

Consulte a [ordem de processamento do ouvinte](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-listeners).

### <a name="where-do-i-find-the-application-gateway-ip-and-dns"></a>Onde encontro o Application Gateway IP e o DNS?

Se estiver a utilizar um endereço IP público como ponto final, encontrará as informações ip e DNS no recurso de endereço IP público. Ou encontre-o no portal, na página geral para o gateway de aplicações. Se estiver a utilizar endereços IP internos, encontre as informações na página geral.

Para o v2 SKU, abra o recurso IP público e selecione **Configuração**. O campo **de marca DNS (opcional)** está disponível para configurar o nome DNS.

### <a name="what-are-the-settings-for-keep-alive-timeout-and-tcp-idle-timeout"></a>Quais são as definições para o tempo limite keep-alive e para o intervalo de tempo do TCP?

*O tempo limite keep-alive* rege quanto tempo o Gateway de aplicação aguardará que um cliente envie outro pedido HTTP sobre uma ligação persistente antes de o reutilizar ou fechar. *O tempo limite de inatividade da TCP* regula o tempo de duração da ligação TCP em caso de não atividade. 

O *tempo limite Keep-Alive* no Application Gateway v1 SKU é de 120 segundos e no V2 SKU são 75 segundos. O *tempo limite de marcha lenta (TCP)* é um padrão de 4 minutos no IP virtual frontal (VIP) de v1 e v2 SKU do Gateway de aplicação. Não pode mudar estes valores.

### <a name="does-the-ip-or-dns-name-change-over-the-lifetime-of-the-application-gateway"></a>O nome IP ou DNS muda ao longo do tempo de vida do gateway de aplicações?

No Gateway de Aplicação V1 SKU, o VIP pode ser alterado se parar e iniciar o gateway de aplicações. Mas o nome DNS associado ao gateway de aplicações não muda ao longo da vida do portal. Como o nome DNS não muda, deve usar um pseudónimo CNAME e apontá-lo para o endereço DNS do gateway de aplicações. No Gateway de Aplicação V2 SKU, pode definir o endereço IP como estático, pelo que o nome IP e DNS não será alterado ao longo do tempo de vida útil do gateway de aplicações. 

### <a name="does-application-gateway-support-static-ip"></a>O Application Gateway suporta IP estático?

Sim, o Application Gateway v2 SKU suporta endereços IP públicos estáticos. O V1 SKU suporta iPs internos estáticos.

### <a name="does-application-gateway-support-multiple-public-ips-on-the-gateway"></a>O Application Gateway suporta vários IPs públicos no gateway?

Uma porta de aplicação suporta apenas um endereço IP público.

### <a name="how-large-should-i-make-my-subnet-for-application-gateway"></a>Qual o tamanho que devo fazer na minha sub-rede para Application Gateway?

Consulte [considerações de tamanho da sub-rede do Gateway de aplicação](https://docs.microsoft.com/azure/application-gateway/configuration-overview#size-of-the-subnet).

### <a name="can-i-deploy-more-than-one-application-gateway-resource-to-a-single-subnet"></a>Posso implantar mais de um recurso Application Gateway numa única sub-rede?

Sim. Além de múltiplas instâncias de uma determinada implementação do Gateway de Aplicação, pode providenciar outro recurso exclusivo do Application Gateway para uma sub-rede existente que contenha um recurso de Gateway de aplicação diferente.

Uma única sub-rede não suporta tanto os SKUs de gateway de aplicação v2 como v1.

### <a name="does-application-gateway-v2-support-user-defined-routes-udr"></a>O Application Gateway v2 suporta rotas definidas pelo utilizador (UDR)?

Sim, mas apenas cenários específicos. Para obter mais informações, consulte a [visão geral da configuração do Gateway de Aplicação](configuration-overview.md#user-defined-routes-supported-on-the-application-gateway-subnet).

### <a name="does-application-gateway-support-x-forwarded-for-headers"></a>O Application Gateway suporta cabeçalhos x-forward-for?

Sim. Ver [Alterações a um pedido](https://docs.microsoft.com/azure/application-gateway/how-application-gateway-works#modifications-to-the-request).

### <a name="how-long-does-it-take-to-deploy-an-application-gateway-will-my-application-gateway-work-while-its-being-updated"></a>Quanto tempo demora a implantar um portal de aplicação? O meu portal de aplicações funcionará enquanto está a ser atualizado?

As novas implementações do Gateway v1 SKU podem demorar até 20 minutos até a provisão. As alterações ao tamanho ou contagem de casos não são disruptivas, e o gateway permanece ativo durante este tempo.

A maioria das implementações que utilizam o V2 SKU demoram cerca de 6 minutos a providenciar. No entanto, pode demorar mais tempo dependendo do tipo de implantação. Por exemplo, as implementações em várias Zonas de Disponibilidade com muitas instâncias podem demorar mais de 6 minutos. 

### <a name="can-i-use-exchange-server-as-a-backend-with-application-gateway"></a>Posso usar o Exchange Server como backend com o Gateway de Aplicações?

Não. Application Gateway não suporta protocolos de e-mail como SMTP, IMAP e POP3.

### <a name="is-there-guidance-available-to-migrate-from-the-v1-sku-to-the-v2-sku"></a>Existe orientação disponível para migrar do V1 SKU para o V2 SKU?

Sim. Para mais detalhes, [migrate Azure Application Gateway e Web Application Firewall de v1 a v2](migrate-v1-v2.md).

### <a name="will-the-application-gateway-v1-sku-continue-to-be-supported"></a>O Gateway de Aplicação v1 SKU continuará a ser suportado?

Sim. O Gateway de Aplicação v1 SKU continuará a ser suportado. No entanto, é fortemente recomendado que mude para v2 para aproveitar as atualizações de funcionalidades nesse SKU. Para obter mais informações, consulte [Autoscaling e Zone-redundante Application Gateway v2](application-gateway-autoscaling-zone-redundant.md).

### <a name="does-application-gateway-v2-support-proxying-requests-with-ntlm-authentication"></a>O Application Gateway V2 suporta pedidos de procuração com autenticação NTLM?

Não. O Gateway V2 de aplicação ainda não suporta pedidos de procuração com autenticação NTLM.

### <a name="does-application-gateway-affinity-cookie-support-samesite-attribute"></a>O cookie de afinidade do Portal de Aplicação suporta o atributo SameSite?
Sim, a [atualização v80](https://chromiumdash.appspot.com/schedule) do [navegador de Crómio](https://www.chromium.org/Home) introduziu um mandato em cookies HTTP sem atributo SameSite para ser tratado como SameSite=Lax. Isto significa que o cookie de afinidade Do Gateway de Aplicação não será enviado pelo navegador num contexto de terceiros. 

Para suportar este cenário, o Application Gateway injeta outro cookie chamado *ApplicationGatewayAffinityCORS,* além do cookie *applicationGatewayAffinity* existente.  Estes cookies são semelhantes, mas o cookie *ApplicationGatewayAffinityCORS* tem mais dois atributos adicionados: *SameSite=Nenhum; Seguro*. Estes atributos mantêm sessões pegajosas mesmo para pedidos de origem cruzada. Consulte a [secção de afinidade baseada em cookies](configuration-overview.md#cookie-based-affinity) para obter mais informações.

## <a name="performance"></a>Desempenho

### <a name="how-does-application-gateway-support-high-availability-and-scalability"></a>Como é que o Application Gateway suporta alta disponibilidade e escalabilidade?

O SKU Application Gateway v1 suporta cenários de alta disponibilidade quando implementou duas ou mais instâncias. O Azure distribui estas instâncias por domínios de atualização e falhas para garantir que as instâncias não falham todas ao mesmo tempo. O V1 SKU suporta a escalabilidade adicionando múltiplas instâncias da mesma porta de entrada para partilhar a carga.

O V2 SKU garante automaticamente que novas instâncias são espalhadas por domínios de falhas e domínios de atualização. Se optar pela redundância da zona, as instâncias mais recentes também estão espalhadas por zonas de disponibilidade para oferecer resiliência de falhas zonais.

### <a name="how-do-i-achieve-a-dr-scenario-across-datacenters-by-using-application-gateway"></a>Como posso alcançar um cenário DR entre centros de dados utilizando o Application Gateway?

Utilize o Traffic Manager para distribuir tráfego por vários gateways de aplicações em diferentes datacenters.

### <a name="does-application-gateway-support-autoscaling"></a>O Application Gateway suporta a autoscalagem?

Sim, o Gateway de Aplicação v2 SKU suporta autoscaling. Para obter mais informações, consulte [Autoscaling e Zone-redundante Application Gateway](application-gateway-autoscaling-zone-redundant.md).

### <a name="does-manual-or-automatic-scale-up-or-scale-down-cause-downtime"></a>A escala manual ou automática para cima ou para baixo causa tempo de inatividade?

Não. As instâncias são distribuídas por domínios de atualização e domínios de falhas.

### <a name="does-application-gateway-support-connection-draining"></a>O Application Gateway suporta a drenagem da ligação?

Sim. Pode configurar a drenagem de ligação para alterar os membros dentro de uma piscina de backend sem interrupções. Para obter mais informações, consulte [a secção de drenagem de ligação do Gateway de Aplicação](features.md#connection-draining).

### <a name="can-i-change-instance-size-from-medium-to-large-without-disruption"></a>Posso mudar o tamanho da instância de médio para grande sem perturbações?

Sim.

## <a name="configuration"></a>Configuração

### <a name="is-application-gateway-always-deployed-in-a-virtual-network"></a>O Application Gateway está sempre implantado numa rede virtual?

Sim. O Gateway de Aplicação é sempre implantado numa sub-rede de rede virtual. Esta sub-rede pode conter apenas portais de aplicação. Para obter mais informações, consulte [os requisitos de rede virtual e sub-rede.](https://docs.microsoft.com/azure/application-gateway/configuration-overview#azure-virtual-network-and-dedicated-subnet)

### <a name="can-application-gateway-communicate-with-instances-outside-of-its-virtual-network-or-outside-of-its-subscription"></a>A Application Gateway pode comunicar com casos fora da sua rede virtual ou fora da sua subscrição?

Enquanto tiver conectividade IP, o Application Gateway pode comunicar com casos fora da rede virtual em que se encontre. O Application Gateway também pode comunicar com casos fora da subscrição em que está. Se pretender utilizar os IPs internos como membros do pool backend, utilize [o espreitamento de rede virtual](../virtual-network/virtual-network-peering-overview.md) ou o Gateway [Azure VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="can-i-deploy-anything-else-in-the-application-gateway-subnet"></a>Posso colocar mais alguma coisa na sub-rede de gateway de aplicação?

Não. Mas pode implementar outros gateways de aplicações na sub-rede.

### <a name="are-network-security-groups-supported-on-the-application-gateway-subnet"></a>Os grupos de segurança da rede são suportados na sub-rede de gateway de aplicação?

Consulte [os grupos de segurança da rede na sub-rede 'Gateway' de aplicação.](https://docs.microsoft.com/azure/application-gateway/configuration-overview#network-security-groups-on-the-application-gateway-subnet)

### <a name="does-the-application-gateway-subnet-support-user-defined-routes"></a>A sub-rede de gateway de aplicação suporta rotas definidas pelo utilizador?

Consulte [as rotas definidas pelo utilizador suportadas na sub-rede Do Gateway de Aplicação](https://docs.microsoft.com/azure/application-gateway/configuration-overview#user-defined-routes-supported-on-the-application-gateway-subnet).

### <a name="what-are-the-limits-on-application-gateway-can-i-increase-these-limits"></a>Quais são os limites do Gateway de Aplicação? Posso aumentar estes limites?

Ver [limites de Gateway de Aplicação](../azure-resource-manager/management/azure-subscription-service-limits.md#application-gateway-limits).

### <a name="can-i-simultaneously-use-application-gateway-for-both-external-and-internal-traffic"></a>Posso utilizar simultaneamente o Gateway de Aplicação para tráfego externo e interno?

Sim. O Gateway de Aplicação suporta um IP interno e um IP externo por gateway de aplicação.

### <a name="does-application-gateway-support-virtual-network-peering"></a>O Application Gateway suporta o espreitamento da rede virtual?

Sim. O espreitamento de rede virtual ajuda o tráfego de equilíbrio de carga em outras redes virtuais.

### <a name="can-i-talk-to-on-premises-servers-when-theyre-connected-by-expressroute-or-vpn-tunnels"></a>Posso falar com servidores no local quando estiverem ligados por túneis ExpressRoute ou VPN?

Sim, desde que o trânsito seja permitido.

### <a name="can-one-backend-pool-serve-many-applications-on-different-ports"></a>Uma piscina de backend pode servir muitas aplicações em diferentes portas?

A arquitetura de microserviços é suportada. Para sondar diferentes portas, é necessário configurar várias definições HTTP.

### <a name="do-custom-probes-support-wildcards-or-regex-on-response-data"></a>As sondas personalizadas suportam wildcards ou regex em dados de resposta?

Não. 

### <a name="how-are-routing-rules-processed-in-application-gateway"></a>Como são processadas as regras de encaminhamento no Application Gateway?

Consulte [as regras de processamento da Ordem](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-rules).

### <a name="for-custom-probes-what-does-the-host-field-signify"></a>Para sondas personalizadas, o que significa o campo Host?

O campo Host especifica o nome para enviar a sonda para quando tiver configurado várioss itens no Application Gateway. Caso contrário, utilize '127.0.0.1'. Este valor é diferente do nome do anfitrião da máquina virtual. O seu formato é \<protocol\> \<host\> : . \<port\> \<path\> .

### <a name="can-i-allow-application-gateway-access-to-only-a-few-source-ip-addresses"></a>Posso permitir o acesso do Application Gateway a apenas alguns endereços IP de origem?

Sim. Consulte [o acesso restrito a IPs de origem específica](https://docs.microsoft.com/azure/application-gateway/configuration-overview#allow-application-gateway-access-to-a-few-source-ips).

### <a name="can-i-use-the-same-port-for-both-public-facing-and-private-facing-listeners"></a>Posso usar o mesmo porto para ouvintes virados para o público e para os privados?

Não.

### <a name="does-application-gateway-support-ipv6"></a>O Application Gateway suporta o IPv6?

O Application Gateway v2 não suporta atualmente o IPv6. Pode funcionar numa VNet de dupla pilha usando apenas IPv4, mas a sub-rede gateway deve ser apenas IPv4. O Application Gateway v1 não suporta VNets de pilha dupla. 

### <a name="how-do-i-use-application-gateway-v2-with-only-private-frontend-ip-address"></a>Como uso o Gateway V2 de aplicação apenas com endereço IP frontend privado?

Atualmente, o Gateway V2 da aplicação não suporta apenas o modo IP privado. Suporta as seguintes combinações
* IP privado e IP Público
* APENAS IP público

Mas se quiser utilizar o Gateway V2 de aplicação apenas com IP privado, pode seguir o processo abaixo:
1. Criar um Gateway de aplicações com endereço IP de frontend público e privado
2. Não crie nenhum ouvinte para o endereço IP do frontend público. O Application Gateway não ouvirá qualquer tráfego no endereço IP público se não forem criados ouvintes para o mesmo.
3. Criar e anexar um [Grupo de Segurança de Rede](https://docs.microsoft.com/azure/virtual-network/security-overview) para a sub-rede Gateway de Aplicação com a seguinte configuração na ordem de prioridade:
    
    a. Permitir o tráfego a partir da Fonte como etiqueta de serviço **GatewayManager** e Porto destino como **porta 65200-65535**. **Any** Esta gama portuária é necessária para a comunicação da infraestrutura Azure. Estas portas estão protegidas (bloqueadas) por autenticação de certificados. Entidades externas, incluindo os administradores de utilizadores gateway, não podem iniciar alterações nesses pontos finais sem certificados adequados em vigor
    
    b. Permitir tráfego a partir de Source como **AzureLoadBalancer** tag de serviço e destino e porto de destino como **Qualquer**
    
    c. Negue todo o tráfego de entrada da Fonte como etiqueta de serviço de **Internet** e destino e porto de destino como **Qualquer**. Dê a esta regra a *menor prioridade* nas regras de entrada
    
    d. Mantenha as regras padrão como permitir a entrada da VirtualNetwork para que o acesso no endereço IP privado não seja bloqueado
    
    e. A conectividade de saída da Internet não pode ser bloqueada. Caso contrário, enfrentará problemas com a exploração madeireira, métricas, etc.

Configuração NSG de amostra apenas para acesso ip privado: ![ Configuração NSG do Gateway de aplicações apenas para acesso IP privado](./media/application-gateway-faq/appgw-privip-nsg.png)

## <a name="configuration---tls"></a>Configuração - TLS

### <a name="what-certificates-does-application-gateway-support"></a>Que certificados suporta o Application Gateway?

A Application Gateway suporta certificados auto-assinados, certificados da autoridade de certificados (CA), certificados de validação estendida (EV) e certificados wildcard.

### <a name="what-cipher-suites-does-application-gateway-support"></a>Que suítes de cifra suportam o Application Gateway?

Application Gateway suporta as seguintes suítes de cifra. 

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

Para obter informações sobre como personalizar as opções TLS, consulte [versões de política configure TLS e suítes de cifra no Gateway de Aplicações](application-gateway-configure-ssl-policy-powershell.md).

### <a name="does-application-gateway-support-reencryption-of-traffic-to-the-backend"></a>O Application Gateway suporta a reencriptação do tráfego para o backend?

Sim. O Application Gateway suporta o descarregamento TLS e o TLS de ponta a ponta, que reencriptam o tráfego para o backend.

### <a name="can-i-configure-tls-policy-to-control-tls-protocol-versions"></a>Posso configurar a política TLS para controlar as versões do protocolo TLS?

Sim. Pode configurar o Gateway de aplicações para negar TLS1.0, TLS1.1 e TLS1.2. Por padrão, os SSL 2.0 e 3.0 já estão desativadas e não são configuráveis.

### <a name="can-i-configure-cipher-suites-and-policy-order"></a>Posso configurar suítes de cifra e ordem política?

Sim. No Application Gateway, pode [configurar suítes cifras](application-gateway-ssl-policy-overview.md). Para definir uma política personalizada, ative pelo menos uma das seguintes suítes cifra. 

* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_256_CBC_SHA256
* TLS_RSA_WITH_AES_128_CBC_SHA256

Application Gateway usa SHA256 para gestão de backend.

### <a name="how-many-tlsssl-certificates-does-application-gateway-support"></a>Quantos certificados TLS/SSL suportam o Application Gateway?

O Application Gateway suporta até 100 certificados TLS/SSL.

### <a name="how-many-authentication-certificates-for-backend-reencryption-does-application-gateway-support"></a>Quantos certificados de autenticação para reencriptação de backend suporta o Application Gateway?

O Application Gateway suporta até 100 certificados de autenticação.

### <a name="does-application-gateway-natively-integrate-with-azure-key-vault"></a>O Application Gateway integra-se de forma nativa com o Cofre da Chave Azure?

Sim, o Gateway de Aplicação v2 SKU suporta o Key Vault. Para obter mais informações, consulte [a rescisão de TLS com certificados Key Vault](key-vault-certs.md).

### <a name="how-do-i-configure-https-listeners-for-com-and-net-sites"></a>Como configurar os ouvintes HTTPS para sites .com e .net? 

Para o encaminhamento de vários domínios (baseado em anfitriões), pode criar ouvintes multisite, configurar os ouvintes que usam HTTPS como protocolo e associar os ouvintes às regras de encaminhamento. Para obter mais informações, consulte [hospedar vários sites utilizando o Gateway de Aplicações.](https://docs.microsoft.com/azure/application-gateway/multiple-site-overview)

### <a name="can-i-use-special-characters-in-my-pfx-file-password"></a>Posso usar caracteres especiais na minha senha de ficheiro .pfx?

Não, utilize apenas caracteres alfanuméricos na sua senha de ficheiro .pfx.

## <a name="configuration---ingress-controller-for-aks"></a>Configuração - controlador de entrada para AKS

### <a name="what-is-an-ingress-controller"></a>O que é um Controlador ingress?

Kubernetes permite a criação `deployment` e recursos para expor um grupo de `service` cápsulas internamente no cluster. Para expor o mesmo serviço externamente, é definido um [`Ingress`](https://kubernetes.io/docs/concepts/services-networking/ingress/) recurso que proporciona equilíbrio de carga, rescisão TLS e hospedagem virtual baseada em nomes.
Para satisfazer este `Ingress` recurso, é necessário um Controlador de Entrada que ouça quaisquer alterações de `Ingress` recursos e configura as políticas do equilibrador de carga.

O Controlador de Entrada de Gateway de Aplicação permite que o [Gateway de Aplicação Azure](https://azure.microsoft.com/services/application-gateway/) seja usado como entrada para um [Serviço Azure Kubernetes](https://azure.microsoft.com/services/kubernetes-service/) também conhecido como um cluster AKS.

### <a name="can-a-single-ingress-controller-instance-manage-multiple-application-gateways"></a>Pode um único controlador de entrada gerir vários Gateways de aplicação?

Atualmente, um caso de Controlador de Entrada só pode ser associado a um Gateway de Aplicação.

## <a name="diagnostics-and-logging"></a>Diagnóstico e registos

### <a name="what-types-of-logs-does-application-gateway-provide"></a>Que tipos de registos o Application Gateway fornece?

O Application Gateway fornece três registos: 

* **ApplicationGatewayAccessLog**: O registo de acesso contém cada pedido submetido ao frontend do gateway de aplicação. Os dados incluem o IP do chamador, URL solicitado, latência de resposta, código de devolução e bytes dentro e fora. Contém um registo por porta de aplicação.
* **ApplicationGatewayPerformanceLog**: O registo de desempenho capta informações de desempenho para cada porta de aplicação. A informação inclui o produção em bytes, o total de pedidos servidos, a contagem de pedidos falhadas e a contagem de casos de backend saudável e pouco saudável.
* **ApplicationGatewayFirewallLog**: Para os gateways de aplicações que configura com WAF, o registo de firewall contém pedidos que são registados através do modo de deteção ou do modo de prevenção.

Todos os registos são recolhidos a cada 60 segundos. Para obter mais informações, consulte [a saúde backend, registos de diagnóstico e métricas para o Gateway de Aplicações.](application-gateway-diagnostics.md)

### <a name="how-do-i-know-if-my-backend-pool-members-are-healthy"></a>Como sei se os meus membros da piscina estão saudáveis?

Verifique a saúde utilizando o cmdlet PowerShell `Get-AzApplicationGatewayBackendHealth` ou o portal. Para mais informações, consulte os [diagnósticos do Application Gateway](application-gateway-diagnostics.md).

### <a name="whats-the-retention-policy-for-the-diagnostic-logs"></a>Qual é a política de retenção para os registos de diagnóstico?

Os registos de diagnóstico fluem para a conta de armazenamento do cliente. Os clientes podem definir a política de retenção com base na sua preferência. Os registos de diagnóstico também podem ser enviados para um centro de eventos ou registos do Azure Monitor. Para mais informações, consulte os [diagnósticos do Application Gateway](application-gateway-diagnostics.md).

### <a name="how-do-i-get-audit-logs-for-application-gateway"></a>Como recebo registos de auditoria para o Application Gateway?

No portal, na lâmina do menu de um gateway de aplicações, selecione **Registo de Atividades** para aceder ao registo de auditoria. 

### <a name="can-i-set-alerts-with-application-gateway"></a>Posso definir alertas com o Application Gateway?

Sim. No Gateway de Aplicação, os alertas são configurados em métricas. Para mais informações, consulte [as métricas do Gateway de Aplicação](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics) e [receba notificações de alerta.](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

### <a name="how-do-i-analyze-traffic-statistics-for-application-gateway"></a>Como analiso as estatísticas de tráfego do Application Gateway?

Pode visualizar e analisar registos de acesso de várias maneiras. Utilize registos do Monitor Azure, Excel, Power BI, e assim por diante.

Também pode utilizar um modelo de Gestor de Recursos que instala e executa o popular analisador [de registos GoAccess](https://goaccess.io/) para registos de acesso de Gateway de Aplicação. O GoAccess fornece valiosas estatísticas de tráfego HTTP, tais como visitantes exclusivos, ficheiros solicitados, anfitriões, sistemas operativos, navegadores e códigos de estado HTTP. Para obter mais informações, no GitHub, consulte o [ficheiro Readme na pasta do modelo do Gestor de Recursos](https://aka.ms/appgwgoaccessreadme).

### <a name="what-could-cause-backend-health-to-return-an-unknown-status"></a>O que poderia fazer com que a saúde de backend devolvesse um estatuto desconhecido?

Normalmente, vê-se um estado desconhecido quando o acesso ao backend é bloqueado por um grupo de segurança de rede (NSG), DNS personalizado ou encaminhamento definido pelo utilizador (UDR) na sub-rede do gateway de aplicação. Para obter mais informações, consulte [a saúde backend, o registo de diagnósticos e as métricas para o Gateway de Aplicações.](application-gateway-diagnostics.md)

### <a name="is-there-any-case-where-nsg-flow-logs-wont-show-allowed-traffic"></a>Há algum caso em que os registos de fluxo nSG não mostrem tráfego permitido?

Sim. Se a sua configuração corresponder ao seguinte cenário, não verá tráfego permitido nos seus registos de fluxo NSG:
- Você implantou o Application Gateway v2
- Você tem um NSG na sub-rede de gateway de aplicação
- Você ativou registos de fluxo NSG em que NSG

### <a name="how-do-i-use-application-gateway-v2-with-only-private-frontend-ip-address"></a>Como uso o Gateway V2 de aplicação apenas com endereço IP frontend privado?

Atualmente, o Gateway V2 não suporta apenas o modo IP privado. Suporta as seguintes combinações
* IP privado e IP Público
* APENAS IP público

Mas se quiser utilizar o Gateway V2 de aplicação apenas com IP privado, pode seguir o processo abaixo:
1. Criar um Gateway de aplicações com endereço IP de frontend público e privado
2. Não crie nenhum ouvinte para o endereço IP do frontend público. O Application Gateway não ouvirá qualquer tráfego no endereço IP público se não forem criados ouvintes para o mesmo.
3. Criar e anexar um [Grupo de Segurança de Rede](https://docs.microsoft.com/azure/virtual-network/security-overview) para a sub-rede Gateway de Aplicação com a seguinte configuração na ordem de prioridade:
    
    a. Permitir o tráfego a partir da Fonte como etiqueta de serviço **GatewayManager** e Porto destino como **porta 65200-65535**. **Any** Esta gama portuária é necessária para a comunicação da infraestrutura Azure. Estas portas estão protegidas (bloqueadas) por autenticação de certificados. Entidades externas, incluindo os administradores de utilizadores gateway, não podem iniciar alterações nesses pontos finais sem certificados adequados em vigor
    
    b. Permitir tráfego a partir de Source como **AzureLoadBalancer** tag e porto de destino como **Qualquer**
    
    c. Negue todo o tráfego de entrada da Fonte como etiqueta de serviço de **Internet** e porto de destino como **Any**. Dê a esta regra a *menor prioridade* nas regras de entrada
    
    d. Mantenha as regras padrão como permitir a entrada da VirtualNetwork para que o acesso no endereço IP privado não seja bloqueado
    
    e. A conectividade de saída da Internet não pode ser bloqueada. Caso contrário, enfrentará problemas com o registo, métricas, e assim por diante.

Configuração NSG de amostra apenas para acesso ip privado: ![ Configuração NSG do Gateway de aplicações apenas para acesso IP privado](./media/application-gateway-faq/appgw-privip-nsg.png)


## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o Application Gateway, veja [o que é O Gateway de Aplicação Azure?](overview.md)
