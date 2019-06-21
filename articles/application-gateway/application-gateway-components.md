---
title: Componentes de gateway de aplicação
description: Este artigo fornece informações sobre os vários componentes num gateway de aplicação
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/20/2019
ms.author: absha
ms.openlocfilehash: 49b6b49d908a7426e7cfd1bae5260ff399d9953b
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67273228"
---
# <a name="application-gateway-components"></a>Componentes de gateway de aplicação

 Um gateway de aplicação serve como o único ponto de contacto para clientes. Distribui o tráfego das aplicações recebido em vários conjuntos de back-end, que incluem as VMs do Azure, conjuntos de dimensionamento de máquina virtual, o serviço de aplicações do Azure e servidores no local/externo. Para distribuir o tráfego, um gateway de aplicação utiliza vários componentes descritos neste artigo.

![Os componentes utilizados num gateway de aplicação](./media/application-gateway-components/application-gateway-components.png)

## <a name="frontend-ip-addresses"></a>Endereços IP de front-end

Um endereço IP de front-end é o endereço IP associado um gateway de aplicação. Pode configurar um gateway de aplicação para ter um endereço IP público, um endereço IP privado ou ambos. Um gateway de aplicação suporta um público ou um endereço IP privado. A rede virtual e o endereço IP público tem de ser na mesma localização que o gateway de aplicação. Depois de criado, um endereço IP de front-end está associado um serviço de escuta.

### <a name="static-versus-dynamic-public-ip-address"></a>Estática versus o endereço IP público dinâmico

O SKU de V2 do Gateway de aplicação do Azure pode ser configurado para suportar o tanto interno endereços IP estáticos e endereço IP público estático ou apenas endereço IP público estático. Ele não pode ser configurado para suportar apenas interno endereço IP estático.

O SKU de V1 pode ser configurado para suportar o endereço IP estático interno e o endereço IP público dinâmico, apenas interno endereços IP estáticos, ou apenas endereço IP público dinâmico. O endereço IP dinâmico do Gateway de aplicação não é alterada num gateway em execução. Ele pode alterar apenas quando parar ou iniciar o Gateway. Não é alterada em falhas do sistema, atualizações, anfitrião do Azure atualizações etc. 

O nome DNS associado um gateway de aplicação não é alterado ao longo do ciclo de vida do gateway. Como resultado, deve utilizar um alias CNAME e apontá-lo para o endereço DNS do gateway de aplicação.

## <a name="listeners"></a>Ouvintes

Um serviço de escuta é uma entidade lógica que verifica a existência de pedidos recebidos de ligação. Um serviço de escuta aceita um pedido se o protocolo, porta, anfitrião e endereço IP associados à solicitação corresponderem os mesmos elementos associados com a configuração do serviço de escuta.

Antes de utilizar um gateway de aplicação, tem de adicionar pelo menos um serviço de escuta. É possível que várias escutas anexadas a um gateway de aplicação e pode ser utilizados para o mesmo protocolo.

Depois de um serviço de escuta Deteta os pedidos recebidos de clientes, o gateway de aplicação encaminha estes pedidos para membros no agrupamento de back-end. O gateway de aplicação utiliza as regras de encaminhamento de pedido definidas para o serviço de escuta que recebeu o pedido de entrada.

Serviços de escuta suportam as seguintes portas e protocolos.

### <a name="ports"></a>Portas

Uma porta está em que um serviço de escuta para o pedido de cliente. Pode configurar as portas entre 1 e 65502 dos SKUS v1 e 1 para 65199 para o SKU da v2.

### <a name="protocols"></a>Protocolos

Gateway de aplicação suporta quatro protocolos: HTTP, HTTPS, HTTP/2 e WebSocket:

