---
title: Descrição geral da configuração de Gateway de aplicação do Azure
description: Este artigo fornece informações sobre como configurar os vários componentes no Gateway de aplicação do Azure
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 03/20/2019
ms.author: absha
ms.openlocfilehash: 18013050546cc5e204d9cc07a2f499388596164c
ms.sourcegitcommit: 5e4ca656baf3c7d370ab3c0fbad0278aa2c9f1e6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58319453"
---
# <a name="application-gateway-configuration-overview"></a>Descrição geral de configuração do Gateway de aplicação

Gateway de aplicação é composto por vários componentes que podem ser configurados de maneiras diferentes para a realização de diferentes cenários. Este artigo orienta-o através de como cada componente está a ser configurado.

![componentes de gateway de aplicação](./media/configuration-overview/configuration-overview1.png)

A imagem de exemplo acima ilustra a configuração de um aplicativo com serviços de 3 escuta. Os primeiros dois são serviços de escuta de múltiplos sites para `http://acme.com/*` e `http://fabrikam.com/*`, respectivamente. Ambos estão à escuta na porta 80. O serviço de escuta de terceiro é um serviço de escuta básico com a terminação de SSL de ponta a ponta. 

## <a name="prerequisites"></a>Pré-requisitos

### <a name="azure-virtual-network-and-dedicated-subnet"></a>Rede virtual do Azure e a sub-rede dedicada

Gateway de aplicação é uma implementação dedicada na sua rede virtual. Na sua rede virtual, numa sub-rede dedicada é necessária para o gateway de aplicação. Pode ter várias instâncias de uma implementação de gateway de aplicação específica nesta sub-rede. Também pode implementar outros gateways de aplicação na sub-rede, mas não é possível implementar qualquer outro recurso na sub-rede de gateway de aplicação.  

> [!NOTE]   
> Misturar Standard_v2 e o padrão Gateway de aplicação na mesma sub-rede não é suportada.

#### <a name="size-of-the-subnet"></a>Tamanho da sub-rede

Gateway de aplicação consome um endereço IP privado por instância, além de outro endereço IP privado, se uma configuração de IP de front-end privado está configurada. Além disso, o Azure reserva cinco endereços IP - os quatro primeiros e últimos de endereços IP - em cada sub-rede para utilização interna. Por exemplo, se um gateway de aplicação é definido como 15 instâncias e não existe nenhum IP de front-end privado, pelo menos de 20 endereços IP serão necessário na sub-rede - cinco endereços IP para utilização interna e 15 endereços IP para as 15 instâncias de gateway de aplicação. Portanto, neste caso, / 27 sub-rede tamanho ou superior é necessária. Se tiver 27 instâncias e um de endereços IP para o front-end privado configuração de IP, em seguida, 33 endereços IP que será necessários - 27 endereços IP para as instâncias de 27 de gateway de aplicação, um endereço IP para IP de front-end privado e IP cinco endereços para utilização interna. Portanto, neste caso, um /26 sub-rede tamanho ou superior é necessária.

É recomendado que utilize, pelo menos, / 28 tamanho da sub-rede. Isto dá-lhe 11 endereços utilizáveis suficientes. Se a carga de aplicação necessitar de mais de 10 instâncias, deve considerar/27 ou/26 tamanho da sub-rede.

#### <a name="network-security-groups-supported-on-the-application-gateway-subnet"></a>Grupos de segurança de rede suportados na sub-rede de Gateway de aplicação

Grupos de segurança de rede (NSGs) são suportados na sub-rede de Gateway de aplicação com as seguintes restrições: 

- Exceções tiver de ser colocadas no tráfego de entrada nas portas 65503 65534 para o Gateway de aplicação v1 SKU e portas 65200-65535 para o SKU de v2. Este intervalo de portas é necessário para a comunicação de infraestrutura do Azure. Estão protegidas (bloqueadas) pelos certificados do Azure. Sem os certificados adequados, as entidades externas, incluindo os clientes desses gateways, não são possível iniciar quaisquer alterações nesses pontos finais.

- Conectividade de internet de saída não pode ser bloqueada. Regras de saída padrão no NSG já permitem a conectividade à internet. Recomendamos que não remove as regras de saída predefinido e que não criar outras regras de saída que negam conectividade de internet de saída.

- Tráfego a partir da etiqueta AzureLoadBalancer têm de ser permitido.

