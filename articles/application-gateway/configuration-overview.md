---
title: Visão geral da configuração do Gateway de Aplicação Azure
description: Este artigo descreve como configurar os componentes do Gateway de Aplicação Azure
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 03/24/2020
ms.author: absha
ms.openlocfilehash: bd6f04ca7e24e380ad657f967284704ad613375a
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83996402"
---
# <a name="application-gateway-configuration-overview"></a>Visão geral da configuração do Gateway de aplicação

O Portal de Aplicações Azure é composto por vários componentes que pode configurar de várias formas para diferentes cenários. Este artigo mostra-lhe como configurar cada componente.

![Gráfico de fluxo de componentes gateway de aplicação](./media/configuration-overview/configuration-overview1.png)

Esta imagem ilustra uma aplicação que tem três ouvintes. Os dois primeiros são ouvintes multi-sites para `http://acme.com/*` `http://fabrikam.com/*` e, respectivamente. Ambos ouvem no porto 80. O terceiro é um ouvinte básico que tem a rescisão de segurança da camada de transporte de ponta a ponta (TLS), anteriormente conhecida como rescisão secure sockets layer (SSL).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

### <a name="azure-virtual-network-and-dedicated-subnet"></a>Rede virtual Azure e subnet dedicada

Um gateway de aplicação é uma implementação dedicada na sua rede virtual. Dentro da sua rede virtual, é necessária uma subnet dedicada para o gateway da aplicação. Pode ter vários casos de uma determinada implementação de gateway de aplicação numa subnet. Também pode implantar outros gateways de aplicação na sub-rede. Mas não pode implantar nenhum outro recurso na sub-rede de porta de entrada de aplicações.

> [!NOTE]
> Não é possível misturar Standard_v2 e Standard Azure Application Gateway na mesma subnet.

#### <a name="size-of-the-subnet"></a>Tamanho da sub-rede

O Application Gateway utiliza um endereço IP privado por instância, além de outro endereço IP privado se estiver configurado um IP frontal privado.

O Azure também reserva cinco endereços IP em cada subnet para uso interno: os quatro primeiros e os últimos endereços IP. Por exemplo, considere 15 casos de gateway de aplicação sem IP frontal privado. Precisa de pelo menos 20 endereços IP para esta sub-rede: cinco para uso interno e 15 para as instâncias de gateway da aplicação. Então, precisa de uma sub-rede de /27 ou maior.

Considere uma sub-rede que tenha 27 instâncias de gateway de aplicação e um endereço IP para um IP frontal privado. Neste caso, necessita de 33 endereços IP: 27 para os casos de gateway de aplicação, um para a frente privada e cinco para uso interno. Então, precisa de uma sub-rede de /26 ou maior.

Recomendamos que utilize uma subrede de pelo menos /28. Este tamanho dá-lhe 11 endereços IP utilizáveis. Se a sua carga de aplicação necessitar de mais de 10 instâncias de Gateway de aplicação, considere um tamanho de sub-rede /27 ou /26.

#### <a name="network-security-groups-on-the-application-gateway-subnet"></a>Grupos de segurança de rede na subnet Application Gateway

Os grupos de segurança da rede (NSGs) são suportados no Application Gateway. Mas há algumas restrições:

- Deve permitir a entrada de tráfego na Internet nas portas TCP 65503-65534 para o Gateway de aplicação v1 SKU, e as portas TCP 65200-65535 para o V2 SKU com a subnet de destino como **Qualquer** e fonte como etiqueta de serviço **GatewayManager.** Esta gama portuária é necessária para a comunicação da infraestrutura Azure. Estas portas são protegidas (bloqueadas) por certificados Azure. Entidades externas, incluindo os clientes desses gateways, não conseguem comunicar sobre estes pontos finais.

- A conectividade da Internet não pode ser bloqueada. As regras de saída padrão no NSG permitem a conectividade à Internet. É recomendável que:

  - Não remova as regras de saída por defeito.
  - Não crie outras regras de saída que negam qualquer conectividade de saída.

- O tráfego a partir da etiqueta **AzureLoadBalancer** deve ser permitido.

#### <a name="allow-application-gateway-access-to-a-few-source-ips"></a>Permitir o acesso do Gateway de Aplicação a alguns IPs de origem

Para este cenário, utilize NSGs na subnet Application Gateway. Coloque as seguintes restrições na sub-rede nesta ordem de prioridade:

