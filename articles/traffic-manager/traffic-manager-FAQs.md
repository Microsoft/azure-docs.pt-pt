---
title: Gerenciador de tráfego do Azure-perguntas frequentes
description: Este artigo fornece respostas para perguntas frequentes sobre o Gerenciador de tráfego
services: traffic-manager
documentationcenter: ''
author: asudbring
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/26/2019
ms.author: allensu
ms.openlocfilehash: d7feb0f7c32ab544df2b9de08daaf8cd007318b5
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76045304"
---
# <a name="traffic-manager-frequently-asked-questions-faq"></a>Perguntas frequentes sobre o Traffic Manager

## <a name="traffic-manager-basics"></a>Noções básicas do Gerenciador de tráfego

### <a name="what-ip-address-does-traffic-manager-use"></a>Qual endereço IP o Gerenciador de tráfego usa?

Conforme explicado em [como funciona o Gerenciador de tráfego, o](../traffic-manager/traffic-manager-how-it-works.md)Gerenciador de tráfego funciona no nível do DNS. Ele envia respostas DNS para direcionar clientes para o ponto de extremidade de serviço apropriado. Em seguida, os clientes se conectam ao ponto de extremidade de serviço diretamente, não pelo Gerenciador de tráfego.

Portanto, o Gerenciador de tráfego não fornece um endereço IP ou ponto de extremidade para os clientes se conectarem ao. Se você quiser um endereço IP estático para seu serviço, ele deverá ser configurado no serviço, não no Gerenciador de tráfego.

