---
title: Perguntas freqüentes sobre o Gateway de aplicação do Azure
description: Encontre respostas para perguntas freqüentes sobre o Gateway de aplicação do Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 6/1/2019
ms.author: victorh
ms.openlocfilehash: 25779cd62f70df41a7734d2f8097504923dcb3a3
ms.sourcegitcommit: 087ee51483b7180f9e897431e83f37b08ec890ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/31/2019
ms.locfileid: "66430877"
---
# <a name="frequently-asked-questions-about-application-gateway"></a>Perguntas freqüentes sobre o Gateway de aplicação

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Seguem-se comuns perguntas mais frequentes sobre o Gateway de aplicação do Azure.

## <a name="general"></a>Geral

### <a name="what-is-application-gateway"></a>O que é o Gateway de Aplicação?

Gateway de aplicação do Azure fornece um controlador de entrega de aplicações (ADC) como um serviço. Ele oferece vários grupos de recursos de balanceamento de carga de camada 7 para as suas aplicações. Este serviço é de elevada disponibilidade, dimensionáveis e totalmente gerido pelo Azure.

### <a name="what-features-does-application-gateway-support"></a>Quais recursos o Gateway de aplicação suporta?

Gateway de aplicação suporta o dimensionamento automático, descarga de SSL e SSL ponto a ponto, um firewall de aplicações web (WAF), afinidade de sessão baseada em cookies, URL encaminhamento baseado no caminho, alojamento de múltiplos sites e outros recursos. Para obter uma lista completa das funcionalidades suportadas, consulte [introdução ao Gateway de aplicação](application-gateway-introduction.md).

### <a name="how-do-application-gateway-and-azure-load-balancer-differ"></a>Como o Gateway de aplicação e o Balanceador de carga do Azure diferem?

Gateway de aplicação é um balanceador de carga de camada 7, o que significa que ele funciona apenas com o tráfego da web (HTTP, HTTPS, WebSocket e HTTP/2). Ele oferece suporte a recursos, tais como a terminação de SSL, afinidade de sessão baseada em cookies e round robin para balanceamento de carga tráfego. Carga balanceador faz o balanceamento de carga o tráfego na camada 4 (TCP ou UDP).

### <a name="what-protocols-does-application-gateway-support"></a>Quais protocolos o Gateway de aplicação suporta?

Gateway de aplicação suporta HTTP, HTTPS, HTTP/2 e WebSocket.

### <a name="how-does-application-gateway-support-http2"></a>Como é que o Gateway de aplicação suporta HTTP/2?

Ver [suporte de HTTP/2](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http2-support).

### <a name="what-resources-are-supported-as-part-of-a-backend-pool"></a>Os recursos que são suportados como parte de um conjunto de back-end?

