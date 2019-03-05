---
title: Perguntas mais frequentes sobre o Gateway de aplicação do Azure
description: Esta página fornece respostas para perguntas freqüentes sobre o Gateway de aplicação do Azure
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 1/11/2019
ms.author: victorh
ms.openlocfilehash: 5552ca80059b4aa7ef96caf7984b4c15ec177d38
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57316596"
---
# <a name="frequently-asked-questions-for-application-gateway"></a>Perguntas mais frequentes sobre o Gateway de aplicação

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="general"></a>Geral

### <a name="what-is-application-gateway"></a>O que é o Gateway de Aplicação?

O Gateway de aplicação do Azure é um controlador de entrega de aplicação (ADC) como um serviço, oferecendo vários grupos de recursos de balanceamento de carga de camada 7 para as suas aplicações. Ele oferece o serviço de elevada disponibilidade e dimensionável, que é totalmente gerido pelo Azure.

### <a name="what-features-does-application-gateway-support"></a>Quais recursos o Gateway de aplicação suporta?

Gateway de aplicação suporta o dimensionamento automático, descarga de SSL e SSL ponto a ponto, Firewall de aplicações Web, afinidade de sessão baseada em cookies, url baseado no caminho do encaminhamento, múltiplos sites de hospedagem e outros. Para obter uma lista completa das funcionalidades suportadas, consulte [introdução ao Gateway de aplicação](application-gateway-introduction.md).

### <a name="what-is-the-difference-between-application-gateway-and-azure-load-balancer"></a>O que é a diferença entre o Gateway de aplicação e o Balanceador de carga do Azure?

Gateway de aplicação é um balanceador de carga de camada 7, o que significa que ele funciona com apenas tráfego web (HTTP/HTTPS/WebSocket). Ele oferece suporte a recursos como a terminação de SSL, afinidade de sessão baseada em cookies e round robin de tráfego de balanceamento de carga. Carregar tráfego de saldos do Balanceador de carga na camada 4 (TCP/UDP).

### <a name="what-protocols-does-application-gateway-support"></a>Quais protocolos o Gateway de aplicação suporta?

Gateway de aplicação suporta HTTP, HTTPS, HTTP/2 e WebSocket.

### <a name="how-does-application-gateway-support-http2"></a>Como é que o Gateway de aplicação suporta HTTP/2?

Suporte de protocolo HTTP/2 está disponível para clientes que se conectam para ouvintes de gateway de aplicação apenas. A comunicação para agrupamentos de servidores de back-end é através de HTTP/1.1. 

Por predefinição, o suporte de HTTP/2 está desativado. O exemplo de fragmento de código do Azure PowerShell seguinte mostra como pode ativá-la:

```azurepowershell
$gw = Get-AzApplicationGateway -Name test -ResourceGroupName hm
$gw.EnableHttp2 = $true
Set-AzApplicationGateway -ApplicationGateway $gw
```

### <a name="what-resources-are-supported-today-as-part-of-backend-pool"></a>Os recursos que são atualmente suportados como parte do conjunto de back-end?

Conjuntos de back-end podem ser compostos de NICs, conjuntos de dimensionamento de máquinas virtuais, IPs públicos, nomes de IPs interno, de domínio completamente qualificado (FQDN) e back-ends de multi-inquilino, como o serviço de aplicações do Azure. Membros do conjunto de back-end de Gateway de aplicação não estão associados a um conjunto de disponibilidade. Membros dos agrupamentos de back-end podem ser em clusters, centros de dados, ou fora do Azure, desde que eles têm conectividade IP.

### <a name="what-regions-is-the-service-available-in"></a>Que regiões o serviço está disponível no?