- Especifica entre o HTTP e HTTPS protocolos na configuração do serviço de escuta.
- Suporte para [protocolos WebSockets e HTTP/2](https://docs.microsoft.com/azure/application-gateway/overview#websocket-and-http2-traffic) é fornecida de forma nativa, e [suporte de WebSocket](https://docs.microsoft.com/azure/application-gateway/application-gateway-websocket) está ativada por predefinição. Não existe qualquer definição configurável pelo utilizador para ativar ou desativar seletivamente o suporte de WebSocket. Utilize WebSockets com serviços de escuta HTTP e HTTPS.
- Suporte de protocolo HTTP/2 está disponível para clientes que se conectam para ouvintes de gateway de aplicação apenas. A comunicação para agrupamentos de servidores de back-end é através de HTTP/1.1. Por predefinição, o suporte de HTTP/2 está desativado. Pode optar por ativá-la.

Utilize um serviço de escuta HTTPS para a terminação de SSL. Um serviço de escuta HTTPS tira a carga criptografia e descriptografia para o gateway de aplicação, para que seus servidores web não são de encargo por sobrecarga. As suas aplicações, em seguida, são livres para se concentrar na lógica de negócios.

### <a name="custom-error-pages"></a>Páginas de erro personalizadas

Gateway de aplicação permite-lhe criar páginas de erro personalizada em vez de apresentar as páginas de erro padrão. Pode utilizar a sua própria imagem e esquema corporativos através de uma página de erro personalizada. Gateway de aplicação apresenta uma página de erro personalizada quando um pedido não é possível alcançar o back-end.

Para obter mais informações, consulte [páginas de erro personalizada para o gateway de aplicação](https://docs.microsoft.com/azure/application-gateway/custom-error).

### <a name="types-of-listeners"></a>Tipos de serviços de escuta

Existem dois tipos de serviços de escuta:

- **Básico**. Este tipo de serviço de escuta de escuta a um site de domínio único, onde tem um único mapeamento de DNS para o endereço IP do gateway de aplicação. Esta configuração de serviço de escuta é necessária ao hospedar um único site por trás de um gateway de aplicação.

- **Multilocal**. Esta configuração de serviço de escuta é necessária quando configura a mais do que uma aplicação web na mesma instância de gateway de aplicação. Permite-lhe configurar uma topologia mais eficiente para as implementações ao adicionar até 100 Web sites para um gateway de aplicação. Cada site pode ser direcionado para o seu próprio agrupamento de back-end. Por exemplo, três subdomínios, abc.contoso.com, xyz.contoso.com e pqr.contoso.com, apontam para o endereço IP do gateway de aplicação. Poderia criar três serviços de escuta de múltiplos sites e configurar cada serviço de escuta para a respetiva porta e a definição do protocolo.

    Para obter mais informações, consulte [alojamento de múltiplos sites](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview).

Depois de criar um serviço de escuta, associa-a com uma regra de encaminhamento do pedido. Esta regra determina como o pedido recebido do serviço de escuta de deve ser encaminhado para o back-end.

Gateway de aplicação processa os serviços de escuta pela ordem apresentada. Se o serviço de escuta básico corresponde a uma solicitação de entrada, é processada pela primeira vez. Para encaminhar o tráfego para o back-end correto, configure um serviço de escuta de múltiplos site antes de um serviço de escuta básico.

## <a name="request-routing-rules"></a>Regras de encaminhamento de pedidos

Uma regra de encaminhamento de pedido é um componente fundamental de um gateway de aplicação pois ela determina como rotear tráfego sobre o serviço de escuta. A regra vincula o serviço de escuta, o conjunto de servidores de back-end e as definições de HTTP de back-end.

Quando um serviço de escuta aceita um pedido, a regra de encaminhamento de pedido reencaminha o pedido para o back-end ou redireciona-o em outro lugar. Se a solicitação é encaminhada para o back-end, a regra de encaminhamento de pedido define que conjunto de servidores de back-end reencaminhá-lo para. Além disso, a regra de encaminhamento de pedido também determina se os cabeçalhos na solicitação devem ser reescrito. Um serviço de escuta pode ser anexado a uma regra.

Existem dois tipos de regras de encaminhamento do pedido:

- **Básico**. Todos os pedidos no serviço de escuta associado (por exemplo, blog.contoso.com/*) são reencaminhados para o conjunto de back-end associados, utilize a definição de HTTP associada.

- **Baseado no caminho**. Esta regra de encaminhamento permite encaminhar os pedidos no serviço de escuta associado a um conjunto de back-end específico, com base no URL no pedido. Se o caminho do URL num pedido corresponde ao padrão caminho numa regra baseado no caminho, a regra encaminha essa solicitação. Aplica-se o padrão de caminho apenas o caminho de URL, não para seus parâmetros de consulta. Se o caminho do URL num pedido de serviço de escuta não corresponde a qualquer uma das regras com base no caminho, encaminha o pedido para o conjunto de back-end predefinido e as definições de HTTP.

Para obter mais informações, consulte [encaminhamento baseado em URL](https://docs.microsoft.com/azure/application-gateway/url-route-overview).

### <a name="redirection-support"></a>Suporte de redirecionamento

A regra de encaminhamento de pedido também permite-lhe redirecionar o tráfego no gateway de aplicação. Este é um mecanismo de redirecionamento genérico, para que pode redirecionar para e a partir de qualquer porta definir através de regras.

Pode escolher o destino de redirecionamento para ser outro serviço de escuta (que pode ajudar a ativar automática redirecionamento de HTTP para HTTPS) ou um site externo. Também é possível ter o redirecionamento ser temporária ou permanente, ou anexe a cadeia de caminho e a consulta do URI para o URL redirecionado.

Para obter mais informações, consulte [redirecionar o tráfego no seu gateway de aplicação](https://docs.microsoft.com/azure/application-gateway/redirect-overview).

### <a name="rewrite-http-headers"></a>Rescrever cabeçalhos HTTP

Ao utilizar as regras de encaminhamento de pedido, pode adicionar, remover ou atualizar o pedido de HTTP (S) e cabeçalhos de resposta, como os pacotes de solicitação e resposta mover entre o cliente e o back-end agrupamentos através do gateway de aplicação.

Os cabeçalhos podem ser definidos para valores estáticos ou para outros cabeçalhos e as variáveis de servidor. Isto ajuda a com importante utilizar casos, como extrair os endereços IP de cliente, remoção das informações confidenciais sobre o back-end, adicionar mais segurança e assim por diante.

Para obter mais informações, consulte [cabeçalhos de HTTP de reescrever no seu gateway de aplicação](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers).

## <a name="http-settings"></a>Definições de HTTP

Um gateway de aplicação encaminha o tráfego para os servidores de back-end (especificada na regra de encaminhamento de pedido incluem definições de HTTP) usando o número de porta, protocolo e outras definições detalhadas neste componente.

A porta e protocolo utilizado nas definições de HTTP determinam se o tráfego entre os servidores de gateway e o back-end de aplicação é encriptado (fornecendo o SSL ponto a ponto) ou não encriptada.

Este componente também é utilizado para:

- Determinar se uma sessão de utilizador é estarão no mesmo servidor, utilizando o [afinidade de sessão baseada em cookies](https://docs.microsoft.com/azure/application-gateway/overview#session-affinity).

- Simplesmente remover membros do agrupamento de back-end através de [drenagem de ligação](https://docs.microsoft.com/azure/application-gateway/overview#connection-draining).

- Associe uma sonda personalizada para monitorizar o estado de funcionamento do back-end, definir o intervalo de tempo limite do pedido, substituir o nome de anfitrião e caminho no pedido e fornecer facilidade de um clique para especificar definições para o back-end do serviço de aplicações.

## <a name="backend-pools"></a>Conjuntos de back-end

Um conjunto de back-end encaminha o pedido para servidores de back-end, que irá atender à solicitação. Os conjuntos de back-end podem conter:

- NICs
- Conjuntos de dimensionamento de máquinas virtuais
- Endereços IP públicos
- Endereços IP internos
- FQDN
- Back-ends multi-inquilino (por exemplo, o serviço de aplicações)

Membros do conjunto de back-end de Gateway de aplicação não estão amarrados a um conjunto de disponibilidade. Um gateway de aplicação pode comunicar com instâncias de fora da rede virtual que está a ser. Como resultado, os membros dos agrupamentos de back-end podem ser em clusters, em centros de dados ou fora do Azure, desde que existe conectividade IP.

Se utilizar IPs interno como membros do agrupamento de back-end, tem de utilizar [peering de rede virtual](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) ou uma [gateway de VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Peering de rede virtual é suportado e útil para tráfego de balanceamento de carga nas outras redes virtuais.

Um gateway de aplicação também pode comunicar com servidores no local quando estão ligados por túneis VPN ou ExpressRoute do Azure se o tráfego é permitido.

Pode criar conjuntos de back-end diferente para diferentes tipos de pedidos. Por exemplo, crie um conjunto de back-end para pedidos gerais e, em seguida, outro conjunto de back-end para os pedidos para os microsserviços para a sua aplicação.

## <a name="health-probes"></a>Sondas do estado de funcionamento

Por predefinição, o gateway de aplicação monitoriza o estado de funcionamento de todos os recursos no seu conjunto de back-end e remove automaticamente aqueles mau estado de funcionamento. Depois, monitora instâncias mau estado de funcionamento e adiciona-o novamente para o pool de back-end em bom estado quando eles se tornarem disponíveis e respondem às sondas de estado de funcionamento.

Além de utilizar a monitorização de sonda de estado de funcionamento do predefinido, também pode personalizar a sonda de estado de funcionamento para atender às necessidades da sua aplicação. As pesquisas personalizadas permitem um controle mais granular sobre a monitorização de estado de funcionamento. Ao utilizar sondas personalizadas, pode configurar o intervalo de pesquisa, o URL e caminho para testar e quantos respostas com falhas para aceitar antes da instância de conjunto de back-end está marcada como mau estado de funcionamento. Recomendamos que configure as pesquisas personalizadas para monitorizar o estado de funcionamento de cada conjunto de back-end.

Para obter mais informações, consulte [monitorizar o estado de funcionamento do seu gateway de aplicação](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview).

## <a name="next-steps"></a>Passos Seguintes

Crie um gateway de aplicação:

* [No portal do Azure](quick-create-portal.md)
* [Com o Azure PowerShell](quick-create-powershell.md)
* [Ao utilizar a CLI do Azure](quick-create-cli.md)
