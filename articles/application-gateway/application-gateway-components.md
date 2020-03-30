---
title: Componentes de gateway de aplicação
description: Este artigo fornece informações sobre os vários componentes em um gateway de aplicação
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/20/2019
ms.author: absha
ms.openlocfilehash: 90b3c3fd18bc9211c731ccf16dd646a64a4a1116
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80133098"
---
# <a name="application-gateway-components"></a>Componentes de gateway de aplicação

 Um gateway de aplicação serve como o único ponto de contacto para os clientes. Distribui o tráfego de aplicações através de várias piscinas de backend, que incluem VMs Azure, conjuntos de escala de máquinavirtual, Serviço de Aplicações Azure e servidores externos no local. Para distribuir o tráfego, um gateway de aplicação utiliza vários componentes descritos neste artigo.

![Os componentes utilizados numa porta de aplicação](./media/application-gateway-components/application-gateway-components.png)

## <a name="frontend-ip-addresses"></a>Endereços IP frontend

Um endereço IP frontal é o endereço IP associado a um gateway de aplicação. Pode configurar um portal de aplicação para ter um endereço IP público, um endereço IP privado, ou ambos. Um gateway de aplicação suporta um endereço IP público ou um privado. A sua rede virtual e o endereço IP público devem estar no mesmo local que o portal da sua aplicação. Depois de criado, um endereço IP frontal está associado a um ouvinte.

### <a name="static-versus-dynamic-public-ip-address"></a>Endereço IP público estático versus dinâmico

O Portal de Aplicação Azure V2 SKU pode ser configurado para suportar tanto o endereço IP interno estático como o endereço IP público estático, ou apenas um endereço IP público estático. Não pode ser configurado para suportar apenas endereço IP interno estático.

O V1 SKU pode ser configurado para suportar endereço ip interno estático ou dinâmico e endereço IP público dinâmico. O endereço IP dinâmico do Gateway de aplicação não muda numa porta de entrada em execução. Só pode mudar quando paras ou começas o Gateway. Não altera falhas do sistema, atualizações, atualizações do anfitrião Do Azure, etc. 

O nome DNS associado a um gateway de aplicação não muda ao longo do ciclo de vida do portal. Como resultado, deve utilizar um pseudónimo CNAME e apontá-lo para o endereço DNS do gateway da aplicação.

## <a name="listeners"></a>Serviços de escuta

Um ouvinte é uma entidade lógica que verifica os pedidos de ligação. Um ouvinte aceita um pedido se o protocolo, porta, nome de anfitrião e endereço IP associado sofram os mesmos elementos associados à configuração do ouvinte.

Antes de utilizar um portal de aplicação, deve adicionar pelo menos um ouvinte. Pode haver vários ouvintes ligados a um portal de aplicação, e podem ser usados para o mesmo protocolo.

Depois de um ouvinte detetar pedidos de entrada de clientes, o gateway de aplicação encaminha estes pedidos para os membros no pool backend configurado na regra.

Os ouvintes apoiam as seguintes portas e protocolos.

### <a name="ports"></a>Portas

Um porto é onde um ouvinte ouve o pedido do cliente. Pode configurar portas que variam de 1 a 65502 para o V1 SKU e 1 a 65199 para o V2 SKU.

### <a name="protocols"></a>Protocolos

O Application Gateway suporta quatro protocolos: HTTP, HTTPS, HTTP/2 e WebSocket:
>[!NOTE]
>O suporte do protocolo HTTP/2 está disponível apenas para clientes que se ligam aos ouvintes de gateway de aplicação. A comunicação para backend server pools é sempre sobre HTTP/1.1. Por defeito, o suporte HTTP/2 é desativado. Pode optar por o ativar.

