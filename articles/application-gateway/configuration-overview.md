---
title: Visão geral da configuração do Gateway de aplicação Azure
description: Este artigo descreve como configurar os componentes do Azure Application Gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: absha
ms.openlocfilehash: 1e3ef1133628f0470ee92237abf20d3bb0a9e21a
ms.sourcegitcommit: bf99428d2562a70f42b5a04021dde6ef26c3ec3a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/23/2020
ms.locfileid: "85254672"
---
# <a name="application-gateway-configuration-overview"></a>Visão geral da configuração do Gateway de Aplicação

O Azure Application Gateway é composto por vários componentes que pode configurar de várias formas para diferentes cenários. Este artigo mostra-lhe como configurar cada componente.

![Gráfico de fluxo de componentes do Gateway de aplicação](./media/configuration-overview/configuration-overview1.png)

Esta imagem ilustra uma aplicação que tem três ouvintes. Os dois primeiros são ouvintes multi-sites para `http://acme.com/*` `http://fabrikam.com/*` e, respectivamente. Ambos ouvem no porto 80. O terceiro é um ouvinte básico que tem a rescisão de segurança da camada de transporte (TLS) de ponta a ponta, anteriormente conhecida como terminação da Camada de Tomadas Seguras (SSL).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

### <a name="azure-virtual-network-and-dedicated-subnet"></a>Rede virtual Azure e sub-rede dedicada

Um gateway de aplicações é uma implementação dedicada na sua rede virtual. Dentro da sua rede virtual, é necessária uma sub-rede dedicada para o gateway de aplicações. Pode ter várias instâncias de uma determinada implementação de gateway de aplicação numa sub-rede. Também pode implementar outros gateways de aplicações na sub-rede. Mas não pode implementar nenhum outro recurso na sub-rede de gateway de aplicações.

> [!NOTE]
> Não é possível misturar Standard_v2 e Standard Azure Application Gateway na mesma sub-rede.

#### <a name="size-of-the-subnet"></a>Tamanho da sub-rede

O Application Gateway utiliza um endereço IP privado por exemplo, além de outro endereço IP privado se um IP frontal privado estiver configurado.

O Azure também reserva cinco endereços IP em cada sub-rede para uso interno: os quatro primeiros e os últimos endereços IP. Por exemplo, considere 15 instâncias de gateway de aplicações sem IP frontal privado. Precisa de pelo menos 20 endereços IP para esta sub-rede: cinco para uso interno e 15 para as instâncias de gateway de aplicação. Então, precisa de um tamanho de sub-rede /27 ou maior.

Considere uma sub-rede que tem 27 instâncias de gateway de aplicação e um endereço IP para um IP frontal privado. Neste caso, precisa de 33 endereços IP: 27 para as instâncias de gateway de aplicação, um para a frente privada e cinco para uso interno. Então, precisa de um tamanho de sub-rede /26 ou maior.

Recomendamos que utilize um tamanho de sub-rede de pelo menos /28. Este tamanho dá-lhe 11 endereços IP utilizáveis. Se a carga da sua aplicação necessitar de mais de 10 instâncias do Gateway de aplicação, considere um tamanho de sub-rede /27 ou /26.

#### <a name="network-security-groups-on-the-application-gateway-subnet"></a>Grupos de segurança de rede na sub-rede Do Gateway de Aplicação

Os grupos de segurança da rede (NSGs) são suportados no Gateway de Aplicações. Mas há algumas restrições:

- Deve permitir a entrada de tráfego de Internet nas portas TCP 65503-65534 para o Gateway de Aplicação v1 SKU e portas TCP 65200-65535 para o V2 SKU com a sub-rede de destino como **Qualquer** e fonte como etiqueta de serviço **GatewayManager.** Esta gama portuária é necessária para a comunicação da infraestrutura Azure. Estas portas estão protegidas (bloqueadas) pelos certificados Azure. Entidades externas, incluindo os clientes desses gateways, não podem comunicar nestes pontos finais.

- A conectividade de saída da Internet não pode ser bloqueada. As regras de saída predefinidos no NSG permitem a conectividade da Internet. É recomendável que:

  - Não remova as regras de saída padrão.
  - Não crie outras regras de saída que neguem qualquer conectividade de saída.

- O tráfego da etiqueta **AzureLoadBalancer** deve ser permitido.

#### <a name="allow-application-gateway-access-to-a-few-source-ips"></a>Permitir o acesso do Gateway de Aplicações a alguns IPs de origem

Para este cenário, utilize NSGs na sub-rede Do Gateway de Aplicação. Colocar as seguintes restrições à sub-rede nesta ordem de prioridade:

