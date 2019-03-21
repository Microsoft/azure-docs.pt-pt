---
title: Componentes de Gateway de aplicação do Azure
description: Este artigo fornece informações sobre os vários componentes no Gateway de aplicação
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/20/2019
ms.author: absha
ms.openlocfilehash: 44c8b331ebb258c39a003c91e0711e6dfb87cb12
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58076303"
---
# <a name="application-gateway-components"></a>Componentes de gateway de aplicação

 Um gateway de aplicação serve como o único ponto de contacto para clientes. Distribui o tráfego das aplicações recebido em vários conjuntos de back-end, como as VMs do Azure, conjuntos de dimensionamento de máquinas virtuais, serviços de aplicações ou servidores no local/externo. Para tal, utiliza vários componentes descritos neste artigo.

![componentes de gateway de aplicação](./media/application-gateway-components/application-gateway-components.png)

## <a name="frontend-ip-address"></a>Endereço IP de front-end

Um endereço IP de front-end é o endereço IP associado com o gateway de aplicação. Pode configurar o gateway de aplicação para ter um endereço IP público ou um endereço IP privado ou ambos. Apenas um endereço IP ou um endereço IP privado é suportado. A rede virtual e o endereço IP público tem de ser na mesma localização que o gateway de aplicação.

Um endereço IP de front-end está associado a um *serviço de escuta* depois de criado. 

### <a name="static-vs-dynamic-public-ip-address"></a>Estático vs dinâmico endereço IP

O SKU de v1 suporta endereços IP internos estáticos, mas não suporta endereços IP públicos estáticos. O VIP pode alterar se o gateway de aplicação é parado e iniciado. O nome DNS associado com o gateway de aplicação não se altera ao longo do ciclo de vida do gateway. Por esse motivo, é recomendado utilizar um alias CNAME e apontá-lo para o endereço DNS do gateway de aplicação.

O SKU do Gateway de aplicação v2 suporta endereços IP públicos estáticos, bem como endereços IP internos estáticos. 

## <a name="listeners"></a>Ouvintes

Antes de começar a utilizar o gateway de aplicação, tem de adicionar um ou mais serviços de escuta. Um serviço de escuta é uma entidade lógica que verifica a existência de pedidos recebidos de ligação e aceita os pedidos, se o protocolo, porta, anfitrião e endereço IP associado a correspondência de pedido com o protocolo, porta, anfitrião e endereço IP associado a configuração do serviço de escuta. É possível que várias escutas anexadas a um gateway de aplicação e pode ser utilizados para o mesmo protocolo. Depois do serviço de escuta detecta solicitações de entrada dos clientes, o Gateway de aplicação encaminha estes pedidos para os membros no agrupamento de back-end, com as regras de encaminhamento de pedido por si para o serviço de escuta que recebeu o pedido de entrada.

Serviços de escuta suportam as seguintes portas e protocolos:

### <a name="ports"></a>Portas

Esta é a porta na qual o serviço de escuta faz a escuta para o pedido de cliente. Pode configurar as portas entre 1 e 65502 para a V1 SKU e 1 para 65199 para o SKU da V2.

### <a name="protocols"></a>Protocolos

Gateway de aplicação suporta os protocolos de quatro seguintes: HTTP, HTTPS, HTTP/2 e WebSocket