- Especifique entre os protocolos HTTP e HTTPS na configuração do ouvinte.
- O suporte para [WebSockets e protocolos HTTP/2](features.md#websocket-and-http2-traffic) é fornecido de forma nativa, e o [suporte WebSocket](application-gateway-websocket.md) é ativado por padrão. Não existe qualquer definição configurável pelo utilizador para ativar ou desativar seletivamente o suporte de WebSocket. Utilize WebSockets com os ouvintes HTTP e HTTPS.

Utilize um ouvinte HTTPS para a rescisão de TLS. Um ouvinte HTTPS descarrega o trabalho de encriptação e desencriptação para o seu portal de aplicações, para que os seus servidores web não sejam sobrecarregados com a sobrecarga.

### <a name="custom-error-pages"></a>Páginas de erros personalizadas

O Gateway de aplicação permite-lhe criar páginas de erro personalizadas em vez de apresentar páginas de erro predefinidas. Pode utilizar a sua própria imagem e esquema corporativos através de uma página de erro personalizada. O Application Gateway apresenta uma página de erro personalizada quando um pedido não consegue chegar ao backend.

Para mais informações, consulte as páginas de [erro personalizadas para o seu gateway](custom-error.md)de aplicação .

### <a name="types-of-listeners"></a>Tipos de ouvintes

Há dois tipos de ouvintes:

- **Básico.** Este tipo de ouvinte ouve um único site de domínio, onde tem um único mapeamento dNS para o endereço IP do gateway da aplicação. Esta configuração do ouvinte é necessária quando hospeda um único site atrás de um gateway de aplicação.

- **Multi-site**. Esta configuração do ouvinte é necessária quando configura mais de uma aplicação web na mesma instância de gateway de aplicação. Permite-lhe configurar uma topologia mais eficiente para as suas implementações, adicionando até 100 websites a um portal de aplicações. Cada site pode ser direcionado para o seu próprio agrupamento de back-end. Por exemplo, três subdomínios, abc.contoso.com, xyz.contoso.com e pqr.contoso.com, apontam para o endereço IP do gateway da aplicação. Criaria três ouvintes multi-sites e configurava cada ouvinte para a respetiva definição de porta e protocolo.

    Para mais informações, consulte o [alojamento em vários locais.](application-gateway-web-app-overview.md)

Depois de criar um ouvinte, associa-o a uma regra de encaminhamento de pedidos. Esta regra determina como o pedido recebido no ouvinte deve ser encaminhado para o backend.

Application Gateway processa ouvintes na [ordem mostrada](configuration-overview.md#order-of-processing-listeners).

## <a name="request-routing-rules"></a>Solicitar regras de encaminhamento

Uma regra de encaminhamento de pedidos é um componente chave de um gateway de aplicação porque determina como encaminhar o tráfego no ouvinte. A regra liga o ouvinte, o conjunto de servidores de back-end e as definições DE HTTP de backend.

Quando um ouvinte aceita um pedido, a regra de encaminhamento de pedidos reencaminha o pedido para o backend ou redireciona-o para outro lado. Se o pedido for reencaminhado para o backend, a regra de encaminhamento de pedidos define para que reserva o conjunto de servidores para encaminhar. A regra de encaminhamento do pedido também determina se os cabeçalhos do pedido devem ser reescritos. Um ouvinte pode ser ligado a uma regra.

Existem dois tipos de regras de encaminhamento de pedidos:

- **Básico.** Todos os pedidos sobre o ouvinte associado (por exemplo, blog.contoso.com/*) são encaminhados para a piscina de backend associada utilizando a definição http associada.

- **Baseado no caminho.** Esta regra de encaminhamento permite-lhe encaminhar os pedidos no ouvinte associado para uma piscina específica de backend, com base no URL no pedido. Se o caminho do URL num pedido corresponder ao padrão de caminho numa regra baseada no caminho, as rotas de regra que solicitam. Aplica o padrão do caminho apenas ao caminho do URL, não aos seus parâmetros de consulta. Se o caminho de URL num pedido de ouvinte não corresponder a nenhuma das regras baseadas no caminho, encaminha o pedido para o pool de backend padrão e definições HTTP.

Para mais informações, consulte o [encaminhamento baseado em URL](url-route-overview.md).

### <a name="redirection-support"></a>Suporte de redirecionamento

A regra de encaminhamento de pedidos também permite redirecionar o tráfego no gateway da aplicação. Este é um mecanismo genérico de reorientação, para que possa redirecionar de e para qualquer porta que defina usando regras.

Pode escolher o alvo de reorientação para ser outro ouvinte (que pode ajudar a ativar a reorientação automática http para HTTPS) ou um site externo. Também pode optar por que a reorientação seja temporária ou permanente, ou anexar o caminho URI e consultar a cadeia de consulta ao URL redirecionado.

Para mais informações, consulte [Redirecionamento de tráfego na sua porta](redirect-overview.md)de entrada de aplicações .

### <a name="rewrite-http-headers"></a>Rescrever cabeçalhos HTTP

Ao utilizar as regras de encaminhamento de pedidos, pode adicionar, remover ou atualizar os cabeçalhos de pedido e resposta HTTP(S) à medida que os pacotes de pedido e resposta se movem entre o cliente e os pools de backend através do gateway da aplicação.

Os cabeçalhos podem ser definidos para valores estáticos ou para outros cabeçalhos e variáveis do servidor. Isto ajuda com casos de uso importantes, como extrair endereços IP do cliente, remover informações sensíveis sobre o backend, adicionar mais segurança, e assim por diante.

Para mais informações, consulte [os cabeçalhos http da reescrita na sua porta](rewrite-http-headers.md)de entrada de aplicação .

## <a name="http-settings"></a>Definições de HTTP

Um gateway de aplicação encaminha o tráfego para os servidores backend (especificado na regra de encaminhamento de pedidos que incluem as definições http) utilizando o número de porta, protocolo e outras definições detalhadas neste componente.

A porta e o protocolo utilizados nas definições http determinam se o tráfego entre o gateway da aplicação e os servidores backend está encriptado (fornecendo TLS de ponta a ponta) ou não encriptado.

Este componente também é utilizado para:

- Determine se uma sessão de utilizador deve ser mantida no mesmo servidor utilizando a [afinidade da sessão baseada em cookies](features.md#session-affinity).

- Retire graciosamente os membros da piscina de backend utilizando [a drenagem de ligação](features.md#connection-draining).

- Associe uma sonda personalizada para monitorizar a saúde do backend, detete o intervalo de tempo de tempo de pedido, sobrepor o nome e o caminho do anfitrião no pedido e fornecer facilidade de um clique para especificar as definições para o backend do Serviço de Aplicações.

## <a name="backend-pools"></a>Piscinas de backend

Um pedido de rotas de reserva para backend servidores, que servem o pedido. As piscinas de backend podem conter:

- NICs
- Conjuntos de dimensionamento de máquinas virtuais
- Endereços IP públicos
- Endereços IP internos
- FQDN
- Backends multiarrendatários (como o Serviço de Aplicações)

Os membros do pool de backend do Gateway de aplicação não estão ligados a um conjunto de disponibilidade. Um portal de aplicações pode comunicar com instâncias fora da rede virtual em que está. Como resultado, os membros das piscinas de backend podem estar em conjunto sinuosos, através de datacenters, ou fora do Azure, desde que haja conectividade IP.

Se utilizar os IPs internos como membros do pool backend, deve utilizar o [peering de rede virtual](../virtual-network/virtual-network-peering-overview.md) ou um [gateway VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). O epeering de rede virtual é suportado e benéfico para o tráfego de equilíbrio de carga em outras redes virtuais.

Um portal de aplicações também pode comunicar com servidores no local quando estão ligados por túneis Azure ExpressRoute ou VPN se o tráfego for permitido.

Você pode criar diferentes piscinas de backend para diferentes tipos de pedidos. Por exemplo, crie um pool de backend para pedidos gerais, e depois outra reserva de backend para pedidos aos microserviços para a sua aplicação.

## <a name="health-probes"></a>Sondas do estado de funcionamento

Por padrão, um gateway de aplicação monitoriza a saúde de todos os recursos na sua piscina de backend e remove automaticamente os não saudáveis. Em seguida, monitoriza casos pouco saudáveis e adiciona-os de volta à piscina saudável de backend quando ficam disponíveis e respondem a sondas de saúde.

Além de utilizar a monitorização da sonda de saúde padrão, também pode personalizar a sonda de saúde de acordo com os requisitos da sua aplicação. As sondas personalizadas permitem um maior controlo granular sobre a monitorização da saúde. Ao utilizar sondas personalizadas, pode configurar o intervalo da sonda, o URL e o caminho para testar, e quantas respostas falhadas aceitar antes da instância da piscina de backend é marcada como insalubre. Recomendamos que configure sondas personalizadas para monitorizar a saúde de cada piscina de backend.

Para mais informações, consulte [Monitorize a saúde da sua porta de entrada de aplicação](../application-gateway/application-gateway-probe-overview.md).

## <a name="next-steps"></a>Passos seguintes

Criar um portal de aplicação:

* [No portal Azure](quick-create-portal.md)
* [Ao utilizar o Azure PowerShell](quick-create-powershell.md)
* [Ao utilizar o Azure CLI](quick-create-cli.md)
