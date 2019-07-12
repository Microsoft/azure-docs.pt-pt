---
title: Descrição geral da configuração de Gateway de aplicação do Azure
description: Este artigo descreve como configurar os componentes do Gateway de aplicação do Azure
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 6/1/2019
ms.author: absha
ms.openlocfilehash: 86b41bb2554dbefeaeb724e746bcaf757463f8ae
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795909"
---
# <a name="application-gateway-configuration-overview"></a>Descrição geral de configuração do Gateway de aplicação

O Gateway de aplicação do Azure é composta por vários componentes que pode configurar de várias formas para diferentes cenários. Este artigo mostra-lhe como configurar cada componente.

![Gráfico de fluxo de componentes de Gateway de aplicação](./media/configuration-overview/configuration-overview1.png)

Esta imagem ilustra uma aplicação que tenha três serviços de escuta. As duas primeiras são serviços de escuta de múltiplos sites para `http://acme.com/*` e `http://fabrikam.com/*`, respectivamente. Ambos escutam na porta 80. O terceiro é um serviço de escuta básico que tenha a terminação de Secure Sockets Layer (SSL)-a-ponto.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

### <a name="azure-virtual-network-and-dedicated-subnet"></a>Rede virtual do Azure e a sub-rede dedicada

Um gateway de aplicação é uma implementação dedicada na sua rede virtual. Na sua rede virtual, numa sub-rede dedicada é necessária para o gateway de aplicação. Pode ter várias instâncias de uma implementação de gateway de aplicação específica numa sub-rede. Também pode implementar outros gateways de aplicação na sub-rede. Mas não é possível implementar qualquer outro recurso na sub-rede de gateway de aplicação.

> [!NOTE]
> Não é possível misturar Standard_v2 e Standard do Azure para o Gateway de aplicação na mesma sub-rede.

#### <a name="size-of-the-subnet"></a>Tamanho da sub-rede

Gateway de aplicação consome 1 endereço IP privado por instância, além de outro endereço IP privado se estiver configurado um IP de front-end privado.

O Azure reserva também 5 endereços IP em cada sub-rede para uso interno: o primeiro 4 e o último endereços IP. Por exemplo, considere 15 instâncias de gateway de aplicação com nenhum IP de front-end privado. Terá de, pelo menos, 20 endereços IP para esta sub-rede: 5 para uso interno e 15 para as instâncias de gateway de aplicação. Por isso, terá de/27 sub-rede do tamanho ou superior.

Considere uma sub-rede que tem a 27 de instâncias de gateway de aplicação e um endereço IP para um IP de front-end privado. Neste caso, terá de 33 endereços IP: 27 para as instâncias de gateway de aplicação, 1 para o front-end privado e 5 para utilização interna. Por isso, terá um /26 sub-rede do tamanho ou superior.

Recomendamos que utilize um tamanho de sub-rede pelo menos/28. Este tamanho oferece utilizáveis 11 de endereços IP. Se a carga de aplicação necessitar de mais de 10 endereços IP, considere/27 ou/26 tamanho da sub-rede.

#### <a name="network-security-groups-on-the-application-gateway-subnet"></a>Grupos de segurança de rede na sub-rede de Gateway de aplicação

Grupos de segurança de rede (NSGs) são suportados no Gateway de aplicação. No entanto, existem várias restrições:

- Tem de incluir exceções para o tráfego de entrada nas portas 65503 65534 para o SKU do Gateway de aplicação v1 e portas 65200-65535 para o SKU da v2. Este intervalo de porta é necessário para a comunicação de infraestrutura do Azure. Estas portas são protegidas (bloqueadas) pelos certificados do Azure. Entidades externas, incluindo os clientes desses gateways, não é possível iniciar alterações nesses pontos finais sem os certificados apropriados no local.

- Conectividade de internet de saída não pode ser bloqueada. Regras de saída padrão no NSG permitem a conectividade à internet. Recomendamos que:

  - Não remova as regras de saída padrão.
  - Não crie outras regras de saída que negam conectividade de internet de saída.

- Tráfego a partir da **AzureLoadBalancer** etiqueta tem de ter permissão.

##### <a name="allow-application-gateway-access-to-a-few-source-ips"></a>Permitir o acesso de Gateway de aplicação para alguns IPs de origem