### <a name="what-types-of-traffic-can-be-routed-using-traffic-manager"></a>Quais tipos de tráfego podem ser roteados usando o Gerenciador de tráfego?
Conforme explicado em [como funciona o Gerenciador de tráfego](../traffic-manager/traffic-manager-how-it-works.md), um ponto de extremidade do Gerenciador de tráfego pode ser qualquer serviço voltado para a Internet hospedado dentro ou fora do Azure. Portanto, o Traffic Manager pode rotear o tráfego originado da Internet pública para um conjunto de pontos de extremidade que também são voltados para a Internet. Se você tiver pontos de extremidade que estejam dentro de uma rede privada (por exemplo, uma versão interna do [Azure Load Balancer](../load-balancer/concepts-limitations.md#internalloadbalancer)) ou fazer com que os usuários façam solicitações de DNS dessas redes internas, você não poderá usar o Gerenciador de tráfego para rotear esse tráfego.

### <a name="does-traffic-manager-support-sticky-sessions"></a>O Gerenciador de tráfego dá suporte a sessões "adesivas"?

Conforme explicado em [como funciona o Gerenciador de tráfego, o](../traffic-manager/traffic-manager-how-it-works.md)Gerenciador de tráfego funciona no nível do DNS. Ele usa respostas DNS para direcionar os clientes para o ponto de extremidade de serviço apropriado. Os clientes se conectam ao ponto de extremidade de serviço diretamente, não por meio do Gerenciador de tráfego. Portanto, o Gerenciador de tráfego não vê o tráfego HTTP entre o cliente e o servidor.

Além disso, o endereço IP de origem da consulta DNS recebida pelo Gerenciador de tráfego pertence ao serviço DNS recursivo, não ao cliente. Portanto, o Gerenciador de tráfego não tem como rastrear clientes individuais e não pode implementar sessões "adesivas". Essa limitação é comum a todos os sistemas de gerenciamento de tráfego baseados em DNS e não é específica ao Gerenciador de tráfego.

### <a name="why-am-i-seeing-an-http-error-when-using-traffic-manager"></a>Por que estou vendo um erro HTTP ao usar o Gerenciador de tráfego?

Conforme explicado em [como funciona o Gerenciador de tráfego, o](../traffic-manager/traffic-manager-how-it-works.md)Gerenciador de tráfego funciona no nível do DNS. Ele usa respostas DNS para direcionar os clientes para o ponto de extremidade de serviço apropriado. Em seguida, os clientes se conectam ao ponto de extremidade de serviço diretamente, não pelo Gerenciador de tráfego. O Gerenciador de tráfego não vê o tráfego HTTP entre o cliente e o servidor. Portanto, qualquer erro de HTTP que você veja deve ser proveniente do seu aplicativo. Para que o cliente se conecte ao aplicativo, todas as etapas de resolução de DNS são concluídas. Isso inclui qualquer interação que o Gerenciador de tráfego tem sobre o fluxo de tráfego do aplicativo.

Uma investigação adicional, portanto, deve se concentrar no aplicativo.

O cabeçalho de host HTTP Enviado do navegador do cliente é a fonte de problemas mais comum. Verifique se o aplicativo está configurado para aceitar o cabeçalho de host correto para o nome de domínio que você está usando. Para pontos de extremidade usando o serviço Azure App, consulte [Configurando um nome de domínio personalizado para um aplicativo Web no serviço Azure App usando o Gerenciador de tráfego](../app-service/web-sites-traffic-manager-custom-domain-name.md).

### <a name="what-is-the-performance-impact-of-using-traffic-manager"></a>Qual é o impacto no desempenho do uso do Gerenciador de tráfego?

Conforme explicado em [como funciona o Gerenciador de tráfego, o](../traffic-manager/traffic-manager-how-it-works.md)Gerenciador de tráfego funciona no nível do DNS. Como os clientes se conectam diretamente aos pontos de extremidade de serviço, não há nenhum impacto no desempenho ao usar o Gerenciador de tráfego depois que a conexão é estabelecida.

Como o Traffic Manager se integra aos aplicativos no nível do DNS, ele requer uma pesquisa DNS adicional a ser inserida na cadeia de resolução DNS. O impacto do Gerenciador de tráfego no tempo de resolução DNS é mínimo. O Gerenciador de tráfego usa uma rede global de servidores de nomes e usa a rede [anycast](https://en.wikipedia.org/wiki/Anycast) para garantir que as consultas DNS sempre sejam roteadas para o servidor de nomes mais próximo disponível. Além disso, o cache de respostas DNS significa que a latência DNS adicional incorrida pelo uso do Gerenciador de tráfego se aplica apenas a uma fração de sessões.

O método de desempenho roteia o tráfego para o ponto de extremidade mais próximo disponível. O resultado líquido é que o impacto geral no desempenho associado a esse método deve ser mínimo. Qualquer aumento na latência de DNS deve ser deslocado diminuindo a latência de rede para o ponto de extremidade.

### <a name="what-application-protocols-can-i-use-with-traffic-manager"></a>Quais protocolos de aplicativo posso usar com o Gerenciador de tráfego?

Conforme explicado em [como funciona o Gerenciador de tráfego, o](../traffic-manager/traffic-manager-how-it-works.md)Gerenciador de tráfego funciona no nível do DNS. Depois que a pesquisa de DNS é concluída, os clientes se conectam diretamente ao ponto de extremidade do aplicativo, não por meio do Gerenciador de tráfego. Portanto, a conexão pode usar qualquer protocolo de aplicativo. Se você selecionar TCP como o protocolo de monitoramento, o monitoramento de integridade do ponto de extremidade do Gerenciador de tráfego poderá ser feito sem usar nenhum protocolo de aplicativo. Se você optar por ter a integridade verificada usando um protocolo de aplicativo, o ponto de extremidade precisará ser capaz de responder às solicitações GET HTTP ou HTTPS.

### <a name="can-i-use-traffic-manager-with-a-naked-domain-name"></a>Posso usar o Gerenciador de tráfego com um nome de domínio "Naked"?

Sim. Para saber como criar um registro de alias para o Apex do nome de domínio para fazer referência a um perfil do Gerenciador de tráfego do Azure, consulte [configurar um registro de alias para dar suporte a nomes de domínio Apex com o Gerenciador de tráfego](../dns/tutorial-alias-tm.md).

### <a name="does-traffic-manager-consider-the-client-subnet-address-when-handling-dns-queries"></a>O Gerenciador de tráfego considera o endereço de sub-rede do cliente ao manipular consultas DNS? 

Sim, além do endereço IP de origem da consulta DNS que recebe (que geralmente é o endereço IP do resolvedor de DNS), ao executar pesquisas para métodos de roteamento geográfico, de desempenho e de sub-rede, o Gerenciador de tráfego também considera o endereço de sub-rede do cliente se Ele é incluído na consulta pelo resolvedor que faz a solicitação em nome do usuário final.  
Especificamente, [a RFC 7871 – sub-rede de cliente em consultas DNS](https://tools.ietf.org/html/rfc7871) que fornece um [mecanismo de extensão para DNS (EDNS0)](https://tools.ietf.org/html/rfc2671) que pode passar o endereço de sub-rede do cliente de resolvedores que dão suporte a ele.

### <a name="what-is-dns-ttl-and-how-does-it-impact-my-users"></a>O que é o TTL do DNS e como ele afeta meus usuários?

Quando uma consulta DNS chega ao Traffic Manager, ela define um valor na resposta chamada TTL (vida útil). Esse valor, cuja unidade está em segundos, indica ao downstream os resolvedores de DNS por quanto tempo armazenar em cache essa resposta. Embora não haja garantia de que os resolvedores de DNS armazenem em cache esse resultado, o armazenamento em cache permite que eles respondam a todas as consultas subsequentes fora do cache em vez de ir para os servidores DNS do Gerenciador de tráfego. Isso afeta as respostas da seguinte maneira:

- uma TTL maior reduz o número de consultas que se esvidam nos servidores DNS do Gerenciador de tráfego, o que pode reduzir o custo de um cliente, uma vez que o número de consultas atendidas é um uso faturável.
- uma TTL mais alta pode reduzir potencialmente o tempo necessário para fazer uma pesquisa de DNS.
- uma TTL mais alta também significa que seus dados não refletem as informações de integridade mais recentes que o Gerenciador de tráfego obteve por meio de seus agentes de investigação.

### <a name="how-high-or-low-can-i-set-the-ttl-for-traffic-manager-responses"></a>O quão alto ou baixo posso definir o TTL das respostas do Gerenciador de tráfego?

Você pode definir, em um nível por perfil, o TTL do DNS para ser tão baixo quanto 0 segundos e tão alto quanto 2.147.483.647 segundos (o intervalo máximo em conformidade com [RFC-1035](https://www.ietf.org/rfc/rfc1035.txt )). Um TTL de 0 significa que os resolvedores de DNS downstream não armazenam em cache respostas de consulta e todas as consultas devem alcançar os servidores DNS do Gerenciador de tráfego para resolução.

### <a name="how-can-i-understand-the-volume-of-queries-coming-to-my-profile"></a>Como posso entender o volume de consultas que estão chegando ao meu perfil? 

Uma das métricas fornecidas pelo Gerenciador de tráfego é o número de consultas respondidas por um perfil. Você pode obter essas informações em uma agregação de nível de perfil ou pode dividi-la ainda mais para ver o volume de consultas em que pontos de extremidade específicos foram retornados. Além disso, você pode configurar alertas para notificá-lo se o volume de resposta da consulta cruzar as condições que você definiu. Para obter mais detalhes, [métricas e alertas do Gerenciador de tráfego](traffic-manager-metrics-alerts.md).

## <a name="traffic-manager-geographic-traffic-routing-method"></a>Método de roteamento de tráfego geográfico do Gerenciador de tráfego

### <a name="what-are-some-use-cases-where-geographic-routing-is-useful"></a>Quais são alguns casos de uso em que o roteamento geográfico é útil?

O tipo de roteamento geográfico pode ser usado em qualquer cenário em que um cliente do Azure precise distinguir seus usuários com base em regiões geográficas. Por exemplo, usando o método de roteamento de tráfego geográfico, você pode dar aos usuários de regiões específicas uma experiência de usuário diferente das outras regiões. Outro exemplo é a conformidade com as normas da soberania de dados locais que exigem que os usuários de uma região específica sejam servidos somente por pontos de extremidade nessa região.

### <a name="how-do-i-decide-if-i-should-use-performance-routing-method-or-geographic-routing-method"></a>Como fazer decidir se devo usar o método de roteamento de desempenho ou o método de roteamento geográfico?

A principal diferença entre esses dois métodos de roteamento populares é que, no método de roteamento de desempenho, seu principal objetivo é enviar tráfego para o ponto de extremidade que possa fornecer a menor latência ao chamador, enquanto que, no roteamento geográfico, o objetivo principal é impor uma área geográfica limite para seus chamadores para que você possa encaminhá-los deliberadamente para um ponto de extremidade específico. A sobreposição acontece porque há uma correlação entre a proximidade geográfica e a latência inferior, embora isso nem sempre seja verdadeiro. Pode haver um ponto de extremidade em uma geografia diferente que pode fornecer uma melhor experiência de latência para o chamador e, nesse caso, o roteamento de desempenho enviará o usuário para esse ponto de extremidade, mas o roteamento geográfico sempre os enviará para o ponto de extremidade que você mapeou para seus região geográfica. Para torná-lo mais claro, considere o exemplo a seguir – com o roteamento geográfico, você pode fazer mapeamentos incomuns, como enviar todo o tráfego da Ásia para pontos de extremidade nos EUA e todos os tráfegos dos EUA para pontos de extremidade na Ásia. Nesse caso, o roteamento geográfico deliberadamente fará exatamente o que você configurou para fazer e a otimização do desempenho não é uma consideração. 
>[!NOTE]
>Pode haver cenários em que você possa precisar de recursos de roteamento geográfico e de desempenho, para esses cenários, os perfis aninhados podem ser excelentes opções. Por exemplo, você pode configurar um perfil pai com roteamento geográfico, no qual você envia todo o tráfego de América do Norte para um perfil aninhado que tem pontos de extremidade nos EUA e usa o roteamento de desempenho para enviar o tráfego para o melhor ponto final dentro desse conjunto. 

### <a name="what-are-the-regions-that-are-supported-by-traffic-manager-for-geographic-routing"></a>Quais são as regiões com suporte do Gerenciador de tráfego para roteamento geográfico?

A hierarquia de país/região usada pelo Gerenciador de tráfego pode ser encontrada [aqui](traffic-manager-geographic-regions.md). Embora essa página seja mantida atualizada com as alterações, você também pode recuperar programaticamente as mesmas informações usando a [API REST do Gerenciador de tráfego do Azure](https://docs.microsoft.com/rest/api/trafficmanager/). 

### <a name="how-does-traffic-manager-determine-where-a-user-is-querying-from"></a>Como o Gerenciador de tráfego determina de onde um usuário está consultando?

O Gerenciador de tráfego examina o IP de origem da consulta (isso provavelmente é um resolvedor DNS local fazendo a consulta em nome do usuário) e usa um IP interno para o mapa de região para determinar o local. Esse mapa é atualizado em uma base contínua para considerar alterações na Internet. 

### <a name="is-it-guaranteed-that-traffic-manager-can-correctly-determine-the-exact-geographic-location-of-the-user-in-every-case"></a>É garantido que o Gerenciador de tráfego possa determinar corretamente a localização geográfica exata do usuário em todos os casos?

Não, o Gerenciador de tráfego não pode garantir que a região geográfica que inferemos do endereço IP de origem de uma consulta DNS sempre corresponderá ao local do usuário devido aos seguintes motivos:

- Primeiro, conforme descrito nas perguntas frequentes anteriores, o endereço IP de origem que vemos é o de um resolvedor de DNS que faz a pesquisa em nome do usuário. Embora a localização geográfica do resolvedor de DNS seja um bom proxy para a localização geográfica do usuário, ela também pode ser diferente dependendo da superfície do serviço resolvedor de DNS e do serviço de resolvedor DNS específico que um cliente optou por usar. Por exemplo, um cliente localizado na Malásia pode especificar nas configurações do dispositivo usar um serviço de resolvedor de DNS cujo servidor DNS em Cingapura pode ser escolhido para lidar com as resoluções de consulta para esse usuário/dispositivo. Nesse caso, o Gerenciador de tráfego só pode ver o endereço IP do resolvedor que corresponde ao local de Cingapura. Além disso, consulte as perguntas frequentes anteriores sobre o suporte ao endereço de sub-rede do cliente nesta página.

- Em segundo lugar, o Gerenciador de tráfego usa um mapa interno para fazer o endereço IP para a tradução da região geográfica. Embora esse mapa seja validado e atualizado de forma contínua para aumentar sua precisão e conta para a natureza em evolução da Internet, ainda há a possibilidade de que nossas informações não sejam uma representação exata da localização geográfica de todos os IP atende.

###  <a name="does-an-endpoint-need-to-be-physically-located-in-the-same-region-as-the-one-it-is-configured-with-for-geographic-routing"></a>Um ponto de extremidade precisa estar fisicamente localizado na mesma região com a qual ele está configurado para roteamento geográfico?

Não, o local do ponto de extremidade impõe nenhuma restrição sobre quais regiões podem ser mapeadas para ele. Por exemplo, um ponto de extremidade na região central do Azure dos EUA pode fazer com que todos os usuários da Índia sejam direcionados para ele.

### <a name="can-i-assign-geographic-regions-to-endpoints-in-a-profile-that-is-not-configured-to-do-geographic-routing"></a>Posso atribuir regiões geográficas a pontos de extremidade em um perfil que não está configurado para fazer o roteamento geográfico?

Sim, se o método de roteamento de um perfil não for geográfico, você poderá usar a [API REST do Gerenciador de tráfego do Azure](https://docs.microsoft.com/rest/api/trafficmanager/) para atribuir regiões geográficas a pontos de extremidade nesse perfil. No caso de perfis de tipo de roteamento não geográfico, essa configuração é ignorada. Se você alterar esse perfil para tipo de roteamento geográfico em um momento posterior, o Gerenciador de tráfego poderá usar esses mapeamentos.

### <a name="why-am-i-getting-an-error-when-i-try-to-change-the-routing-method-of-an-existing-profile-to-geographic"></a>Por que estou recebendo um erro quando tento alterar o método de roteamento de um perfil existente para geográfico?

Todos os pontos de extremidade em um perfil com roteamento geográfico precisam ter pelo menos uma região mapeada para ele. Para converter um perfil existente em tipo de roteamento geográfico, primeiro você precisa associar regiões geográficas a todos os seus pontos de extremidade usando a [API REST do Gerenciador de tráfego do Azure](https://docs.microsoft.com/rest/api/trafficmanager/) antes de alterar o tipo de roteamento para geográfico. Se estiver usando o portal, primeiro exclua os pontos de extremidade, altere o método de roteamento do perfil para geográfico e, em seguida, adicione os pontos de extremidade junto com seu mapeamento de região geográfica.

### <a name="why-is-it-strongly-recommended-that-customers-create-nested-profiles-instead-of-endpoints-under-a-profile-with-geographic-routing-enabled"></a>Por que é altamente recomendável que os clientes criem perfis aninhados em vez de pontos de extremidade em um perfil com roteamento geográfico habilitado?

Uma região pode ser atribuída a apenas um ponto de extremidade em um perfil se ele estiver usando o método de roteamento geográfico. Se esse ponto de extremidade não for um tipo aninhado com um perfil filho anexado a ele, se esse ponto de extremidade ficar não íntegro, o Traffic Manager continuará a enviar tráfego para ele, pois a alternativa de não enviar nenhum tráfego não será melhor. O Gerenciador de tráfego não faz failover para outro ponto de extremidade, mesmo quando a região atribuída é um "pai" da região atribuída ao ponto de extremidade que não estava íntegro (por exemplo, se um ponto de extremidade que tem a região Espanha ficar não íntegro, não realizamos failover para outro ponto de extremidade que tem a região Europe atribuída a ela). Isso é feito para garantir que o Gerenciador de tráfego respeite os limites geográficos que um cliente tem configurado em seu perfil. Para obter o benefício de fazer failover para outro ponto de extremidade quando um ponto de extremidade não estiver íntegro, é recomendável que as regiões geográficas sejam atribuídas a perfis aninhados com vários pontos de extremidade dentro dele, em vez de pontos de extremidade individuais. Dessa forma, se um ponto de extremidade no perfil filho aninhado falhar, o tráfego poderá fazer failover para outro ponto de extremidade dentro do mesmo perfil filho aninhado.

### <a name="are-there-any-restrictions-on-the-api-version-that-supports-this-routing-type"></a>Há alguma restrição na versão da API que dá suporte a esse tipo de roteamento?

Sim, somente a versão de API 2017-03-01 e mais recente dá suporte ao tipo de roteamento geográfico. Nenhuma versão de API mais antiga não pode ser usada para criar perfis de tipo de roteamento geográfico ou atribuir regiões geográficas a pontos de extremidade. Se uma versão de API mais antiga for usada para recuperar perfis de uma assinatura do Azure, qualquer perfil do tipo de roteamento geográfico não será retornado. Além disso, ao usar versões de API mais antigas, qualquer perfil retornado que tenha pontos de extremidade com uma atribuição de região geográfica não terá sua atribuição de região geográfica mostrada.

## <a name="traffic-manager-subnet-traffic-routing-method"></a>Método de roteamento de tráfego de sub-rede do Gerenciador de tráfego

### <a name="what-are-some-use-cases-where-subnet-routing-is-useful"></a>Quais são alguns casos de uso em que o roteamento de sub-rede é útil?

O roteamento de sub-rede permite diferenciar a experiência que você fornece para conjuntos específicos de usuários identificados pelo IP de origem de seu endereço IP de solicitações DNS. Um exemplo seria Mostrar conteúdo diferente se os usuários estiverem se conectando a um site da sede corporativa. Outra seria restringir os usuários de determinados ISPs a acessarem apenas os pontos de extremidade que só dão suporte a conexões IPv4 se esses ISPs tiverem um desempenho de nível inferior quando o IPv6 for usado.
Outro motivo para usar o método de roteamento de sub-rede é em conjunto com outros perfis em um conjunto de perfis aninhado. Por exemplo, se você quiser usar o método de roteamento geográfico para o isolamento geográfico de seus usuários, mas para um ISP específico que você deseja fazer um método de roteamento diferente, você pode ter um método de roteamento de sub-rede com perfil como o perfil pai e substituir esse ISP para usar um profissional filho específico arquivo e ter o perfil geográfico padrão para todos os outros.

### <a name="how-does-traffic-manager-know-the-ip-address-of-the-end-user"></a>Como o Gerenciador de tráfego sabe o endereço IP do usuário final?

Os dispositivos de usuário final normalmente usam um resolvedor de DNS para fazer a pesquisa de DNS em seu nome. O IP de saída desses resolvedores é o que o Gerenciador de tráfego vê como o IP de origem. Além disso, o método de roteamento de sub-rede também procura ver se há informações de ECS (sub-rede estendida do cliente) do EDNS0 que foram aprovadas com a solicitação. Se as informações do ECS estiverem presentes, esse será o endereço usado para determinar o roteamento. Na ausência de informações de ECS, o IP de origem da consulta é usado para fins de roteamento.

### <a name="how-can-i-specify-ip-addresses-when-using-subnet-routing"></a>Como especificar endereços IP ao usar o roteamento de sub-rede?

Os endereços IP a serem associados a um ponto de extremidade podem ser especificados de duas maneiras. Primeiro, você pode usar a notação de octeto decimal pontilhado com um endereço inicial e final para especificar o intervalo (por exemplo, 1.2.3.4-5.6.7.8 ou 3.4.5.6-3.4.5.6). Em segundo lugar, você pode usar a notação CIDR para especificar o intervalo (por exemplo, 1.2.3.0/24). Você pode especificar vários intervalos e pode usar ambos os tipos de notação em um conjunto de intervalos. Algumas restrições se aplicam.

-   Você não pode ter sobreposição de intervalos de endereços, pois cada IP precisa ser mapeado para apenas um único ponto de extremidade
-   O endereço inicial não pode ser maior que o endereço final
-   No caso da notação CIDR, o endereço IP antes de '/' deve ser o endereço inicial desse intervalo (por exemplo, 1.2.3.0/24 é válido, mas 1.2.3.4.4/24 não é válido)

### <a name="how-can-i-specify-a-fallback-endpoint-when-using-subnet-routing"></a>Como posso especificar um ponto de extremidade de fallback ao usar o roteamento de sub-rede?

Em um perfil com roteamento de sub-rede, se você tiver um ponto de extremidade sem sub-redes mapeadas, qualquer solicitação que não corresponda a outros pontos de extremidade será direcionada para aqui. É altamente recomendável que você tenha esse ponto de extremidade de fallback em seu perfil, uma vez que o Traffic Manager retornará uma resposta NXDOMAIN se uma solicitação entrar e não estiver mapeada para nenhum ponto de extremidade ou se estiver mapeada para um EndPoint, mas esse ponto de extremidade não estiver íntegro.

### <a name="what-happens-if-an-endpoint-is-disabled-in-a-subnet-routing-type-profile"></a>O que acontece se um ponto de extremidade estiver desabilitado em um perfil de tipo de roteamento de sub-rede?

Em um perfil com roteamento de sub-rede, se você tiver um ponto de extremidade com o que está desabilitado, o Gerenciador de tráfego se comportará como se esse ponto de extremidade e os mapeamentos de sub-rede não existirem. Se uma consulta que teria correspondido com seu mapeamento de endereço IP for recebida e o ponto de extremidade estiver desabilitado, o Gerenciador de tráfego retornará um ponto de extremidade de fallback (um sem mapeamentos) ou se tal ponto de extremidade não estiver presente, retornará uma resposta NXDOMAIN.

## <a name="traffic-manager-multivalue-traffic-routing-method"></a>Método de roteamento de tráfego de vários valores do Gerenciador de tráfego

### <a name="what-are-some-use-cases-where-multivalue-routing-is-useful"></a>Quais são alguns casos de uso em que o roteamento de vários valores é útil?

O roteamento de múltiplos valores retorna vários pontos de extremidade íntegros em uma única resposta de consulta. A principal vantagem disso é que, se um ponto de extremidade não estiver íntegro, o cliente terá mais opções para tentar novamente sem fazer outra chamada DNS (que pode retornar o mesmo valor de um cache upstream). Isso é aplicável a aplicativos sensíveis à disponibilidade que desejam minimizar o tempo de inatividade.
Outro uso para o método de roteamento de vários valores é se um ponto de extremidade for "Dual-homed" para endereços IPv4 e IPv6 e você quiser dar ao chamador as duas opções para escolher quando iniciar uma conexão com o ponto de extremidade.

### <a name="how-many-endpoints-are-returned-when-multivalue-routing-is-used"></a>Quantos pontos de extremidade são retornados quando o roteamento de vários valores é usado?

Você pode especificar o número máximo de pontos de extremidade a serem retornados e vários valores não retornarão mais do que muitos pontos de extremidade íntegros quando uma consulta for recebida. O valor máximo possível para essa configuração é 10.

### <a name="will-i-get-the-same-set-of-endpoints-when-multivalue-routing-is-used"></a>Eu receberei o mesmo conjunto de pontos de extremidade quando o roteamento de vários valores for usado?

Não podemos garantir que o mesmo conjunto de pontos de extremidade será retornado em cada consulta. Isso também é afetado pelo fato de que alguns dos pontos de extremidade podem ficar não íntegros no ponto em que não serão incluídos na resposta

## <a name="real-user-measurements"></a>Medidas de Utilizadores Reais

### <a name="what-are-the-benefits-of-using-real-user-measurements"></a>Quais são os benefícios do uso do Medidas de Usuário Reais?

Quando você usa o método de roteamento de desempenho, o Gerenciador de tráfego escolhe a melhor região do Azure para o usuário final se conectar inspecionando o IP de origem e a sub-rede do cliente EDNS (se passadas) e verificando-o em relação à inteligência de latência de rede que o serviço mantém. Medidas de Usuário Reais aprimora isso para sua base de usuários finais fazendo com que sua experiência contribua para essa tabela de latência, além de garantir que essa tabela abranja adequadamente as redes do usuário final de onde os usuários finais se conectam ao Azure. Isso leva a uma maior precisão no roteamento do usuário final.

### <a name="can-i-use-real-user-measurements-with-non-azure-regions"></a>Posso usar Medidas de Usuário Reais com regiões não Azure?

Medidas de Usuário Reais medidas e relatórios apenas sobre a latência para alcançar regiões do Azure. Se você estiver usando o roteamento baseado em desempenho com pontos de extremidade hospedados em regiões que não são do Azure, ainda poderá se beneficiar desse recurso com mais informações de latência sobre a região do Azure do representante que você selecionou para ser associada a esse ponto de extremidade.

### <a name="which-routing-method-benefits-from-real-user-measurements"></a>Qual método de roteamento se beneficia da Medidas de Usuário Reais?

As informações adicionais obtidas por meio de Medidas de Usuário Reais são aplicáveis somente para perfis que usam o método de roteamento de desempenho. O link Medidas de Usuário Reais está disponível de todos os perfis quando você o exibe por meio do portal do Azure.

### <a name="do-i-need-to-enable-real-user-measurements-each-profile-separately"></a>É necessário habilitar Medidas de Usuário Reais cada perfil separadamente?

Não, você só precisa habilitá-la uma vez por assinatura e todas as informações de latência medidas e relatadas estão disponíveis para todos os perfis.

### <a name="how-do-i-turn-off-real-user-measurements-for-my-subscription"></a>Como fazer desligar Medidas de Usuário Reais para minha assinatura?

Você pode parar a acumulação de encargos relacionados a Medidas de Usuário Reais quando você parar de coletar e enviar medidas de latência de retorno do seu aplicativo cliente. Por exemplo, quando o JavaScript de medida é inserido em páginas da Web, você pode parar de usar esse recurso removendo o JavaScript ou desativando sua invocação quando a página é renderizada.

Você também pode desativar Medidas de Usuário Reais excluindo sua chave. Depois de excluir a chave, todas as medições enviadas ao Gerenciador de tráfego com essa chave serão descartadas.

### <a name="can-i-use-real-user-measurements-with-client-applications-other-than-web-pages"></a>Posso usar Medidas de Usuário Reais com aplicativos cliente diferentes de páginas da Web?

Sim, Medidas de Usuário Reais é projetado para ingerir dados coletados por meio de diferentes tipos de clientes de usuário final. Essas perguntas frequentes serão atualizadas à medida que novos tipos de aplicativos cliente forem suportados.

### <a name="how-many-measurements-are-made-each-time-my-real-user-measurements-enabled-web-page-is-rendered"></a>Quantas medições são feitas cada vez que minha página da Web habilitada Medidas de Usuário Reais é renderizada?

Quando Medidas de Usuário Reais é usado com o JavaScript de medida fornecido, cada página processando os resultados em seis medidas são realizadas. Eles são relatados de volta ao serviço do Gerenciador de tráfego. Você é cobrado por esse recurso com base no número de medições relatadas para o serviço do Gerenciador de tráfego. Por exemplo, se o usuário sair da sua página da Web enquanto as medidas estiverem sendo executadas, mas antes de ser relatadas, essas medidas não serão consideradas para fins de cobrança.

### <a name="is-there-a-delay-before-real-user-measurements-script-runs-in-my-webpage"></a>Há um atraso antes que Medidas de Usuário Reais script seja executado em minha página da Web?

Não, não há nenhum atraso programado antes que o script seja invocado.

### <a name="can-i-use-real-user-measurements-with-only-the-azure-regions-i-want-to-measure"></a>Posso usar Medidas de Usuário Reais apenas com as regiões do Azure que desejo medir?

Não, cada vez que ele é invocado, o script de Medidas de Usuário Reais mede um conjunto de seis regiões do Azure, conforme determinado pelo serviço. Esse conjunto é alterado entre invocações diferentes e quando um grande número de tais invocações acontece, a cobertura de medida se estende entre diferentes regiões do Azure.

### <a name="can-i-limit-the-number-of-measurements-made-to-a-specific-number"></a>Posso limitar o número de medições feitas a um número específico?

A medida JavaScript é inserida em sua página da Web e você tem controle total sobre quando começar e parar de usá-la. Desde que o serviço do Gerenciador de tráfego receba uma solicitação para que uma lista de regiões do Azure seja medida, um conjunto de regiões será retornado.

### <a name="can-i-see-the-measurements-taken-by-my-client-application-as-part-of-real-user-measurements"></a>Posso ver as medições feitas pelo meu aplicativo cliente como parte do Medidas de Usuário Reais?

Como a lógica de medida é executada no aplicativo cliente, você tem controle total do que acontece, incluindo a exibição das medidas de latência. O Gerenciador de tráfego não relata uma exibição agregada das medidas recebidas sob a chave vinculada à sua assinatura.

### <a name="can-i-modify-the-measurement-script-provided-by-traffic-manager"></a>Posso modificar o script de medidas fornecido pelo Gerenciador de tráfego?

Embora você esteja no controle do que está inserido em sua página da Web, é altamente recomendável que você faça alterações no script de medida para garantir que ele meça e relate as latências corretamente.

### <a name="will-it-be-possible-for-others-to-see-the-key-i-use-with-real-user-measurements"></a>Será possível que outras pessoas vejam a chave que uso com Medidas de Usuário Reais?

Quando você inserir o script de medida em uma página da Web, será possível que outras pessoas vejam o script e sua chave de Medidas de Usuário Reais (RUM). Mas é importante saber que essa chave é diferente da sua ID de assinatura e é gerada pelo Gerenciador de tráfego para ser usada somente para essa finalidade. Saber sua chave de RUM não comprometerá a segurança da sua conta do Azure.

### <a name="can-others-abuse-my-rum-key"></a>Outras pessoas podem abuse da minha chave de RUM?

Embora seja possível que outras pessoas usem sua chave para enviar informações erradas para o Azure, algumas medidas erradas não alterarão o roteamento, uma vez que elas são levadas em conta junto com todas as outras medidas que recebemos. Se você precisar alterar suas chaves, poderá gerar novamente a chave no ponto em que a chave antiga se torna descartada.

### <a name="do-i-need-to-put-the-measurement-javascript-in-all-my-web-pages"></a>É necessário colocar o JavaScript de medida em todas as minhas páginas da Web?

Medidas de Usuário Reais oferece mais valor conforme o número de medições aumenta. Dito isso, é sua decisão de se você precisa colocá-la em todas as suas páginas da Web ou em uma seleção. Nossa recomendação é começar colocando-o na sua página mais visitada, na qual um usuário deve permanecer nessa página cinco segundos ou mais.

### <a name="can-information-about-my-end-users-be-identified-by-traffic-manager-if-i-use-real-user-measurements"></a>As informações sobre meus usuários finais podem ser identificadas pelo Gerenciador de tráfego se eu usar Medidas de Usuário Reais?

Quando o JavaScript de medida fornecido for usado, o Gerenciador de tráfego terá visibilidade do endereço IP do cliente do usuário final e do endereço IP de origem do resolvedor de DNS local que eles usam. O Gerenciador de tráfego usa o endereço IP do cliente somente depois de tê-lo truncado para não poder identificar o usuário final específico que enviou as medições.

### <a name="does-the-webpage-measuring-real-user-measurements-need-to-be-using-traffic-manager-for-routing"></a>A página da Web está medindo Medidas de Usuário Reais precisa estar usando o Gerenciador de tráfego para roteamento?

Não, não é necessário usar o Gerenciador de tráfego. O lado de roteamento do Traffic Manager Opera separadamente da parte real da medição do usuário e, embora seja uma ótima ideia tê-los na mesma propriedade da Web, eles não precisam ser.

### <a name="do-i-need-to-host-any-service-on-azure-regions-to-use-with-real-user-measurements"></a>É necessário hospedar qualquer serviço nas regiões do Azure para usar com Medidas de Usuário Reais?

Não, você não precisa hospedar nenhum componente do lado do servidor no Azure para que Medidas de Usuário Reais funcionem. A imagem de pixel único baixada pelo JavaScript de medida e o serviço que a executa em diferentes regiões do Azure é hospedado e gerenciado pelo Azure. 

### <a name="will-my-azure-bandwidth-usage-increase-when-i-use-real-user-measurements"></a>O uso da largura de banda do Azure será aumentado quando eu usar Medidas de Usuário Reais?

Conforme mencionado na resposta anterior, os componentes do lado do servidor de Medidas de Usuário Reais são de propriedade e são gerenciados pelo Azure. Isso significa que o uso de largura de banda do Azure não aumentará, pois você usa Medidas de Usuário Reais. Isso não inclui nenhum uso de largura de banda fora do que as cobranças do Azure. Minimizamos a largura de banda usada ao baixar apenas uma imagem de pixel único para medir a latência em uma região do Azure. 

## <a name="traffic-view"></a>Vista de Tráfego

### <a name="what-does-traffic-view-do"></a>O que Exibição de Tráfego faz?

Exibição de Tráfego é um recurso do Gerenciador de tráfego que ajuda você a entender mais sobre seus usuários e como sua experiência é. Ele usa as consultas recebidas pelo Gerenciador de tráfego e as tabelas de inteligência de latência de rede que o serviço mantém para fornecer o seguinte:

- As regiões de onde os usuários estão se conectando aos seus pontos de extremidade no Azure.
- O volume de usuários que se conectam a partir dessas regiões.
- As regiões do Azure às quais eles estão sendo roteados.
- Sua experiência de latência para essas regiões do Azure.

Essas informações estão disponíveis para você consumir por meio da sobreposição de mapa geográfico e exibições tabulares no portal, além de estarem disponíveis como dados brutos para download.

### <a name="how-can-i-benefit-from-using-traffic-view"></a>Como posso aproveitar o uso de Exibição de Tráfego?

Exibição de Tráfego fornece a visão geral do tráfego que seus perfis do Gerenciador de tráfego recebem. Em particular, ele pode ser usado para entender onde sua base de usuários se conecta e, igualmente, qual é a sua experiência de latência média. Você pode usar essas informações para encontrar áreas nas quais você precisa se concentrar, por exemplo, expandindo sua superfície do Azure para uma região que pode atender a esses usuários com menor latência. Outra informação que você pode derivar do uso do Exibição de Tráfego é Ver os padrões de tráfego para regiões diferentes que, por sua vez, podem ajudá-lo a tomar decisões sobre como aumentar ou diminuir o estoque nessas regiões.

### <a name="how-is-traffic-view-different-from-the-traffic-manager-metrics-available-through-azure-monitor"></a>Como o Exibição de Tráfego é diferente das métricas do Gerenciador de tráfego disponíveis por meio do Azure monitor?

Azure Monitor pode ser usado para entender em um nível agregado o tráfego recebido pelo seu perfil e seus pontos de extremidade. Ele também permite que você acompanhe o status de integridade dos pontos de extremidade expondo os resultados da verificação de integridade. Quando você precisa ir além disso e entender a experiência do usuário final se conectando ao Azure em um nível regional, Exibição de Tráfego pode ser usada para fazer isso.

### <a name="does-traffic-view-use-edns-client-subnet-information"></a>Exibição de Tráfego usar informações de sub-rede do cliente EDNS?

As consultas DNS servidas pelo Gerenciador de tráfego do Azure consideram informações de ECS para aumentar a precisão do roteamento. Mas, ao criar o conjunto de dados que mostra de onde os usuários estão se conectando, Exibição de Tráfego está usando apenas o endereço IP do resolvedor de DNS.

### <a name="how-many-days-of-data-does-traffic-view-use"></a>Quantos dias de dados Exibição de Tráfego usar?

Exibição de Tráfego cria sua saída processando os dados dos sete dias anteriores ao dia anterior ao ser exibido por você. Esta é uma janela móvel e os dados mais recentes serão usados cada vez que você visitar.

### <a name="how-does-traffic-view-handle-external-endpoints"></a>Como Exibição de Tráfego lidar com pontos de extremidade externos?

Quando você usa pontos de extremidade externos hospedados fora de regiões do Azure em um perfil do Gerenciador de tráfego, você pode optar por tê-lo mapeado para uma região do Azure que é um proxy para suas características de latência (isso é, na verdade, necessário se você usar o método de roteamento de desempenho). Se ele tiver esse mapeamento de região do Azure, as métricas de latência da região do Azure serão usadas ao criar a saída de Exibição de Tráfego. Se nenhuma região do Azure for especificada, as informações de latência estarão vazias nos dados para esses pontos de extremidade externos.

### <a name="do-i-need-to-enable-traffic-view-for-each-profile-in-my-subscription"></a>Preciso Habilitar Exibição de Tráfego para cada perfil em minha assinatura?

Durante o período de visualização, o Exibição de Tráfego foi habilitado em um nível de assinatura. Como parte dos aprimoramentos que fizemos antes da disponibilidade geral, agora você pode Habilitar Exibição de Tráfego em um nível de perfil, permitindo que você tenha uma habilitação mais granular desse recurso. Por padrão, Exibição de Tráfego será desabilitada para um perfil.

>[!NOTE]
>Se você tiver habilitado Exibição de Tráfego em um nível de assinatura durante o tempo de visualização, agora será necessário habilitá-lo novamente para cada perfil sob essa assinatura.
 
### <a name="how-can-i-turn-off-traffic-view"></a>Como desativo Exibição de Tráfego?

Você pode desativar Exibição de Tráfego para qualquer perfil usando o portal ou a API REST. 

### <a name="how-does-traffic-view-billing-work"></a>Como funciona a cobrança Exibição de Tráfego?

Exibição de Tráfego preço é baseado no número de pontos de dados usados para criar a saída. Atualmente, o único tipo de dados com suporte é as consultas que seu perfil recebe. Além disso, você será cobrado apenas pelo processamento feito quando Exibição de Tráfego habilitado. Isso significa que, se você habilitar Exibição de Tráfego por algum período de tempo em um mês e desativá-lo durante outros horários, somente os pontos de dados processados enquanto você tiver a contagem de recursos habilitados em direção à sua fatura.

## <a name="traffic-manager-endpoints"></a>Pontos finais do Gestor de tráfego

### <a name="can-i-use-traffic-manager-with-endpoints-from-multiple-subscriptions"></a>Posso usar o Gerenciador de tráfego com pontos de extremidade de várias assinaturas?

Não é possível usar pontos de extremidade de várias assinaturas com os aplicativos Web do Azure. Os aplicativos Web do Azure exigem que qualquer nome de domínio personalizado usado com aplicativos Web seja usado apenas dentro de uma única assinatura. Não é possível usar aplicativos Web de várias assinaturas com o mesmo nome de domínio.

Para outros tipos de ponto de extremidade, é possível usar o Gerenciador de tráfego com pontos de extremidades de mais de uma assinatura. No Gerenciador de recursos, os pontos de extremidade de qualquer assinatura podem ser adicionados ao Gerenciador de tráfego, contanto que a pessoa que configurou o perfil do Traffic Manager tenha acesso de leitura ao ponto de extremidade. Essas permissões podem ser concedidas usando [Azure Resource Manager controle de acesso baseado em função (RBAC)](../role-based-access-control/role-assignments-portal.md).

### <a name="can-i-use-traffic-manager-with-cloud-service-staging-slots"></a>Posso usar o Gerenciador de tráfego com slots de ' preparo ' do serviço de nuvem?

Sim. Os slots de ' preparo ' do serviço de nuvem podem ser configurados no Gerenciador de tráfego como pontos de extremidade externos. As verificações de integridade ainda são cobradas com a taxa de Pontos de Extremidade do Azure.

### <a name="does-traffic-manager-support-ipv6-endpoints"></a>O Gerenciador de tráfego dá suporte a pontos de extremidade IPv6?

Atualmente, o Gerenciador de tráfego não fornece servidores de nome endereçáveis IPv6. No entanto, o Gerenciador de tráfego ainda pode ser usado por clientes IPv6 que se conectam a pontos de extremidade IPv6. Um cliente não faz solicitações de DNS diretamente para o Gerenciador de tráfego. Em vez disso, o cliente usa um serviço DNS recursivo. Um cliente somente IPv6 envia solicitações para o serviço DNS recursivo via IPv6. Em seguida, o serviço recursivo deve ser capaz de contatar os servidores de nome do Gerenciador de tráfego usando IPv4.

O Gerenciador de tráfego responde com o nome DNS ou o endereço IP do ponto de extremidade. Para dar suporte a um ponto de extremidade IPv6, há duas opções. Você pode adicionar o ponto de extremidade como um nome DNS que tem um registro AAAA associado e o Gerenciador de tráfego verificará a integridade desse ponto de extremidade e o retornará como um tipo de registro CNAME na resposta da consulta. Você também pode adicionar esse ponto de extremidade diretamente usando o endereço IPv6 e o Gerenciador de tráfego retornará um registro de tipo AAAA na resposta da consulta.

### <a name="can-i-use-traffic-manager-with-more-than-one-web-app-in-the-same-region"></a>Posso usar o Gerenciador de tráfego com mais de um aplicativo Web na mesma região?

Normalmente, o Gerenciador de tráfego é usado para direcionar o tráfego para aplicativos implantados em regiões diferentes. No entanto, ele também pode ser usado onde um aplicativo tem mais de uma implantação na mesma região. Os pontos de extremidade do Azure do Gerenciador de tráfego não permitem que mais de um ponto final do aplicativo Web da mesma região do Azure seja adicionado ao mesmo perfil do Gerenciador de tráfego.

### <a name="how-do-i-move-my-traffic-manager-profiles-azure-endpoints-to-a-different-resource-group-or-subscription"></a>Como fazer mover os pontos de extremidade do Azure do meu perfil do Gerenciador de tráfego para um grupo de recursos ou assinatura diferente?

Os pontos de extremidade do Azure associados a um perfil do Gerenciador de tráfego são controlados usando suas IDs de recurso. Quando um recurso do Azure que está sendo usado como um ponto de extremidade (por exemplo, IP público, serviço de nuvem clássico, WebApp ou outro perfil do Gerenciador de tráfego usado de maneira aninhada) é movido para um grupo de recursos ou assinatura diferente, sua ID de recurso é alterada. Nesse cenário, no momento, você deve atualizar o perfil do Gerenciador de tráfego primeiro excluindo e depois adicionando novamente os pontos de extremidade ao perfil.

## <a name="traffic-manager-endpoint-monitoring"></a>Monitorização do ponto final do Gestor de Tráfego

### <a name="is-traffic-manager-resilient-to-azure-region-failures"></a>O Gerenciador de tráfego é resiliente a falhas de região do Azure?

O Gerenciador de tráfego é um componente fundamental da entrega de aplicativos altamente disponíveis no Azure.
Para fornecer alta disponibilidade, o Gerenciador de tráfego deve ter um nível de disponibilidade excepcionalmente alto e ser resiliente a falhas regionais.

Por design, os componentes do Traffic Manager são resilientes a uma falha completa de qualquer região do Azure. Essa resiliência se aplica a todos os componentes do Traffic Manager: os servidores de nomes DNS, a API, a camada de armazenamento e o serviço de monitoramento de ponto de extremidade.

No caso improvável de uma interrupção de uma região inteira do Azure, espera-se que o Gerenciador de tráfego continue a funcionar normalmente. Os aplicativos implantados em várias regiões do Azure podem confiar no Gerenciador de tráfego para direcionar o tráfego para uma instância disponível de seu aplicativo.

### <a name="how-does-the-choice-of-resource-group-location-affect-traffic-manager"></a>Como a escolha do local do grupo de recursos afeta o Gerenciador de tráfego?

O Gerenciador de tráfego é um serviço global único. Não é regional. A escolha do local do grupo de recursos não faz diferença com os perfis do Gerenciador de tráfego implantados nesse grupo de recursos.

Azure Resource Manager requer que todos os grupos de recursos especifiquem um local, o que determina o local padrão para os recursos implantados nesse grupo de recursos. Quando você cria um perfil do Gerenciador de tráfego, ele é criado em um grupo de recursos. Todos os perfis do Gerenciador de tráfego usam **global** como seu local, substituindo o padrão do grupo de recursos.

### <a name="how-do-i-determine-the-current-health-of-each-endpoint"></a>Como fazer determinar a integridade atual de cada ponto de extremidade?

O status de monitoramento atual de cada ponto de extremidade, além do perfil geral, é exibido no portal do Azure. Essas informações também estão disponíveis por meio da [API REST](https://msdn.microsoft.com/library/azure/mt163667.aspx)do monitor de tráfego, [cmdlets do PowerShell](https://docs.microsoft.com/powershell/module/az.trafficmanager)e [CLI do Azure de plataforma cruzada](../cli-install-nodejs.md).

Você também pode usar Azure Monitor para controlar a integridade de seus pontos de extremidade e ver uma representação visual deles. Para obter mais informações sobre como usar Azure Monitor, consulte a [documentação de monitoramento do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics).

### <a name="can-i-monitor-https-endpoints"></a>Posso monitorar pontos de extremidade HTTPS?

Sim. O Gerenciador de tráfego dá suporte à investigação por HTTPS. Configure o **https** como o protocolo na configuração de monitoramento.

O Gerenciador de tráfego não pode fornecer nenhuma validação de certificado, incluindo:

* Certificados do lado do servidor não são validados
* Os certificados do lado do servidor SNI não são validados
* Não há suporte para certificados de cliente

### <a name="do-i-use-an-ip-address-or-a-dns-name-when-adding-an-endpoint"></a>Eu uso um endereço IP ou um nome DNS ao adicionar um ponto de extremidade?

O Gerenciador de tráfego dá suporte à adição de pontos de extremidade usando três maneiras de consultá-los – como um nome DNS, como um endereço IPv4 e como um endereço IPv6. Se o ponto de extremidade for adicionado como um endereço IPv4 ou IPv6, a resposta de consulta será do tipo de registro A ou AAAA, respectivamente. Se o ponto de extremidade foi adicionado como um nome DNS, a resposta de consulta será do tipo de registro CNAME. A adição de pontos de extremidade como endereço IPv4 ou IPv6 só será permitida se o ponto final for do tipo **externo**.
Todos os métodos de roteamento e configurações de monitoramento têm suporte dos três tipos de endereçamento de ponto de extremidade.

### <a name="what-types-of-ip-addresses-can-i-use-when-adding-an-endpoint"></a>Que tipos de endereços IP posso usar ao adicionar um ponto de extremidade?

O Gerenciador de tráfego permite que você use endereços IPv4 ou IPv6 para especificar pontos de extremidade. Há algumas restrições listadas abaixo:

- Endereços que correspondem a espaços de endereços IP privados reservados não são permitidos. Esses endereços incluem aqueles chamados de RFC 1918, RFC 6890, RFC 5737, RFC 3068, RFC 2544 e RFC 5771
- O endereço não deve conter nenhum número de porta (você pode especificar as portas a serem usadas nas definições de configuração do perfil)
- Dois pontos de extremidade no mesmo perfil podem ter o mesmo endereço IP de destino

### <a name="can-i-use-different-endpoint-addressing-types-within-a-single-profile"></a>Posso usar diferentes tipos de endereçamento de ponto de extremidade em um único perfil?

Não, o Gerenciador de tráfego não permite misturar tipos de endereçamento de ponto de extremidade dentro de um perfil, exceto para o caso de um perfil com o tipo de roteamento de vários valores, no qual você pode misturar tipos de endereçamento IPv4 e IPv6

### <a name="what-happens-when-an-incoming-querys-record-type-is-different-from-the-record-type-associated-with-the-addressing-type-of-the-endpoints"></a>O que acontece quando o tipo de registro de uma consulta de entrada é diferente do tipo de registro associado ao tipo de endereçamento dos pontos de extremidade?

Quando uma consulta é recebida em um perfil, o Gerenciador de tráfego primeiro localiza o ponto de extremidade que precisa ser retornado de acordo com o método de roteamento especificado e o status de integridade dos pontos de extremidade. Em seguida, ele examina o tipo de registro solicitado na consulta de entrada e o tipo de registro associado ao ponto de extremidade antes de retornar uma resposta com base na tabela a seguir.

Para perfis com qualquer método de roteamento diferente de vários valores:

|Solicitação de consulta de entrada|    Tipo de ponto de extremidade|  Resposta fornecida|
|--|--|--|
|OUTRO |  A/AAAA/CNAME |  Ponto de extremidade de destino| 
|A |    A/CNAME | Ponto de extremidade de destino|
|A |    AAAA |  NODATA |
|AAAA | AAAA/CNAME |  Ponto de extremidade de destino|
|AAAA | A | NODATA |
|CNAME |    CNAME | Ponto de extremidade de destino|
|CNAME  |A / AAAA | NODATA |
|

Para perfis com o método de roteamento definido como vários valores:

|Solicitação de consulta de entrada|    Tipo de ponto de extremidade | Resposta fornecida|
|--|--|--|
|OUTRO |  Combinação de A e AAAA | Pontos de extremidade de destino|
|A |    Combinação de A e AAAA | Somente pontos de extremidade de destino do tipo A|
|AAAA   |Combinação de A e AAAA|     Somente pontos de extremidade de destino do tipo AAAA|
|CNAME |    Combinação de A e AAAA | NODATA |

### <a name="can-i-use-a-profile-with-ipv4--ipv6-addressed-endpoints-in-a-nested-profile"></a>Posso usar um perfil com pontos de extremidade endereçados por IPv4/IPv6 em um perfil aninhado?

Sim, você pode, com a exceção de que um perfil do tipo de vários valores não pode ser um perfil pai em um conjunto de perfis aninhado.

### <a name="i-stopped-an-web-application-endpoint-in-my-traffic-manager-profile-but-i-am-not-receiving-any-traffic-even-after-i-restarted-it-how-can-i-fix-this"></a>Parei um ponto de extremidade do aplicativo Web no meu perfil do Gerenciador de tráfego, mas não estou recebendo nenhum tráfego mesmo depois de reiniciá-lo. Como posso corrigir este problema?

Quando um ponto de extremidade de aplicativo Web do Azure é interrompido, o Gerenciador de tráfego para de verificar sua integridade e reinicia as verificações de integridade somente após detectar que o ponto de extremidade foi reiniciado. Para evitar esse atraso, desabilite e reabilite o ponto de extremidade no perfil do Gerenciador de tráfego depois de reiniciar o ponto de extremidade.

### <a name="can-i-use-traffic-manager-even-if-my-application-does-not-have-support-for-http-or-https"></a>Posso usar o Gerenciador de tráfego mesmo que meu aplicativo não tenha suporte para HTTP ou HTTPS?

Sim. Você pode especificar o TCP como o protocolo de monitoramento e o Gerenciador de tráfego pode iniciar uma conexão TCP e aguardar uma resposta do ponto de extremidade. Se o ponto de extremidade responder à solicitação de conexão com uma resposta para estabelecer a conexão, dentro do período de tempo limite, esse ponto de extremidade será marcado como íntegro.

### <a name="what-specific-responses-are-required-from-the-endpoint-when-using-tcp-monitoring"></a>Quais respostas específicas são necessárias do ponto de extremidade ao usar o monitoramento de TCP?

Quando o monitoramento de TCP é usado, o Gerenciador de tráfego inicia um handshake TCP de três vias enviando uma solicitação SYN ao ponto de extremidade na porta especificada. Em seguida, ele aguarda uma resposta de SYN-ACK do ponto de extremidade por um período de tempo (especificado nas configurações de tempo limite).

- Se uma resposta de SYN-ACK for recebida dentro do período de tempo limite especificado nas configurações de monitoramento, esse ponto de extremidade será considerado íntegro. Uma ACK de FIN ou fin é a resposta esperada do Traffic Manager quando ele termina regularmente um soquete.
- Se uma resposta de SYN-ACK for recebida após o tempo limite especificado, o Gerenciador de tráfego responderá com um RST para redefinir a conexão.

### <a name="how-fast-does-traffic-manager-move-my-users-away-from-an-unhealthy-endpoint"></a>Com que velocidade o Gerenciador de tráfego move meus usuários para fora de um ponto de extremidade não íntegro?

O Gerenciador de tráfego fornece várias configurações que podem ajudá-lo a controlar o comportamento de failover do seu perfil do Gerenciador de tráfego da seguinte maneira:

- Você pode especificar que o Gerenciador de tráfego investigue os pontos de extremidade com mais frequência definindo o intervalo de investigação em 10 segundos. Isso garante que qualquer ponto de extremidade que esteja se tornando não íntegro pode ser detectado assim que possível. 
- Você pode especificar quanto tempo esperar antes que uma solicitação de verificação de integridade expire (o valor de tempo limite mínimo é de 5 segundos).
- Você pode especificar quantas falhas podem ocorrer antes que o ponto de extremidade seja marcado como não íntegro. Esse valor pode ser baixo como 0. nesse caso, o ponto de extremidade é marcado como não íntegro assim que falha na primeira verificação de integridade. No entanto, usar o valor mínimo de 0 para o número tolerado de falhas pode levar a pontos de extremidade que estão sendo retirados da rotação devido a problemas transitórios que podem ocorrer no momento da investigação.
- Você pode especificar o tempo de vida (TTL) para que a resposta DNS seja tão baixa quanto 0. Isso significa que os resolvedores de DNS não podem armazenar em cache a resposta e cada nova consulta Obtém uma resposta que incorpora as informações de integridade mais atualizadas que o Gerenciador de tráfego tem.

Usando essas configurações, o Gerenciador de tráfego pode fornecer failovers menos de 10 segundos depois que um ponto de extremidade não estiver íntegro e uma consulta DNS for feita no perfil correspondente.

### <a name="how-can-i-specify-different-monitoring-settings-for-different-endpoints-in-a-profile"></a>Como especificar diferentes configurações de monitoramento para diferentes pontos de extremidade em um perfil?

As configurações de monitoramento do Traffic Manager estão em um nível por perfil. Se você precisar usar uma configuração de monitoramento diferente para apenas um ponto de extremidade, isso poderá ser feito tendo esse ponto de extremidade como um [perfil aninhado](traffic-manager-nested-profiles.md) cujas configurações de monitoramento são diferentes do perfil pai.

### <a name="how-can-i-assign-http-headers-to-the-traffic-manager-health-checks-to-my-endpoints"></a>Como posso atribuir cabeçalhos HTTP às verificações de integridade do Gerenciador de tráfego aos meus pontos de extremidade?

O Gerenciador de tráfego permite que você especifique cabeçalhos personalizados nas verificações de integridade de HTTP (S) que ele inicia em seus pontos de extremidade. Se você quiser especificar um cabeçalho personalizado, poderá fazer isso no nível do perfil (aplicável a todos os pontos de extremidade) ou especificá-lo no nível do ponto de extremidades. Se um cabeçalho for definido em ambos os níveis, o especificado no nível do ponto de extremidade substituirá o nível do perfil.
Um caso de uso comum para isso é especificar cabeçalhos de host para que as solicitações do Gerenciador de tráfego possam ser roteadas corretamente para um ponto de extremidade hospedado em um ambiente de vários locatários. Outro caso de uso disso é identificar as solicitações do Gerenciador de tráfego dos logs de solicitação de HTTP (S) de um ponto de extremidade

### <a name="what-host-header-do-endpoint-health-checks-use"></a>Qual cabeçalho de host as verificações de integridade do ponto de extremidade usam?

Se nenhuma configuração de cabeçalho de host personalizado for fornecida, o cabeçalho de host usado pelo Gerenciador de tráfego será o nome DNS do destino do ponto de extremidade configurado no perfil, se disponível.

### <a name="what-are-the-ip-addresses-from-which-the-health-checks-originate"></a>Quais são os endereços IP dos quais as verificações de integridade se originam?

Clique [aqui](https://azuretrafficmanagerdata.blob.core.windows.net/probes/azure/probe-ip-ranges.json) para exibir o arquivo JSON que lista os endereços IP dos quais as verificações de integridade do Gerenciador de tráfego podem se originar. Examine os IPs listados no arquivo JSON para garantir que as conexões de entrada desses endereços IP sejam permitidas nos pontos de extremidade para verificar seu status de integridade.

### <a name="how-many-health-checks-to-my-endpoint-can-i-expect-from-traffic-manager"></a>Quantas verificações de integridade no meu ponto de extremidade posso esperar do Gerenciador de tráfego?

O número de verificações de integridade do Gerenciador de tráfego que atingem seu ponto de extremidade depende do seguinte:

- o valor que você definiu para o intervalo de monitoramento (intervalo menor significa mais aterrissagem de solicitações no ponto de extremidade em um determinado período de tempo).
- o número de locais de onde as verificações de integridade se originam (os endereços IP de onde você pode esperar essas verificações estão listados nas perguntas frequentes anteriores).

### <a name="how-can-i-get-notified-if-one-of-my-endpoints-goes-down"></a>Como posso ser notificado se um dos meus pontos de extremidade ficar inativo?

Uma das métricas fornecidas pelo Gerenciador de tráfego é o status de integridade de pontos de extremidade em um perfil. Você pode ver isso como uma agregação de todos os pontos de extremidade dentro de um perfil (por exemplo, 75% de seus pontos de extremidade estão íntegros) ou, em um nível por ponto de extremidades. As métricas do Gerenciador de tráfego são expostas por meio de Azure Monitor e você pode usar seus [recursos de alerta](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md) para obter notificações quando houver uma alteração no status de integridade de seu ponto de extremidade. Para obter mais detalhes, consulte [métricas e alertas do Gerenciador de tráfego](traffic-manager-metrics-alerts.md).  

## <a name="traffic-manager-nested-profiles"></a>Perfis aninhados do Gerenciador de tráfego

### <a name="how-do-i-configure-nested-profiles"></a>Como fazer configurar perfis aninhados?

Os perfis aninhados do Gerenciador de tráfego podem ser configurados usando as APIs REST do Azure Azure Resource Manager e clássicas, os cmdlets Azure PowerShell e os comandos CLI do Azure entre plataformas. Eles também têm suporte por meio do novo portal do Azure.

### <a name="how-many-layers-of-nesting-does-traffic-manger-support"></a>Quantas camadas de aninhamento o Gerenciador de tráfego dá suporte?

Você pode aninhar perfis de até 10 níveis de profundidade. ' Loops ' não são permitidos.

### <a name="can-i-mix-other-endpoint-types-with-nested-child-profiles-in-the-same-traffic-manager-profile"></a>Posso misturar outros tipos de ponto de extremidade com perfis filho aninhados, no mesmo perfil do Gerenciador de tráfego?

Sim. Não há restrições sobre como você combina pontos de extremidade de diferentes tipos dentro de um perfil.

### <a name="how-does-the-billing-model-apply-for-nested-profiles"></a>Como o modelo de cobrança se aplica a perfis aninhados?

Não há nenhum impacto negativo no preço do uso de perfis aninhados.

A cobrança do Gerenciador de tráfego tem dois componentes: verificações de integridade do ponto de extremidade e milhões de consultas DNS

* Verificações de integridade do ponto de extremidade: não há nenhum encargo para um perfil filho quando configurado como um ponto de extremidade em um perfil pai. O monitoramento dos pontos de extremidade no perfil filho é cobrado da maneira usual.
* Consultas DNS: cada consulta é contada apenas uma vez. Uma consulta em relação a um perfil pai que retorna um ponto de extremidade de um perfil filho é contada somente no perfil pai.

Para obter detalhes completos, consulte a [página de preços do Traffic Manager](https://azure.microsoft.com/pricing/details/traffic-manager/).

### <a name="is-there-a-performance-impact-for-nested-profiles"></a>Há um impacto no desempenho para perfis aninhados?

Não. Não há impacto no desempenho ao usar perfis aninhados.

Os servidores de nome do Gerenciador de tráfego atravessam a hierarquia de perfil internamente ao processar cada consulta DNS. Uma consulta DNS para um perfil pai pode receber uma resposta DNS com um ponto de extremidade de um perfil filho. Um único registro CNAME é usado se você estiver usando um único perfil ou perfis aninhados. Não é necessário criar um registro CNAME para cada perfil na hierarquia.

### <a name="how-does-traffic-manager-compute-the-health-of-a-nested-endpoint-in-a-parent-profile"></a>Como o Gerenciador de tráfego computa a integridade de um ponto de extremidade aninhado em um perfil pai?

O perfil pai não executa verificações de integridade diretamente no filho. Em vez disso, a integridade dos pontos de extremidade do perfil filho é usada para calcular a integridade geral do perfil filho. Essas informações são propagadas na hierarquia do perfil aninhado para determinar a integridade do ponto de extremidade aninhado. O perfil pai usa essa integridade agregada para determinar se o tráfego pode ser direcionado para o filho.

A tabela a seguir descreve o comportamento das verificações de integridade do Gerenciador de tráfego para um ponto de extremidade aninhado.

| Status do monitor de perfil filho | Status do monitor de ponto de extremidade pai | Notas |
| --- | --- | --- |
| Desativado. O perfil filho foi desabilitado. |Parada |O estado do ponto de extremidade pai é parado, não desabilitado. O estado desabilitado é reservado para indicar que você desabilitou o ponto de extremidade no perfil pai. |
| Degradado. Pelo menos um ponto de extremidade de perfil filho está em um estado degradado. |Online: o número de pontos de extremidade online no perfil filho é pelo menos o valor de MinChildEndpoints.<BR>CheckingEndpoint: o número de pontos de extremidade online Plus CheckingEndpoint no perfil filho é pelo menos o valor de MinChildEndpoints.<BR>Degradado: caso contrário. |O tráfego é roteado para um ponto de extremidade do status CheckingEndpoint. Se MinChildEndpoints for definido muito alto, o ponto de extremidade sempre será degradado. |
| Online. Pelo menos um ponto de extremidade de perfil filho é um estado online. Nenhum ponto de extremidade está no estado degradado. |Veja acima. | |
| Verificando pontos. Pelo menos um ponto de extremidade de perfil filho é ' CheckingEndpoint '. Nenhum ponto de extremidade está ' online ' ou ' degradado ' |O mesmo que acima. | |
| Inativo. Todos os pontos de extremidade de perfil filho estão desabilitados ou interrompidos ou este perfil não tem nenhum ponto de extremidade. |Parada | |

## <a name="next-steps"></a>Passos seguintes:

- Saiba mais sobre o [monitoramento de ponto de extremidade](../traffic-manager/traffic-manager-monitoring.md)do Traffic Manager e o failover automático.
- Saiba mais sobre os [métodos de roteamento de tráfego](../traffic-manager/traffic-manager-routing-methods.md)do Traffic Manager.
