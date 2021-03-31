---
title: Componentes do Gateway de Aplicação
description: Este artigo fornece informações sobre os vários componentes em um gateway de aplicação
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: conceptual
ms.date: 08/21/2020
ms.author: surmb
ms.openlocfilehash: ebd06b0b78ee511dce535ff4220df03087fb6906
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "88723321"
---
# <a name="application-gateway-components"></a>Componentes do Gateway de Aplicação

 Um gateway de aplicação serve como único ponto de contacto para os clientes. Distribui o tráfego de aplicações de entrada em vários pools de backend, que incluem VMs Azure, conjuntos de escala de máquinas virtuais, Serviço de Aplicações Azure e servidores externos no local. Para distribuir tráfego, um gateway de aplicações utiliza vários componentes descritos neste artigo.

![Os componentes utilizados num gateway de aplicação](./media/application-gateway-components/application-gateway-components.png)

## <a name="frontend-ip-addresses"></a>Endereços IP frontend

Um endereço IP frontend é o endereço IP associado a um gateway de aplicação. Pode configurar uma porta de aplicação para ter um endereço IP público, um endereço IP privado ou ambos. Uma porta de aplicação suporta um endereço IP público ou privado. A sua rede virtual e o endereço IP público devem estar no mesmo local que o seu gateway de aplicações. Depois de criado, um endereço IP frontend é associado a um ouvinte.

### <a name="static-versus-dynamic-public-ip-address"></a>Endereço IP público estático versus dinâmico

O Azure Application Gateway V2 SKU pode ser configurado para suportar tanto o endereço IP interno estático como o endereço IP público estático, ou apenas o endereço IP público estático. Não pode ser configurado para suportar apenas um endereço IP interno estático.

O V1 SKU pode ser configurado para suportar endereço IP interno estático ou dinâmico e endereço IP público dinâmico. O endereço IP dinâmico do Gateway de Aplicação não muda num gateway em funcionamento. Só pode mudar quando paras ou iniciares o Gateway. Não altera falhas do sistema, atualizações, atualizações do anfitrião Azure, etc. 

O nome DNS associado a uma porta de aplicação não muda ao longo do ciclo de vida do gateway. Como resultado, deve utilizar um pseudónimo CNAME e apontá-lo para o endereço DNS do gateway de aplicação.

## <a name="listeners"></a>Serviços de escuta

Um ouvinte é uma entidade lógica que verifica os pedidos de ligação recebidas. Um ouvinte aceita um pedido se o protocolo, porta, nome de anfitrião e endereço IP associado ao pedido corresponderem aos mesmos elementos associados à configuração do ouvinte.

Antes de utilizar um gateway de aplicação, deve adicionar pelo menos um ouvinte. Pode haver vários ouvintes ligados a um gateway de aplicação, e podem ser usados para o mesmo protocolo.

Depois de um ouvinte detetar pedidos de entrada de clientes, o gateway de aplicação encaminha estes pedidos para os membros na piscina de backend configurado na regra.

Os ouvintes apoiam as seguintes portas e protocolos.

### <a name="ports"></a>Portas

Um porto é onde um ouvinte ouve o pedido do cliente. Pode configurar portas que variam de 1 a 65502 para o V1 SKU e 1 a 65199 para o v2 SKU.

### <a name="protocols"></a>Protocolos

O Application Gateway suporta quatro protocolos: HTTP, HTTPS, HTTP/2 e WebSocket:
>[!NOTE]
>O suporte ao protocolo HTTP/2 está disponível para clientes que se conectam apenas aos ouvintes do gateway de aplicações. A comunicação para backend pools de servidores está sempre acima de HTTP/1.1. Por predefinição, o suporte HTTP/2 é desativado. Pode escolher ative-lo.