Para este cenário, utilize NSGs na sub-rede de Gateway de aplicação. Coloca as seguintes restrições na sub-rede por esta ordem de prioridade:

1. Permitir o tráfego de entrada de um intervalo de IP/IP de origem.
2. Permitir pedidos recebidos de todas as origens para portas 65503 65534 para [comunicação de estado de funcionamento do back-end](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics). Este intervalo de porta é necessário para a comunicação de infraestrutura do Azure. Estas portas são protegidas (bloqueadas) pelos certificados do Azure. Sem os certificados apropriados no local, entidades externas não é possível iniciar alterações nesses pontos finais.
3. Permitir a entrada do Azure Load Balancer sonda (*AzureLoadBalancer* marca) e tráfego de rede virtual de entrada (*VirtualNetwork* marca) no [grupo de segurança de rede](https://docs.microsoft.com/azure/virtual-network/security-overview).
4. Bloquear todos os outros tráfegos de entrada com uma regra negar.
5. Permitir o tráfego de saída à Internet para todos os destinos.

#### <a name="user-defined-routes-supported-on-the-application-gateway-subnet"></a>Rotas definidas pelo utilizador suportadas na sub-rede de Gateway de aplicação

Para o SKU de v1, as rotas definidas pelo utilizador (UDRs) são suportadas na sub-rede de Gateway de aplicação, desde que não alteram a comunicação de ponto-a-ponto de solicitação/resposta. Por exemplo, pode configurar um UDR na sub-rede de Gateway de aplicação para apontar para um dispositivo de firewall para a inspeção de pacotes. Mas deve certificar-se de que o pacote pode contactar o destino pretendido após a inspeção. Falha ao fazer isso pode resultar em sonda de estado de funcionamento incorreta ou o comportamento de encaminhamento de tráfego. Isto inclui as rotas aprendidas ou rotas predefinidas que 0.0.0.0/0 que sejam propagadas pelos gateways VPN ou ExpressRoute do Azure na rede virtual.

Para o SKU de v2, as UDRs não são suportadas na sub-rede de Gateway de aplicação. Para obter mais informações, consulte [SKU do Gateway de aplicação do Azure v2](application-gateway-autoscaling-zone-redundant.md#differences-with-v1-sku).

> [!NOTE]
> As UDRs não são suportadas para o SKU da v2.  Se necessitar de UDRs deve continuar a implementar o SKU de v1.

> [!NOTE]
> Utilizar as UDRs na sub-rede de Gateway de aplicação faz com que o estado de funcionamento no [vista de estado de funcionamento do back-end](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#back-end-health) a aparecer como "Desconhecido". Também faz com que a geração de registos do Gateway de aplicação e métricas para efetuar a ativação. Recomendamos que não use as UDRs na sub-rede de Gateway de aplicação para que pode ver o estado de funcionamento do back-end, registos e métricas.

## <a name="front-end-ip"></a>IP de front-end

Pode configurar o gateway de aplicação para ter um endereço IP público, um endereço IP privado ou ambos. Um IP público é necessário ao hospedar um back-end que os clientes têm de aceder através da internet por meio de um acesso à internet IP virtual (VIP). 

Um IP público não é necessário para um ponto final interno não exposto à internet. Que é conhecido como um *Balanceador de carga interno* ponto final (ILB). Um gateway de aplicação ILB é útil para aplicações de linha de negócio internas, que não são expostas à internet. Também é útil para os serviços e camadas num aplicativo de várias camada dentro de um limite de segurança que não são expostos à internet, mas que requerem que o round robin carga distribuição, a persistência da sessão ou a terminação de SSL.

Apenas 1 endereço IP público ou 1 endereço IP privado é suportado. Escolha o IP de front-end ao criar o gateway de aplicação.

- Para um IP público, pode criar um novo endereço IP público ou utilizar um IP público existente na mesma localização que o gateway de aplicação. Se criar um novo IP público, o tipo de endereço IP que selecionou (estático ou dinâmico) não é possível alterar mais tarde. Para obter mais informações, consulte [estático vs. o endereço IP público dinâmico](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#static-versus-dynamic-public-ip-address).

- Para um IP privado, pode especificar um endereço IP privado da sub-rede em que o gateway de aplicação é criado. Se não for especificada, é selecionado automaticamente um endereço IP arbitrário da sub-rede. Para obter mais informações, consulte [criar um gateway de aplicação com um balanceador de carga interno](https://docs.microsoft.com/azure/application-gateway/application-gateway-ilb-arm).

Um endereço IP Front-end está associado a um *serviço de escuta*, que verifica a existência de pedidos recebidos no IP de front-end.

## <a name="listeners"></a>Ouvintes

Um serviço de escuta é uma entidade lógica que verifica a existência de pedidos recebidos de ligação utilizando a porta, protocolo, anfitrião e endereço IP. Quando configurar o serviço de escuta, tem de introduzir valores para estas que correspondem aos valores correspondentes no pedido de entrada no gateway.

Quando cria um gateway de aplicação com o portal do Azure, também criar um serviço de escuta do padrão ao escolher o protocolo e a porta para o serviço de escuta. Pode escolher se pretende ativar o suporte para HTTP2 sobre o serviço de escuta. Depois de criar o gateway de aplicação, pode editar as definições do serviço de escuta padrão (*appGatewayHttpListener*/*appGatewayHttpsListener*) ou criar novos serviços de escuta.

### <a name="listener-type"></a>Tipo de serviço de escuta

Ao criar um novo serviço de escuta, é escolher entre [ *básica* e *multilocal*](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#types-of-listeners).

- Se estiver hospedando um único site por trás de um gateway de aplicação, escolha básica. Saiba mais [como criar um gateway de aplicação com um serviço de escuta básico](https://docs.microsoft.com/azure/application-gateway/quick-create-portal).

- Se estiver a configurar mais de um aplicativo web ou vários subdomínios do mesmo domínio principal na mesma instância de gateway de aplicação, escolha o serviço de escuta de múltiplos site. Para um serviço de escuta de múltiplos site, também tem de introduzir um nome de anfitrião. Isto acontece porque o Gateway de aplicação baseia-se em cabeçalhos de anfitrião HTTP 1.1 para alojar mais do que um site no mesmo endereço IP público e porta.

#### <a name="order-of-processing-listeners"></a>Ordem de processamento de serviços de escuta

Para o SKU de v1, serviços de escuta são processados na ordem que estão listados. Se um serviço de escuta básico corresponder a uma solicitação de entrada, o serviço de escuta processa pela primeira vez que a solicitação. Por isso, configure os serviços de escuta de múltiplos sites antes de serviços de escuta básicos para se certificar de que o tráfego é encaminhado para o back-end correto.

Para o SKU de v2, serviços de escuta de múltiplos sites são processados antes de serviços de escuta básicos.

### <a name="front-end-ip"></a>IP de front-end

Escolha o endereço IP Front-end que pretende associar este serviço de escuta. O serviço de escuta irá escutar os pedidos recebidos neste IP.

### <a name="front-end-port"></a>Porta de front-end

Escolha a porta de front-end. Selecione uma porta existente ou crie um novo. Escolha qualquer valor entre o [intervalo de portas permitido](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#ports). Pode usar não apenas portas bem conhecidas, como 80 e 443, mas qualquer porta permitida personalizada que é adequada. Uma porta pode ser utilizada para serviços de escuta de destinado ao público ou privado com acesso à escuta.

### <a name="protocol"></a>Protocol

Escolha HTTP ou HTTPS:

- Se optar por HTTP, o tráfego entre o cliente e o gateway de aplicação é não encriptado.

- Escolha o HTTPS se desejar [terminação de SSL](https://docs.microsoft.com/azure/application-gateway/overview#secure-sockets-layer-ssltls-termination) ou [encriptação SSL de ponta a ponta](https://docs.microsoft.com/azure/application-gateway/ssl-overview). O tráfego entre o cliente e o gateway de aplicação é encriptado. E termina a ligação de SSL no gateway de aplicação. Se pretender que a encriptação SSL ponto a ponto, tem de escolher o HTTPS e configurar o **HTTP de back-end** definição. Isto garante que o tráfego é criptografado novamente quando ele viaja do gateway de aplicação para o back-end.

Para configurar a encriptação de SSL de ponto-a-ponto e terminação de SSL, tem de adicionar um certificado para o serviço de escuta para ativar o gateway de aplicação derivar uma chave simétrica. Isso é ditado pela especificação de protocolo SSL. A chave simétrica é utilizada para encriptar e desencriptar o tráfego enviado para o gateway. O certificado do gateway tem de estar no formato Personal Information Exchange (PFX). Este formato permite-lhe exportar a chave privada que o gateway utiliza para encriptar e desencriptar o tráfego.

#### <a name="supported-certificates"></a>Certificados suportados

Ver [certificados suportados para a terminação de SSL](https://docs.microsoft.com/azure/application-gateway/ssl-overview#certificates-supported-for-ssl-termination).

### <a name="additional-protocol-support"></a>Suporte de protocolos adicionais

#### <a name="http2-support"></a>Suporte de HTTP2

Suporte de protocolo HTTP/2 está disponível para clientes que se ligam para ouvintes de gateway de aplicação apenas. A comunicação para agrupamentos de servidores de back-end é através de HTTP/1.1. Por predefinição, o suporte de HTTP/2 está desativado. O trecho de código do Azure PowerShell seguinte mostra como ativar esta opção:

```azurepowershell
$gw = Get-AzApplicationGateway -Name test -ResourceGroupName hm

$gw.EnableHttp2 = $true

Set-AzApplicationGateway -ApplicationGateway $gw
```

#### <a name="websocket-support"></a>Suporte do WebSocket

Suporte de WebSocket está ativado por predefinição. Não existe qualquer definição configurável pelo utilizador para ativar ou desativar ele. Pode utilizar WebSockets com serviços de escuta HTTP e HTTPS.

### <a name="custom-error-pages"></a>Páginas de erro personalizadas

Pode definir o erro personalizado ao nível global ou ao nível de serviço de escuta. Mas a criação de páginas de erro personalizadas de nível global no portal do Azure não é atualmente suportada. Pode configurar uma página de erro personalizado para um erro de firewall de aplicação 403 web ou uma página de 502 manutenção no nível de serviço de escuta. Também tem de especificar um URL acessível publicamente blob para o código de estado de erro fornecida. Para obter mais informações, consulte [Criar páginas de erro personalizadas do Gateway de Aplicação](https://docs.microsoft.com/azure/application-gateway/custom-error).

![Códigos de erro do Gateway de aplicação](https://docs.microsoft.com/azure/application-gateway/media/custom-error/ag-error-codes.png)

Para configurar uma página de erro personalizada global, consulte [configuração do Azure PowerShell](https://docs.microsoft.com/azure/application-gateway/custom-error#azure-powershell-configuration).

### <a name="ssl-policy"></a>Política de SSL

Pode centralizar a gestão de certificados SSL e reduzir a encriptação-desencriptação sobrecarga de um farm de servidores de back-end. Também o tratamento de SSL centralizado permite-lhe especificar uma política SSL central que é adequada aos seus requisitos de segurança. Pode escolher *predefinição*, *predefinidos*, ou *personalizado* política SSL.

Configurar política SSL para as versões de protocolo SSL do controle. Pode configurar um gateway de aplicação para negar o TLS 1.0, TLS1.1 e TLS1.2. Por padrão, SSL 2.0 e 3.0 estão desativados e não são configuráveis. Para obter mais informações, consulte [descrição geral da política de SSL do Gateway de aplicação](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-policy-overview).

Depois de criar um serviço de escuta, associa-a com uma regra de encaminhamento de pedidos. Essa regra determina como os pedidos que são recebidos do serviço de escuta de são encaminhados para o back-end.

## <a name="request-routing-rules"></a>Regras de encaminhamento de pedidos

Quando cria um gateway de aplicação com o portal do Azure, criar uma regra predefinida (*rule1*). Esta regra vincula o serviço de escuta do padrão (*appGatewayHttpListener*) com o conjunto de back-end predefinido (*appGatewayBackendPool*) e as predefinições de HTTP de back-end ( *appGatewayBackendHttpSettings*). Depois de criar o gateway, pode editar as definições da regra predefinida ou criar novas regras.

### <a name="rule-type"></a>Tipo de regra

Ao criar uma regra, é escolher entre [ *básica* e *baseado no caminho*](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#request-routing-rules).

- Escolha básico, se quiser reencaminhar todos os pedidos no serviço de escuta associado (por exemplo, *blog<i></i>.contoso.com/\*)* para um único conjunto de back-end.
- Escolha baseado no caminho se quiser encaminhar os pedidos de caminhos de URL específicos para conjuntos de back-end específicos. O padrão de caminho é aplicado apenas para o caminho do URL, não para seus parâmetros de consulta.

#### <a name="order-of-processing-rules"></a>Ordem das regras de processamento

Para o SKU de v1, a correspondência de mensagens em fila de pedidos recebidos é processada pela ordem em que os caminhos estão listados no mapa de caminho de URL da regra com base no caminho. Se um pedido corresponde ao padrão em duas ou mais caminhos do mapa do caminho, o caminho que está listado em primeiro lugar é correspondido. E a solicitação é encaminhada para o back-end que está associada esse caminho.

Para o SKU de v2, uma correspondência exata é de prioridade superior à ordem de caminho no mapa do caminho de URL. Se um pedido corresponde ao padrão em duas ou mais caminhos, a solicitação é encaminhada para o back-end que está associada o caminho que corresponde exatamente ao pedido. Se o caminho no pedido de entrada não corresponde exatamente a qualquer caminho no mapa, a correspondência do pedido é processada na lista de ordem de mapa de caminho para a regra com base no caminho.

### <a name="associated-listener"></a>Serviço de escuta associado

Associar um serviço de escuta para a regra para que o *regra de encaminhamento de pedidos* que está associado com o serviço de escuta é avaliado para determinar o conjunto de back-end para encaminhar o pedido para.

### <a name="associated-back-end-pool"></a>Conjunto de back-end associado

Recebe o conjunto de back-end que contém os destinos de back-end que atendem a solicitações de que o serviço de escuta associados à regra.

 - Para uma regra básica, é permitido apenas um conjunto de back-end. Todos os pedidos no serviço de escuta associado são reencaminhados para esse conjunto de back-end.

 - Para uma regra com base no caminho, adicione vários conjuntos de back-end que correspondem a cada caminho de URL. Os pedidos que corresponde ao caminho do URL que é introduzido são reencaminhados para o conjunto de back-end correspondente. Além disso, adicione um conjunto de back-end predefinido. Pedidos que não correspondam a qualquer caminho de URL na regra são reencaminhados para o mesmo.

### <a name="associated-back-end-http-setting"></a>Associado à definição de HTTP de back-end

Adicione uma definição de HTTP de back-end para cada regra. Usando o número de porta, protocolo e outras informações que são especificadas nesta definição, os pedidos são encaminhados do gateway de aplicação para os destinos de back-end.

Para uma regra básica, é permitida apenas uma definição de HTTP de back-end. Todos os pedidos no serviço de escuta associado são reencaminhados para os destinos de back-end correspondentes ao utilizar esta definição de HTTP.

Adicione uma definição de HTTP de back-end para cada regra. Usando o número de porta, protocolo e outras informações de que é especificada nesta definição, os pedidos são encaminhados do gateway de aplicação para os destinos de back-end.

Para uma regra básica, é permitida apenas uma definição de HTTP de back-end. Todos os pedidos no serviço de escuta associado são reencaminhados para os destinos de back-end correspondentes ao utilizar esta definição de HTTP.

Para uma regra com base no caminho, adicione várias definições de HTTP de back-end que correspondem a cada caminho de URL. Pedidos que corresponde ao caminho do URL nesta definição são reencaminhados para os destinos de back-end correspondentes com as definições de HTTP que correspondem a cada caminho de URL. Além disso, adicione uma definição de HTTP padrão. Pedidos que não correspondam a qualquer caminho de URL nesta regra são reencaminhados para o conjunto de back-end predefinido, utilize a definição de HTTP padrão.

### <a name="redirection-setting"></a>Configuração de redirecionamento

Se o redirecionamento está configurado para uma regra básica, todos os pedidos no serviço de escuta associado são redirecionados para o destino. Isto é *global* redirecionamento. Se o redirecionamento está configurado para uma regra baseada em caminho, são redirecionados apenas pedidos numa área de site específico. Um exemplo é uma área de carrinho de compras é indicada pelo */cart/\** . Isto é *baseado no caminho* redirecionamento.

Para obter mais informações sobre redirecionamentos, consulte [descrição geral do redirecionamento de Gateway de aplicação](https://docs.microsoft.com/azure/application-gateway/redirect-overview).

#### <a name="redirection-type"></a>Tipo de redirecionamento

Escolha o tipo de redirecionamento de: *Permanent(301)* , *Temporary(307)* , *Found(302)* , ou *ver other(303)* .

#### <a name="redirection-target"></a>Destino de redirecionamento

Escolha outro serviço de escuta ou um site externo, como o destino de redirecionamento.

##### <a name="listener"></a>Serviço de escuta

Escolha o serviço de escuta de como o destino de redirecionamento para redirecionar o tráfego de um serviço de escuta para outra no gateway. Esta definição é necessária para ativar o redirecionamento de HTTP para HTTPS. Ele redireciona o tráfego a partir do serviço de escuta de origem que verifica a existência de pedidos de HTTP de entrada para o serviço de escuta de destino que verifica a existência de pedidos recebidos de HTTPS. Também pode optar por incluir a cadeia de consulta e o caminho do pedido original no pedido que seja reencaminhado para o destino de redirecionamento.

![Caixa de diálogo de componentes de Gateway de aplicação](./media/configuration-overview/configure-redirection.png)

Para obter mais informações sobre o redirecionamento de HTTP para HTTPS, consulte:
- [Redirecionamento de HTTP para HTTPS com o portal do Azure](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-portal)
- [Redirecionamento de HTTP para HTTPS com o PowerShell](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-powershell)
- [Redirecionamento de HTTP para HTTPS, utilizando a CLI do Azure](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-cli)

##### <a name="external-site"></a>Site externo

Escolha o site externo quando deseja redirecionar o tráfego no serviço de escuta que está associada esta regra para um site externo. Pode optar por incluir a cadeia de consulta do pedido original no pedido que seja reencaminhado para o destino de redirecionamento. Não é possível reencaminhar o caminho para o site externo que estava na solicitação original.

Para obter mais informações sobre o redirecionamento, consulte:
- [Redirecionar o tráfego para um site externo com o PowerShell](https://docs.microsoft.com/azure/application-gateway/redirect-external-site-powershell)
- [Redirecionar o tráfego para um site externo com a CLI](https://docs.microsoft.com/azure/application-gateway/redirect-external-site-cli)

#### <a name="rewrite-the-http-header-setting"></a>Reescrever a definição de cabeçalho HTTP

Esta definição adiciona, remove ou cabeçalhos de solicitação e resposta HTTP enquanto o pedido de atualizações e pacotes de resposta se mover entre o cliente e os conjuntos de back-end. Só pode configurar esse recurso através do PowerShell. Portal do Azure e o suporte da CLI ainda não disponíveis. Para obter mais informações, consulte:

 - [Descrição geral de cabeçalhos HTTP de reescrever](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers)
 - [Configurar a reescrita de cabeçalho HTTP](https://docs.microsoft.com/azure/application-gateway/add-http-header-rewrite-rule-powershell#specify-the-http-header-rewrite-rule-configuration)

## <a name="http-settings"></a>Definições de HTTP

O gateway de aplicação encaminha o tráfego para os servidores de back-end utilizando a configuração que especificar aqui. Depois de criar uma definição de HTTP, precisa de associá-lo a uma ou mais regras de encaminhamento de pedidos.

### <a name="cookie-based-affinity"></a>Afinidade com base no cookie

Esta funcionalidade é útil quando deseja manter uma sessão de utilizador no mesmo servidor. Cookies geridos por gateway permitem que o application gateway direto tráfego subsequente de uma sessão de utilizador para o mesmo servidor para processamento. Isso é importante quando o estado da sessão é guardado localmente no servidor para uma sessão de utilizador. Se a aplicação não é possível processar a afinidade com base em cookies, não é possível utilizar esta funcionalidade. Para usá-lo, certifique-se de que os clientes suportam a cookies.

### <a name="connection-draining"></a>Drenagem de ligação

Drenagem de ligação ajuda-o com elegância remover membros do conjunto de back-end durante as atualizações de serviço planeada. Pode aplicar esta definição para todos os membros de um conjunto de back-end durante a criação de regra. Ele garante que todas as instâncias de anular o registo de um conjunto de back-end não recebem as novas solicitações. Enquanto isso, são permitidos pedidos existentes para ser concluída no prazo de um limite de tempo configurado. Drenagem de ligação aplica-se com as instâncias de back-end que são explicitamente removidas do conjunto back-end por uma chamada à API. Também se aplica às instâncias de back-end que são apresentadas como *mau estado de funcionamento* pelo Estado de funcionamento sondas.

### <a name="protocol"></a>Protocol

Gateway de aplicação suporta HTTP e HTTPS de encaminhamento de pedidos para os servidores de back-end. Se optar por HTTP, o tráfego para os servidores de back-end é não encriptado. Se a comunicação desencriptada não for aceitável, escolha o HTTPS.

Esta definição juntamente com HTTPS em suporta o serviço de escuta [SSL ponto a ponto](https://docs.microsoft.com/azure/application-gateway/ssl-overview). Isto permite-lhe transmitir dados confidenciais encriptados para o back-end de forma segura. Cada servidor de back-end no conjunto de back-end com SSL de ponta a ponta ativado deve ser configurado com um certificado para permitir a comunicação segura.

### <a name="port"></a>Port

Esta definição especifica a porta em que os servidores de back-end escutam o tráfego do gateway de aplicação. Pode configurar as portas entre 1 e 65535.

### <a name="request-timeout"></a>Tempo limite do pedido

Esta definição é o número de segundos que o gateway de aplicação aguarda para receber uma resposta do conjunto de back-end antes de devolver uma mensagem de erro "atingiu o tempo limite de ligação".

### <a name="override-back-end-path"></a>Substituir o caminho de back-end

Esta definição permite-lhe configurar um caminho de encaminhamento personalizado opcional para utilizar quando a solicitação é encaminhada para o back-end. Qualquer parte do caminho de entrada que corresponde ao caminho personalizado no **substituir o caminho de back-end** campo é copiado para o caminho reencaminhado. A tabela seguinte mostra como funciona este recurso:

- Quando a definição de HTTP está ligada a uma regra básica de encaminhamento de pedidos:

  | Solicitação original  | Substituir o caminho de back-end | Pedido reencaminhado para o back-end |
  | ----------------- | --------------------- | ---------------------------- |
  | /Home/            | /override/            | / Substituir/home /              |
  | / home/secondhome / | /override/            | / substituição/home/secondhome /   |

- Quando a definição de HTTP está ligada a uma regra de encaminhamento de pedidos com base no caminho:

  | Solicitação original           | Regra de caminho       | Substituir o caminho de back-end | Pedido reencaminhado para o back-end |
  | -------------------------- | --------------- | --------------------- | ---------------------------- |
  | /pathrule/home/            | /pathrule*      | /override/            | / Substituir/home /              |
  | /pathrule/home/secondhome/ | /pathrule*      | /override/            | / substituição/home/secondhome /   |
  | /Home/                     | /pathrule*      | /override/            | / Substituir/home /              |
  | / home/secondhome /          | /pathrule*      | /override/            | / substituição/home/secondhome /   |
  | /pathrule/home/            | /pathrule/home* | /override/            | /override/                   |
  | /pathrule/home/secondhome/ | /pathrule/home* | /override/            | / Substituir/secondhome /        |
  | /pathrule/                 | /pathrule/      | /override/            | /override/                   |

### <a name="use-for-app-service"></a>Utilização para o serviço de aplicações

Este é um atalho de interface do Usuário que seleciona as duas definições necessárias para o back-end de serviço de aplicações do Azure. Ele permite **escolha o nome de anfitrião do endereço de back-end**, e cria uma sonda personalizada nova. (Para obter mais informações, consulte a [escolha o nome de anfitrião do endereço de back-end](#pick) definição seção deste artigo.) É criada uma nova sonda e o cabeçalho de sonda é escolhido do endereço do membro de back-end.

### <a name="use-custom-probe"></a>Utilizar sonda personalizada

Esta definição associa um [sonda personalizada](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#custom-health-probe) com uma definição de HTTP. Pode associar apenas uma sonda personalizada com uma definição de HTTP. Se não associar explicitamente uma sonda personalizada, o [sonda predefinida](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#default-health-probe-settings) é utilizado para monitorizar o estado de funcionamento do back-end. Recomendamos que crie uma sonda personalizada para maior controlo sobre a monitorização de estado de funcionamento do seu back-ends.

> [!NOTE]
> A sonda personalizada não monitorizar o estado de funcionamento do conjunto de back-end, a menos que a definição de HTTP correspondente é explicitamente associada um serviço de escuta.

### <a id="pick"/></a>Escolha o nome de anfitrião do endereço de back-end

Esta capacidade dinamicamente define a *anfitrião* cabeçalho no pedido para o nome de anfitrião do conjunto de back-end. Ele usa um endereço IP ou FQDN.

Esta funcionalidade ajuda a quando o nome de domínio de back-end for diferente do nome DNS do gateway de aplicação e o back-end se baseia num cabeçalho de anfitrião específico ou a extensão de indicação de nome de servidor (SNI) para resolver para o ponto final correto.

Num cenário de exemplo é os serviços de multi-inquilinos, como o back-end. Um serviço de aplicações é um serviço de multi-inquilino que utiliza um espaço compartilhado com um único endereço IP. Por isso, um serviço de aplicações apenas pode ser acedido através de nomes de anfitriões que estão configuradas nas definições de domínio personalizado.

Por predefinição, é o nome de domínio personalizado *example.azurewebsites.<i> </i>net*. Para aceder ao seu serviço de aplicações ao utilizar um gateway de aplicação através de um nome de anfitrião que não tenham sido explicitamente está registado no serviço de aplicações ou o gateway de aplicação FQDN, substitua o nome de anfitrião no pedido original para o nome de anfitrião do serviço de aplicações. Para tal, ativar a **escolha o nome de anfitrião do endereço de back-end** definição.

Para um domínio personalizado cujo nome DNS personalizado já existente é mapeado para o serviço de aplicações, não é necessário que ativar esta definição.

> [!NOTE]
> Esta definição não é necessária para o ambiente de serviço de aplicações no PowerApps, que é uma implementação dedicada.

### <a name="host-name-override"></a>Substituição do nome de anfitrião

Esta funcionalidade substitui o *anfitrião* cabeçalho no pedido de entrada no gateway de aplicação com o nome de anfitrião que especificar.

Por exemplo, se *www.contoso<i></i>.com* é especificada no **nome de anfitrião** a solicitação original, a definição *https:/<i></i>/appgw.eastus.cloudapp.net/path1* é alterado para *https:/<i></i>/www.contoso.com/path1* quando a solicitação é encaminhada para o servidor de back-end.

## <a name="back-end-pool"></a>Conjunto de back-end

Pode apontar um conjunto de back-end para quatro tipos de membros de back-end: uma máquina virtual específica, um conjunto de dimensionamento de máquina virtual, um endereço IP/FQDN ou um serviço de aplicações. Cada conjunto de back-end pode apontar para vários membros do mesmo tipo. Não é suportada a apontar para membros de tipos diferentes no mesmo conjunto de back-end.

Depois de criar um conjunto de back-end, precisa de associá-lo a uma ou mais regras de encaminhamento de pedidos. Também tem de configurar sondas de estado de funcionamento para cada conjunto de back-end no gateway de aplicação. Quando for cumprida uma condição de regra de encaminhamento de pedidos, o gateway de aplicação encaminha o tráfego para os servidores de bom estado de funcionamento (conforme determinado pelas sondas de estado de funcionamento) no conjunto de back-end correspondente.

## <a name="health-probes"></a>Sondas do estado de funcionamento

Um gateway de aplicação monitoriza o estado de funcionamento de todos os recursos no seu back-end, por predefinição. Contudo, recomendamos vivamente que crie uma sonda personalizada para cada definição de HTTP de back-end obter maior controle sobre a monitorização de estado de funcionamento. Para saber como configurar uma sonda personalizada, veja [definições de sonda de estado de funcionamento personalizadas](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#custom-health-probe-settings).

> [!NOTE]
> Depois de criar uma sonda de estado de funcionamento personalizadas, terá de associá-la a uma definição de HTTP de back-end. Uma sonda personalizada não monitorizar o estado de funcionamento do conjunto de back-end, a menos que a definição de HTTP correspondente é explicitamente associada um serviço de escuta.

## <a name="next-steps"></a>Passos seguintes

Agora que sabe sobre os componentes de Gateway de aplicação, pode:

- [Criar um gateway de aplicação no portal do Azure](quick-create-portal.md)
- [Criar um gateway de aplicação com o PowerShell](quick-create-powershell.md)
- [Criar um gateway de aplicação com a CLI do Azure](quick-create-cli.md)