1. Permitir o tráfego de entrada a partir de uma gama IP ou IP de origem com o destino como toda a gama de endereços de subnet da Aplicação Gateway e porta de destino como a sua porta de acesso de entrada, por exemplo, porta 80 para acesso HTTP.
2. Permita os pedidos de entrada de origem como etiqueta de serviço **gatewayManager** e destino como **portos de** destino 65503-65534 para o Gateway de Aplicação v1 SKU, e portas 65200-65535 para v2 SKU para comunicação de estado de [saúde back-end](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics). Esta gama portuária é necessária para a comunicação da infraestrutura Azure. Estas portas são protegidas (bloqueadas) por certificados Azure. Sem certificados adequados, entidades externas não podem iniciar alterações nesses pontos finais.
3. Permitir a entrada de sondas Azure Load Balancer (etiqueta*AzureLoadBalancer)* e tráfego de rede virtual de entrada *(virtualNetwork* tag) no grupo de segurança da [rede](https://docs.microsoft.com/azure/virtual-network/security-overview).
4. Bloqueie todos os outros tráfegos que entram usando uma regra de negação.
5. Permita tráfego de saída para a internet para todos os destinos.

#### <a name="user-defined-routes-supported-on-the-application-gateway-subnet"></a>Rotas definidas pelo utilizador suportadas na sub-rede do Gateway de Aplicação

> [!IMPORTANT]
> A utilização de UDRs na subnet Application Gateway pode fazer com que o estado de saúde na visão de saúde de [back-end](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#back-end-health) apareça como **Desconhecido**. Também pode fazer com que a geração de registos e métricas de Gateway de aplicação falhe. Recomendamos que não utilize UDRs na subnet Application Gateway para que possa ver a saúde, registos e métricas de back-end.

- **v1**

   Para o V1 SKU, as rotas definidas pelo utilizador (UDRs) são suportadas na subnet Application Gateway, desde que não alterem a comunicação de pedido/resposta de ponta a ponta. Por exemplo, pode configurar um UDR na subnet Application Gateway para apontar para um aparelho de firewall para inspeção de pacotes. Mas deve certificar-se de que o pacote pode chegar ao destino pretendido após a inspeção. Se não o fizer, pode resultar em comportamentos incorretos de saúde ou de encaminhamento de tráfego. Isto inclui rotas aprendidas ou rotas padrão 0.0.0.0/0 que são propagadas por gateways Azure ExpressRoute ou VPN na rede virtual.

- **v2**

   Para o V2 SKU, existem cenários apoiados e não apoiados:

   **cenários apoiados v2**
   > [!WARNING]
   > Uma configuração incorreta da tabela de rotas pode resultar em encaminhamento assimétrico no Gateway aplicativo v2. Certifique-se de que todo o tráfego de aviões de gestão/controlo é enviado diretamente para a Internet e não através de um aparelho virtual. A exploração madeireira e as métricas também podem ser afetadas.


  **Cenário 1**: UDR para desativar a propagação da rota do Protocolo de Gateway fronteiriço (BGP) para a subnet de gateway de aplicação

   Por vezes, a rota de gateway padrão (0.0.0.0/0) é publicitada através dos gateways ExpressRoute ou VPN associados à rede virtual Application Gateway. Isto quebra o tráfego de aviões de gestão, o que requer um caminho direto para a Internet. Nestes cenários, um UDR pode ser usado para desativar a propagação da rota bGP. 

   Para desativar a propagação da rota BGP, utilize os seguintes passos:

   1. Crie um recurso de Mesa de Rota em Azure.
   2. Desative o parâmetro de propagação da rota de gateway da **rede virtual.** 
   3. Associe a Tabela de Rota à sub-rede apropriada. 

   Permitir o UDR para este cenário não deve quebrar nenhuma configuração existente.

  **Cenário 2**: UDR para dirigir 0.0.0.0/0 para a Internet

   Pode criar um UDR para enviar o tráfego 0.0.0.0/0 diretamente para a Internet. 

  **Cenário 3**: UDR para Serviço Azure Kubernetes com kubenet

  Se estiver a utilizar kubenet com o Serviço Azure Kubernetes (AKS) e o Application Gateway Ingress Controller (AGIC), precisará de uma tabela de rotas para permitir que o tráfego enviado para as cápsulas do Application Gateway seja encaminhado para o nó correto. Isto não será necessário se usar estoque sinuoso. 

  Para utilizar a tabela de rotas para permitir que kubenet funcione, siga os passos abaixo:

  1. Vá ao grupo de recursos criado pela AKS (o nome do grupo de recursos deve começar com "MC_")
  2. Encontre a tabela de rotas criada pela AKS nesse grupo de recursos. A tabela de rotas deve ser povoada com as seguintes informações:
     - O prefixo de endereço deve ser o intervalo IP das cápsulas que pretende alcançar no AKS. 
     - O próximo tipo de lúpulo deve ser o Aparelho Virtual. 
     - O próximo endereço de lúpulo deve ser o endereço IP do nó que acolhe as cápsulas.
  3. Associe esta tabela de rotas à subnet Application Gateway. 
    
  **v2 cenários não apoiados**

  **Cenário 1**: UDR para aparelhos virtuais

  Qualquer cenário em que 0.0.0.0/0 precise de ser redirecionado através de qualquer aparelho virtual, de uma rede virtual hub/spoke, ou no local (túnel forçado) não é suportado para V2.

## <a name="front-end-ip"></a>IP frontal

Pode configurar o portal de aplicação para ter um endereço IP público, um endereço IP privado, ou ambos. É necessário um IP público quando acolhe uma parte traseira que os clientes devem ter acesso através da internet através de um IP virtual virado para a Internet (VIP). 

Um IP público não é necessário para um ponto final interno que não esteja exposto à internet. É conhecido como um ponto final *de equilíbrio de carga interna* (ILB) ou IP frontal privado. Um gateway de aplicação ILB é útil para aplicações internas de linha de negócio que não estão expostas à internet. Também é útil para serviços e níveis numa aplicação de vários níveis dentro de um limite de segurança que não está exposto à internet, mas que requer distribuição de carga de robin redondo, stickiness de sessão ou tls terminação.

Apenas 1 endereço IP público ou um endereço IP privado é suportado. Escolha o IP frontal quando criar o gateway da aplicação.

- Para um IP público, pode criar um novo endereço IP público ou utilizar um IP público existente no mesmo local que o gateway da aplicação. Para mais informações, consulte [o endereço IP público estática vs. dinâmico](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#static-versus-dynamic-public-ip-address).

- Para um IP privado, pode especificar um endereço IP privado da subnet onde o gateway da aplicação é criado. Se não especificar um, é selecionado automaticamente um endereço IP arbitrário a partir da sub-rede. O tipo de endereço IP que selecionar (estático ou dinâmico) não pode ser alterado mais tarde. Para mais informações, consulte [Criar um portal de aplicação com um equilibrador](https://docs.microsoft.com/azure/application-gateway/application-gateway-ilb-arm)de carga interna .

Um endereço IP frontal está associado a um *ouvinte,* que verifica os pedidos de entrada no IP frontal.

## <a name="listeners"></a>Serviços de escuta

Um ouvinte é uma entidade lógica que verifica os pedidos de ligação que chegam utilizando o endereço porta, protocolo, anfitrião e IP. Quando configurar o ouvinte, deve introduzir valores para estes que correspondam aos valores correspondentes no pedido de entrada no gateway.

Ao criar um portal de aplicação utilizando o portal Azure, também cria um ouvinte predefinido, escolhendo o protocolo e a porta para o ouvinte. Pode escolher se ativa o suporte HTTP2 no ouvinte. Depois de criar o gateway da aplicação, pode editar as definições desse ouvinte padrão *(appGatewayHttpListener*) ou criar novos ouvintes.

### <a name="listener-type"></a>Tipo de ouvinte

Quando cria um novo ouvinte, escolhe-se entre [ *o básico* e *o multi-site*](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#types-of-listeners).

- Se quiser que todos os seus pedidos (para qualquer domínio) sejam aceites e encaminhados para piscinas de backend, escolha o básico. Aprenda a criar um portal de [aplicação com um ouvinte básico](https://docs.microsoft.com/azure/application-gateway/quick-create-portal).

- Se pretender encaminhar pedidos para diferentes piscinas de backend com base no cabeçalho do *anfitrião* ou no nome de anfitrião, escolha um ouvinte multi-site, onde também deve especificar um nome de anfitrião que corresponda ao pedido de entrada. Isto porque o Application Gateway conta com cabeçalhos de anfitrião HTTP 1.1 para acolher mais de um site no mesmo endereço IP público e porta.

#### <a name="order-of-processing-listeners"></a>Ordem de processamento de ouvintes

Para o V1 SKU, os pedidos são combinados de acordo com a ordem das regras e o tipo de ouvinte. Se uma regra com ouvinte básico vier em primeiro lugar na ordem, é processada primeiro e aceitará qualquer pedido para essa combinação de porta e IP. Para evitar isto, configure as regras com os ouvintes multi-sites primeiro e empurre a regra com o ouvinte básico para o último da lista.

Para o V2 SKU, os ouvintes multi-sites são processados antes dos ouvintes básicos.

### <a name="front-end-ip"></a>IP frontal

Escolha o endereço IP frontal que pretende associar a este ouvinte. O ouvinte ouvirá os pedidos de entrada neste IP.

### <a name="front-end-port"></a>Porta frontal

Escolha a porta frontal. Selecione uma porta existente ou crie uma nova. Escolha qualquer valor da [gama permitida de portas](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#ports). Você pode usar não só portas bem conhecidas, como 80 e 443, mas qualquer porta personalizada permitida que seja adequada. Um porto pode ser usado para ouvintes virados para o público ou para os ouvintes virados para o privado.

### <a name="protocol"></a>Protocolo

Escolha HTTP ou HTTPS:

- Se escolher HTTP, o tráfego entre o cliente e o gateway da aplicação não está encriptado.

- Escolha HTTPS se quiser [a rescisão de TLS](features.md#secure-sockets-layer-ssltls-termination) ou a [encriptação TLS](https://docs.microsoft.com/azure/application-gateway/ssl-overview)de ponta a ponta . O tráfego entre o cliente e o portal da aplicação está encriptado. E a ligação TLS termina na porta de entrada de aplicações. Se pretender encriptação TLS de ponta a ponta, tem de escolher HTTPS e configurar a definição **http-end.** Isto garante que o tráfego é reencriptado quando viaja desde a porta de entrada da aplicação até à parte de trás.


Para configurar a terminação do TLS e a encriptação TLS de ponta a ponta, deve adicionar um certificado ao ouvinte para permitir que o portal de aplicação obtenha uma chave simétrica. Isto é ditado pela especificação do protocolo TLS. A chave simétrica é usada para encriptar e desencriptar o tráfego que é enviado para o portal. O certificado de gateway deve estar no formato Personal Information Exchange (PFX). Este formato permite-lhe exportar a chave privada que o gateway utiliza para encriptar e desencriptar o tráfego.

#### <a name="supported-certificates"></a>Certificados suportados

Consulte [os certificados suportados para a rescisão de TLS](https://docs.microsoft.com/azure/application-gateway/ssl-overview#certificates-supported-for-ssl-termination).

### <a name="additional-protocol-support"></a>Suporte adicional ao protocolo

#### <a name="http2-support"></a>Suporte HTTP2

O suporte do protocolo HTTP/2 está disponível para clientes que se ligam apenas aos ouvintes de gateway de aplicação. A comunicação para piscinas de servidores de back-end é superior a HTTP/1.1. Por defeito, o suporte HTTP/2 é desativado. O seguinte código Azure PowerShell mostra como ativar isto:

```azurepowershell
$gw = Get-AzApplicationGateway -Name test -ResourceGroupName hm

$gw.EnableHttp2 = $true

Set-AzApplicationGateway -ApplicationGateway $gw
```

#### <a name="websocket-support"></a>Suporte do WebSocket

O suporte WebSocket é ativado por padrão. Não existe uma definição configurável pelo utilizador para o ativar ou desativar. Pode utilizar WebSockets com ouvintes HTTP e HTTPS.

### <a name="custom-error-pages"></a>Páginas de erro personalizadas

Pode definir erro personalizado a nível global ou ao nível do ouvinte. Mas a criação de páginas de erro personalizadas a nível global a partir do portal Azure não é atualmente suportada. Pode configurar uma página de erro personalizada para um erro de firewall de aplicação web 403 ou uma página de manutenção 502 ao nível do ouvinte. Deve também especificar um URL de bolha acessível ao público para o código de estado de erro dado. Para obter mais informações, consulte [Criar páginas de erro personalizadas do Gateway de Aplicação](https://docs.microsoft.com/azure/application-gateway/custom-error).

![Códigos de erro gateway de aplicação](https://docs.microsoft.com/azure/application-gateway/media/custom-error/ag-error-codes.png)

Para configurar uma página de erro personalizada global, consulte a [configuração Do PowerShell do Azure](https://docs.microsoft.com/azure/application-gateway/custom-error#azure-powershell-configuration).

### <a name="tls-policy"></a>Política de TLS

Pode centralizar a gestão de certificados TLS/SSL e reduzir a sobrecarga de encriptação-desencriptação para uma exploração de servidores de back-end. O manuseamento centralizado de TLS também permite especificar uma política central de TLS que é adequada aos seus requisitos de segurança. Pode escolher a política de TLS *padrão,* *predefinida*ou *personalizada.*

Configura a política tLS para controlar as versões de protocolo TLS. Pode configurar uma porta de entrada de aplicação para utilizar uma versão de protocolo mínimo para apertos de mão TLS1.0, TLS1.1 e TLS1.2. Por predefinição, sSL 2.0 e 3.0 estão desativados e não são configuráveis. Para mais informações, consulte a visão geral da política do [Application Gateway TLS](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-policy-overview).

Depois de criar um ouvinte, associa-o a uma regra de encaminhamento de pedidos. Esta regra determina como os pedidos que são recebidos no ouvinte são encaminhados para o final.

## <a name="request-routing-rules"></a>Solicitar regras de encaminhamento

Quando cria um portal de aplicação utilizando o portal Azure, cria-se uma regra predefinida *(regra 1*). Esta regra liga o ouvinte predefinido *(appGatewayHttpListener)* com o pool de back-end padrão *(appGatewayBackendPool*) e as definições de HTTP de back-end padrão *(appGatewayBackendHttpSettings*). Depois de criar o gateway, pode editar as definições da regra padrão ou criar novas regras.

### <a name="rule-type"></a>Tipo de regra

Quando se cria uma regra, [ *basic* *escolhe-se*](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#request-routing-rules)entre o básico e o caminho.

- Escolha o básico se quiser encaminhar todos os pedidos no ouvinte associado (por exemplo, *blog <i></i> .contoso.com/ \* )* para uma única piscina de back-end.
- Escolha os pedidos baseados em percursos se quiser encaminhar pedidos de caminhos de URL específicos para piscinas específicas de back-end. O padrão do caminho é aplicado apenas ao caminho do URL, não aos seus parâmetros de consulta.

#### <a name="order-of-processing-rules"></a>Ordem das regras de processamento

Para o V1 e v2 SKU, a correspondência de padrões dos pedidos de entrada é processada na ordem de que os caminhos estão listados no mapa do caminho URL da regra baseada no caminho. Se um pedido corresponder ao padrão em dois ou mais caminhos no mapa do caminho, o caminho que está listado primeiro é combinado. E o pedido é encaminhado para a parte de trás que está associado a esse caminho.

### <a name="associated-listener"></a>Ouvinte associado

Associe um ouvinte à regra para que a *regra de encaminhamento de pedidos* que esteja associada ao ouvinte seja avaliada para determinar o pool back-end para encaminhar o pedido para.

### <a name="associated-back-end-pool"></a>Piscina de back-end associada

Associar à regra o pool back-end que contém os alvos de back-end que servem pedidos que o ouvinte recebe.

 - Para uma regra básica, apenas uma piscina de back-end é permitida. Todos os pedidos sobre o ouvinte associado são encaminhados para aquela piscina de back-end.

 - Para uma regra baseada no caminho, adicione várias piscinas traseiras que correspondem a cada caminho url. Os pedidos que correspondem ao caminho url introduzido são encaminhados para a piscina traseira correspondente. Além disso, adicione uma piscina de back-end padrão. Pedidos que não correspondam a nenhum caminho de URL na regra são encaminhados para essa piscina.

### <a name="associated-back-end-http-setting"></a>Definição de HTTP de back-end associada

Adicione uma definição HTTP de back-end para cada regra. Os pedidos são encaminhados da porta de entrada de aplicação para os alvos de back-end utilizando o número de porta, protocolo e outras informações especificadas nesta definição.

Para uma regra básica, apenas é permitida uma definição http back-end. Todos os pedidos no ouvinte associado são encaminhados para os alvos correspondentes de back-end utilizando esta definição HTTP.

Para uma regra baseada no caminho, adicione várias definições de HTTP de back-end que correspondem a cada caminho URL. Os pedidos que correspondam ao caminho do URL nesta definição são encaminhados para os alvos correspondentes de back-end utilizando as definições HTTP que correspondem a cada caminho de URL. Além disso, adicione uma definição DE HTTP padrão. Os pedidos que não correspondem a nenhum caminho de URL nesta regra são encaminhados para o pool de back-end padrão utilizando a definição padrão HTTP.

### <a name="redirection-setting"></a>Definição de reorientação

Se a reorientação estiver configurada para uma regra básica, todos os pedidos no ouvinte associado são redirecionados para o alvo. Isto é uma reorientação *global.* Se a reorientação for configurada para uma regra baseada no caminho, apenas os pedidos numa área específica do local são redirecionados. Um exemplo é uma área de carrinho de compras que é denotada por */carrinho/ \* *. Isto é uma redirecção *baseada no caminho.*

Para mais informações sobre redirecionamentos, consulte a visão geral do [Redirect Gateway](redirect-overview.md).

#### <a name="redirection-type"></a>Tipo de reorientação

Escolha o tipo de reorientação exigido: *Permanente(301),* *Temporário (307),* *Found(302) ou* *Ver outro(303)*.

#### <a name="redirection-target"></a>Alvo de reorientação

Escolha outro ouvinte ou um site externo como alvo de reorientação.

##### <a name="listener"></a>Serviço de Escuta

Escolha o ouvinte como alvo de reorientação para redirecionar o tráfego de um ouvinte para outro na porta de entrada. Esta definição é necessária quando pretende ativar a reorientação http-to-HTTPS. Redireciona o tráfego do ouvinte de origem que verifica os pedidos de HTTP para o ouvinte de destino que verifica os pedidos https que chegam. Também pode optar por incluir a corda de consulta e o caminho a partir do pedido original no pedido que é encaminhado para o alvo de reorientação.

![Caixa de diálogo de componentes gateway de aplicação](./media/configuration-overview/configure-redirection.png)

Para mais informações sobre a reorientação http-to-HTTPS, consulte:
- [Reorientação HTTP-to-HTTPS utilizando o portal Azure](redirect-http-to-https-portal.md)
- [Reorientação HTTP-to-HTTPS utilizando a PowerShell](redirect-http-to-https-powershell.md)
- [Reorientação HTTP-to-HTTPS utilizando o Azure CLI](redirect-http-to-https-cli.md)

##### <a name="external-site"></a>Site externo

Escolha o site externo quando pretender redirecionar o tráfego no ouvinte que está associado a esta regra para um site externo. Pode optar por incluir a corda de consulta do pedido original no pedido que é encaminhado para o alvo de reorientação. Não pode encaminhar o caminho para o local externo que estava no pedido original.

Para mais informações sobre a reorientação, consulte:
- [Redirecione o tráfego para um site externo utilizando o PowerShell](redirect-external-site-powershell.md)
- [Redirecione o tráfego para um local externo utilizando o CLI](redirect-external-site-cli.md)

#### <a name="rewrite-the-http-header-setting"></a>Reescreva a definição do cabeçalho HTTP

Esta definição adiciona, remove ou atualiza os cabeçalhos de pedido e resposta http enquanto os pacotes de pedido e resposta se movem entre o cliente e piscinas traseiras. Para obter mais informações, consulte:

 - [Reescrever a visão geral dos cabeçalhos http](rewrite-http-headers.md)
 - [Configure http cabeçalho reescrever](rewrite-http-headers-portal.md)

## <a name="http-settings"></a>Definições de HTTP

O gateway de aplicação direciona o tráfego para os servidores back-end utilizando a configuração que especifica aqui. Depois de criar uma definição HTTP, deve associá-la a uma ou mais regras de encaminhamento de pedidos.

### <a name="cookie-based-affinity"></a>Afinidade com base no cookie

O Portal de Aplicações Azure utiliza cookies geridos por gateway para manter as sessões de utilizador. Quando um utilizador envia o primeiro pedido para o Application Gateway, define um cookie de afinidade na resposta com um valor de hash que contém os detalhes da sessão, de modo que os pedidos subsequentes que transportam o cookie de afinidade serão encaminhados para o mesmo servidor de backend para manter a stickiness. 

Esta funcionalidade é útil quando pretende manter uma sessão de utilizador no mesmo servidor e quando o estado da sessão é guardado localmente no servidor para uma sessão de utilizador. Se a aplicação não conseguir lidar com a afinidade baseada em cookies, não pode utilizar esta funcionalidade. Para usá-lo, certifique-se de que os clientes suportam cookies.

A [atualização v80](https://chromiumdash.appspot.com/schedule) do [navegador Chromium](https://www.chromium.org/Home) trouxe um mandato em que os cookies HTTP sem atributo [sameSite](https://tools.ietf.org/id/draft-ietf-httpbis-rfc6265bis-03.html#rfc.section.5.3.7) têm de ser tratados como SameSite=Lax. No caso dos pedidos cors (Cross-Origin Resource Sharing), se o cookie tiver de ser enviado num contexto de terceiros, tem de utilizar o *SameSite=None; Atributos seguros* e deve ser enviado apenas em HTTPS. Caso contrário, num cenário apenas HTTP, o navegador não envia os cookies no contexto de terceiros. O objetivo desta atualização do Chrome é aumentar a segurança e evitar ataques de falsificação de pedido de cross-site (CSRF). 

Para suportar esta alteração, a partir de 17 de fevereiro de 2020, o Application Gateway (todos os tipos SKU) injetará outro cookie chamado *ApplicationGatewayAffinityCORS,* além do cookie *ApplicationGatewayAffinity* existente. O cookie *ApplicationGatewayAffinityCORS* tem mais dois atributos adicionados ao mesmo (*"SameSite=None; Seguro"*) para que a sessão pegajosa seja mantida mesmo para pedidos de origem cruzada.

Note que o nome padrão do cookie de afinidade é *ApplicationGatewayAffinity* e pode alterá-lo. No caso de utilizar um nome personalizado de cookie de afinidade, um cookie adicional é adicionado com CORS como sufixo. Por exemplo, *CustomCookieNameCORS*.

> [!NOTE]
> Se o atributo *SameSite=None* estiver definido, é obrigatório que o cookie também contenha a bandeira *Secure,* e deve ser enviado em HTTPS.  Se for necessária afinidade da sessão em relação ao CORS, tem de migrar a sua carga de trabalho para HTTPS. Consulte a documentação TLS de descarregar e tLS de ponta a ponta para gateway de aplicação aqui – [Visão geral](ssl-overview.md), Configure um gateway de [aplicação com terminação TLS utilizando o portal Azure,](create-ssl-portal.md) [Configure TLS](end-to-end-ssl-portal.md)de ponta a ponta utilizando o Gateway de aplicação com o portal .

### <a name="connection-draining"></a>Drenagem de ligação

A drenagem de ligações ajuda-o a remover graciosamente os membros da piscina de back-end durante as atualizações de serviço planeadas. Você pode aplicar este cenário a todos os membros de uma piscina back-end durante a criação de regras. Garante que todos os casos de desregistro de um pool de back-end continuam a manter as ligações existentes e a servir pedidos em curso para um tempo de tempo configurável e não recebem quaisquer novos pedidos ou ligações. A única exceção a esta questão são os pedidos de desregisto de casos devido à afinidade da sessão gerida por gateways e continuará a ser encaminhado para os casos de desregistando. A drenagem da ligação aplica-se a instâncias traseiras que são explicitamente removidas da piscina traseira.

### <a name="protocol"></a>Protocolo

O Gateway de aplicações suporta http e HTTPS para encaminhamento de pedidos para os servidores de back-end. Se escolher HTTP, o tráfego para os servidores de back-end não está encriptado. Se a comunicação não encriptada não for aceitável, escolha HTTPS.

Esta definição combinada com HTTPS no ouvinte suporta [TLS de ponta a ponta](ssl-overview.md). Isto permite-lhe transmitir de forma segura dados sensíveis encriptados para a parte de trás. Cada servidor de back-end no pool de back-end que tenha TLS de ponta a ponta deve ser configurado com um certificado que permita uma comunicação segura.

### <a name="port"></a>Porta

Esta definição especifica a porta onde os servidores de back-end ouvem o tráfego a partir do gateway da aplicação. Pode configurar portas que variam entre 1 e 65535.

### <a name="request-timeout"></a>Pedir tempo de descanso

Esta definição é o número de segundos que o gateway da aplicação aguarda para receber uma resposta do servidor de back-end.

### <a name="override-back-end-path"></a>Anular o caminho de trás

Esta definição permite configurar um caminho de encaminhamento personalizado opcional para usar quando o pedido é encaminhado para a parte de trás. Qualquer parte do caminho que corresponde ao caminho personalizado no campo de caminho de **backend** é copiada para o caminho para a frente. A tabela seguinte mostra como esta funcionalidade funciona:

- Quando a definição HTTP estiver anexada a uma regra básica de encaminhamento de pedidos:

  | Pedido original  | Anular o caminho de trás | Pedido encaminhado para o fim |
  | ----------------- | --------------------- | ---------------------------- |
  | /casa/            | /sobreposição/            | /sobreposição/casa/              |
  | /casa/segunda casa/ | /sobreposição/            | /override/home/secondhome/   |

- Quando a definição HTTP estiver ligada a uma regra de encaminhamento de pedidos baseada no caminho:

  | Pedido original           | Regra do caminho       | Anular o caminho de trás | Pedido encaminhado para o fim |
  | -------------------------- | --------------- | --------------------- | ---------------------------- |
  | /pathrule/home/            | /pathrule*      | /sobreposição/            | /sobreposição/casa/              |
  | /pathrule/home/secondhome/ | /pathrule*      | /sobreposição/            | /override/home/secondhome/   |
  | /casa/                     | /pathrule*      | /sobreposição/            | /sobreposição/casa/              |
  | /casa/segunda casa/          | /pathrule*      | /sobreposição/            | /override/home/secondhome/   |
  | /pathrule/home/            | /pathrule/home* | /sobreposição/            | /sobreposição/                   |
  | /pathrule/home/secondhome/ | /pathrule/home* | /sobreposição/            | /override/secondhome/        |
  | /pathrule/                 | /pathrule/      | /sobreposição/            | /sobreposição/                   |

### <a name="use-for-app-service"></a>Utilização para serviço de aplicações

This is a UI only shortcut that selects the two required settings for the Azure App Service back end. Permite **escolher o nome do anfitrião a partir do endereço back-end**, e cria uma nova sonda personalizada se ainda não tiver um. (Para mais informações, consulte o nome do anfitrião Pick na secção de definição de [endereço sinuoso](#pick) deste artigo.) Uma nova sonda é criada, e o cabeçalho da sonda é escolhido a partir do endereço do membro de trás para trás.

### <a name="use-custom-probe"></a>Use sonda personalizada

Esta definição associa uma [sonda personalizada](application-gateway-probe-overview.md#custom-health-probe) com uma definição HTTP. Só pode associar uma sonda personalizada com uma definição HTTP. Se não associar explicitamente uma sonda personalizada, a [sonda padrão](application-gateway-probe-overview.md#default-health-probe-settings) é usada para monitorizar a saúde da parte de trás. Recomendamos que crie uma sonda personalizada para um maior controlo sobre a monitorização da saúde das suas costas.

> [!NOTE]
> A sonda personalizada não monitoriza a saúde da piscina traseira a menos que a definição correspondente de HTTP esteja explicitamente associada a um ouvinte.

### <a name="pick-host-name-from-back-end-address"></a><a id="pick"/></a>Escolha o nome do anfitrião do endereço back-end

Esta capacidade define dinamicamente o cabeçalho do *anfitrião* no pedido ao nome do anfitrião da piscina de fundo. Utiliza um endereço IP ou FQDN.

Esta funcionalidade ajuda quando o nome de domínio da parte de trás é diferente do nome DNS do gateway da aplicação, e a parte traseira depende de um cabeçalho específico do hospedeiro para resolver o ponto final correto.

Um caso de exemplo são os serviços multi-inquilinos como o final. Um serviço de aplicativos é um serviço multi-inquilino que usa um espaço partilhado com um único endereço IP. Assim, um serviço de aplicações só pode ser acedido através dos nomes de anfitriões que estão configurados nas definições de domínio personalizados.

Por predefinição, o nome de domínio personalizado é *example.azurewebsites.net*. Para aceder ao seu serviço de aplicações utilizando um portal de aplicações através de um nome de anfitrião que não esteja explicitamente registado no serviço de aplicações ou através do FQDN do gateway da aplicação, você sobrepõe o nome de anfitrião no pedido original para o nome de anfitrião do serviço de aplicações. Para tal, ative o nome do anfitrião da definição **de endereço de backend.**

Para um domínio personalizado cujo nome DNS personalizado existente está mapeado para o serviço de aplicações, não precisa de ativar esta definição.

> [!NOTE]
> Esta definição não é necessária para o Ambiente de Serviço de Aplicações, que é uma implementação dedicada.

### <a name="host-name-override"></a>Anulação do nome do anfitrião

Esta capacidade substitui o cabeçalho do *anfitrião* no pedido de entrada no gateway da aplicação com o nome de anfitrião que especifica.

Por exemplo, se *www.contoso.com* for especificado na definição de nome do **Anfitrião,** o pedido original * `https://appgw.eastus.cloudapp.azure.com/path1` é alterado para * quando o pedido é encaminhado para o servidor de `https://www.contoso.com/path1` back-end.

## <a name="back-end-pool"></a>Conjunto back-end

Pode apontar um pool de back-end para quatro tipos de membros de backend: uma máquina virtual específica, um conjunto de escala de máquina virtual, um endereço IP/FQDN ou um serviço de aplicações. 

Depois de criar uma piscina de back-end, deve associá-la a uma ou mais regras de encaminhamento de pedidos. Também deve configurar sondas de saúde para cada piscina de fundo na sua porta de entrada de aplicação. Quando uma condição de regra de encaminhamento de pedidos é satisfeita, o gateway da aplicação encaminha o tráfego para os servidores saudáveis (conforme determinado pelas sondas de saúde) no conjunto de back-end correspondente.

## <a name="health-probes"></a>Sondas do estado de funcionamento

Um gateway de aplicação monitoriza a saúde de todos os recursos na sua parte de trás por defeito. Mas recomendamos vivamente que crie uma sonda personalizada para cada definição http-end para obter um maior controlo sobre a monitorização da saúde. Para aprender a configurar uma sonda personalizada, consulte as configurações da sonda de [saúde personalizada](application-gateway-probe-overview.md#custom-health-probe-settings).

> [!NOTE]
> Depois de criar uma sonda de saúde personalizada, precisa associá-la a uma definição HTTP de back-end. Uma sonda personalizada não monitorizará a saúde da piscina traseira a menos que a definição correspondente de HTTP esteja explicitamente associada a um ouvinte usando uma regra.

## <a name="next-steps"></a>Próximos passos

Agora que sabe sobre componentes do Application Gateway, pode:

- [Criar uma porta de aplicação no portal Azure](quick-create-portal.md)
- [Criar um portal de aplicação usando o PowerShell](quick-create-powershell.md)
- [Criar um portal de aplicação utilizando o Azure CLI](quick-create-cli.md)