##### <a name="whitelist-application-gateway-access-to-a-few-source-ips"></a>Acesso de Gateway de aplicação de lista branca de IPs de origem alguns

Este cenário pode ser feito com NSGs na sub-rede de gateway de aplicação. As seguintes restrições devem ser colocadas na sub-rede na ordem listada de prioridade:

1. Permitir o tráfego de entrada do intervalo de IP/IP de origem.
2. Permitir pedidos recebidos de todas as origens para portas 65503 65534 para [comunicação de estado de funcionamento do back-end](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics). Este intervalo de porta é necessário para a comunicação de infraestrutura do Azure. Estão protegidas (bloqueadas) pelos certificados do Azure. Sem os certificados adequados, as entidades externas, incluindo os clientes desses gateways, não será capazes de iniciar quaisquer alterações nesses pontos finais.
3. Permitir entradas sondas do Balanceador de carga do Azure (etiqueta AzureLoadBalancer) e virtual rede tráfego de entrada (etiqueta VirtualNetwork) a [NSG](https://docs.microsoft.com/azure/virtual-network/security-overview).
4. Bloquear todas as outras tráfego de entrada com uma negação todas as regras.
5. Permitir o tráfego de saída à Internet para todos os destinos.

#### <a name="user-defined-routes-supported-on-the-application-gateway-subnet"></a>Rotas definidas pelo utilizador suportadas na sub-rede de Gateway de aplicação

Em caso de SKU do v1, as rotas definidas pelo utilizador (UDRs) são suportadas na sub-rede de gateway de aplicação, desde que elas não alteram a comunicação de ponto-a-ponto de solicitação/resposta. Por exemplo, pode configurar um UDR na sub-rede de gateway de aplicação para apontar para um dispositivo de firewall para a inspeção de pacotes, mas tem de garantir que o pacote pode contactar a inspeção de mensagem do seu destino pretendido. Falha ao fazer isso pode resultar em comportamento do encaminhamento sonda ou tráfego de estado de funcionamento incorreto. Isto inclui as rotas aprendidas ou rotas predefinidas que 0.0.0.0/0 propagadas pelo ExpressRoute ou Gateways de VPN na rede virtual.

Em caso de v2 SKU, UDRs na sub-rede de gateway de aplicação não são suportadas. Para obter mais informações, consulte [dimensionamento automático e o Gateway de aplicação com redundância de zona (pré-visualização pública)](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant#known-issues-and-limitations).

> [!NOTE]
> Utilizar as UDRs na sub-rede de gateway de aplicação, fará com que o estado de funcionamento no [vista de estado de funcionamento do back-end](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#back-end-health) a ser apresentado como **desconhecido** e também irá resultar em failue de geração de registos do gateway de aplicação e métricas. Recomenda-se que não utilizar UDRs na sub-rede de gateway de aplicação para poder ver o estado de funcionamento do back-end, registos e métricas.

## <a name="frontend-ip"></a>IP de front-end

Pode configurar o gateway de aplicação para ter um endereço IP público ou um endereço IP privado ou ambos. IP público é necessário quando hospeda um back-end, que é necessário para ser acedidos por clientes através da internet por meio de um VIP de acesso à Internet. IP público não é necessário para um ponto final interno não exposto à Internet, também conhecido como um endpoint de (ILB) do Balanceador de carga interno. Configurar o gateway com um ILB é útil para as aplicações de linha de negócio internas não expostas à Internet. Também é útil para serviços e camadas dentro de uma aplicação de várias camadas que assenta num limite de segurança que, não sendo exposto à Internet, requer a distribuição de carga round-robin, a persistência da sessão ou a terminação SSL (Secure Sockets Layer).

Apenas um endereço IP ou um endereço IP privado é suportado. Escolha o IP de front-end ao criar o Gateway de aplicação. 

- Em caso de um IP público, pode optar por criar um novo IP público ou utilizar um IP público existente na mesma localização que o Gateway de aplicação. Se criar um novo endereço IP público, o tipo de endereço IP selecionado (estáticos ou dinâmicos) não é possível alterar mais tarde. Para obter mais informações, consulte [estático vs dinâmico de IP público](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#static-vs-dynamic-public-ip) 

- Em caso de um IP privado, pode optar por especificar um endereço IP privado da sub-rede na qual o Gateway de aplicação é criado. Se não for especificado explicitamente, um endereço IP arbitrário será selecionado automaticamente da sub-rede. Para obter mais informações, consulte [criar um gateway de aplicação com um ponto final (ILB) do Balanceador de carga interno.](https://docs.microsoft.com/azure/application-gateway/application-gateway-ilb-arm)

Um IP de front-end está associado à *serviço de escuta* que verifica a existência de pedidos recebidos no IP de front-end.

## <a name="listeners"></a>Ouvintes

Um serviço de escuta é uma entidade lógica que verifica a existência de pedidos recebidos de conexão, usando a porta, protocolo, anfitrião e endereço IP. Por conseguinte, quando configurar o serviço de escuta que tem de introduzir os valores de porta, protocolo, anfitrião e endereços IP que são as mesmo que os valores correspondentes no pedido de entrada no gateway. Quando cria um gateway de aplicação do portal do Azure, também criar um serviço de escuta do padrão ao escolher o protocolo e a porta para o serviço de escuta. Além disso pode escolher se pretende ativar o suporte para HTTP2 sobre o serviço de escuta. Depois de criar o Gateway de aplicação, pode editar a definição deste serviço de escuta do padrão (*appGatewayHttpListener*/*appGatewayHttpsListener*) e/ou criar novos serviços de escuta.

### <a name="listener-type"></a>Tipo de serviço de escuta

Pode escolher entre [serviço de escuta básico ou de vários locais](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#types-of-listeners) ao criar um novo serviço de escuta. 

- Se estiver a alojar um site único por trás de um gateway de aplicação, escolha o serviço de escuta básico. Saiba mais [como criar um gateway de aplicação com o serviço de escuta básico](https://docs.microsoft.com/azure/application-gateway/quick-create-portal).

- Se estiver a configurar mais de um aplicativo web ou vários subdomínios do mesmo domínio principal na mesma instância de gateway de aplicação, em seguida, escolha o serviço de escuta de múltiplos site. Para o serviço de escuta de múltiplos site, além disso terá de introduzir um nome de anfitrião. Isto acontece porque o Gateway de aplicação baseia-se em cabeçalhos de anfitrião HTTP 1.1 para alojar mais do que um site no mesmo endereço IP público e porta.

#### <a name="order-of-processing-listeners"></a>Ordem de processamento de serviços de escuta

Em caso de v1 SKUs, serviços de escuta são processados na ordem em que forem sendo apresentados. Por esse motivo se uma solicitação de entrada corresponder a um serviço de escuta básico processa-lo primeiro. Por conseguinte, os serviços de escuta de múltiplos sites devem ser configurados antes de um serviço de escuta básico para garantir que o tráfego é encaminhado para o back-end correto.

Em caso de v2 SKUs, serviços de escuta de múltiplos sites são processados antes de serviços de escuta básicos.

### <a name="frontend-ip"></a>IP de front-end

Escolha o IP de front-end que pretende associar este serviço de escuta. O serviço de escuta será deve ainda ouvir a pedido a receber neste IP.

### <a name="frontend-port"></a>Porta de front-end

Escolha a porta de front-end. Pode escolher entre as portas existentes ou crie um novo. Pode escolher qualquer valor entre o [intervalo de portas permitido](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#ports). Isso permite que não só para utiliza portas bem conhecidas como 80 e 443 mas permitido qualquer porta personalizado adequado para a sua utilização. Uma porta pode ser utilizada para serviços de escuta de destinado ao públicos ou privados serviços de escuta com acesso.

### <a name="protocol"></a>Protocolo

Tem de escolher entre o protocolo HTTP e HTTPS. 

- Se optar por HTTP, o tráfego entre o gateway de cliente e a aplicação irá fluir não encriptada.

- Selecione HTTPS, se estiver interessado em [terminação de Secure Sockets Layer (SSL)](https://docs.microsoft.com/azure/application-gateway/overview#secure-sockets-layer-ssl-terminationl) ou [encriptação SSL de ponta a ponta](https://docs.microsoft.com/azure/application-gateway/ssl-overview). Se optar por HTTPS, o tráfego entre o gateway de cliente e a aplicação será encriptado e será terminada a ligação SSL no gateway de aplicação.  Se pretender que a encriptação SSL ponto a ponto, além disso terá de escolher o protocolo HTTPS durante a configuração *definição de HTTP de back-end*. Isto irá garantir que o tráfego é criptografado novamente quando ele viaja do Gateway de aplicação para o back-end.

  Para configurar a terminação de Secure Sockets Layer (SSL) e a encriptação SSL ponto a ponto, é necessário um certificado a ser adicionado para o serviço de escuta de modo a permitir o Gateway de aplicação derivar uma chave simétrica de acordo com a especificação de protocolo SSL. A chave simétrica, em seguida, é utilizada para encriptar e desencriptar o tráfego enviado para o gateway. O certificado de gateway tem de estar no formato Personal Information Exchange (PFX). Esse formato de arquivo permite-lhe exportar a chave privada que é necessário pelo gateway de aplicação para efetuar a encriptação e desencriptação de tráfego. 

#### <a name="supported-certificates"></a>Certificados suportados

Ver [certificados suportados para a terminação de SSL](https://docs.microsoft.com/azure/application-gateway/ssl-overview#certificates-supported-for-ssl-termination).

### <a name="additional-protocol-support"></a>Suporte de protocolos adicionais

#### <a name="http2-support"></a>Suporte de HTTP2

Suporte de protocolo HTTP/2 está disponível para clientes que se conectam para ouvintes de gateway de aplicação apenas. A comunicação para agrupamentos de servidores de back-end é através de HTTP/1.1. Por predefinição, o suporte de HTTP/2 está desativado. O exemplo de fragmento de código do Azure PowerShell seguinte mostra como pode ativá-la:

```azurepowershell
$gw = Get-AzureRmApplicationGateway -Name test -ResourceGroupName hm

$gw.EnableHttp2 = $true

Set-AzureRmApplicationGateway -ApplicationGateway $gw
```

#### <a name="websocket-support"></a>Suporte de Websocket

Suporte de Websocket está ativado por predefinição. Não existe qualquer definição configurável pelo utilizador para ativar ou desativar seletivamente o suporte de WebSocket. Pode utilizar WebSockets com serviços de escuta HTTP e HTTPS. 

### <a name="custom-error-page"></a>Página de erro personalizada

Páginas de erro personalizadas podem ser definidas ao nível global, bem como o nível de serviço de escuta, no entanto, a criação de páginas de erro personalizadas de nível global no portal do Azure não é atualmente suportada. Pode configurar uma página de erro personalizado para um erro 403 do WAF ou uma página de manutenção 502 no nível de serviço de escuta. Terá também de especificar um URL do blob publicamente acessível para o código de estado de erro fornecida. Para obter mais informações, consulte [página de criação de erro personalizada](https://docs.microsoft.com/azure/application-gateway/custom-error).

![Códigos de erro do Gateway de aplicação](https://docs.microsoft.com/azure/application-gateway/media/custom-error/ag-error-codes.png)

Para configurar uma página de erro personalizada global, utilize [Azure PowerShell para a configuração](https://docs.microsoft.com/azure/application-gateway/custom-error#azure-powershell-configuration) 

### <a name="ssl-policy"></a>Política SSL

Pode centralizar a gestão de certificados SSL e reduzir a sobrecarga de encriptação e desencriptação de um farm de servidores de back-end. Este SSL centralizado manipulação também permite-lhe especificar uma política SSL central que é adequada aos seus requisitos de segurança organizacional.  Pode escolher entre padrão, predefinidas e a política SSL personalizada. 

Pode configurar uma política SSL para controlar as versões de protocolo SSL. Pode configurar o gateway de aplicação para negar o TLS 1.0, TLS1.1 e TLS1.2. SSL 2.0 e 3.0 já estão desativadas por predefinição e não são configuráveis. Para obter mais informações, consulte [descrição geral da política de SSL do Gateway de aplicação](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-policy-overview).

Depois de criar um serviço de escuta, associá-la com uma regra de encaminhamento de pedido que determina como o pedido recebido do serviço de escuta de deve ser encaminhado para o back-end.

## <a name="request-routing-rule"></a>Solicitar regra de encaminhamento

Ao criar o gateway de aplicação do portal do Azure, criar uma regra predefinida (*rule1*), que vincula o serviço de escuta do padrão (*appGatewayHttpListener*) com o conjunto de back-end predefinido (*appGatewayBackendPool*) e as definições de HTTP de back-end predefinido (*appGatewayBackendHttpSettings*). Depois de criar o gateway de aplicação, pode editar a definição desta regra padrão e/ou criar novas regras.

### <a name="rule-type"></a>Tipo de regra

Pode escolher entre [regra básica ou baseada em caminho](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#request-routing-rule) ao criar uma nova regra. 

- Se pretender reencaminhar todos os pedidos no serviço de escuta associado (por exemplo: blog.contoso.com/*) para um conjunto de back-end único, escolha o serviço de escuta básico. 
- Escolha baseado no caminho do serviço de escuta se quiser encaminhar pedidos com o caminho do URL especificado para conjuntos de back-end específicas. O padrão de caminho é aplicado apenas para o caminho do URL, não para seus parâmetros de consulta.


#### <a name="order-of-processing-rules"></a>Ordem das regras de processamento

Em caso de v1 SKUs, a correspondência de padrão do pedido a receber é processada pela ordem em que os caminhos estão listados no mapa de caminho de URL da regra com base no caminho. Por esse motivo, se um pedido corresponde ao padrão em duas ou mais caminhos do mapa do caminho de URL, em seguida, o caminho que está listado primeiro será correspondido e o pedido será reencaminhado para o back-end associado a esse caminho.

Em caso de v2 SKUs, uma correspondência exata mantém maior prioridade sobre a ordem na qual os caminhos estão listados no mapa do caminho de URL. Para que o motivo, se um pedido corresponde ao padrão em duas ou mais caminhos, em seguida, o pedido será reencaminhado para o back-end associadas com que o caminho que corresponde exatamente com o pedido. Se o caminho no pedido de entrada não exatamente corresponde a qualquer caminho no mapa do caminho de URL, em seguida, a correspondência de padrão do pedido a receber é processada pela ordem em que os caminhos estão listados no mapa de caminho de URL da regra com base no caminho.

### <a name="associated-listener"></a>Serviço de escuta associado

Precisa associar um serviço de escuta para a regra para que o *solicitar regra de encaminhamento* associadas com o *serviço de escuta* é avaliada para determinar o *conjunto back-end* ao qual o o pedido é para ser encaminhado.

### <a name="associated-backend-pool"></a>Associado back-end conjunto

Associe o conjunto de back-end que contém os destinos de back-end que irão servir as solicitações recebidas pela escuta. Em caso de uma regra básica, apenas um conjunto de back-end é permitido uma vez que todos os pedidos no serviço de escuta associado serão reencaminhados para este conjunto de back-end. Em caso de uma regra com base no caminho, adicione vários conjuntos de back-end correspondente a cada caminho de URL. Os pedidos que corresponde ao caminho do URL introduzido aqui, serão reencaminhados para o conjunto de back-end correspondente. Além disso, adicione um conjunto de back-end predefinido, uma vez que os pedidos que não correspondam a qualquer caminho de URL introduzido nesta regra serão reencaminhados para ele.

### <a name="associated-backend-http-setting"></a>Definição de HTTP de back-end associados

Adicione uma definição de HTTP de back-end para cada regra. Os pedidos serão encaminhados a partir do Gateway de aplicação para os destinos de back-end com o número de porta, protocolo e outras definições especificadas nesta definição. Em caso de uma regra básica, apenas uma definição de HTTP de back-end é permitida uma vez que todos os pedidos no serviço de escuta associado serão reencaminhados para os destinos de back-end correspondente utiliza esta definição de HTTP. Em caso de uma regra com base no caminho, adicione várias definições de HTTP de back-end correspondente a cada caminho de URL. Os pedidos que corresponde ao caminho do URL introduzido aqui, serão reencaminhados para os destinos de back-end correspondente com as definições de HTTP correspondente a cada caminho de URL. Além disso, adicione uma definição de HTTP padrão, uma vez que os pedidos que não correspondam a qualquer caminho de URL introduzido nesta regra serão reencaminhados para o conjunto de back-end predefinido a utilizar a predefinição de HTTP.

### <a name="redirection-setting"></a>Configuração de redirecionamento

Se o redirecionamento está configurado para uma regra básica, todos os pedidos no serviço de escuta associado serão redirecionados para o destino de redirecionamento, assim que habilita o redirecionamento global. Se o redirecionamento está configurado para uma regra baseada em caminho, os pedidos apenas numa área de site específico, por exemplo um carrinho de compras área indicada pelos/carrinho / *, será redirecionado para o destino de redirecionamento, assim que habilita o redirecionamento baseado no caminho. 

Para obter informações sobre a capacidade de redirecionamento, veja [descrição geral do redirecionamento](https://docs.microsoft.com/azure/application-gateway/redirect-overview).

- #### <a name="redirection-type"></a>Tipo de redirecionamento

  Escolha o tipo de redirecionamento necessário a partir de: Other(303) Permanent(301), Temporary(307), Found(302) ou consulte.

- #### <a name="redirection-target"></a>Destino de redirecionamento

  Pode escolher entre o serviço de escuta do outro ou um site externo como destino de redirecionamento. 

  - ##### <a name="listener"></a>Serviço de Escuta

    Serviço de escuta ao escolher como o destino de redirecionamento ajuda a partir de um serviço de escuta a redirecionar para outro serviço de escuta no gateway. Esta definição é necessária para habilitar HTTP para redirecionamento a HTTPS, ou seja, o tráfego de redirecionamento do serviço de escuta de origem, verificar a existência de pedidos HTTP recebidos para o serviço de escuta de destino, verificar a existência de pedidos HTTPS recebidos. Também pode escolher a cadeia de consulta e o caminho na solicitação original a serem incluídos no pedido reencaminhado para o destino de redirecionamento.![componentes de gateway de aplicação](./media/configuration-overview/configure-redirection.png)

    Para obter mais informações sobre HTTP para redirecionamento a HTTPS, consulte [redirecionamento de HTTP para HTTP através do portal](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-portal), [redirecionamento de HTTP para HTTP com o PowerShell](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-powershell), [redirecionamento de HTTP para HTTP com a CLI](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-cli)

  - ##### <a name="external-site"></a>Site externo

    Escolha o site externo quando deseja redirecionar o tráfego do serviço de escuta de associado à regra, portanto, para ser redirecionado para um site externo. Pode escolher a cadeia de consulta no pedido a serem incluídos no pedido reencaminhado para o destino de redirecionamento original. Não é possível reencaminhar o caminho na solicitação original para o site externo.

    Para obter mais informações sobre o redirecionamento para o site externo, consulte [redirecionar o tráfego para o site externo com o PowerShell](https://docs.microsoft.com/azure/application-gateway/redirect-external-site-powershell) e [https://docs.microsoft.com/azure/application-gateway/redirect-external-site-cli](https://docs.microsoft.com/azure/application-gateway/redirect-external-site-cli)

#### <a name="rewrite-http-header-setting"></a>Reescrever a definição de cabeçalho HTTP

Esta capacidade permite-lhe adicionar, remover ou atualizar os cabeçalhos de solicitação e resposta HTTP enquanto o pedido e pacotes de resposta se mover entre os conjuntos de cliente e o back-end.    Pode configurar esta capacidade apenas através do PowerShell. Suporte do portal e CLI ainda não está disponível. Para obter mais informações, consulte [cabeçalhos HTTP reescrever](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers) descrição geral e [reescrita de cabeçalho de HTTP configurar](https://docs.microsoft.com/azure/application-gateway/add-http-header-rewrite-rule-powershell#specify-your-http-header-rewrite-rule-configuration).

## <a name="http-settings"></a>Definições de HTTP

O gateway de aplicação encaminha o tráfego para os servidores de back-end através da configuração especificada neste componente. Depois de criar uma definição de HTTP, terá de associá-la com um ou mais regras de encaminhamento do pedido.

### <a name="cookie-based-affinity"></a>Afinidade baseada em cookies

Esta funcionalidade é útil quando deseja manter uma sessão de utilizador no mesmo servidor. Ao utilizar cookies geridos por um gateway, o Gateway de Aplicação pode direcionar o tráfego subsequente de uma sessão de utilizador para o mesmo servidor para processamento. Isto é importante nos casos em que o estado da sessão é guardado localmente no servidor para uma sessão de utilizador. Se a aplicação não é possível processar a afinidade com base no cookie, em seguida, não será capaz de utilizar esta capacidade. Para utilizar a afinidade de sessão baseada em cookies, deve certificar-se de que os clientes têm de suportar cookies. 

### <a name="connection-draining"></a>Drenagem de ligação

A drenagem de ligação ajuda a conseguir uma remoção correta de membros do conjunto de back-end durante as atualizações de serviço planeadas. Esta definição pode ser aplicada a todos os membros de um conjunto de back-end durante a criação de regra. Uma vez ativada, o gateway de aplicação garante que todas as instâncias de anular o registo de um conjunto de back-end não recebem as novas solicitações enquanto permite que os pedidos existentes ser concluída no prazo de um limite de tempo configurado. Isto aplica-se às instâncias de back-end que são explicitamente removidas do conjunto de back-end por uma chamada de API, bem como às instâncias de back-end que são reportadas como danificadas, conforme determinado pelas sondas de estado de funcionamento.

### <a name="protocol"></a>Protocolo

Gateway de aplicação suporta protocolos HTTP e HTTPS de encaminhamento de pedidos para os servidores de back-end. Se for escolhido o protocolo HTTP, em seguida, o tráfego não criptografados para os servidores de back-end de fluxos. Nesses casos em que a comunicação sem encriptação para os servidores de back-end não é uma opção aceitável, deve escolher o protocolo HTTPS. Esta definição combinada com a escolher o protocolo HTTPS no serviço de escuta permite-lhe ativar [SSL ponto a ponto](https://docs.microsoft.com/azure/application-gateway/ssl-overview). Que permite-lhe transmitir dados confidenciais para o back-end encriptado de forma segura. Cada servidor de back-end no conjunto de back-end com SSL de ponta a ponta ativado deve ser configurado com um certificado, para permitir a comunicação segura.

### <a name="port"></a>Porta

Esta é a porta que os servidores de back-end estão à escuta para o tráfego proveniente do Gateway de aplicação. Pode configurar as portas entre 1 e 65535.

### <a name="request-timeout"></a>Tempo limite do pedido

O número de segundos que o gateway de aplicação aguarda para receber a resposta do conjunto de back-end antes de retornar um erro de "Ligação excedido".

### <a name="override-backend-path"></a>Substituir caminho de back-end

Esta definição permite-lhe configurar um caminho de encaminhamento personalizado opcional para utilizar quando a solicitação é encaminhada para o back-end. Isto irá copiar qualquer parte do caminho de entrada que corresponde ao caminho personalizado especificado no **substituir o caminho de back-end** campo para o caminho reencaminhado. Consulte a tabela abaixo para compreender como funciona a capacidade.

- Quando a definição de HTTP está ligada a uma regra de encaminhamento de pedido básico:

  | Solicitação original  | Substituir caminho de back-end | Pedido reencaminhado para o back-end |
  | ----------------- | --------------------- | ---------------------------- |
  | /Home/            | /override/            | / Substituir/home /              |
  | / home/secondhome / | /override/            | / substituição/home/secondhome /   |

- Quando a definição de HTTP está ligada a uma regra de encaminhamento baseado no caminho do pedido:

  | Solicitação original           | Regra de caminho       | Substituir caminho de back-end | Pedido reencaminhado para o back-end |
  | -------------------------- | --------------- | --------------------- | ---------------------------- |
  | /pathrule/home/            | /pathrule*      | /override/            | / Substituir/home /              |
  | /pathrule/home/secondhome/ | /pathrule*      | /override/            | / substituição/home/secondhome /   |
  | /Home/                     | /pathrule*      | /override/            | / Substituir/home /              |
  | / home/secondhome /          | /pathrule*      | /override/            | / substituição/home/secondhome /   |
  | /pathrule/home/            | /pathrule/home* | /override/            | /override/                   |
  | /pathrule/home/secondhome/ | /pathrule/home* | /override/            | / Substituir/secondhome /        |

### <a name="use-for-app-service"></a>Utilizar para o Serviço de Aplicações

Esse é um atalho de interface do Usuário que seleciona as duas definições necessárias para o back-end de serviço de aplicações – permite a escolha de nome de anfitrião do endereço de back-end e cria uma sonda personalizada nova. O motivo por que é feito o primeiro é explicado na seção para **escolha o nome de anfitrião do endereço de back-end** definição. É criada uma sonda de novo em que o cabeçalho de sonda também é escolhido do endereço de back-end do membro.

### <a name="use-custom-probe"></a>Utilizar sonda personalizada

Esta definição é utilizada para associar um [sonda personalizada](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#custom-health-probe) com esta definição de HTTP. Pode associar apenas uma sonda personalizada com uma definição de HTTP. Se não explicitamente associar uma sonda personalizada, em seguida, [sonda predefinida](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#default-health-probe-settings) será utilizado para monitorizar o estado de funcionamento do back-end. Recomenda-se que crie uma sonda personalizada para que o controle mais granular sobre a monitorização de estado de funcionamento do seu back-ends.

> [!NOTE]   
> Sonda personalizada não irá começar a monitorizar o estado de funcionamento do conjunto de back-end, a menos que a definição de HTTP correspondente é explicitamente associada um serviço de escuta.

### <a name="pick-host-name-from-backend-address"></a>Escolha o nome de anfitrião a partir do endereço de back-end

Esta capacidade dinamicamente define a *anfitrião* cabeçalho no pedido para o nome de anfitrião do conjunto de back-end com um endereço IP ou nome de domínio completamente qualificado (FQDN). Isso é útil nos cenários em que o nome de domínio de back-end for diferente do nome DNS do gateway de aplicação e o back-end se baseia num cabeçalho de anfitrião específico ou a extensão SNI para resolver para o ponto final correto, tal como no caso de serviços de multi-inquilinos como o back-end. Uma vez que o serviço de aplicações é um serviço de multi-inquilino com um espaço compartilhado com um único endereço IP, um serviço de aplicações pode ser acessado apenas com os nomes de anfitriões configurado nas definições de domínio personalizado. Por predefinição, é o nome de domínio personalizado *example.azurewebsites.net*. Por conseguinte, se desejar acessar seu serviço de aplicações com o gateway de aplicação com qualquer um de um nome de anfitrião não tenham sido explicitamente registado no serviço de aplicações ou pelo FQDN de gateway de aplicação, tem de substituir o nome de anfitrião no pedido original para o nome de anfitrião do serviço de aplicações, ao habilitando **escolha o nome de anfitrião do endereço de back-end** definição.

Se possuir um domínio personalizado e mapeou o nome DNS personalizado já existente para o serviço de aplicações, não é necessário ativar esta definição.

> [!NOTE]   
> Esta definição não é necessária para o ambiente de serviço de aplicações (ASE), uma vez que o ASE é uma implementação dedicada. 

### <a name="host-name-override"></a>Substituição do nome de anfitrião

Esta funcionalidade substitui o *anfitrião* cabeçalho no pedido de entrada no gateway de aplicação para o nome de anfitrião que especificar aqui. Por exemplo, se www\.contoso.com é especificado como o **nome de anfitrião** definição, a solicitação original https://appgw.eastus.cloudapp.net/path1 será alterado para https://www.contoso.com/path1 quando a solicitação é encaminhada para o servidor de back-end. 

## <a name="backend-pool"></a>Conjunto back-end

Um conjunto de back-end pode ser apontado para quatro tipos de membros de back-end: uma máquina virtual específica, o conjunto de dimensionamento de máquina virtual, um endereço IP/FQDN ou um serviço de aplicações. Cada conjunto de back-end pode apontar para vários membros do mesmo tipo. Apontar os membros de tipos diferentes no mesmo conjunto de back-end não é suportada. 

Depois de criar um conjunto de back-end, terá de associá-lo a uma ou mais regras de encaminhamento de pedido. Terá também de configurar sondas de estado de funcionamento para cada conjunto de back-end no gateway de aplicação. Quando for cumprido um condição de regra de encaminhamento de pedido, o gateway de aplicação encaminha o tráfego para os servidores de bom estado de funcionamento (conforme determinado pelas sondas de estado de funcionamento) no agrupamento de back-end correspondente.

## <a name="health-probes"></a>Sondas do estado de funcionamento

Apesar do gateway de aplicação monitoriza o estado de funcionamento de todos os recursos no seu back-end por predefinição, é altamente recomendável que cria uma sonda personalizada para cada definição de HTTP de back-end de modo a ter um controle mais granular sobre a monitorização de estado de funcionamento. Para saber como configurar a sonda de estado de funcionamento personalizadas, consulte [definições de sonda de estado de funcionamento personalizadas](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#custom-health-probe-settings).

> [!NOTE]   
> Depois de criar uma sonda de estado de funcionamento personalizadas, terá de associá-la a uma definição de HTTP de back-end. Sonda personalizada não irá começar a monitorizar o estado de funcionamento do conjunto de back-end, a menos que a definição de HTTP correspondente é explicitamente associada um serviço de escuta.

## <a name="next-steps"></a>Passos Seguintes

Depois de saber sobre os componentes de Gateway de aplicação, pode:

- [Criar um Gateway de aplicação no portal do Azure](quick-create-portal.md)
- [Criar um Gateway de aplicação com o PowerShell](quick-create-powershell.md)
- [Criar um Gateway de aplicação com a CLI do Azure](quick-create-cli.md)