Ver [recursos de back-end suportados](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#backend-pool).

### <a name="in-what-regions-is-application-gateway-available"></a>Em que regiões é o Gateway de aplicação disponível?

Gateway de aplicação está disponível em todas as regiões do global Azure. Também está disponível no [Azure China 21Vianet](https://www.azure.cn/) e [Azure Government](https://azure.microsoft.com/overview/clouds/government/).

### <a name="is-this-deployment-dedicated-for-my-subscription-or-is-it-shared-across-customers"></a>É esta implementação dedicado para a minha subscrição ou é partilhado entre os clientes?

Gateway de aplicação é uma implementação dedicada na sua rede virtual.

### <a name="does-application-gateway-support-http-to-https-redirection"></a>Gateway de aplicação suporta o redirecionamento de HTTP para HTTPS?

O redirecionamento é suportado. Ver [descrição geral do redirecionamento de Gateway de aplicação](application-gateway-redirect-overview.md).

### <a name="in-what-order-are-listeners-processed"></a>Ordem pela qual são serviços de escuta processados?

Consulte a [por ordem de processamento do serviço de escuta](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-listeners).

### <a name="where-do-i-find-the-application-gateway-ip-and-dns"></a>Onde posso encontrar o IP do Gateway de aplicação e o DNS?

Se estiver a utilizar um endereço IP público como um ponto final, encontrará as informações de IP e DNS no recurso de endereço IP público. Ou, disponível no portal, na página de descrição geral para o gateway de aplicação. Se estiver a utilizar endereços IP internos, localize as informações na página de descrição geral.

### <a name="what-are-the-settings-for-keep-alive-timeout-and-tcp-idle-timeout"></a>Quais são as definições de tempo limite de ligação Keep-Alive e o tempo limite de inatividade de TCP?

 A SKU de Gateway de aplicação v1, o tempo limite da ligação Keep-Alive é de 120 segundos. O tempo limite da ligação Keep-Alive para o SKU de v2 é 75 segundos. O tempo de limite de inatividade de TCP é um padrão de 4 minutos no front-end virtual IP (VIP) do Gateway de aplicação.

### <a name="does-the-ip-or-dns-name-change-over-the-lifetime-of-the-application-gateway"></a>O nome IP ou DNS se altera ao longo da duração do gateway de aplicação?

O VIP pode mudar se parar e iniciar o gateway de aplicação. Mas o nome DNS associado com o gateway de aplicação não é alterado durante a duração do gateway. Uma vez que o nome DNS não é alterado, deve utilizar um alias CNAME e apontá-lo para o endereço DNS do gateway de aplicação.

### <a name="does-application-gateway-support-static-ip"></a>Gateway de aplicação suporta o IP estático?

Sim, o SKU do Gateway de aplicação v2 suporta endereços IP públicos estáticos. O SKU de v1 suporta IPs internos estáticos.

### <a name="does-application-gateway-support-multiple-public-ips-on-the-gateway"></a>O Gateway de aplicação suporta múltiplos IPs públicos no gateway?

Um gateway de aplicação suporta apenas um endereço IP público.

### <a name="how-large-should-i-make-my-subnet-for-application-gateway"></a>Como grande deve verifico minha sub-rede de Gateway de aplicação?

Ver [considerações de tamanho de sub-rede de Gateway de aplicação](https://docs.microsoft.com/azure/application-gateway/configuration-overview#size-of-the-subnet).

### <a name="can-i-deploy-more-than-one-application-gateway-resource-to-a-single-subnet"></a>Pode implementar mais do que um recurso de Gateway de aplicação para uma única sub-rede?

Sim. Além de várias instâncias de uma determinada implantação de Gateway de aplicação, pode aprovisionar outro recurso de Gateway de aplicação exclusivo a uma sub-rede existente que contenha um recurso de Gateway de aplicação diferente.

Uma única sub-rede não pode suportar Standard_v2 e Gateway de aplicação padrão em conjunto.

### <a name="does-application-gateway-support-x-forwarded-for-headers"></a>O Gateway de aplicação suporta cabeçalhos x-reencaminhados-para?

Sim. Ver [modificações a um pedido](https://docs.microsoft.com/azure/application-gateway/how-application-gateway-works#modifications-to-the-request).

### <a name="how-long-does-it-take-to-deploy-an-application-gateway-will-my-application-gateway-work-while-its-being-updated"></a>Quanto tempo demora a implementar um gateway de aplicação? Meu gateway de aplicação funcionará enquanto ele está a ser atualizado?

Novas implementações de SKU de Gateway de aplicação v1 podem demorar até 20 minutos para aprovisionar. Alterações ao tamanho da instância ou a contagem não são disruptivas e o gateway permanece ativo durante este período.

Implementações que utilizem o SKU de v2 podem demorar até seis minutos a aprovisionar.

### <a name="can-i-use-exchange-server-as-a-backend-with-application-gateway"></a>Posso utilizar o Exchange Server como um back-end com o Gateway de aplicação?

Não. Gateway de aplicação não suporta protocolos de e-mail, como o SMTP, IMAP e POP3. 

## <a name="performance"></a>Desempenho

### <a name="how-does-application-gateway-support-high-availability-and-scalability"></a>Como o Gateway de aplicação suporta elevada disponibilidade e escalabilidade?

O SKU do Gateway de aplicação v1 suporta cenários de elevada disponibilidade, quando implementar duas ou mais instâncias. Em domínios de atualização e com falha para garantir que as instâncias não falhar todos ao mesmo tempo, o Azure distribui essas instâncias. O SKU de v1 oferece suporte a escalabilidade com a adição de várias instâncias do mesmo gateway para partilhar a carga.

O SKU de v2 assegura automaticamente que as novas instâncias são distribuídas por domínios de falha e domínios de atualização. Se optar por redundância de zona, as instâncias mais recentes também são distribuídas por zonas de disponibilidade para oferecer resiliência de falha zonais.

### <a name="how-do-i-achieve-a-dr-scenario-across-datacenters-by-using-application-gateway"></a>Como posso obter um cenário de DR em centros de dados com o Gateway de aplicação?

Utilize o Gestor de tráfego para distribuir o tráfego por vários gateways de aplicação em datacenters diferentes.

### <a name="does-application-gateway-support-autoscaling"></a>Gateway de aplicação suporta o dimensionamento automático?

Sim, o SKU do Gateway de aplicação v2 suporta o dimensionamento automático. Para obter mais informações, consulte [dimensionamento automático e o Gateway de aplicação com redundância de zona](application-gateway-autoscaling-zone-redundant.md).

### <a name="does-manual-scale-up-or-scale-down-cause-downtime"></a>Não dimensionamento manual a cópia de segurança ou reduzir verticalmente o tempo de inatividade causa?

Não. Instâncias são distribuídas por domínios de atualização e domínios de falha.

### <a name="does-application-gateway-support-connection-draining"></a>O Gateway de aplicação suporta drenagem de ligação?

Sim. Pode configurar a ligação a ser drenado para alterar os membros dentro de um conjunto de back-end sem interrupção. Esta configuração permite-lhe continuar a enviar ligações existentes ao seu destino anterior até ser essa conexão é fechada ou um tempo limite configurável expira. Ligação a ser drenado aguarda apenas ligações em trânsito atuais concluir. Gateway de aplicação não conhece o estado de sessão da aplicação.

### <a name="can-i-change-instance-size-from-medium-to-large-without-disruption"></a>Posso alterar o tamanho de instância média para grandes sem interrupção?

Sim. Em domínios de atualização e com falha para garantir que as instâncias não falharem todos ao mesmo tempo, o Azure distribui instâncias. Gateway de aplicação suporta o dimensionamento ao adicionar várias instâncias do mesmo gateway para partilhar a carga.

## <a name="configuration"></a>Configuração

### <a name="is-application-gateway-always-deployed-in-a-virtual-network"></a>Gateway de aplicação é sempre implementado numa rede virtual?

Sim. Gateway de aplicação sempre é implementado numa sub-rede de rede virtual. Esta sub-rede pode conter apenas os gateways de aplicação. Para obter mais informações, consulte [requisitos de rede e sub-rede virtual](https://docs.microsoft.com/azure/application-gateway/configuration-overview#azure-virtual-network-and-dedicated-subnet).

### <a name="can-application-gateway-communicate-with-instances-outside-of-its-virtual-network-or-outside-of-its-subscription"></a>Gateway de aplicação pode comunicar com instâncias de fora da sua rede virtual ou fora da sua subscrição?

Desde que tiver conectividade IP, Gateway de aplicação pode comunicar com instâncias de fora da rede virtual que está a ser. Gateway de aplicação também pode comunicar com instâncias de fora a subscrição está a ser. Se quiser utilizar IPs interno como membros do agrupamento de back-end, utilize [peering de rede virtual](../virtual-network/virtual-network-peering-overview.md) ou [Gateway de VPN do Azure](../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="can-i-deploy-anything-else-in-the-application-gateway-subnet"></a>Posso implementar qualquer coisa na sub-rede de gateway de aplicação?

Não. Mas pode implementar outros gateways de aplicação na sub-rede.

### <a name="are-network-security-groups-supported-on-the-application-gateway-subnet"></a>Grupos de segurança de rede são suportados na sub-rede de gateway de aplicação?

Ver [grupos de segurança na sub-rede de Gateway de aplicação de rede](https://docs.microsoft.com/azure/application-gateway/configuration-overview#network-security-groups-on-the-application-gateway-subnet).

### <a name="does-the-application-gateway-subnet-support-user-defined-routes"></a>A sub-rede do gateway de aplicação suporta rotas definidas pelo utilizador?

Ver [rotas definidas pelo utilizador suportadas na sub-rede de Gateway de aplicação](https://docs.microsoft.com/azure/application-gateway/configuration-overview#user-defined-routes-supported-on-the-application-gateway-subnet).

### <a name="what-are-the-limits-on-application-gateway-can-i-increase-these-limits"></a>Quais são os limites no Gateway de aplicação? Pode aumentar estes limites?

Ver [Gateway de aplicação limita](../azure-subscription-service-limits.md#application-gateway-limits).

### <a name="can-i-simultaneously-use-application-gateway-for-both-external-and-internal-traffic"></a>Ao mesmo tempo posso utilizar o Gateway de aplicação para o tráfego externo e interno?

Sim. Gateway de aplicação suporta um IP interno e um IP externo por gateway de aplicação.

### <a name="does-application-gateway-support-virtual-network-peering"></a>O Gateway de aplicação suporta peering de rede virtual?

Sim. Peering de rede virtual ajuda o tráfego de balanceamento de carga nas outras redes virtuais.

### <a name="can-i-talk-to-on-premises-servers-when-theyre-connected-by-expressroute-or-vpn-tunnels"></a>Posso falar para servidores no local quando estão ligados por túneis VPN ou ExpressRoute?

Sim, desde que o tráfego é permitido.

### <a name="can-one-backend-pool-serve-many-applications-on-different-ports"></a>Um conjunto de back-end sirva muitos aplicativos nas portas diferentes?

Arquitetura de Microsserviços é suportada. Para a sonda em portas diferentes, terá de configurar várias definições de HTTP.

### <a name="do-custom-probes-support-wildcards-or-regex-on-response-data"></a>As pesquisas personalizadas suportam carateres universais ou regex nos dados de resposta?

Não. 

### <a name="how-are-routing-rules-processed-in-application-gateway"></a>Como são processadas as regras de encaminhamento no Gateway de aplicação?

Ver [ordem das regras de processamento](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-rules).

### <a name="for-custom-probes-what-does-the-host-field-signify"></a>Para as pesquisas personalizadas, o que o campo Host significar?

O campo Host Especifica o nome para enviar a sonda para quando configurar múltiplos sites no Gateway de aplicação. Caso contrário, utilize "127.0.0.1". Este valor é diferente do nome de anfitrião da máquina virtual. Seu formato é \<protocolo\>://\<anfitrião\>:\<porta\>\<caminho\>.

### <a name="can-i-allow-application-gateway-access-to-only-a-few-source-ip-addresses"></a>Pode permitir acesso de Gateway de aplicação apenas endereços de IP de origem alguns?

Sim. Ver [restringir o acesso a IPs de origem específico](https://docs.microsoft.com/azure/application-gateway/configuration-overview#allow-application-gateway-access-to-a-few-source-ips).

### <a name="can-i-use-the-same-port-for-both-public-facing-and-private-facing-listeners"></a>Pode utilizar a mesma porta para as escutas de destinado ao público e privado com acesso à?

Não.

### <a name="is-there-guidance-available-to-migrate-from-the-v1-sku-to-the-v2-sku"></a>Há diretrizes disponíveis para migrar de SKU do v1 para o SKU do v2?

Sim. Para obter detalhes, veja [migrar Gateway de aplicação do Azure e de Firewall de aplicações Web do v1 para v2](migrate-v1-v2.md).


## <a name="configuration---ssl"></a>Configuração - SSL

### <a name="what-certificates-does-application-gateway-support"></a>Quais certificados suporta o Gateway de aplicação?

Gateway de aplicação suporta certificados autoassinados, certificados da autoridade (CA), certificados de validação estendida (EV) e certificados de caráter universal.

### <a name="what-cipher-suites-does-application-gateway-support"></a>Que conjuntos de cifras faz o Gateway de aplicação suporta?

Gateway de aplicação suporta os seguintes conjuntos de cifras. 

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

Para obter informações sobre como personalizar as opções de SSL, consulte [versões de política de configurar o SSL e conjuntos de cifras no Gateway de aplicação](application-gateway-configure-ssl-policy-powershell.md).

### <a name="does-application-gateway-support-reencryption-of-traffic-to-the-backend"></a>O Gateway de aplicação suporta reencryption de tráfego para o back-end?

Sim. Gateway de aplicação suporta a descarga de SSL e o SSL de ponto-a-ponto, que reencriptar o tráfego para o back-end.

### <a name="can-i-configure-ssl-policy-to-control-ssl-protocol-versions"></a>Posso configurar a política SSL para as versões de protocolo do controle SSL?

Sim. Pode configurar o Gateway de aplicação para negar o TLS 1.0, TLS1.1 e TLS1.2. Por predefinição, o SSL 2.0 e 3.0, já estão desativadas e não são configuráveis.

### <a name="can-i-configure-cipher-suites-and-policy-order"></a>Pode configurar conjuntos de cifras e ordem de política?

Sim. No Gateway de aplicação, pode [configurar conjuntos de cifras](application-gateway-ssl-policy-overview.md). Para definir uma política personalizada, ative, pelo menos, um dos conjuntos de cifras seguintes. 

* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_256_CBC_SHA256
* TLS_RSA_WITH_AES_128_CBC_SHA256

Gateway de aplicação utiliza SHA256 para a gestão de back-end.

### <a name="how-many-ssl-certificates-does-application-gateway-support"></a>Quantos certificados SSL suporta o Gateway de aplicação?

Gateway de aplicação suporta até 100 certificados SSL.

### <a name="how-many-authentication-certificates-for-backend-reencryption-does-application-gateway-support"></a>Quantos certificados de autenticação de back-end reencryption suporta o Gateway de aplicação?

Gateway de aplicação suporta até 10 certificados de autenticação. A predefinição é 5.

### <a name="does-application-gateway-natively-integrate-with-azure-key-vault"></a>Gateway de aplicação nativamente integrar com o Azure Key Vault?

Sim, o SKU do Gateway de aplicação v2 suporta Key Vault. Para obter mais informações, consulte [terminação de SSL com certificados do Key Vault](key-vault-certs.md).

### <a name="how-do-i-configure-https-listeners-for-com-and-net-sites"></a>Como posso configurar serviços de escuta HTTPS para os sites. com e .net? 

Para vários baseado em domínio (baseados em host) encaminhamento, pode criar serviços de escuta de múltiplos sites, configurar serviços de escuta que utilizam o HTTPS como o protocolo e associar os serviços de escuta com as regras de encaminhamento. Para obter mais informações, consulte [alojar vários sites com o Gateway de aplicação](https://docs.microsoft.com/azure/application-gateway/multiple-site-overview). 

## <a name="configuration---web-application-firewall-waf"></a>Configuração – firewall de aplicações web (WAF)

### <a name="does-the-waf-sku-offer-all-the-features-available-in-the-standard-sku"></a>O SKU da WAF oferece todas as funcionalidades disponíveis na Standard SKU?

Sim. WAF suporta todas as funcionalidades do SKU Standard.

### <a name="which-crs-versions-does-application-gateway-support"></a>As versões que CRS suportam o Gateway de aplicação?

Gateway de aplicação suporta CRS [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229) e o CRS [3.0](application-gateway-crs-rulegroups-rules.md#owasp30).

### <a name="how-do-i-monitor-waf"></a>Como posso monitorizar o WAF?

Monitor WAF com o log de diagnóstico. Para obter mais informações, consulte [registo de diagnósticos e métricas para o Gateway de aplicação](application-gateway-diagnostics.md).

### <a name="does-detection-mode-block-traffic"></a>Modo de deteção bloquear o tráfego?

Não. Modo de deteção apenas registos de tráfego que aciona uma regra de WAF.

### <a name="can-i-customize-waf-rules"></a>Pode personalizar regras WAF?

Sim. Para obter mais informações, consulte [regras e grupos de regras WAF personalizar](application-gateway-customize-waf-rules-portal.md).

### <a name="what-rules-are-currently-available-for-waf"></a>As regras que estão atualmente disponíveis para WAF?

Atualmente, o WAF suporta CRS [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229) e [3.0](application-gateway-crs-rulegroups-rules.md#owasp30). Estas regras fornecem segurança de linha de base contra a maioria das 10 principais vulnerabilidades que identifica o Open Web Application Security Project (OWASP): 

* Proteção contra injeção de SQL
* Proteção de criação de scripts entre sites
* Proteção contra ataques web comuns, como injeção de comandos, contrabando, divisão de respostas HTTP de pedidos HTTP e ataques de inclusão de ficheiros remota
* Proteção contra violações de protocolo HTTP
* Proteção contra anomalias de protocolo HTTP, como agente de utilizador de anfitrião e cabeçalhos de aceitação em falta
* Prevenção de contra bots, crawlers e scanners
* Deteção de aplicação incorretas comuns (ou seja, Apache, IIS etc.)

Para obter mais informações, consulte [vulnerabilidades do OWASP top 10](https://www.owasp.org/index.php/Top10#OWASP_Top_10_for_2013).

### <a name="does-waf-support-ddos-protection"></a>O WAF suporta a proteção contra DDoS?

Sim. Pode ativar a proteção contra DDoS na rede virtual em que o gateway de aplicação é implementado. Esta definição garante que o serviço Azure DDoS Protection também protege o IP de virtual de gateway de aplicação (VIP).

### <a name="is-there-guidance-available-to-migrate-from-the-v1-sku-to-the-v2-sku"></a>Há diretrizes disponíveis para migrar de SKU do v1 para o SKU do v2?

Sim. Para obter detalhes, veja [migrar Gateway de aplicação do Azure e de Firewall de aplicações Web do v1 para v2](migrate-v1-v2.md).

## <a name="diagnostics-and-logging"></a>Diagnóstico e registos

### <a name="what-types-of-logs-does-application-gateway-provide"></a>Que tipos de registos de fornecer o Gateway de aplicação?

Gateway de aplicação fornece três logs: 

* **ApplicationGatewayAccessLog**: O registo de acesso contém cada pedido submetido para o front-end de gateway de aplicação. Os dados incluem o chamador IP, URL pedido, latência de resposta, código de retorno e bytes e reduzir. O registo de acesso é recolhido de 300 segundos. Ele contém um registo por gateway de aplicação.
* **ApplicationGatewayPerformanceLog**: O registo de desempenho captura informações de desempenho para cada gateway de aplicação. Informações incluem o débito em bytes, total de pedidos servidos, contagem de pedidos com falha e a contagem de instâncias de back-end com e sem integridade.
* **ApplicationGatewayFirewallLog**: Para gateways de aplicação que configurar com a WAF, o registo de firewall contém pedidos que são registados através do modo de deteção ou modo de prevenção.

Para obter mais informações, consulte [back-end estado de funcionamento, registos de diagnóstico e métricas para o Gateway de aplicação](application-gateway-diagnostics.md).

### <a name="how-do-i-know-if-my-backend-pool-members-are-healthy"></a>Como posso saber se minha membros do agrupamento de back-end estão em bom Estados?

Verificar o estado de funcionamento com o cmdlet do PowerShell `Get-AzApplicationGatewayBackendHealth` ou no portal. Para obter mais informações, consulte [diagnóstico do Gateway de aplicação](application-gateway-diagnostics.md).

### <a name="whats-the-retention-policy-for-the-diagnostic-logs"></a>O que é a política de retenção para os registos de diagnóstico?

Fluxo de registos de diagnóstico para a conta de armazenamento do cliente. Os clientes podem definir a política de retenção com base nas suas preferências. Os registos de diagnóstico também podem ser enviados para um hub de eventos ou registos do Azure Monitor. Para obter mais informações, consulte [diagnóstico do Gateway de aplicação](application-gateway-diagnostics.md).

### <a name="how-do-i-get-audit-logs-for-application-gateway"></a>Como posso obter registos de auditoria para o Gateway de aplicação?

No portal, no painel do menu de um gateway de aplicação, selecione **registo de atividades** para aceder ao registo de auditoria. 

### <a name="can-i-set-alerts-with-application-gateway"></a>Pode definir alertas com o Gateway de aplicação?

Sim. No Gateway de aplicação, os alertas são configurados em métricas. Para obter mais informações, consulte [métricas de Gateway de aplicação](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#metrics) e [receber notificações de alerta](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

### <a name="how-do-i-analyze-traffic-statistics-for-application-gateway"></a>Como posso analisar as estatísticas de tráfego para o Gateway de aplicação?

Pode ver e analisar registos de acesso de várias formas. Utilize os registos do Azure Monitor, Excel, Power BI e assim por diante.

Também pode utilizar um modelo do Resource Manager que instala e executa o popular [GoAccess](https://goaccess.io/) log analyzer para aceder aos registos de Gateway de aplicação. GoAccess fornece estatísticas de tráfego HTTP valiosas como visitantes exclusivos, arquivos solicitados, anfitriões, sistemas operacionais, navegadores e códigos de estado HTTP. Para obter mais informações, no GitHub, consulte a [ficheiro Leia-me na pasta de modelo do Resource Manager](https://aka.ms/appgwgoaccessreadme).

### <a name="what-could-cause-backend-health-to-return-an-unknown-status"></a>O que pode fazer com que o estado de funcionamento do back-end retornar um Estado desconhecido?

Normalmente, verá um Estado desconhecido quando o acesso ao back-end está bloqueado por um grupo de segurança de rede (NSG), o DNS personalizado ou o encaminhamento definido pelo utilizador (UDR) na sub-rede de gateway de aplicação. Para obter mais informações, consulte [back-end estado de funcionamento, o registo de diagnósticos e métricas para o Gateway de aplicação](application-gateway-diagnostics.md).

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre o Gateway de aplicação, veja [o que é o Gateway de aplicação do Azure?](overview.md).