Gateway de aplicação está disponível em todas as regiões do global Azure. Também está disponível no [Azure China 21Vianet](https://www.azure.cn/) e [Azure Government](https://azure.microsoft.com/overview/clouds/government/)

### <a name="is-this-a-dedicated-deployment-for-my-subscription-or-is-it-shared-across-customers"></a>Esta é uma implementação dedicada para a minha subscrição ou é partilhado entre os clientes?

Gateway de aplicação é uma implementação dedicada na sua rede virtual.

### <a name="is-http-https-redirection-supported"></a>É HTTP -> redirecionamento a HTTPS suportado?

O redirecionamento é suportado. Ver [descrição geral do redirecionamento de Gateway de aplicação](application-gateway-redirect-overview.md) para saber mais.

### <a name="in-what-order-are-listeners-processed"></a>Ordem pela qual são serviços de escuta processados?

Serviços de escuta são processados na ordem em que forem sendo apresentados. Por esse motivo se uma solicitação de entrada corresponder a um serviço de escuta básico processa-lo primeiro.  Serviços de escuta de múltiplos sites devem ser configurados antes de um serviço de escuta básico para garantir que o tráfego é encaminhado para o back-end correto.

### <a name="where-do-i-find-application-gateways-ip-and-dns"></a>Onde posso encontrar IP do Gateway de aplicação e o DNS?

Quando utiliza um endereço IP público como um ponto de extremidade, estes pode encontrar informações no recurso de endereço IP público ou na página de descrição geral para o gateway de aplicação no portal. Para endereços IP internos, isso pode ser encontrado na página de descrição geral.

### <a name="what-is-keep-alive-timeout-and-tcp-idle-timeout-setting-on-application-gateway"></a>O que é o tempo limite de ligação Keep-Alive e definição de tempo limite de inatividade de TCP no Gateway de aplicação?

Tempo limite de ligação Keep-Alive no v1 SKU é o tempo limite de seg. 120 Keep-Alive no v2 SKU é o tempo limite de inatividade de TCP do segundo 75. é o padrão de 4 min no VIP do Gateway de aplicação de front-end.

### <a name="does-the-ip-or-dns-name-change-over-the-lifetime-of-the-application-gateway"></a>O nome IP ou DNS se altera ao longo da duração do Gateway de aplicação?

O VIP pode alterar se o gateway de aplicação é parado e iniciado. O nome DNS associado com o gateway de aplicação não se altera ao longo do ciclo de vida do gateway. Por esse motivo, é recomendado utilizar um alias CNAME e apontá-lo para o endereço DNS do gateway de aplicação.

### <a name="does-application-gateway-support-static-ip"></a>Gateway de aplicação suporta o IP estático?

Sim, o SKU do Gateway de aplicação v2 oferece suporte a endereços IP públicos estáticos. O SKU de v1 suporta IPs internos estáticos.

### <a name="does-application-gateway-support-multiple-public-ips-on-the-gateway"></a>O Gateway de aplicação suporta múltiplos IPs públicos no gateway?

Apenas um endereço IP público é suportado num gateway de aplicação.

### <a name="how-large-should-i-make-my-subnet-for-application-gateway"></a>Como grande deve verifico minha sub-rede de Gateway de aplicação?

Gateway de aplicação consome um endereço IP privado por instância, além de outro endereço IP privado, se uma configuração de IP de front-end privado está configurada. Além disso, o Azure reserva os primeiros quatro e o último endereço IP em cada sub-rede para utilização interna.
Por exemplo, se um gateway de aplicação está definido como três instâncias e não existe nenhum IP de front-end privado, em seguida, / 29 sub-rede tamanho ou superior é necessária. Neste caso, o gateway de aplicação utiliza três endereços IP. Se tiver três instâncias e um endereço IP para a configuração de IP de front-end privado, em seguida, / 28 sub-rede tamanho ou superior é necessário porque são necessários quatro endereços IP.

### <a name="q-can-i-deploy-more-than-one-application-gateway-resource-to-a-single-subnet"></a>P. Pode implementar mais do que um recurso de Gateway de aplicação para uma única sub-rede?

Sim, além de ter várias instâncias de uma determinada implantação de Gateway de aplicação, pode aprovisionar outro recurso de Gateway de aplicação exclusivo a uma sub-rede existente que contenha um recurso de Gateway de aplicação diferente.

Misturar Standard_v2 e o padrão Gateway de aplicação na mesma sub-rede não é suportada. Além disso, se o dimensionamento automático está ativado, uma sub-rede pode ter apenas um gateway de aplicação.

### <a name="does-application-gateway-support-x-forwarded-for-headers"></a>O Gateway de aplicação suporta cabeçalhos x-reencaminhados-para?

Sim, o Gateway de aplicação insere cabeçalhos x-reencaminhados-para proto x reencaminhados e porta x reencaminhados no pedido reencaminhado para o back-end. O formato para o cabeçalho x-reencaminhados-para-se uma lista separada por vírgulas de IP: porta. Os valores válidos para proto x reencaminhados são http ou https. Porta X reencaminhados Especifica a porta em que o pedido foi atingido no gateway de aplicação.

Gateway de aplicação também insere o cabeçalho X-Original-Host, que contém o cabeçalho de anfitrião original com a qual a solicitação chegou. Este cabeçalho é útil em cenários como a integração do Web site do Azure, onde o cabeçalho de anfitrião recebido é modificado antes do tráfego é encaminhado para o back-end.

### <a name="how-long-does-it-take-to-deploy-an-application-gateway-does-my-application-gateway-still-work-when-being-updated"></a>Quanto tempo demora a implementar um Gateway de aplicação? O meu Gateway de aplicação ainda funciona quando a ser atualizado?

Novas implementações de SKU de Gateway de aplicação v1 podem demorar até 20 minutos para aprovisionar. Alterações ao tamanho/contagem de instâncias não são disruptivas e o gateway permanece ativo durante este período.

Implementações de v2 SKU podem demorar cerca de cinco a seis minutos a aprovisionar.

## <a name="configuration"></a>Configuração

### <a name="is-application-gateway-always-deployed-in-a-virtual-network"></a>Gateway de aplicação é sempre implementado numa rede virtual?

Sim, o Gateway de aplicação é sempre implementado numa sub-rede de rede virtual. Esta sub-rede apenas pode conter os Gateways de aplicação.

### <a name="can-application-gateway-communicate-with-instances-outside-its-virtual-network"></a>Gateway de aplicação pode comunicar com instâncias fora de sua rede virtual?

Gateway de aplicação pode comunicar com instâncias de fora da rede virtual que está a ser, desde que existe conectividade IP. Se planeja usar IPs interno como membros do agrupamento de back-end, em seguida, ele requer [VNET Peering](../virtual-network/virtual-network-peering-overview.md) ou [Gateway de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="can-i-deploy-anything-else-in-the-application-gateway-subnet"></a>Posso implementar qualquer coisa na sub-rede de gateway de aplicação?

Não, mas pode implementar outros gateways de aplicação na sub-rede.

### <a name="are-network-security-groups-supported-on-the-application-gateway-subnet"></a>Grupos de segurança de rede são suportados na sub-rede de gateway de aplicação?

Grupos de segurança de rede (NSGs) são suportados na sub-rede de gateway de aplicação com as seguintes restrições:

* Exceções tiver de ser colocadas no tráfego de entrada nas portas 65503 65534 para o Gateway de aplicação v1 SKU e portas 65200-65535 para o SKU de v2. Este intervalo de portas é necessário para a comunicação de infraestrutura do Azure. Estão protegidas (bloqueadas) pelos certificados do Azure. Sem os certificados adequados, as entidades externas, incluindo os clientes desses gateways, não são possível iniciar quaisquer alterações nesses pontos finais.

* Conectividade de internet de saída não pode ser bloqueada. Regras de saída padrão no NSG já permitem a conectividade à internet. Recomendamos que não remove as regras de saída predefinido e que não criar outras regras de saída que negam conectividade de internet de saída.

* Tráfego a partir da etiqueta AzureLoadBalancer têm de ser permitido.

### <a name="are-user-defined-routes-supported-on-the-application-gateway-subnet"></a>Rotas definidas pelo utilizador são suportadas na sub-rede de gateway de aplicação?

Rotas definidas pelo utilizador (UDRs) são suportadas na sub-rede de gateway de aplicação, desde que elas não alteram a comunicação de ponto-a-ponto de solicitação/resposta.

Por exemplo, pode configurar um UDR na sub-rede de gateway de aplicação para apontar para um dispositivo de firewall para a inspeção de pacotes, mas tem de garantir que o pacote pode contactar a inspeção de mensagem do seu destino pretendido. Falha ao fazer isso pode resultar em comportamento do encaminhamento sonda ou tráfego de estado de funcionamento incorreto. Isto inclui as rotas aprendidas ou rotas predefinidas que 0.0.0.0/0 propagadas pelo ExpressRoute ou Gateways de VPN na rede virtual.

As UDRs na sub-rede de gateway de aplicação são **não** suportadas no v2 SKU. Para obter mais informações, consulte [dimensionamento automático e o Gateway de aplicação com redundância de zona (pré-visualização pública)](application-gateway-autoscaling-zone-redundant.md#known-issues-and-limitations).

### <a name="what-are-the-limits-on-application-gateway-can-i-increase-these-limits"></a>Quais são os limites no Gateway de aplicação? Pode aumentar estes limites?

Ver [limites do Gateway de aplicação](../azure-subscription-service-limits.md#application-gateway-limits) para ver os limites.

### <a name="can-i-use-application-gateway-for-both-external-and-internal-traffic-simultaneously"></a>Posso utilizar o Gateway de aplicação para o tráfego externo e interno ao mesmo tempo?

Sim, o Gateway de aplicação suporta a ter um IP interno e um IP externo por gateway de aplicação.

### <a name="is-vnet-peering-supported"></a>Peering de VNet suportado?

Sim, o VNet peering é suportado e é benéfico para tráfego de outras redes virtuais de balanceamento de carga.

### <a name="can-i-talk-to-on-premises-servers-when-they-are-connected-by-expressroute-or-vpn-tunnels"></a>Posso falar para servidores no local quando eles são ligados através de túneis VPN ou ExpressRoute?

Sim, desde que o tráfego é permitido.

### <a name="can-i-have-one-backend-pool-serving-many-applications-on-different-ports"></a>Pode ter um conjunto de back-end que serve vários aplicativos em portas diferentes?

Arquitetura do serviço micro é suportada. Precisaria várias definições de http configuradas para pesquisa em portas diferentes.

### <a name="do-custom-probes-support-wildcardsregex-on-response-data"></a>As pesquisas personalizadas suportam carateres universais/regex nos dados de resposta?

As pesquisas personalizadas não suportam carateres universais ou regex nos dados de resposta.

### <a name="how-are-rules-processed"></a>Como as regras são processadas?

As regras são processadas na ordem que estão configurados. Recomenda-se que as regras de múltiplos sites estão configuradas antes do básico de regras para reduzir a possibilidade de que o tráfego é encaminhado para o back-end inadequado como a regra básica corresponderia tráfego com base na porta antes da regra de múltiplos site a ser avaliada.

### <a name="what-does-the-host-field-for-custom-probes-signify"></a>O que indicar o campo de anfitrião para as pesquisas personalizadas?

Campo de anfitrião Especifica o nome para enviar a sonda a. Aplicável apenas quando vários sites está configurada no Gateway de aplicação, caso contrário, utilize "127.0.0.1". Este valor é diferente do nome de anfitrião VM e está no formato \<protocolo\>://\<anfitrião\>:\<porta\>\<caminho\>.

### <a name="can-i-whitelist-application-gateway-access-to-a-few-source-ips"></a>Posso lista de permissões de acesso de Gateway de aplicação para IPs de origem alguns?

Este cenário pode ser feito com NSGs na sub-rede de gateway de aplicação. As seguintes restrições devem ser colocadas na sub-rede na ordem listada de prioridade:

* Permitir o tráfego de entrada do intervalo de IP/IP de origem.

* Permitir pedidos recebidos de todas as origens para portas 65503 65534 para [comunicação de estado de funcionamento do back-end](application-gateway-diagnostics.md). Este intervalo de porta é necessário para a comunicação de infraestrutura do Azure. Estão protegidas (bloqueadas) pelos certificados do Azure. Sem os certificados adequados, as entidades externas, incluindo os clientes desses gateways, não será capazes de iniciar quaisquer alterações nesses pontos finais.

* Permitir entradas sondas do Balanceador de carga do Azure (etiqueta AzureLoadBalancer) e virtual rede tráfego de entrada (etiqueta VirtualNetwork) a [NSG](../virtual-network/security-overview.md).

* Bloquear todas as outras tráfego de entrada com uma negação todas as regras.

* Permitir o tráfego de saída à Internet para todos os destinos.

### <a name="can-the-same-port-be-used-for-both-public-and-private-facing-listeners"></a>A mesma porta pode ser utilizada para as escutas de destinado ao públicas e privadas?

Não, esta extensão não é suportada.

## <a name="performance"></a>Desempenho

### <a name="how-does-application-gateway-support-high-availability-and-scalability"></a>Como o Gateway de aplicação suporta elevada disponibilidade e escalabilidade?

O SKU do Gateway de aplicação v1 suporta cenários de elevada disponibilidade, quando tem duas ou mais instâncias implementadas. Em domínios de atualização e com falha para garantir que todas as instâncias não falharem ao mesmo tempo, o Azure distribui essas instâncias. O SKU de v1 oferece suporte a escalabilidade com a adição de várias instâncias do mesmo gateway para partilhar a carga.

O SKU de v2 assegura automaticamente que as novas instâncias são distribuídas por domínios de falha e domínios de atualização. Se for escolhida a redundância de zona, as instâncias mais recentes também são distribuídas por zonas de disponibilidade para oferecer resiliência de falha zonais.

### <a name="how-do-i-achieve-dr-scenario-across-data-centers-with-application-gateway"></a>Como posso obter o cenário de DR em centros de dados com o Gateway de aplicação?

Os clientes podem utilizar o Gestor de tráfego para distribuir o tráfego por vários Gateways de aplicação em datacenters diferentes.

### <a name="is-autoscaling-supported"></a>Dimensionamento automático é suportado?

Sim, o SKU do Gateway de aplicação v2 suporta o dimensionamento automático. Para obter mais informações, consulte [dimensionamento automático e o Gateway de aplicação com redundância de zona (pré-visualização pública)](application-gateway-autoscaling-zone-redundant.md).

### <a name="does-manual-scale-updown-cause-downtime"></a>Faz o dimensionamento manual para cima ou para baixo de tempo de inatividade causa?

Não há nenhum tempo de inatividade. Instâncias são distribuídas por domínios de atualização e domínios de falha.

### <a name="does-application-gateway-support-connection-draining"></a>O Gateway de aplicação suporta drenagem de ligação?

Sim. Pode configurar a ligação a ser drenado para alterar os membros dentro de um conjunto de back-end sem interrupção. Isso permite que as ligações existentes continuem a ser enviadas ao seu destino anterior até que essa conexão é fechada ou um tempo limite configurável expira. Drenagem de ligação apenas aguarda para ligações em trânsito atuais concluir. Gateway de aplicação não está ciente do Estado de sessão do aplicativo.

### <a name="can-i-change-instance-size-from-medium-to-large-without-disruption"></a>Posso alterar o tamanho de instância média para grandes sem interrupção?

Sim, o Azure distribui instâncias em vários domínios de atualização e com falha para garantir que todas as instâncias não falharem ao mesmo tempo. Gateway de aplicação suporta o dimensionamento ao adicionar várias instâncias do mesmo gateway para partilhar a carga.

## <a name="ssl-configuration"></a>Configuração do SSL

### <a name="what-certificates-are-supported-on-application-gateway"></a>Quais certificados são suportados no Gateway de aplicação?

São suportados certificados Autoassinados, certificados de AC, certificados EV e certificados de caráter universal.

### <a name="what-are-the-current-cipher-suites-supported-by-application-gateway"></a>Quais são os conjuntos de cifras atual suportados pelo Gateway de aplicação?

Seguem-se a atual conjuntos de cifras suportados pelo Gateway de aplicação. Ver [versões de política de configurar o SSL e conjuntos de cifras no Gateway de aplicação](application-gateway-configure-ssl-policy-powershell.md) para aprender a personalizar as opções de SSL.

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

### <a name="does-application-gateway-also-support-re-encryption-of-traffic-to-the-backend"></a>O Gateway de aplicação suporta também a reencriptação do tráfego para o back-end?

Sim, o Gateway de aplicação suporta a descarga de SSL e o SSL de ponto a ponto, o que reencripta o tráfego para o back-end.

### <a name="can-i-configure-ssl-policy-to-control-ssl-protocol-versions"></a>Posso configurar a política SSL para controlar as versões de protocolo SSL?

Sim, pode configurar o Gateway de aplicação para negar o TLS 1.0, TLS1.1 e TLS1.2. SSL 2.0 e 3.0 já estão desativadas por predefinição e não são configuráveis.

### <a name="can-i-configure-cipher-suites-and-policy-order"></a>Pode configurar conjuntos de cifras e ordem de política?

Sim, [configuração de conjuntos de cifras](application-gateway-ssl-policy-overview.md) é suportada. Ao definir uma política personalizada, pelo menos um dos conjuntos de cifras seguintes tem de estar ativado. Gateway de aplicação utiliza SHA256 para a gestão de back-end.

* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_256_CBC_SHA256
* TLS_RSA_WITH_AES_128_CBC_SHA256

### <a name="how-many-ssl-certificates-are-supported"></a>Quantos certificados SSL são suportados?

Até 100 SSL os certificados são suportados.

### <a name="how-many-authentication-certificates-for-backend-re-encryption-are-supported"></a>Quantos certificados de autenticação de back-end nova criptografia são suportados?

Certificados são suportados com uma predefinição de 5 até 10 de autenticação.

### <a name="does-application-gateway-integrate-with-azure-key-vault-natively"></a>O Gateway de aplicação integrar com o Azure Key Vault nativamente?

Não, não é integrado com o Azure Key Vault.

## <a name="web-application-firewall-waf-configuration"></a>Configuração do Web Application Firewall (WAF)

### <a name="does-the-waf-sku-offer-all-the-features-available-with-the-standard-sku"></a>O SKU da WAF oferece todas as funcionalidades disponíveis com o SKU Standard?

Sim, o WAF suporta todas as funcionalidades do SKU Standard.

### <a name="what-is-the-crs-version-application-gateway-supports"></a>Qual é a versão do CRS Gateway de aplicação suporta?

Gateway de aplicação suporta CRS [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229) e o CRS [3.0](application-gateway-crs-rulegroups-rules.md#owasp30).

### <a name="how-do-i-monitor-waf"></a>Como posso monitorizar o WAF?

WAF é monitorizada com o log de diagnóstico, obter mais informações sobre o registo de diagnósticos podem ser encontradas em [log de diagnóstico e métricas para o Gateway de aplicação](application-gateway-diagnostics.md)

### <a name="does-detection-mode-block-traffic"></a>Modo de deteção bloquear o tráfego?

Não, modo de deteção apenas registos de tráfego, que é acionada uma regra de WAF.

### <a name="can-i-customize-waf-rules"></a>Pode personalizar regras WAF?

Sim, as regras de WAF são personalizáveis. Para obter mais informações, consulte [grupos de regras WAF personalizar e regras](application-gateway-customize-waf-rules-portal.md)

### <a name="what-rules-are-currently-available"></a>As regras que estão atualmente disponíveis

Atualmente, o WAF suporta CRS [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229) e [3.0](application-gateway-crs-rulegroups-rules.md#owasp30), que fornecem segurança de linha de base contra a maioria das vulnerabilidades identificadas pelo Open Web Application Security Project (OWASP) os 10 principais encontrar aqui [ OWASP top 10 vulnerabilidades](https://www.owasp.org/index.php/Top10#OWASP_Top_10_for_2013)

* Proteção contra injeção de SQL

* Proteção contra scripting entre sites

* Proteção contra Ataques Web comuns, como, por exemplo, injeção de comandos, contrabando de pedidos HTTP, divisão de respostas HTTP e ataques remotos de inclusão de ficheiros

* Proteção contra violações de protocolo HTTP

* Proteção contra anomalias de protocolo HTTP, como agente de utilizador de anfitrião e cabeçalhos de aceitação em falta

* Prevenção de contra bots, crawlers e scanners

* Deteção de aplicação incorretas comuns (ou seja, Apache, IIS, etc.)

### <a name="does-waf-also-support-ddos-prevention"></a>O WAF também oferece suporte a prevenção de DDoS?

Sim. Pode ativar a proteção contra DDos na VNet em que o gateway de aplicação é implementado. Isto garante que o gateway de aplicação que VIP é também protegido com o serviço Azure DDos Protection.

## <a name="diagnostics-and-logging"></a>Registo e diagnóstico

### <a name="what-types-of-logs-are-available-with-application-gateway"></a>Que tipos de registos estão disponíveis com o Gateway de aplicação?

Existem três logs disponíveis para o Gateway de aplicação. Para obter mais informações sobre estes registos e outros recursos de diagnóstico, consulte [back-end estado de funcionamento, registos de diagnóstico e métricas para o Gateway de aplicação](application-gateway-diagnostics.md).

* **ApplicationGatewayAccessLog** -o registo de acesso contém cada pedido submetido para o front-end de gateway de aplicação. Os dados incluem o IP do chamador, URL solicitada, latência de resposta, devolver o código, de bytes de entrada e saída. Registo de acesso é recolhido de 300 segundos. Este registo contém um registo por instância de um gateway de aplicação.
* **ApplicationGatewayPerformanceLog** -o registo de desempenho captura informações de desempenho na base as instâncias incluindo total pedido servido, débito em bytes, total de pedidos servidos, contagem de pedidos com falhas, com e sem integridade back-end Contagem de instâncias.
* **ApplicationGatewayFirewallLog** -o registo de firewall contém pedidos que são registados através do modo de deteção ou prevenção de um gateway de aplicação que está configurado com firewall de aplicações web.

### <a name="how-do-i-know-if-my-backend-pool-members-are-healthy"></a>Como posso saber se minha membros do agrupamento de back-end estão em bom Estados?

Pode utilizar o cmdlet do PowerShell `Get-AzApplicationGatewayBackendHealth` ou verificar o estado de funcionamento através do portal, visite a página [diagnóstico do Gateway de aplicação](application-gateway-diagnostics.md)

### <a name="what-is-the-retention-policy-on-the-diagnostics-logs"></a>O que é a política de retenção de registos de diagnóstico?

Fluxo de registos de diagnóstico para a conta de armazenamento de clientes e os clientes podem definir a política de retenção com base nas suas preferências. Os registos de diagnóstico também podem ser enviados para um Hub de eventos ou do Azure Monitor registos. Ver [Application Gateway Diagnostics](application-gateway-diagnostics.md) para obter mais detalhes.

### <a name="how-do-i-get-audit-logs-for-application-gateway"></a>Como posso obter registos de auditoria para o Gateway de aplicação?

Registos de auditoria estão disponíveis para o Gateway de aplicação. No portal, clique em **registo de atividades** no painel do menu de um gateway de aplicação para aceder ao registo de auditoria. 

### <a name="can-i-set-alerts-with-application-gateway"></a>Pode definir alertas com o Gateway de aplicação?

Sim, o Gateway de aplicação suporta alertas. Os alertas são configurados em métricas. Ver [as métricas de Gateway de aplicação](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#metrics) para saber mais sobre as métricas de Gateway de aplicação. Para obter mais informações sobre alertas, consulte [receber notificações de alerta](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

### <a name="how-do-i-analyze-traffic-statistics-for-application-gateway"></a>Como posso analisar as estatísticas de tráfego para o Gateway de aplicação?

Pode ver e analisar registos de acesso através de vários mecanismos, como registos do Azure Monitor, Excel, Power BI etc.

Também Publicámos um modelo do Resource Manager que instala e executa o popular [GoAccess](https://goaccess.io/) log analyzer para aceder aos registos do Gateway de aplicação. GoAccess fornece valiosas estatísticas de tráfego HTTP, como visitantes exclusivos, arquivos de pedido, anfitriões, sistemas operacionais, navegadores, códigos de estado de HTTP e muito mais. Para obter mais detalhes, consulte a [ficheiro Leia-me na pasta de modelo do Resource Manager no GitHub](https://aka.ms/appgwgoaccessreadme).

### <a name="backend-health-returns-unknown-status-what-could-be-causing-this-status"></a>Estado de funcionamento do back-end devolve o estado desconhecido, o que pode estar a causar este Estado?

A razão mais comum é o acesso ao back-end é bloqueado por um NSG ou DNS personalizado. Ver [back-end estado de funcionamento, o registo de diagnósticos e métricas para o Gateway de aplicação](application-gateway-diagnostics.md) para saber mais.

## <a name="next-steps"></a>Próximos Passos

Para saber mais sobre o Gateway de aplicação, consulte [o que é o Gateway de aplicação do Azure?](overview.md)