1. Permitir a entrada de tráfego a partir de uma gama IP ou IP de origem com o destino como toda a gama de endereços de sub-rede do Application Gateway e porta de destino como porta de acesso de entrada, por exemplo, porta 80 para acesso HTTP.
2. Permitir pedidos de entrada de fonte como etiqueta de serviço **GatewayManager** e destino como **Todas** as portas e portos de destino como 65503-65534 para o Gateway de Aplicação v1 SKU, e portas 65200-65535 para v2 SKU para [comunicação de estado de saúde back-end](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics). Esta gama portuária é necessária para a comunicação da infraestrutura Azure. Estas portas estão protegidas (bloqueadas) pelos certificados Azure. Sem certificados adequados, as entidades externas não podem iniciar alterações nesses pontos finais.
3. Permitir a entrada de sondas Azure Load Balancer *(AzureLoadBalancer* tag) e o tráfego de rede virtual de entrada *(virtualNetwork* tag) no [grupo de segurança](https://docs.microsoft.com/azure/virtual-network/security-overview)da rede .
4. Bloqueie todo o tráfego de entrada usando uma regra de negação.
5. Permitir tráfego de saída para a internet para todos os destinos.

#### <a name="user-defined-routes-supported-on-the-application-gateway-subnet"></a>Rotas definidas pelo utilizador suportadas na sub-rede do Gateway de Aplicação

> [!IMPORTANT]
> A utilização de UDRs na sub-rede Do Gateway de Aplicação pode fazer com que o estado de saúde na [visão de saúde traseira](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#back-end-health) apareça como **Desconhecido**. Também pode causar a falha na geração de registos e métricas do Gateway de Aplicação. Recomendamos que não utilize UDRs na sub-rede Do Gateway de Aplicação para que possa ver a saúde, registos e métricas de back-end.

- **v1**

   Para o v1 SKU, as rotas definidas pelo utilizador (UDRs) são suportadas na sub-rede Do Gateway de Aplicação, desde que não alterem a comunicação de pedido/resposta de ponta a ponta. Por exemplo, pode configurar um UDR na sub-rede Do Gateway de aplicação para apontar para um aparelho de firewall para inspeção de pacotes. Mas deve certificar-se de que o pacote pode chegar ao destino pretendido após a inspeção. Se não o fizer, pode resultar em comportamentos incorretos de sonda de saúde ou de encaminhamento de tráfego. Isto inclui rotas aprendidas ou rotas padrão 0.0.0.0/0 que são propagadas por gateways Azure ExpressRoute ou VPN na rede virtual.

- **v2**

   Para o V2 SKU, existem cenários suportados e não apoiados:

   **v2 cenários apoiados**
   > [!WARNING]
   > Uma configuração incorreta da tabela de rotas pode resultar num encaminhamento assimétrico no Gateway de Aplicação v2. Certifique-se de que todo o tráfego de avião de gestão/controlo é enviado diretamente para a Internet e não através de um aparelho virtual. O registo e as métricas também podem ser afetados.


  **Cenário 1**: UDR para desativar o Protocolo de Gateway de Fronteira (BGP) Propagação da rota para a sub-rede do Gateway de Aplicação

   Por vezes, a rota de gateway padrão (0.0.0.0/0) é publicitada através dos gateways ExpressRoute ou VPN associados à rede virtual Application Gateway. Isto interrompe a gestão do tráfego de aviões, o que requer um caminho direto para a Internet. Nesses cenários, um UDR pode ser usado para desativar a propagação da rota BGP. 

   Para desativar a propagação da rota BGP, utilize os seguintes passos:

   1. Crie um recurso de tabela de rota em Azure.
   2. Desative o parâmetro **de propagação da rota do gateway de rede Virtual.** 
   3. Associar a Tabela de Rota à sub-rede apropriada. 

   Permitir a UDR para este cenário não deve quebrar quaisquer configurações existentes.

  **Cenário 2**: UDR para dirigir 0.0.0.0/0 para a Internet

   Pode criar um UDR para enviar tráfego de 0.0.0.0/0 diretamente para a Internet. 

  **Cenário 3**: UDR para serviço Azure Kubernetes com kubenet

  Se estiver a utilizar kubenet com o Serviço Azure Kubernetes (AKS) e o Controlador de Entradas de Gateway de Aplicação (AGIC), precisará de uma tabela de rotas para permitir que o tráfego enviado para as cápsulas a partir do Application Gateway seja encaminhado para o nó correto. Isto não será necessário se utilizar o Azure CNI. 

  Para utilizar a tabela de rotas para permitir o trabalho da Kubenet, siga os passos abaixo:

  1. Vá para o grupo de recursos criado pela AKS (o nome do grupo de recursos deve começar com "MC_")
  2. Encontre a tabela de rotas criada pela AKS nesse grupo de recursos. O quadro de rotas deve ser preenchido com as seguintes informações:
     - O prefixo do endereço deve ser a gama IP das cápsulas que pretende alcançar em AKS. 
     - O próximo tipo de lúpulo deve ser o Aparelho Virtual. 
     - O próximo endereço de lúpulo deve ser o endereço IP do nó que hospeda as cápsulas.
  3. Associe esta tabela de rota à sub-rede Application Gateway. 
    
  **v2 cenários não apoiados**

  **Cenário 1**: UDR para Aparelhos Virtuais

  Qualquer cenário em que o 0.0.0.0/0 tenha de ser redirecionado através de qualquer aparelho virtual, de uma rede virtual de hub/spoke, ou no local (túneis forçados) não é suportado para v2.

## <a name="front-end-ip"></a>IP frontal

Pode configurar a porta de aplicação para ter um endereço IP público, um endereço IP privado ou ambos. É necessário um IP público quando acolhe uma parte traseira que os clientes devem aceder através da internet através de um IP virtual (VIP) virado para a Internet. 

Um IP público não é necessário para um ponto final interno que não esteja exposto à internet. É conhecido como um *ponto final interno de load-balancer* (ILB) ou frontend IP privado. Um gateway de aplicações ILB é útil para aplicações internas de linha de negócio que não estão expostas à internet. Também é útil para serviços e níveis numa aplicação de vários níveis dentro de uma fronteira de segurança que não estão expostos à internet, mas que requerem distribuição de carga de robin redondo, adesivo de sessão ou rescisão de TLS.

Apenas 1 endereço IP público ou um endereço IP privado é suportado. Escolha o IP frontal quando criar o gateway de aplicações.

- Para um IP público, pode criar um novo endereço IP público ou utilizar um IP público existente no mesmo local que o gateway de aplicações. Para mais informações, consulte [o endereço IP público estático vs. dinâmico](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#static-versus-dynamic-public-ip-address).

- Para um IP privado, pode especificar um endereço IP privado a partir da sub-rede onde o gateway de aplicações é criado. Se não especificar um, um endereço IP arbitrário é automaticamente selecionado a partir da sub-rede. O tipo de endereço IP que seleciona (estático ou dinâmico) não pode ser alterado mais tarde. Para obter mais informações, consulte [Criar uma porta de aplicação com um equilibrador de carga interno](https://docs.microsoft.com/azure/application-gateway/application-gateway-ilb-arm).

Um endereço IP frontal está associado a um *ouvinte*, que verifica os pedidos de entrada no IP frontal.

## <a name="listeners"></a>Serviços de escuta

Um ouvinte é uma entidade lógica que verifica os pedidos de ligação recebidas utilizando o endereço porta, protocolo, anfitrião e IP. Ao configurar o ouvinte, deve introduzir valores para estes que correspondam aos valores correspondentes no pedido de entrada no gateway.

Quando cria um gateway de aplicações utilizando o portal Azure, também cria um ouvinte predefinido, escolhendo o protocolo e a porta para o ouvinte. Pode escolher se ativa o suporte HTTP2 no ouvinte. Depois de criar o gateway de aplicações, pode editar as definições desse ouvinte predefinido *(appGatewayHttpListener*) ou criar novos ouvintes.

### <a name="listener-type"></a>Tipo de ouvinte

Quando cria um novo ouvinte, escolhe-se entre [ *o básico* e *o multi-site*](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#types-of-listeners).

- Se quiser que todos os seus pedidos (para qualquer domínio) sejam aceites e reencaminhados para piscinas de backend, escolha o básico. Saiba [como criar uma porta de aplicação com um ouvinte básico.](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)

- Se pretender encaminhar pedidos para diferentes piscinas de backend com base no cabeçalho do *anfitrião* ou nome de anfitrião, escolha o ouvinte de vários locais, onde também deve especificar um nome de anfitrião que corresponda ao pedido de entrada. Isto porque o Application Gateway conta com cabeçalhos de anfitrião HTTP 1.1 para hospedar mais de um website no mesmo endereço IP público e porta.

#### <a name="order-of-processing-listeners"></a>Ordem de processamento dos ouvintes

Para o V1 SKU, os pedidos são correspondidos de acordo com a ordem das regras e o tipo de ouvinte. Se uma regra com o ouvinte básico vem em primeiro lugar na ordem, é processada primeiro e aceitará qualquer pedido para que a combinação porta e IP. Para evitar isto, configurar as regras com os ouvintes multi-locais primeiro e empurrar a regra com o ouvinte básico para o último da lista.

Para o v2 SKU, os ouvintes multi-sites são processados antes dos ouvintes básicos.

### <a name="front-end-ip"></a>IP frontal

Escolha o endereço IP frontal que pretende associar a este ouvinte. O ouvinte ouvirá os pedidos de entrada neste IP.

### <a name="front-end-port"></a>Porta frontal

Escolha a porta frontal. Selecione uma porta existente ou crie uma nova. Escolha qualquer valor da [gama permitida de portas.](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#ports) Você pode usar não só portas bem conhecidas, como 80 e 443, mas qualquer porta personalizada permitida que seja adequada. Um porto pode ser usado para ouvintes virados para o público ou para ouvintes de frente para o privado.

### <a name="protocol"></a>Protocolo

Escolha HTTP ou HTTPS:

- Se escolher HTTP, o tráfego entre o cliente e o gateway de aplicação não está encriptado.

- Escolha HTTPS se quiser [a rescisão de TLS](features.md#secure-sockets-layer-ssltls-termination) ou [encriptação TLS de ponta a ponta.](https://docs.microsoft.com/azure/application-gateway/ssl-overview) O tráfego entre o cliente e o portal de aplicações está encriptado. E a ligação TLS termina no portal de aplicação. Se pretender encriptação TLS de ponta a ponta, tem de escolher HTTPS e configurar a definição **HTTP de fundo.** Isto garante que o tráfego é reencrimado quando viaja da porta de entrada da aplicação para a parte de trás.


Para configurar a rescisão de TLS e a encriptação TLS de ponta a ponta, deve adicionar um certificado ao ouvinte para permitir que o gateway da aplicação obtém uma chave simétrica. Isto é ditado pela especificação do protocolo TLS. A chave simétrica é usada para encriptar e desencriptar o tráfego que é enviado para o portal. O certificado gateway deve estar no formato Personal Information Exchange (PFX). Este formato permite-lhe exportar a chave privada que o gateway utiliza para encriptar e desencriptar o tráfego.

#### <a name="supported-certificates"></a>Certificados suportados

Ver [certificados suportados para a rescisão de TLS](https://docs.microsoft.com/azure/application-gateway/ssl-overview#certificates-supported-for-ssl-termination).

### <a name="additional-protocol-support"></a>Suporte adicional ao protocolo

#### <a name="http2-support"></a>Suporte HTTP2

O suporte ao protocolo HTTP/2 está disponível para clientes que se ligam apenas aos ouvintes do gateway de aplicações. A comunicação para as piscinas de servidor de back-end terminou HTTP/1.1. Por predefinição, o suporte HTTP/2 é desativado. O seguinte corte de código Azure PowerShell mostra como permitir isto:

```azurepowershell
$gw = Get-AzApplicationGateway -Name test -ResourceGroupName hm

$gw.EnableHttp2 = $true

Set-AzApplicationGateway -ApplicationGateway $gw
```

#### <a name="websocket-support"></a>Suporte do WebSocket

O suporte webSocket é ativado por padrão. Não existe uma definição configurável para ativar ou desativá-la. Pode utilizar webSockets com ouvintes HTTP e HTTPS.

### <a name="custom-error-pages"></a>Páginas de erro personalizadas

Pode definir erros personalizados a nível global ou ao nível do ouvinte. Mas a criação de páginas de erro personalizadas a nível global a partir do portal Azure não é suportada. Pode configurar uma página de erro personalizada para um erro de firewall de aplicação web 403 ou uma página de manutenção 502 ao nível do ouvinte. Também deve especificar um URL blob acessível ao público para o código de estado de erro dado. Para obter mais informações, consulte [Criar páginas de erro personalizadas do Gateway de Aplicação](https://docs.microsoft.com/azure/application-gateway/custom-error).

![Códigos de erro do Gateway de Aplicação](https://docs.microsoft.com/azure/application-gateway/media/custom-error/ag-error-codes.png)

Para configurar uma página de erro personalizada global, consulte [a configuração Azure PowerShell](https://docs.microsoft.com/azure/application-gateway/custom-error#azure-powershell-configuration).

### <a name="tls-policy"></a>Política TLS

Pode centralizar a gestão de certificados TLS/SSL e reduzir a sobrecarga de encriptação-desencriptação para uma fazenda de servidores de back-end. O manuseamento centralizado de TLS também permite especificar uma política central de TLS que se adequa aos seus requisitos de segurança. Pode escolher a política *padrão,* *predefinida*ou *personalizada* TLS.

Configura a política TLS para controlar as versões do protocolo TLS. Pode configurar um gateway de aplicações para utilizar uma versão de protocolo mínimo para apertos de mão TLS de TLS1.0, TLS1.1 e TLS1.2. Por predefinição, os SSL 2.0 e 3.0 estão desativadas e não são configuráveis. Para obter mais informações, consulte a [visão geral da política do Gateway de Aplicação TLS](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-policy-overview).

Depois de criar um ouvinte, associá-lo a uma regra de encaminhamento de pedidos. Esta regra determina como os pedidos recebidos no ouvinte são encaminhados para a parte de trás.

## <a name="request-routing-rules"></a>Solicitar regras de encaminhamento

Quando cria um gateway de aplicação utilizando o portal Azure, cria uma regra padrão *(regra 1).* Esta regra liga o ouvinte predefinido *(appGatewayHttpListener*) com o pool back-end padrão *(appGatewayBackendPool*) e as definições HTTP de back-end predefinidos *(appGatewayBackendHttpSettings).* Depois de criar o gateway, pode editar as definições da regra padrão ou criar novas regras.

### <a name="rule-type"></a>Tipo de regra

Quando se cria uma regra, [ *escolhe-se*entre o *básico* e o caminho.](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#request-routing-rules)

- Escolha o básico se quiser encaminhar todos os pedidos no ouvinte associado (por exemplo, *blog <i></i> .contoso.com/ \* )* para um único pool back-end.
- Escolha o caminho baseado se quiser encaminhar pedidos de caminhos de URL específicos para piscinas específicas de back-end. O padrão do caminho é aplicado apenas ao caminho do URL, não aos seus parâmetros de consulta.

#### <a name="order-of-processing-rules"></a>Ordem das regras de processamento

Para o V1 e v2 SKU, a correspondência de padrões dos pedidos de entrada é processada na ordem em que os caminhos estão listados no mapa do caminho URL da regra baseada no caminho. Se um pedido corresponder ao padrão em dois ou mais caminhos no mapa do caminho, o caminho que está listado primeiro é combinado. E o pedido é reencaminhado para a parte de trás que está associado a esse caminho.

### <a name="associated-listener"></a>Ouvinte associado

Associar um ouvinte à regra de modo que a *regra de encaminhamento* de pedidos que está associada ao ouvinte seja avaliada para determinar o pool back-end para encaminhar o pedido para.

### <a name="associated-back-end-pool"></a>Piscina traseira associada

Associar à regra o pool back-end que contém os alvos de back-end que servem os pedidos que o ouvinte recebe.

 - Para uma regra básica, apenas uma piscina traseira é permitida. Todos os pedidos no ouvinte associado são encaminhados para a piscina de back-end.

 - Para uma regra baseada em caminhos, adicione várias piscinas de back-end que correspondem a cada caminho URL. Os pedidos que correspondem ao caminho URL que está inserido são reencaminhados para a piscina traseira correspondente. Além disso, adicione uma piscina traseira padrão. Os pedidos que não correspondam a qualquer caminho URL na regra são reencaminhados para essa piscina.

### <a name="associated-back-end-http-setting"></a>Definição HTTP de back-end associada

Adicione uma definição HTTP de back-end para cada regra. Os pedidos são encaminhados do gateway de aplicação para os alvos de back-end usando o número de porta, protocolo e outras informações especificadas nesta definição.

Para uma regra básica, apenas é permitida uma definição HTTP de back-end. Todos os pedidos no ouvinte associado são reencaminhados para os alvos de back-end correspondentes utilizando esta definição HTTP.

Para uma regra baseada em caminhos, adicione várias definições HTTP de back-end que correspondem a cada caminho URL. Os pedidos que correspondam ao caminho do URL nesta definição são reencaminhados para os alvos de back-end correspondentes, utilizando as definições HTTP que correspondem a cada caminho URL. Além disso, adicione uma definição HTTP predefinida. Os pedidos que não correspondam a qualquer caminho URL nesta regra são reencaminhados para o pool de back-end padrão utilizando a definição HTTP predefinida.

### <a name="redirection-setting"></a>Definição de redirecionamento

Se a reorientação for configurada para uma regra básica, todos os pedidos no ouvinte associado são redirecionados para o alvo. Isto é reorientação *global.* Se a reorientação for configurada para uma regra baseada em caminhos, apenas os pedidos numa área específica do site são redirecionados. Um exemplo é uma área de carrinhos de compras que é denotada por */carrinho/ \* *. Esta é a reorientação *baseada no caminho.*

Para obter mais informações sobre redirecionamentos, consulte [a visão geral do redirecionamento do Gateway de Aplicação](redirect-overview.md).

#### <a name="redirection-type"></a>Tipo de redirecionamento

Escolha o tipo de reorientação necessária: *Permanente(301)*, *Temporário(307)*, *Encontrado(302)* ou *Ver outro(303)*.

#### <a name="redirection-target"></a>Alvo de redirecionamento

Escolha outro ouvinte ou um site externo como alvo de redirecionamento.

##### <a name="listener"></a>Serviço de Escuta

Escolha o ouvinte como o alvo de redirecionamento para redirecionar o tráfego de um ouvinte para outro no gateway. Esta definição é necessária quando pretende ativar a reorientação HTTP-to-HTTPS. Redireciona o tráfego do ouvinte de origem que verifica os pedidos http recebidos para o ouvinte de destino que verifica os pedidos https recebidos. Também pode optar por incluir a cadeia de consulta e o caminho a partir do pedido original no pedido que é reencaminhado para o alvo de redirecionamento.

![Caixa de diálogo de componentes do Gateway de aplicação](./media/configuration-overview/configure-redirection.png)

Para obter mais informações sobre a reorientação HTTP-to-HTTPS, consulte:
- [Reorientação HTTP-to-HTTPS utilizando o portal Azure](redirect-http-to-https-portal.md)
- [Reorientação HTTP-to-HTTPS utilizando o PowerShell](redirect-http-to-https-powershell.md)
- [Reorientação HTTP-to-HTTPS utilizando o Azure CLI](redirect-http-to-https-cli.md)

##### <a name="external-site"></a>Site externo

Escolha o site externo quando pretender redirecionar o tráfego no ouvinte que está associado a esta regra para um site externo. Pode optar por incluir a cadeia de consulta do pedido original no pedido que é reencaminhado para o alvo de redirecionamento. Não se pode encaminhar o caminho para o site externo que estava no pedido original.

Para obter mais informações sobre a reorientação, consulte:
- [Redirecione o tráfego para um site externo utilizando o PowerShell](redirect-external-site-powershell.md)
- [Redirecione o tráfego para um site externo utilizando o CLI](redirect-external-site-cli.md)

#### <a name="rewrite-the-http-header-setting"></a>Reescreva a definição do cabeçalho HTTP

Esta definição adiciona, remove ou atualiza os cabeçalhos de pedido e resposta HTTP enquanto os pacotes de pedido e resposta se movem entre o cliente e as piscinas traseiras. Para obter mais informações, consulte:

 - [Reescrever visão geral dos cabeçalhos HTTP](rewrite-http-headers.md)
 - [Configure HTTP reescrita cabeçalho HTTP](rewrite-http-headers-portal.md)

## <a name="http-settings"></a>Definições de HTTP

O gateway de aplicação encaminha o tráfego para os servidores de back-end usando a configuração que especifica aqui. Depois de criar uma definição HTTP, deve associá-la a uma ou mais regras de encaminhamento de pedidos.

### <a name="cookie-based-affinity"></a>Afinidade com base no cookie

O Azure Application Gateway utiliza cookies geridos por gateways para manter as sessões de utilizador. Quando um utilizador envia o primeiro pedido para o Application Gateway, define um cookie de afinidade na resposta com um valor de haxixe que contém os detalhes da sessão, de modo que os pedidos subsequentes que transportam o cookie de afinidade serão encaminhados para o mesmo servidor de backend para manter a pegajosa. 

Esta funcionalidade é útil quando pretende manter uma sessão de utilizador no mesmo servidor e quando o estado de sessão é guardado localmente no servidor para uma sessão de utilizador. Se a aplicação não conseguir lidar com afinidade baseada em cookies, não poderá utilizar esta funcionalidade. Para usá-lo, certifique-se de que os clientes suportam cookies.

A [atualização v80](https://chromiumdash.appspot.com/schedule) [do navegador de Crómio](https://www.chromium.org/Home) trouxe um mandato em que os cookies HTTP sem atributo [SameSite](https://tools.ietf.org/id/draft-ietf-httpbis-rfc6265bis-03.html#rfc.section.5.3.7) têm de ser tratados como SameSite=Lax. No caso dos pedidos do CORS (Cross-Origin Resource Sharing), se o cookie tiver de ser enviado num contexto de terceiros, tem de utilizar *o SameSite=Nenhum; Atributos* seguros e deve ser enviado apenas https. Caso contrário, num cenário http apenas, o navegador não envia os cookies no contexto de terceiros. O objetivo desta atualização do Chrome é aumentar a segurança e evitar ataques de falsificação de pedidos de cross-site (CSRF). 

Para suportar esta mudança, a partir de 17 de fevereiro de 2020, o Application Gateway (todos os tipos SKU) irá injetar outro cookie chamado *ApplicationGatewayAffinityCORS,* além do cookie *applicationGatewayAffinity* existente. O cookie *ApplicationGatewayAffinityCORS* tem mais dois atributos adicionados a ele *("SameSite=Nenhum; Secure"*) para que a sessão pegajosa seja mantida mesmo para pedidos de origem cruzada.

Note que o nome de cookies de afinidade padrão é *ApplicationGatewayAffinity* e pode alterá-lo. Caso você esteja usando um nome de cookie de afinidade personalizado, um cookie adicional é adicionado com CORS como sufixo. Por exemplo, *CustomCookieNameCORS*.

> [!NOTE]
> Se o atributo *SameSite=Nenhum* estiver definido, é obrigatório que o cookie também contenha a bandeira *Secure,* e deve ser enviado em HTTPS.  Se for necessária afinidade da sessão sobre o CORS, deve migrar a sua carga de trabalho para HTTPS. Consulte a documentação TLS de descarregamento de TLS e TLS de ponta a ponta para o Gateway de Aplicações aqui – [Visão geral](ssl-overview.md), [Configure um gateway de aplicações com rescisão de TLS utilizando o portal Azure](create-ssl-portal.md), [Configurar TLS de ponta a ponta utilizando o Gateway de Aplicação com o portal](end-to-end-ssl-portal.md).

### <a name="connection-draining"></a>Drenagem de ligação

A drenagem de ligação ajuda-o a remover graciosamente os membros da piscina de fundo durante as atualizações de serviço planeadas. Pode aplicar esta definição a todos os membros de uma piscina traseira, permitindo a drenagem da ligação na definição HTTP. Garante que todos os casos de desregista de um pool back-end continuem a manter as ligações existentes e a servir pedidos em curso para um tempo limite configurável e não recebam quaisquer novos pedidos ou ligações. A única exceção a esta questão são os pedidos destinados a desregistar casos devido à afinidade da sessão gerida por gateways e continuarão a ser reencaminhados para as instâncias de desregistação. A drenagem de ligação aplica-se a casos de back-end que são explicitamente removidos da piscina traseira.

### <a name="protocol"></a>Protocolo

O Application Gateway suporta http e HTTPS para pedidos de encaminhamento para os servidores de back-end. Se escolher HTTP, o tráfego para os servidores de back-end não é encriptado. Se a comunicação não encriptada não for aceitável, escolha HTTPS.

Esta definição combinada com HTTPS no ouvinte suporta [TLS de ponta a ponta](ssl-overview.md). Isto permite-lhe transmitir de forma segura dados sensíveis encriptados para a parte de trás. Cada servidor de back-end no pool back-end que tenha OLS de ponta a ponta habilitado deve ser configurado com um certificado para permitir uma comunicação segura.

### <a name="port"></a>Porta

Esta definição especifica a porta onde os servidores de back-end ouvem o tráfego a partir do gateway de aplicações. Pode configurar portas que variam entre 1 e 65535.

### <a name="request-timeout"></a>Prazo de pedido

Esta definição é o número de segundos que o gateway de aplicação espera para receber uma resposta do servidor back-end.

### <a name="override-back-end-path"></a>Anular caminho de trás

Esta definição permite-lhe configurar um caminho de encaminhamento personalizado opcional para usar quando o pedido for reencaminhado para a parte de trás. Qualquer parte do caminho de entrada que corresponda ao caminho personalizado no campo do **caminho de reposição de substituição** é copiada para o caminho reencaminhado. A tabela a seguir mostra como esta funcionalidade funciona:

- Quando a definição HTTP é anexada a uma regra básica de encaminhamento de pedidos:

  | Pedido original  | Anular caminho de trás | Pedido reencaminhado para o final |
  | ----------------- | --------------------- | ---------------------------- |
  | /casa/            | /sobreposição/            | /sobreposição/casa/              |
  | /home/secondhome/ | /sobreposição/            | /sobreposição/casa/segunda casa/   |

- Quando a definição HTTP é anexada a uma regra de encaminhamento de pedidos baseada no caminho:

  | Pedido original           | Regra do caminho       | Anular caminho de trás | Pedido reencaminhado para o final |
  | -------------------------- | --------------- | --------------------- | ---------------------------- |
  | /pathrule/home/            | /regra da patula*      | /sobreposição/            | /sobreposição/casa/              |
  | /pathrule/home/secondhome/ | /regra da patula*      | /sobreposição/            | /sobreposição/casa/segunda casa/   |
  | /casa/                     | /regra da patula*      | /sobreposição/            | /sobreposição/casa/              |
  | /home/secondhome/          | /regra da patula*      | /sobreposição/            | /sobreposição/casa/segunda casa/   |
  | /pathrule/home/            | /pathrule/home* | /sobreposição/            | /sobreposição/                   |
  | /pathrule/home/secondhome/ | /pathrule/home* | /sobreposição/            | /sobreposição/secondhome/        |
  | /pathrule/                 | /pathrule/      | /sobreposição/            | /sobreposição/                   |

### <a name="use-for-app-service"></a>Uso para serviço de aplicações

Este é um atalho único da UI que seleciona as duas definições necessárias para o back end do Azure App Service. Permite **escolher o nome do anfitrião a partir do endereço back-end**, e cria uma nova sonda personalizada se ainda não tiver um. (Para mais informações, consulte o [nome do anfitrião Pick a partir da](#pick) secção de definição de endereço de fundo deste artigo.) Uma nova sonda é criada, e o cabeçalho da sonda é escolhido a partir do endereço do membro de trás.

### <a name="use-custom-probe"></a>Use sonda personalizada

Esta definição associa uma [sonda personalizada](application-gateway-probe-overview.md#custom-health-probe) a uma definição HTTP. Pode associar apenas uma sonda personalizada a uma definição HTTP. Se não associar explicitamente uma sonda personalizada, a [sonda padrão](application-gateway-probe-overview.md#default-health-probe-settings) é usada para monitorizar a saúde da parte de trás. Recomendamos que crie uma sonda personalizada para um maior controlo sobre a monitorização da saúde das suas extremidades traseiras.

> [!NOTE]
> A sonda personalizada não monitoriza a saúde da piscina traseira a menos que a definição HTTP correspondente esteja explicitamente associada a um ouvinte.

### <a name="pick-host-name-from-back-end-address"></a><a id="pick"/></a>Escolha o nome do anfitrião a partir do endereço back-end

Esta capacidade define dinamicamente o cabeçalho do *anfitrião* no pedido para o nome de anfitrião da piscina de back-end. Utiliza um endereço IP ou FQDN.

Esta funcionalidade ajuda quando o nome de domínio da parte de trás é diferente do nome DNS do gateway de aplicação, e a parte traseira depende de um cabeçalho de anfitrião específico para resolver o ponto final correto.

Um caso exemplo são os serviços de vários inquilinos como o back-end. Um serviço de aplicações é um serviço multi-inquilino que usa um espaço compartilhado com um único endereço IP. Assim, um serviço de aplicações só pode ser acedido através dos hostnames que são configurados nas definições de domínio personalizado.

Por predefinição, o nome de domínio personalizado é *example.azurewebsites.net*. Para aceder ao serviço da sua aplicação utilizando um gateway de aplicações através de um nome de anfitrião que não está explicitamente registado no serviço de aplicações ou através do FQDN do gateway de aplicações, substitui o nome de anfitrião no pedido original para o nome de anfitrião do serviço de aplicações. Para isso, ative o nome do anfitrião da definição **do endereço de backend.**

Para um domínio personalizado cujo nome DNS personalizado existente está mapeado para o serviço de aplicações, não precisa de ativar esta definição.

> [!NOTE]
> Esta definição não é necessária para o Ambiente de Serviço de Aplicações, que é uma implementação dedicada.

### <a name="host-name-override"></a>Sobreposição de nome de anfitrião

Esta capacidade substitui o cabeçalho do *anfitrião* no pedido de entrada no gateway de aplicações pelo nome de anfitrião que especifica.

Por exemplo, se *www.contoso.com* for especificado na definição do **nome anfitrião,** o pedido original * `https://appgw.eastus.cloudapp.azure.com/path1` é alterado para * quando o pedido é `https://www.contoso.com/path1` reencaminhado para o servidor de back-end.

## <a name="back-end-pool"></a>Conjunto back-end

Pode apontar uma piscina traseira para quatro tipos de membros backend: uma máquina virtual específica, um conjunto de escala de máquina virtual, um endereço IP/FQDN ou um serviço de aplicações. 

Depois de criar uma piscina traseira, deve associá-la a uma ou mais regras de encaminhamento de pedidos. Também deve configurar sondas de saúde para cada piscina traseira no seu gateway de aplicação. Quando uma condição de regra de encaminhamento de pedido é satisfeita, o gateway de aplicação encaminha o tráfego para os servidores saudáveis (conforme determinado pelas sondas de saúde) na piscina traseira correspondente.

## <a name="health-probes"></a>Sondas do estado de funcionamento

Uma porta de aplicação monitoriza a saúde de todos os recursos no seu final por defeito. Mas recomendamos vivamente que crie uma sonda personalizada para cada definição HTTP de back-end para obter um maior controlo sobre a monitorização da saúde. Para aprender a configurar uma sonda personalizada, consulte as [definições de sonda de saúde personalizada](application-gateway-probe-overview.md#custom-health-probe-settings).

> [!NOTE]
> Depois de criar uma sonda de saúde personalizada, tem de a associar a uma definição HTTP de fundo. Uma sonda personalizada não monitorizará a saúde da piscina traseira a menos que a definição HTTP correspondente esteja explicitamente associada a um ouvinte usando uma regra.

## <a name="next-steps"></a>Passos seguintes

Agora que sabe sobre os componentes do Application Gateway, pode:

- [Criar uma porta de entrada de aplicações no portal Azure](quick-create-portal.md)
- [Crie um gateway de aplicações utilizando o PowerShell](quick-create-powershell.md)
- [Criar um gateway de aplicações utilizando o Azure CLI](quick-create-cli.md)