Especificar explicitamente a escolha entre os protocolos HTTP e HTTPS na configuração do serviço de escuta. O [suporte para protocolos WebSockets e HTTP/2](https://docs.microsoft.com/azure/application-gateway/overview#websocket-and-http2-traffic) é fornecida de forma nativa. [Suporte de Websocket](https://docs.microsoft.com/azure/application-gateway/application-gateway-websocket) está ativada por predefinição. Não existe qualquer definição configurável pelo utilizador para ativar ou desativar seletivamente o suporte de WebSocket. Pode utilizar WebSockets com serviços de escuta HTTP e HTTPS. Suporte de protocolo HTTP/2 está disponível para clientes que se conectam para ouvintes de gateway de aplicação apenas. A comunicação para agrupamentos de servidores de back-end é através de HTTP/1.1. Por predefinição, o suporte de HTTP/2 está desativado. Pode optar por ativá-la.

Pode usar um serviço de escuta HTTPS para a terminação de SSL. Um serviço de escuta HTTPS descarrega a criptografia e descriptografia de trabalho para o gateway de aplicação para que seus servidores web não são de encargo por essa sobrecarga. Seus aplicativos, em seguida, são livres para se concentrar na respetiva lógica de negócio.

### <a name="custom-error-pages"></a>Páginas de erro personalizadas

Gateway de aplicação permite-lhe criar páginas de erro personalizada em vez de apresentar as páginas de erro padrão. Pode utilizar a sua própria imagem e esquema corporativos através de uma página de erro personalizada. Gateway de aplicação pode apresentar uma página de erro personalizada quando um pedido não é possível alcançar o back-end. Para obter mais informações, consulte [páginas de erro personalizada para o Gateway de aplicação](https://docs.microsoft.com/azure/application-gateway/custom-error).

### <a name="types-of-listeners"></a>Tipos de serviços de escuta

Existem dois tipos de serviços de escuta:

- **Básico**: Este tipo de serviço de escuta de escuta a um site único domínio em que tenha um único mapeamento de DNS para endereço IP do gateway de aplicação. Esta configuração de serviço de escuta é necessária quando está a alojar um site único por trás de um gateway de aplicação.
- **Multilocal**: Esta configuração de serviço de escuta é necessária quando configura a mais do que uma aplicação web na mesma instância de gateway de aplicação. Isto permite-lhe configurar uma topologia mais eficiente para as implementações ao adicionar até 100 Web sites para um gateway de aplicação. Cada site pode ser direcionado para o seu próprio agrupamento de back-end. Por exemplo:  Para três subdomínios — abc.contoso.com, xyz.contoso.com e pqr.contoso.com apontando para o endereço IP do gateway de aplicação. Criar três serviços de escuta do tipo *multilocal* e configurar cada serviço de escuta para a respetiva porta e protocolo de configuração. Para obter mais informações, consulte [alojamento de vários sites](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview).

Depois de criar um serviço de escuta, associa-a com uma regra de encaminhamento de pedido que determina como o pedido recebido do serviço de escuta de deve ser encaminhado para o back-end.

Serviços de escuta são processados na ordem em que forem sendo apresentados. Por esse motivo, se uma solicitação de entrada corresponder a um serviço de escuta básico processa-lo primeiro. Serviços de escuta de múltiplos sites devem ser configurados antes de um serviço de escuta básico para garantir que o tráfego é encaminhado para o back-end correto.

## <a name="request-routing-rule"></a>Solicitar regra de encaminhamento

Este é o componente mais importante do gateway de aplicação e determina a forma como o tráfego no serviço de escuta associado a esta regra é encaminhado. A regra vincula o serviço de escuta, o conjunto de servidores de back-end e as definições de HTTP de back-end. Depois de um pedido é aceite por um serviço de escuta, a regra de encaminhamento do pedido determina se o pedido está a ser reencaminhados para o back-end ou redirecionado em outro lugar. Se o pedido é determinado para ser reencaminhado para o back-end, a regra de encaminhamento de pedido define para que conjunto de servidores de back-end devem ser reencaminhado para. Além disso, a regra de encaminhamento de pedido também determina se os cabeçalhos na solicitação devem ser reescrito. Um serviço de escuta pode ser anexado a apenas uma regra.

Pode haver dois tipos de regras de encaminhamento do pedido:

- **Básico:** Todos os pedidos no serviço de escuta associado (por exemplo: blog.contoso.com/*) são reencaminhados para o conjunto de back-end associado através da definição de HTTP associada.
- **Path-based:** Este tipo de regra permite-lhe encaminhar os pedidos no serviço de escuta associado a um conjunto de back-end específicas com base no URL no pedido. Se o caminho do URL num pedido corresponde ao padrão caminho numa regra baseado no caminho, o pedido é encaminhado com essa regra. O padrão de caminho é aplicado apenas para o caminho do URL, não para seus parâmetros de consulta. Se o caminho do URL de um pedido de um serviço de escuta de não corresponde a qualquer uma das regras com base no caminho, então o pedido é encaminhado para o *predefinido* conjunto back-end e o *predefinido* as definições de HTTP. Para obter mais informações, consulte [encaminhamento baseado em URL](https://docs.microsoft.com/azure/application-gateway/url-route-overview).

### <a name="redirection-support"></a>Suporte de redirecionamento

A regra de encaminhamento de pedido também permite-lhe redirecionar o tráfego no gateway de aplicação. Trata-se de um mecanismo de redirecionamento genérico, para que possa redirecionar de e para qualquer porta que define através de regras. Pode escolher o destino de redirecionamento para ser outro serviço de escuta (que pode ajudar a ativar automática redirecionamento de HTTP para HTTPS) ou um site externo, escolha o redirecionamento para ser temporária ou permanente ou escolha anexando a cadeia de caminho e a consulta do URI para o URL redirecionado. Para obter mais informações, consulte [redirecionar o tráfego no seu Gateway de aplicação](https://docs.microsoft.com/azure/application-gateway/redirect-overview).

#### <a name="rewrite-http-headers"></a>Rescrever cabeçalhos HTTP

Utilizar as regras de encaminhamento do pedido, pode adicionar, remover ou atualizar HTTP (S) do pedido e cabeçalhos de resposta enquanto o pedido e pacotes de resposta se mover entre os conjuntos de cliente e o back-end, através do gateway de aplicação. Os cabeçalhos não só podem ser definidos para valores estáticos, mas também para outros cabeçalhos e as variáveis de servidor importantes. Isto irá ajudá-lo a realizar vários casos de uso importante, como extrair o endereço IP dos clientes, remoção das informações confidenciais sobre o back-end, adicionar medidas de segurança adicionais, etc. Para obter mais informações, consulte [cabeçalhos de HTTP de reescrever no seu gateway de aplicação](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers).

## <a name="http-settings"></a>Definições de HTTP

As rotas de gateway de aplicação de tráfego para o back-end com o número da porta, protocolo de servidores (especificadas na regra de encaminhamento de pedido para o qual as definições de HTTP estão anexadas) e outras definições especificadas neste componente. A porta e protocolo utilizado nas definições de HTTP determinam se o tráfego entre os servidores de gateway e o back-end de aplicação é encriptado, assim a realização de SSL ponto a ponto ou não encriptada. Este componente também é utilizado para: determinar se uma sessão de utilizador é estarão no mesmo servidor, utilizando o [afinidade de sessão baseada em cookies](https://docs.microsoft.com/azure/application-gateway/overview#session-affinity), realizar a remoção de anulações normal de membros do agrupamento de back-end com [ligação a ser drenado](https://docs.microsoft.com/azure/application-gateway/overview#connection-draining), associe uma sonda personalizada para monitorizar o estado de funcionamento do back-end, definir o intervalo de tempo limite do pedido, substituir o nome de anfitrião e caminho no pedido e fornecer facilidade de um clique para especificar a definição de back-end para back-end de serviço de aplicações. 

## <a name="backend-pool"></a>Conjunto back-end

O conjunto de back-end é utilizado para encaminhar pedidos para os servidores de back-end, que irá atender à solicitação. Conjuntos de back-end podem ser compostos de NICs, a máquina virtual Dimensionar conjuntos, pública IP endereços, interno IP endereços, FQDN e multi-inquilino back-ends, como o serviço de aplicações do Azure. Membros do conjunto de back-end de gateway de aplicação não estão associados a um conjunto de disponibilidade. Gateway de aplicação pode comunicar com instâncias de fora da rede virtual que está a ser, por conseguinte, os membros dos agrupamentos de back-end podem ser em clusters, centros de dados, ou fora do Azure, desde que existe conectividade IP. Se planeja usar IPs interno como membros do agrupamento de back-end, em seguida, ele requer [VNET Peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) ou [Gateway de VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). O VNet peering é suportado e é benéfico para tráfego de balanceamento de carga nas outras redes virtuais. O gateway de aplicação também consegue comunicar com servidores no local quando eles são ligados através de túneis VPN ou ExpressRoute, desde que o tráfego é permitido.

Pode criar conjuntos de back-end diferente para diferentes tipos de pedidos. Por exemplo, crie um conjunto de back-end para pedidos gerais e outro conjunto de back-end para os pedidos para os microsserviços para a sua aplicação.

## <a name="health-probes"></a>Sondas do estado de funcionamento

Gateway de aplicação por predefinição monitoriza o estado de funcionamento de todos os recursos no seu conjunto de back-end e automaticamente remove qualquer recurso considerado em mau estado de funcionamento do conjunto. Ele continua a monitorizar as instâncias em mau estado de funcionamento e adiciona-os novamente para o pool de back-end em bom estado, uma vez que eles se tornarem disponíveis e respondem às sondas de estado de funcionamento. Além de utilizar a monitorização de sonda de estado de funcionamento do predefinido, também pode personalizar a sonda de estado de funcionamento para atender às necessidades da sua aplicação. As pesquisas personalizadas permitem que tenha um controle mais granular sobre a monitorização de estado de funcionamento. Ao utilizar sondas personalizadas, pode configurar o intervalo de pesquisa, o URL e caminho para testar e quantos respostas com falhas para aceitar antes da instância de conjunto de back-end está marcada como mau estado de funcionamento. É altamente recomendável que configure as pesquisas personalizadas para monitorizar o estado de funcionamento de cada conjunto de back-end. Para obter mais informações, consulte [monitorizar o estado de funcionamento do seu gateway de aplicação](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview).

## <a name="next-steps"></a>Passos Seguintes

Depois de saber sobre os componentes de Gateway de aplicação, pode:
* [Criar um Gateway de aplicação no portal do Azure](quick-create-portal.md)
* [Criar um Gateway de aplicação com o Azure PowerShell](quick-create-powershell.md)
* [Criar um Gateway de aplicação com a CLI do Azure](quick-create-cli.md)