- Especificar entre os protocolos HTTP e HTTPS na configuração do ouvinte.
- O suporte para [webSockets e protocolos HTTP/2](features.md#websocket-and-http2-traffic) é fornecido de forma nativa, e [o suporte webSocket](application-gateway-websocket.md) é ativado por padrão. Não existe qualquer definição configurável pelo utilizador para ativar ou desativar seletivamente o suporte de WebSocket. Utilize webSockets com ouvintes HTTP e HTTPS.

Utilize um ouvinte HTTPS para a rescisão de TLS. Um ouvinte HTTPS descarrega o trabalho de encriptação e desencriptação para o seu gateway de aplicações, para que os seus servidores web não sejam sobrecarregados pela sobrecarga.

### <a name="custom-error-pages"></a>Páginas de erro personalizadas

O Application Gateway permite criar páginas de erro personalizadas em vez de apresentar páginas de erro predefinidos. Pode utilizar a sua própria imagem e esquema corporativos através de uma página de erro personalizada. O Application Gateway exibe uma página de erro personalizada quando um pedido não consegue chegar ao backend.

Para obter mais informações, consulte [as páginas de erro personalizadas para o seu gateway de aplicações.](custom-error.md)

### <a name="types-of-listeners"></a>Tipos de ouvintes

Há dois tipos de ouvintes:

- **Básico**. Este tipo de ouvinte ouve um único site de domínio, onde tem um único mapeamento dns para o endereço IP do gateway de aplicações. Esta configuração do ouvinte é necessária quando hospeda um único site atrás de um gateway de aplicações.

- **Multi-site**. Esta configuração do ouvinte é necessária quando pretende configurar o encaminhamento com base no nome do anfitrião ou no nome de domínio para mais de uma aplicação web no mesmo gateway de aplicações. Permite-lhe configurar uma topologia mais eficiente para as implementações ao adicionar até 100 sites a um gateway de aplicação. Cada site pode ser direcionado para o seu próprio agrupamento de back-end. Por exemplo, três domínios, contoso.com, fabrikam.com e adatum.com, apontam para o endereço IP do gateway de aplicação. Criaria três [ouvintes multi-locais](multiple-site-overview.md) e configuraria cada ouvinte para a respetiva definição de porta e protocolo. 

    Além disso, pode definir nomes de anfitrião de caráter universal num serviço de escuta com vários sites e até cinco nomes de anfitrião por serviço de escuta. Para saber mais, consulte [os nomes dos anfitriões wildcard no ouvinte (pré-visualização)](multiple-site-overview.md#wildcard-host-names-in-listener-preview).

    Para obter mais informações sobre como configurar um ouvinte multi-site, consulte [o alojamento em vários locais em Application Gateway utilizando o portal Azure](create-multiple-sites-portal.md).

Depois de criar um ouvinte, associá-lo a uma regra de encaminhamento de pedidos. Esta regra determina como o pedido recebido no ouvinte deve ser encaminhado para o backend. A regra de encaminhamento de pedidos também contém o pool de backend a ser encaminhado para e a definição HTTP onde a porta de backend, protocolo, etc. são mencionados.

## <a name="request-routing-rules"></a>Pedir regras de encaminhamento

Uma regra de encaminhamento de pedidos é um componente chave de um gateway de aplicação porque determina como encaminhar o tráfego no ouvinte. A regra liga o ouvinte, a piscina do servidor de back-end e as definições HTTP backend.

Quando um ouvinte aceita um pedido, a regra de encaminhamento de pedido remete o pedido para o backend ou redireciona-o para outro lugar. Se o pedido for reencaminhado para o backend, a regra de encaminhamento de pedido define qual o conjunto do servidor de backend para encaminhá-lo. A regra de encaminhamento do pedido também determina se os cabeçalhos do pedido devem ser reescritos. Um ouvinte pode ser ligado a uma regra.

Existem dois tipos de regras de encaminhamento de pedidos:

- **Básico**. Todos os pedidos no ouvinte associado (por exemplo, blog.contoso.com/*) são reencaminhados para o pool de backend associado utilizando a definição HTTP associada.

- **Baseado em caminhos.** Esta regra de encaminhamento permite-lhe encaminhar os pedidos no ouvinte associado para um pool de backend específico, com base no URL no pedido. Se o caminho do URL num pedido corresponder ao padrão do caminho numa regra baseada em caminhos, a regra encaminha esse pedido. Aplica o padrão do caminho apenas ao caminho url, não aos seus parâmetros de consulta. Se o caminho URL de um pedido de ouvinte não corresponder a nenhuma das regras baseadas no caminho, ele encaminha o pedido para as definições de backend e HTTP predefinidos.

Para obter mais informações, consulte [o encaminhamento baseado em URL](url-route-overview.md).

### <a name="redirection-support"></a>Suporte de redirecionamento

A regra de encaminhamento de pedido também permite redirecionar o tráfego no gateway de aplicação. Este é um mecanismo de redirecionamento genérico, para que possa redirecionar de e para qualquer porta que definir usando regras.

Pode escolher o alvo de redirecionamento para ser outro ouvinte (o que pode ajudar a permitir a reorientação automática https para HTTPS) ou um site externo. Também pode optar por que a reorientação seja temporária ou permanente, ou para anexar o caminho URI e a cadeia de consulta para o URL redirecionado.

Para obter mais informações, consulte [redirecionar o tráfego no seu gateway de aplicações.](redirect-overview.md)

### <a name="rewrite-http-headers-and-url"></a>Rescrever cabeçalhos HTTP e URL

Ao utilizar regras de reescrita, pode adicionar, remover ou atualizar os cabeçalhos de pedido e resposta HTTP(S), bem como os parâmetros de trajetória e de consulta de URL, à medida que os pacotes de pedido e resposta se movem entre o cliente e as piscinas de backend através do gateway de aplicação.

Os cabeçalhos e parâmetros URL podem ser definidos para valores estáticos ou para outros cabeçalhos e variáveis do servidor. Isto ajuda com casos importantes de utilização, tais como a extração de endereços IP do cliente, a remoção de informações sensíveis sobre o backend, adicionando mais segurança, e assim por diante.

Para obter mais informações, consulte [os cabeçalhos HTTP e URL da sua aplicação gateway](rewrite-http-headers-url.md).

## <a name="http-settings"></a>Definições de HTTP

Um gateway de aplicação encaminha o tráfego para os servidores de backend (especificado na regra de encaminhamento de pedidos que incluem as definições HTTP) utilizando o número de porta, o protocolo e outras definições detalhadas neste componente.

A porta e o protocolo utilizados nas definições HTTP determinam se o tráfego entre o gateway de aplicações e os servidores de backend está encriptado (fornecendo TLS de ponta a ponta) ou não encriptado.

Este componente também é utilizado para:

- Determine se uma sessão de utilizador deve ser mantida no mesmo servidor utilizando a [afinidade da sessão baseada em cookies](features.md#session-affinity).

- Retire graciosamente os membros da piscina de backend utilizando [a drenagem de ligação](features.md#connection-draining).

- Associar uma sonda personalizada para monitorizar a saúde do backend, definir o intervalo de tempo de pedido, substituir o nome e o caminho do anfitrião no pedido, e fornecer uma facilidade de um clique para especificar as definições para o backend do Serviço de Aplicações.

## <a name="backend-pools"></a>Piscinas de backend

Um pedido de rotas de backend pool para backend servidores, que servem o pedido. As piscinas de backend podem conter:

- NICs
- Conjuntos de dimensionamento de máquinas virtuais
- Endereços IP públicos
- Endereços IP internos
- FQDN
- Backends multitenant (como o Serviço de Aplicações)

Os membros do backend do Gateway de aplicação não estão ligados a um conjunto de disponibilidade. Um gateway de aplicações pode comunicar com casos fora da rede virtual em que está. Como resultado, os membros dos pools backend podem ser através de clusters, através de datacenters, ou fora de Azure, desde que haja conectividade IP.

Se utilizar os IPs internos como membros do pool backend, deve utilizar [o espreitamento de rede virtual](../virtual-network/virtual-network-peering-overview.md) ou um gateway [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). O espreitamento de rede virtual é suportado e benéfico para o tráfego de equilíbrio de carga noutras redes virtuais.

Um gateway de aplicações também pode comunicar com servidores no local quando estão ligados por túneis Azure ExpressRoute ou VPN se o tráfego for permitido.

Você pode criar diferentes piscinas de backend para diferentes tipos de pedidos. Por exemplo, crie um pool de backend para pedidos gerais e, em seguida, outro backend pool para pedidos aos microserviços para a sua aplicação.

## <a name="health-probes"></a>Sondas do estado de funcionamento

Por padrão, uma porta de aplicação monitoriza a saúde de todos os recursos na sua piscina de backend e remove automaticamente os pouco saudáveis. Em seguida, monitoriza casos insalubres e adiciona-os de volta à piscina saudável quando ficam disponíveis e respondem a sondas de saúde.

Além de utilizar a monitorização da sonda de saúde padrão, também pode personalizar a sonda de saúde de acordo com os requisitos da sua aplicação. As sondas personalizadas permitem um maior controlo granular sobre a monitorização da saúde. Ao utilizar sondas personalizadas, pode configurar um nome de anfitrião personalizado, caminho URL, intervalo de sonda, e quantas respostas falhadas a aceitar antes de marcar a instância da piscina de back-end como insalubre, códigos de estado personalizados e correspondência corporal de resposta, etc. Recomendamos que configuure sondas personalizadas para monitorizar a saúde de cada piscina de backend.

Para mais informações, consulte [Monitorar a saúde do seu gateway de aplicação.](../application-gateway/application-gateway-probe-overview.md)

## <a name="next-steps"></a>Passos seguintes

Criar um gateway de aplicações:

* [No portal Azure](quick-create-portal.md)
* [Usando a Azure PowerShell](quick-create-powershell.md)
* [Usando o CLI Azure](quick-create-cli.md)
