---
title: Gestor de Tráfego Azure - FAQs
description: Este artigo fornece respostas a perguntas frequentes sobre o Gestor de Tráfego
services: traffic-manager
documentationcenter: ''
author: rohinkoul
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/26/2019
ms.author: rohink
ms.openlocfilehash: acdac6e3eafc5251ebd31a34bcb9a4db34f0ebbe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254368"
---
# <a name="traffic-manager-frequently-asked-questions-faq"></a>Gestor de Tráfego frequentemente perguntas (FAQ)

## <a name="traffic-manager-basics"></a>Básicos do Gestor de Tráfego

### <a name="what-ip-address-does-traffic-manager-use"></a>Que endereço IP usa o Traffic Manager?

Como explicado em Como funciona o Gestor de [Tráfego,](../traffic-manager/traffic-manager-how-it-works.md)o Gestor de Tráfego trabalha ao nível do DNS. Envia respostas dNS aos clientes diretos para o ponto final de serviço apropriado. Os clientes então ligam-se diretamente ao ponto final do serviço, não através do Traffic Manager.

Portanto, o Traffic Manager não fornece um endereço final ou IP para os clientes se conectarem. Se quiser um endereço IP estático para o seu serviço, isso deve ser configurado no serviço e não no Traffic Manager.

### <a name="what-types-of-traffic-can-be-routed-using-traffic-manager"></a>Que tipos de tráfego podem ser encaminhados através do Traffic Manager?
Como explicado em Como funciona o [Traffic Manager,](../traffic-manager/traffic-manager-how-it-works.md)um ponto final do Traffic Manager pode ser qualquer serviço virado para a Internet hospedado dentro ou fora de Azure. Assim, o Traffic Manager pode encaminhar o tráfego que vem da internet pública para um conjunto de pontos finais que também estão virados para a Internet. Se tiver pontos finais dentro de uma rede privada (por exemplo, uma versão interna do [Azure Load Balancer)](../load-balancer/concepts-limitations.md#internalloadbalancer)ou tiver utilizadores a fazer pedidos dNS a partir dessas redes internas, então não pode utilizar o Traffic Manager para encaminhar este tráfego.

### <a name="does-traffic-manager-support-sticky-sessions"></a>O Gestor de Tráfego apoia sessões "pegajosas"?

Como explicado em Como funciona o Gestor de [Tráfego,](../traffic-manager/traffic-manager-how-it-works.md)o Gestor de Tráfego trabalha ao nível do DNS. Utiliza respostas DNS para clientes diretos para o ponto final de serviço apropriado. Os clientes ligam-se diretamente ao ponto final do serviço, não através do Traffic Manager. Portanto, o Gestor de Tráfego não vê o tráfego HTTP entre o cliente e o servidor.

Adicionalmente, o endereço IP de origem da consulta dNS recebida pelo Traffic Manager pertence ao serviço DNS recursivo, não ao cliente. Por isso, o Traffic Manager não tem forma de rastrear clientes individuais e não pode implementar sessões 'pegajosas'. Esta limitação é comum a todos os sistemas de gestão de tráfego baseados em DNS e não é específica para o Traffic Manager.

### <a name="why-am-i-seeing-an-http-error-when-using-traffic-manager"></a>Porque é que estou a ver um erro http quando uso o Traffic Manager?

Como explicado em Como funciona o Gestor de [Tráfego,](../traffic-manager/traffic-manager-how-it-works.md)o Gestor de Tráfego trabalha ao nível do DNS. Utiliza respostas DNS para clientes diretos para o ponto final de serviço apropriado. Os clientes então ligam-se diretamente ao ponto final do serviço, não através do Traffic Manager. O Gestor de Tráfego não vê o tráfego HTTP entre o cliente e o servidor. Portanto, qualquer erro HTTP que você vê deve vir da sua aplicação. Para que o cliente se conectem à aplicação, todos os passos de resolução do DNS estão completos. Isso inclui qualquer interação que o Traffic Manager tenha no fluxo de tráfego da aplicação.

Por conseguinte, uma investigação mais aprofundada deverá centrar-se no pedido.

O cabeçalho do anfitrião HTTP enviado do navegador do cliente é a fonte mais comum de problemas. Certifique-se de que a aplicação está configurada para aceitar o cabeçalho correto do anfitrião para o nome de domínio que está a usar. Para pontos finais utilizando o Serviço de Aplicações Azure, consulte configurar um nome de [domínio personalizado para uma aplicação web no Serviço de Aplicações Azure utilizando](../app-service/configure-domain-traffic-manager.md)o Traffic Manager .

### <a name="what-is-the-performance-impact-of-using-traffic-manager"></a>Qual é o impacto de desempenho da utilização do Traffic Manager?

Como explicado em Como funciona o Gestor de [Tráfego,](../traffic-manager/traffic-manager-how-it-works.md)o Gestor de Tráfego trabalha ao nível do DNS. Uma vez que os clientes se ligam diretamente aos pontos finais do seu serviço, não há nenhum impacto de desempenho incorrido na utilização do Traffic Manager uma vez estabelecida a ligação.

Uma vez que o Traffic Manager se integra com aplicações ao nível do DNS, requer uma procura adicional de DNS para ser inserida na cadeia de resolução DNS. O impacto do Gestor de Tráfego no tempo de resolução do DNS é mínimo. O Traffic Manager utiliza uma rede global de servidores de nomes e utiliza qualquer rede [de difusão](https://en.wikipedia.org/wiki/Anycast) para garantir que as consultas de DNS são sempre encaminhadas para o servidor de nomes mais próximo disponível. Além disso, o cache de respostas DNS significa que a latência adicional de DNS incorrida pela utilização do Traffic Manager aplica-se apenas a uma fração de sessões.

O método performance encaminha o tráfego para o ponto final mais próximo disponível. O resultado líquido é que o impacto global do desempenho associado a este método deve ser mínimo. Qualquer aumento da latência DNS deve ser compensado pela menor latência da rede até ao ponto final.

### <a name="what-application-protocols-can-i-use-with-traffic-manager"></a>Que protocolos de aplicação posso usar com o Gestor de Tráfego?

Como explicado em Como funciona o Gestor de [Tráfego,](../traffic-manager/traffic-manager-how-it-works.md)o Gestor de Tráfego trabalha ao nível do DNS. Uma vez concluída a procura dNS, os clientes conectam-se diretamente ao ponto final da aplicação, não através do Traffic Manager. Portanto, a ligação pode usar qualquer protocolo de aplicação. Se selecionar o TCP como protocolo de monitorização, a monitorização de saúde do Gestor de Tráfego pode ser feita sem utilizar quaisquer protocolos de aplicação. Se optar por verificar a saúde através de um protocolo de candidatura, o ponto final tem de ser capaz de responder a pedidos HTTP ou HTTPS GET.

### <a name="can-i-use-traffic-manager-with-a-naked-domain-name"></a>Posso usar o Gerente de Tráfego com um nome de domínio "nu"?

Sim. Para aprender a criar um registo de pseudónimo para o seu nome de domínio apex para fazer referência a um perfil do Gestor de Tráfego Azure, consulte [um registo de pseudónimo para suportar nomes](../dns/tutorial-alias-tm.md)de domínio apex com Traffic Manager .

### <a name="does-traffic-manager-consider-the-client-subnet-address-when-handling-dns-queries"></a>O Gestor de Tráfego considera o endereço de subnet do cliente ao lidar com consultas de DNS? 

Sim, para além do endereço IP fonte da consulta dNS que recebe (que normalmente é o endereço IP do dNS resolver), ao realizar procuras para métodos de encaminhamento De Geográfico, Performance e Subnet, o gestor de tráfego também considera o endereço de subnet do cliente se está incluído na consulta pelo resolver que faz o pedido em nome do utilizador final.  
Especificamente, [RFC 7871 – Client Subnet in DNS Queries](https://tools.ietf.org/html/rfc7871) que fornece um Mecanismo de [Extensão para DNS (EDNS0)](https://tools.ietf.org/html/rfc2671) que pode transmitir o endereço de subnet do cliente a partir de resolvers que o suportam.

### <a name="what-is-dns-ttl-and-how-does-it-impact-my-users"></a>O que é DNS TTL e como é que isso afeta os meus utilizadores?

Quando uma consulta do DNS aterra no Traffic Manager, estabelece um valor na resposta chamada time-to-live (TTL). Este valor, cuja unidade está em segundos, indica que o DNS resolve a jusante quanto tempo para cache esta resposta. Embora os resolvers dNS não estejam garantidos para cache este resultado, o cache permite-lhes responder a quaisquer consultas subsequentes fora da cache em vez de ir para servidores DNS do Gestor de Tráfego. Isto impacta as respostas da seguinte forma:

- um TTL mais elevado reduz o número de consultas que aterram nos servidores DNS do Gestor de Tráfego, o que pode reduzir o custo para um cliente uma vez que o número de consultas servidas é um uso faturado.
- um TTL mais alto pode potencialmente reduzir o tempo que leva para fazer uma procura de DNS.
- um TTL mais elevado também significa que os seus dados não refletem as últimas informações de saúde que o Gestor de Tráfego obteve através dos seus agentes de sondagem.

### <a name="how-high-or-low-can-i-set-the-ttl-for-traffic-manager-responses"></a>Até que ponto posso definir o TTL para respostas do Gestor de Tráfego?

Pode definir, a um nível de perfil, o DNS TTL para ser tão baixo como 0 segundos e até 2.147.483,647 segundos (o intervalo máximo em conformidade com o [RFC-1035](https://www.ietf.org/rfc/rfc1035.txt )). Um TTL de 0 significa que os resolvers dNS a jusante não cache respostas de consulta e todas as consultas devem chegar aos servidores DNS do Gestor de Tráfego para resolução.

### <a name="how-can-i-understand-the-volume-of-queries-coming-to-my-profile"></a>Como posso entender o volume de consultas que chegam ao meu perfil? 

Uma das métricas fornecidas pelo Gestor de Tráfego é o número de consultas respondidas por um perfil. Você pode obter esta informação em um nível de agregação de nível de perfil ou você pode dividi-lo ainda mais para ver o volume de consultas onde pontos finais específicos foram devolvidos. Além disso, pode configurar alertas para notificá-lo se o volume de resposta à consulta cruzar as condições que definiu. Para mais detalhes, [métricas e alertas do Gestor de Tráfego.](traffic-manager-metrics-alerts.md)

## <a name="traffic-manager-geographic-traffic-routing-method"></a>Método de encaminhamento de tráfego do Gestor de Tráfego Geographic

### <a name="what-are-some-use-cases-where-geographic-routing-is-useful"></a>Quais são os casos de uso em que o encaminhamento geográfico é útil?

O tipo de encaminhamento geográfico pode ser usado em qualquer cenário em que um cliente Azure precise distinguir os seus utilizadores com base em regiões geográficas. Por exemplo, utilizando o método de encaminhamento de tráfego geográfico, pode dar aos utilizadores de regiões específicas uma experiência de utilizador diferente da de outras regiões. Outro exemplo é o cumprimento dos mandatos locais de soberania dos dados que exigem que os utilizadores de uma região específica sejam servidos apenas por pontos finais naquela região.

### <a name="how-do-i-decide-if-i-should-use-performance-routing-method-or-geographic-routing-method"></a>Como decido se devo usar o método de encaminhamento do desempenho ou o método de encaminhamento geográfico?

A diferença fundamental entre estes dois métodos populares de encaminhamento é que no método de encaminhamento de Desempenho o seu principal objetivo é enviar tráfego para o ponto final que pode fornecer a menor latência ao chamador, enquanto, em Geographic encaminhando o objetivo principal é impor um geo cerca para os seus chamadores para que possa encaminhá-los deliberadamente para um ponto final específico. A sobreposição acontece uma vez que existe uma correlação entre a proximidade geográfica e a menor latência, embora isso nem sempre seja verdade. Pode haver um ponto final numa geografia diferente que pode proporcionar uma melhor experiência de latência para o ouvinte e nesse caso o encaminhamento de desempenho enviará o utilizador para esse ponto final, mas o encaminhamento geográfico irá sempre enviá-los para o ponto final que você mapeou para o seu região geográfica. Para deixar ainda mais claro, considere o seguinte exemplo - com o encaminhamento geográfico pode fazer mapeamentos incomuns, como enviar todo o tráfego da Ásia para pontos finais nos EUA e todo o tráfego dos EUA para pontos finais na Ásia. Nesse caso, o encaminhamento geográfico fará deliberadamente exatamente o que configuraste para fazer e a otimização do desempenho não é uma consideração. 
>[!NOTE]
>Pode haver cenários em que você pode precisar de capacidades de desempenho e de encaminhamento geográfico, pois estes cenários os perfis aninhados podem ser uma ótima escolha. Por exemplo, você pode configurar um perfil de pais com encaminhamento geográfico onde você envia todo o tráfego da América do Norte para um perfil aninhado que tem pontos finais nos EUA e usar o encaminhamento de desempenho para enviar esses tráfego para o melhor ponto final dentro desse conjunto. 

### <a name="what-are-the-regions-that-are-supported-by-traffic-manager-for-geographic-routing"></a>Quais são as regiões que são apoiadas pelo Gestor de Tráfego para o encaminhamento geográfico?

A hierarquia país/região que é utilizada pelo Gestor de Tráfego pode ser consultada [aqui.](traffic-manager-geographic-regions.md) Embora esta página seja mantida atualizada com quaisquer alterações, também pode recuperar programáticamente as mesmas informações utilizando a [API REST API](https://docs.microsoft.com/rest/api/trafficmanager/)do Gestor de Tráfego do Azure . 

### <a name="how-does-traffic-manager-determine-where-a-user-is-querying-from"></a>Como é que o gestor de tráfego determina de onde vem o utilizador?

O Gestor de Tráfego olha para a fonte IP da consulta (esta é provavelmente uma resolver local dNS que faz a consulta em nome do utilizador) e usa um IP interno para o mapa da região para determinar a localização. Este mapa é atualizado de forma contínua para responder a mudanças na internet. 

### <a name="is-it-guaranteed-that-traffic-manager-can-correctly-determine-the-exact-geographic-location-of-the-user-in-every-case"></a>É garantido que o Gestor de Tráfego pode determinar corretamente a localização geográfica exata do utilizador em todos os casos?

Não, o Gestor de Tráfego não pode garantir que a região geográfica que inferimos a partir do endereço IP fonte de uma consulta dNS corresponderá sempre à localização do utilizador devido às seguintes razões:

- Em primeiro lugar, como descrito nas FAQ anteriores, o endereço IP de origem que vemos é o de um resolver DNS que faz a pesquisa em nome do utilizador. Embora a localização geográfica do dNS resolver seja um bom proxy para a localização geográfica do utilizador, também pode ser diferente dependendo da pegada do serviço dNS resolver e do serviço específico de DNS resolver que um cliente escolheu usar. Como exemplo, um cliente localizado na Malásia poderia especificar nas definições do seu dispositivo usar um serviço dNS resolver cujo servidor DNS em Singapura pode ser escolhido para lidar com as resoluções de consulta para esse utilizador/dispositivo. Nesse caso, o Gestor de Tráfego só pode ver o endereço IP do resolver que corresponde à localização de Singapura. Consulte também as FAQ anteriores relativas ao suporte de endereço de subnet do cliente nesta página.

- Em segundo lugar, o Gestor de Tráfego usa um mapa interno para fazer o endereço IP para a tradução da região geográfica. Embora este mapa seja validado e atualizado continuamente para aumentar a sua precisão e explicar a evolução da natureza da internet, existe ainda a possibilidade de a nossa informação não ser uma representação exata da localização geográfica de todos os PI endereços.

###  <a name="does-an-endpoint-need-to-be-physically-located-in-the-same-region-as-the-one-it-is-configured-with-for-geographic-routing"></a>Será que um ponto final precisa de ser fisicamente localizado na mesma região que aquele com que está configurado para o encaminhamento geográfico?

Não, a localização do ponto final não impõe restrições às regiões que lhe podem ser mapeadas. Por exemplo, um ponto final na região do Azure Central dos EUA pode ter todos os utilizadores da Índia direcionados para ele.

### <a name="can-i-assign-geographic-regions-to-endpoints-in-a-profile-that-is-not-configured-to-do-geographic-routing"></a>Posso atribuir às regiões geográficas pontos finais num perfil que não está configurado para fazer encaminhamento geográfico?

Sim, se o método de encaminhamento de um perfil não for geográfico, pode utilizar a [API REST API](https://docs.microsoft.com/rest/api/trafficmanager/) do Gestor de Tráfego Azure para atribuir regiões geográficas a pontos finais nesse perfil. No caso de perfis de tipo de encaminhamento não geográfico, esta configuração é ignorada. Se alterar tal perfil para o tipo de encaminhamento geográfico mais tarde, o Traffic Manager pode utilizar esses mapeamentos.

### <a name="why-am-i-getting-an-error-when-i-try-to-change-the-routing-method-of-an-existing-profile-to-geographic"></a>Porque é que estou a ter um erro quando tento mudar o método de encaminhamento de um perfil existente para a Geographic?

Todos os pontos finais sob um perfil com encaminhamento geográfico precisam de ter pelo menos uma região mapeada para ele. Para converter um perfil existente em tipo de encaminhamento geográfico, é necessário primeiro associar as regiões geográficas a todos os seus pontos finais utilizando a [API REST API](https://docs.microsoft.com/rest/api/trafficmanager/) do Gestor de Tráfego Azure antes de mudar o tipo de encaminhamento para geográfico. Se utilizar o portal, primeiro elimine os pontos finais, altere o método de encaminhamento do perfil para geográfico e, em seguida, adicione os pontos finais juntamente com o mapeamento da sua região geográfica.

### <a name="why-is-it-strongly-recommended-that-customers-create-nested-profiles-instead-of-endpoints-under-a-profile-with-geographic-routing-enabled"></a>Por que é fortemente recomendado que os clientes criem perfis aninhados em vez de pontos finais sob um perfil com encaminhamento geográfico habilitado?

Uma região só pode ser atribuída a um ponto final dentro de um perfil se estiver a utilizar o método de encaminhamento geográfico. Se esse ponto final não for um tipo aninhado com um perfil infantil ligado a ele, se esse ponto final não for saudável, o Traffic Manager continua a enviar tráfego para ele, uma vez que a alternativa de não enviar qualquer tráfego não é melhor. O Gestor de Tráfego não falha em outro ponto final, mesmo quando a região atribuída é um "pai" da região atribuída ao ponto final que não foi saudável (por exemplo, se um ponto final que tem região Espanha não for saudável, não falhamos em outro ponto final que tem a região que a Europa lhe atribuiu). Isto é feito para garantir que o Traffic Manager respeita os limites geográficos que um cliente criou no seu perfil. Para obter o benefício de falhar em outro ponto final quando um ponto final não é saudável, recomenda-se que as regiões geográficas sejam atribuídas a perfis aninhados com vários pontos finais dentro dele em vez de pontos finais individuais. Desta forma, se um ponto final no perfil da criança aninhado falhar, o tráfego pode falhar em outro ponto final dentro do mesmo perfil infantil aninhada.

### <a name="are-there-any-restrictions-on-the-api-version-that-supports-this-routing-type"></a>Existem restrições na versão API que suporte este tipo de encaminhamento?

Sim, apenas a versão API 2017-03-01 e mais recente suporta o tipo de encaminhamento Geográfico. Quaisquer versões API mais antigas não podem ser usadas para criar perfis do tipo de encaminhamento geográfico ou atribuir regiões geográficas a pontos finais. Se uma versão API mais antiga for utilizada para recuperar perfis de uma subscrição do Azure, qualquer perfil do tipo de encaminhamento Geográfico não é devolvido. Além disso, ao utilizar versões API mais antigas, qualquer perfil devolvido com pontos finais com uma atribuição de região geográfica, não tem a sua atribuição da região geográfica mostrada.

## <a name="traffic-manager-subnet-traffic-routing-method"></a>Método de encaminhamento de tráfego subnet gestor de tráfego de tráfego

### <a name="what-are-some-use-cases-where-subnet-routing-is-useful"></a>Quais são os casos de utilização em que o encaminhamento de sub-rede é útil?

O encaminhamento de sub-redes permite-lhe diferenciar a experiência que oferece para conjuntos específicos de utilizadores identificados pela fonte IP dos seus pedidos de DNS endereço IP. Um exemplo seria mostrar diferentes conteúdos se os utilizadores estiverem a ligar-se a um website a partir do seu QG corporativo. Outro seria restringir os utilizadores de certos ISPs a apenas aceder a pontos finais que suportam apenas ligações IPv4 se esses ISPs tiverem um desempenho sub-par quando o IPv6 for utilizado.
Outra razão para usar o método de encaminhamento subnet é em conjunto com outros perfis num conjunto de perfis aninhado. Por exemplo, se quiser utilizar o método de encaminhamento geográfico para geo-esgrima dos seus utilizadores, mas para um ISP específico você quer fazer um método de encaminhamento diferente, você pode ter um método de encaminhamento de perfil com subnet de perfil como o perfil principal e sobrepor que ISP para usar uma criança específica perfil e têm o perfil geographic padrão para todos os outros.

### <a name="how-does-traffic-manager-know-the-ip-address-of-the-end-user"></a>Como é que o Gestor de Tráfego conhece o endereço IP do utilizador final?

Os dispositivos de utilizador final normalmente usam um resolver DNS para fazer a pesquisa DNS em seu nome. O IP de saída de tais resolvers é o que o Gestor de Tráfego vê como a fonte IP. Além disso, o método de encaminhamento da Subnet também procura ver se existem informações edns0 Extended Client Subnet (ECS) que foram passadas com o pedido. Se houver informações do CeC, este é o endereço utilizado para determinar o encaminhamento. Na ausência de informações relativas ao SE, o IP de origem da consulta é utilizado para efeitos de encaminhamento.

### <a name="how-can-i-specify-ip-addresses-when-using-subnet-routing"></a>Como posso especificar endereços IP ao utilizar o encaminhamento subnet?

Os endereços IP para associar a um ponto final podem ser especificados de duas formas. Em primeiro lugar, pode utilizar a notação de octeto decimal pontilhada quad com endereços de início e fim para especificar o intervalo (por exemplo, 1.2.3.4-5.6.7.8 ou 3.4.5.6-3.4.5.6). Em segundo lugar, pode utilizar a notação CIDR para especificar o intervalo (por exemplo, 1.2.3.0/24). Pode especificar várias gamas e pode utilizar ambos os tipos de notação num conjunto de intervalos. Aplicam-se algumas restrições.

-    Não pode ter sobreposição de gamas de endereços, uma vez que cada IP precisa de ser mapeado apenas para um ponto final
-    O endereço de início não pode ser mais do que o endereço final
-    No caso da notação CIDR, o endereço IP antes do '/' deve ser o endereço de início dessa gama (por exemplo, 1.2.3.0/24 é válido, mas 1.2.3.4.4/24 não é válido)

### <a name="how-can-i-specify-a-fallback-endpoint-when-using-subnet-routing"></a>Como posso especificar um ponto final de recuo quando se usa o encaminhamento subnet?

Num perfil com o encaminhamento da Subnet, se tiver um ponto final sem subredes mapeadas, qualquer pedido que não corresponda a outros pontos finais será direcionado para aqui. É altamente recomendável que tenha um ponto final de recuo no seu perfil, uma vez que o Traffic Manager devolverá uma resposta NXDOMAIN se um pedido chegar e não for mapeado para quaisquer pontos finais ou se estiver mapeado para um ponto final, mas esse ponto final não é saudável.

### <a name="what-happens-if-an-endpoint-is-disabled-in-a-subnet-routing-type-profile"></a>O que acontece se um ponto final for desativado num perfil do tipo de encaminhamento subnet?

Num perfil com o encaminhamento da Subnet, se tiver um ponto final com o desativado, o Traffic Manager comportar-se-á como se esse ponto final e os mapeamentos da sub-rede não existissem. Se uma consulta que teria combinado com o seu mapeamento de endereço IP for recebida e o ponto final estiver desativado, o Traffic Manager devolverá um ponto final de recuo (um sem mapeamentos) ou se tal ponto final não estiver presente, devolverá uma resposta NXDOMAIN.

## <a name="traffic-manager-multivalue-traffic-routing-method"></a>Método de encaminhamento de tráfego multiValue do Gestor de Tráfego

### <a name="what-are-some-use-cases-where-multivalue-routing-is-useful"></a>Quais são os casos de utilização em que o encaminhamento MultiValue é útil?

O encaminhamento MultiValue devolve vários pontos finais saudáveis numa única resposta de consulta. A principal vantagem disso é que, se um ponto final não é saudável, o cliente tem mais opções para retentar sem fazer outra chamada DNS (que pode devolver o mesmo valor a partir de uma cache a montante). Isto aplica-se a aplicações sensíveis à disponibilidade que pretendem minimizar o tempo de inatividade.
Outra utilização para o método de encaminhamento MultiValue é se um ponto final é "dual-homed" para os endereços IPv4 e IPv6 e você quer dar ao chamador ambas as opções para escolher quando iniciar uma ligação ao ponto final.

### <a name="how-many-endpoints-are-returned-when-multivalue-routing-is-used"></a>Quantos pontos finais são devolvidos quando o encaminhamento MultiValue é usado?

Pode especificar o número máximo de pontos finais a devolver e o MultiValue não voltará mais do que muitos pontos finais saudáveis quando uma consulta é recebida. O valor máximo possível para esta configuração é 10.

### <a name="will-i-get-the-same-set-of-endpoints-when-multivalue-routing-is-used"></a>Vou ter o mesmo conjunto de pontos finais quando o encaminhamento MultiValue for usado?

Não podemos garantir que o mesmo conjunto de pontos finais será devolvido em cada consulta. Isto também é afetado pelo facto de alguns dos pontos finais poderem não ser saudáveis, altura em que não serão incluídos na resposta.

## <a name="real-user-measurements"></a>Medidas Reais de Utilizadores

### <a name="what-are-the-benefits-of-using-real-user-measurements"></a>Quais são os benefícios da utilização de Medições reais do Utilizador?

Quando utiliza o método de encaminhamento de desempenho, o Traffic Manager escolhe a melhor região Azure para o utilizador final ligar-se inspecionando a fonte IP e EDNS Client Subnet (se for passado) e verificando-o contra a inteligência de latência da rede que o serviço mantém. As Medições Reais do Utilizador melhoram-no para a sua base de utilizadores final, tendo a sua experiência contribuído para esta tabela de latências, além de garantir que esta tabela abrange adequadamente as redes de utilizadores finais de onde os seus utilizadores finais se ligam ao Azure. Isto leva a uma maior precisão no encaminhamento do utilizador final.

### <a name="can-i-use-real-user-measurements-with-non-azure-regions"></a>Posso usar medições reais do utilizador com regiões não-Azure?

Medições reais de utilizadores medem e reportam apenas a latência para chegar às regiões de Azure. Se estiver a utilizar o encaminhamento baseado no desempenho com pontos finais alojados em regiões não-Azure, ainda pode beneficiar desta funcionalidade tendo uma maior informação de latência sobre a região representativa do Azure que selecionou para estar associada a este ponto final.

### <a name="which-routing-method-benefits-from-real-user-measurements"></a>Que método de encaminhamento beneficia das Medições reais do Utilizador?

As informações adicionais obtidas através das Medições reais do Utilizador aplicam-se apenas aos perfis que utilizam o método de encaminhamento de desempenho. O link Real User Measurements está disponível em todos os perfis quando o visualiza através do portal Azure.

### <a name="do-i-need-to-enable-real-user-measurements-each-profile-separately"></a>Preciso de ativar as medições reais do utilizador de cada perfil separadamente?

Não, só precisa de o ativar uma vez por subscrição e todas as informações de latência medidas e comunicadas estão disponíveis para todos os perfis.

### <a name="how-do-i-turn-off-real-user-measurements-for-my-subscription"></a>Como desligo as Medições reais do Utilizador para a minha subscrição?

Pode parar de acumular encargos relacionados com medições reais do utilizador quando deixar de recolher e enviar medições de latência da sua aplicação cliente. Por exemplo, ao medir o JavaScript incorporado nas páginas web, pode parar de utilizar esta funcionalidade removendo o JavaScript ou desligando a sua invocação quando a página é renderizada.

Também pode desligar as Medições reais do Utilizador, apagando a sua chave. Uma vez eliminada a tecla, quaisquer medições enviadas ao Traffic Manager com essa tecla são descartadas.

### <a name="can-i-use-real-user-measurements-with-client-applications-other-than-web-pages"></a>Posso usar Medições reais do utilizador com aplicações de clientes que não as páginas web?

Sim, as Medições reais do Utilizador foram concebidas para ingerir dados recolhidos através de diferentes tipos de clientes de utilizadores finais. Este FAQ será atualizado à medida que novos tipos de aplicações de clientes forem suportados.

### <a name="how-many-measurements-are-made-each-time-my-real-user-measurements-enabled-web-page-is-rendered"></a>Quantas medidas são feitas cada vez que a minha página web ativada pelo utilizador real é renderizada?

Quando as medições reais do utilizador são utilizadas com a medição fornecida javaScript fornecida, cada renderização de cada página resulta em seis medições sendo tomadas. Estes são depois comunicados ao serviço do Gestor de Tráfego. É cobrado por esta funcionalidade com base no número de medições reportadas ao serviço Traffic Manager. Por exemplo, se o utilizador navegar para longe da sua página web enquanto as medições estão a ser efetuadas, mas antes de ser reportada, essas medições não são consideradas para efeitos de faturação.

### <a name="is-there-a-delay-before-real-user-measurements-script-runs-in-my-webpage"></a>Há algum atraso antes que o script de Medições de Utilizador Real seja executado na minha página web?

Não, não há atraso programado antes do guião ser invocado.

### <a name="can-i-use-real-user-measurements-with-only-the-azure-regions-i-want-to-measure"></a>Posso usar medições reais de utilizador apenas com as regiões Azure que quero medir?

Não, cada vez que é invocado, o script Real User Measurements mede um conjunto de seis regiões Azure determinadas pelo serviço. Este conjunto muda entre diferentes invocações e quando um grande número dessas invocações acontecem, a cobertura de medição estende-se por diferentes regiões do Azure.

### <a name="can-i-limit-the-number-of-measurements-made-to-a-specific-number"></a>Posso limitar o número de medições feitas a um número específico?

A medição JavaScript está incorporada na sua página web e está em total controlo sobre quando começar e parar de usá-lo. Enquanto o serviço de Gestor de Tráfego receber um pedido de uma lista das regiões de Azure a medir, um conjunto de regiões são devolvidos.

### <a name="can-i-see-the-measurements-taken-by-my-client-application-as-part-of-real-user-measurements"></a>Posso ver as medidas feitas pela aplicação do meu cliente como parte das Medições reais do Utilizador?

Uma vez que a lógica de medição é executada a partir da sua aplicação cliente, você está em total controlo do que acontece, incluindo ver as medidas de latência. O Gestor de Tráfego não reporta uma visão agregada das medições recebidas sob a chave ligada à sua subscrição.

### <a name="can-i-modify-the-measurement-script-provided-by-traffic-manager"></a>Posso modificar o script de medição fornecido pelo Traffic Manager?

Enquanto você está controlando o que está incorporado na sua página web, nós o desencorajamos fortemente de fazer quaisquer alterações no script de medição para garantir que ele mede e reporta corretamente as latenciências.

### <a name="will-it-be-possible-for-others-to-see-the-key-i-use-with-real-user-measurements"></a>Será possível que outros vejam a chave que uso com medições reais do utilizador?

Quando incorporar o script de medição numa página web, será possível que outros vejam o script e a sua chave De medidas de utilizador real (RUM). Mas é importante saber que esta chave é diferente do seu id de subscrição e é gerada pelo Traffic Manager para ser usada apenas para este fim. Saber a sua chave RUM não comprometerá a segurança da sua conta Azure.

### <a name="can-others-abuse-my-rum-key"></a>Outros podem abusar da minha chave RUM?

Embora seja possível que outros utilizem a sua chave para enviar informações erradas para o Azure, algumas medidas erradas não alterarão o encaminhamento, uma vez que é tida em conta, juntamente com todas as outras medidas que recebemos. Se precisar de mudar as chaves, pode regerar a chave em que a tecla antiga fica descartada.

### <a name="do-i-need-to-put-the-measurement-javascript-in-all-my-web-pages"></a>Preciso colocar a medida JavaScript em todas as minhas páginas web?

As medições reais do utilizador proporcionam mais valor à medida que o número de medições aumenta. Dito isto, a decisão é sua sobre se precisa colocá-lo em todas as suas páginas web ou em alguns selecionados. A nossa recomendação é começar por colocá-la na sua página mais visitada, onde se espera que um utilizador permaneça nessa página cinco segundos ou mais.

### <a name="can-information-about-my-end-users-be-identified-by-traffic-manager-if-i-use-real-user-measurements"></a>As informações sobre os meus utilizadores finais podem ser identificadas pelo Traffic Manager se eu utilizar medições reais do utilizador?

Quando for utilizada a medição fornecida JavaScript, o Traffic Manager terá visibilidade no endereço IP do cliente do utilizador final e no endereço IP fonte do dNS local que eles usam. O Traffic Manager utiliza o endereço IP do cliente apenas depois de o ter truncado para não conseguir identificar o utilizador final específico que enviou as medições.

### <a name="does-the-webpage-measuring-real-user-measurements-need-to-be-using-traffic-manager-for-routing"></a>A página web que mede as medições reais do utilizador precisa de estar a utilizar o Traffic Manager para o encaminhamento?

Não, não precisa de usar o Gerente de Trânsito. O lado de encaminhamento do Traffic Manager funciona separadamente da parte de Medição de Utilizador Real e embora seja uma boa ideia tê-los ambos na mesma propriedade web, eles não precisam de estar.

### <a name="do-i-need-to-host-any-service-on-azure-regions-to-use-with-real-user-measurements"></a>Preciso de receber algum serviço nas regiões do Azure para utilizar com medições reais do utilizador?

Não, não precisa de hospedar nenhum componente do lado do servidor no Azure para que as medições reais do utilizador funcionem. A imagem de pixel única descarregada pela medição JavaScript e pelo serviço que o executa em diferentes regiões do Azure é hospedada e gerida pelo Azure. 

### <a name="will-my-azure-bandwidth-usage-increase-when-i-use-real-user-measurements"></a>O meu uso de largura de banda Azure aumentará quando utilizar medições reais do utilizador?

Como mencionado na resposta anterior, os componentes do lado do servidor das Medições reais do Utilizador são propriedade e geridos pelo Azure. Isto significa que o seu uso de largura de banda Azure não aumentará porque utiliza medições reais do utilizador. Isto não inclui qualquer utilização de largura de banda fora do que o Azure cobra. Minimizamos a largura de banda utilizada ao descarregar apenas uma única imagem de pixel para medir a latência para uma região do Azure. 

## <a name="traffic-view"></a>Vista de Tráfego

### <a name="what-does-traffic-view-do"></a>O que faz a Vista de Trânsito?

Traffic View é uma funcionalidade do Traffic Manager que o ajuda a entender mais sobre os seus utilizadores e como está a sua experiência. Utiliza as consultas recebidas pelo Traffic Manager e as tabelas de inteligência de latência da rede que o serviço mantém para lhe fornecer o seguinte:

- As regiões de onde os seus utilizadores estão a ligar-se aos seus pontos finais em Azure.
- O volume de utilizadores que se ligam a partir destas regiões.
- As regiões de Azure para as quais estão a ser encaminhadas.
- A sua experiência de latência nestas regiões de Azure.

Esta informação está disponível para que possa consumir através de sobreposições geográficas e vistas tabulares no portal, além de estar disponível como dados brutos para você descarregar.

### <a name="how-can-i-benefit-from-using-traffic-view"></a>Como posso beneficiar da utilização da Traffic View?

A Traffic View dá-lhe a visão geral do tráfego que os seus perfis do Traffic Manager recebem. Em particular, pode ser usado para entender de onde a sua base de utilizador se conecta e igualmente importante qual é a sua experiência média de latência. Em seguida, pode utilizar esta informação para encontrar áreas nas quais precisa de se concentrar, por exemplo, expandindo a sua pegada Azure para uma região que pode servir os utilizadores com menor latência. Outra visão que pode obter da utilização da Traffic View é ver os padrões de tráfego para diferentes regiões que, por sua vez, podem ajudá-lo a tomar decisões sobre o aumento ou diminuição de inventar nessas regiões.

### <a name="how-is-traffic-view-different-from-the-traffic-manager-metrics-available-through-azure-monitor"></a>Como é que a Traffic View é diferente das métricas do Traffic Manager disponíveis através do monitor Azure?

O Monitor Azure pode ser usado para entender a um nível agregado o tráfego recebido pelo seu perfil e seus pontos finais. Também lhe permite acompanhar o estado de saúde dos pontos finais expondo os resultados do exame de saúde. Quando precisa de ir além destes e compreender a experiência do utilizador final em ligar-se ao Azure a nível regional, o Traffic View pode ser usado para o conseguir.

### <a name="does-traffic-view-use-edns-client-subnet-information"></a>A Vista de Tráfego utiliza informações da Subnet do cliente EDNS?

As consultas do DNS servidas pelo Gestor de Tráfego Azure consideram as informações da ECS para aumentar a precisão do encaminhamento. Mas ao criar o conjunto de dados que mostra de onde os utilizadores estão a ligar-se, o Traffic View está a utilizar apenas o endereço IP do resolver dNS.

### <a name="how-many-days-of-data-does-traffic-view-use"></a>Quantos dias de dados o Traffic View utiliza?

A Traffic View cria a sua produção processando os dados dos sete dias anteriores ao dia anterior, quando é visto por si. Esta é uma janela em movimento e os dados mais recentes serão usados cada vez que você visitar.

### <a name="how-does-traffic-view-handle-external-endpoints"></a>Como é que a Traffic View lida com pontos finais externos?

Quando utiliza pontos finais externos alojados fora das regiões de Azure num perfil do Traffic Manager, pode optar por mapeá-lo para uma região do Azure que é um proxy pelas suas características de latência (isto é, de facto, necessário se utilizar o método de encaminhamento de desempenho). Se tiver esta região de Azure a mapear, as métricas de latência da região de Azure serão usadas na criação da saída do Traffic View. Se não for especificada nenhuma região de Azure, as informações de latência ficarão vazias nos dados desses pontos finais externos.

### <a name="do-i-need-to-enable-traffic-view-for-each-profile-in-my-subscription"></a>Preciso de ativar a Vista de Tráfego para cada perfil na minha subscrição?

Durante o período de pré-visualização, o Traffic View foi ativado a nível de subscrição. Como parte das melhorias que fizemos antes da disponibilidade geral, pode agora ativar a Visualização de Tráfego a um nível de perfil, permitindo-lhe ter mais habilitação granular desta funcionalidade. Por predefinição, a Visualização de Tráfego será desativada para um perfil.

>[!NOTE]
>Se ativou a Visualização de Tráfego a um nível de subscrição durante o tempo de pré-visualização, agora precisa de o reativar para cada um dos perfis sob essa subscrição.
 
### <a name="how-can-i-turn-off-traffic-view"></a>Como posso desligar a Vista de Trânsito?

Pode desativar a Vista de Tráfego para qualquer perfil que utilize o Portal ou a API REST. 

### <a name="how-does-traffic-view-billing-work"></a>Como funciona a faturação da Traffic View?

Os preços do Traffic View baseiam-se no número de pontos de dados utilizados para criar a saída. Atualmente, o único tipo de dados suportado são as consultas que o seu perfil recebe. Além disso, só é cobrado para o processamento que foi feito quando tem a Traffic View ativada. Isto significa que, se ativar a Visualização de Tráfego durante algum período de tempo num mês e desligá-la durante outras ocasiões, apenas os pontos de dados processados enquanto tiver a funcionalidade ativada conta para a sua conta.

## <a name="traffic-manager-endpoints"></a>Pontos finais do Gestor de tráfego

### <a name="can-i-use-traffic-manager-with-endpoints-from-multiple-subscriptions"></a>Posso usar o Traffic Manager com pontos finais de várias subscrições?

A utilização de pontos finais de várias subscrições não é possível com aplicações Web Do Azure. As Aplicações Web Do Azure exigem que qualquer nome de domínio personalizado utilizado com Aplicações Web seja utilizado apenas numa única subscrição. Não é possível utilizar Aplicações Web a partir de múltiplas subscrições com o mesmo nome de domínio.

Para outros tipos de pontos finais, é possível utilizar o Traffic Manager com pontos finais de mais de uma subscrição. No Gestor de Recursos, os pontos finais de qualquer subscrição podem ser adicionados ao Traffic Manager, desde que a pessoa que configura o perfil do Gestor de Tráfego tenha lido o acesso ao ponto final. Estas permissões podem ser concedidas utilizando o controlo de acesso baseado em funções do Gestor de [Recursos Azure (RBAC)](../role-based-access-control/role-assignments-portal.md).

### <a name="can-i-use-traffic-manager-with-cloud-service-staging-slots"></a>Posso usar o Gestor de Tráfego com slots de 'Staging' do Serviço cloud?

Sim. As faixas horárias de 'staging' do Cloud Service podem ser configuradas no Traffic Manager como pontos finais externos. Os controlos de saúde continuam a ser cobrados à taxa De pontos Finais do Azure.

### <a name="does-traffic-manager-support-ipv6-endpoints"></a>O Gestor de Tráfego suporta pontos finais IPv6?

O Traffic Manager não fornece atualmente servidores de nome seledores iPv6. No entanto, o Traffic Manager ainda pode ser utilizado por clientes IPv6 que ligam aos pontos finais do IPv6. Um cliente não faz pedidos de DNS diretamente ao Traffic Manager. Em vez disso, o cliente usa um serviço DNS recursivo. Um cliente só iPv6 envia pedidos para o serviço DNS recursivo via IPv6. Em seguida, o serviço recursivo deve ser capaz de contactar os servidores de nome do Gestor de Tráfego utilizando o IPv4.

O Gestor de Tráfego responde com o nome DNS ou endereço IP do ponto final. Para suportar um ponto final IPv6, existem duas opções. Pode adicionar o ponto final como um nome DNS que tem um registo AAAA associado e o Traffic Manager verificará esse ponto final e devolvê-lo-á como um tipo de registo CNAME na resposta à consulta. Também pode adicionar esse ponto final diretamente utilizando o endereço IPv6 e o Traffic Manager devolverá um registo do tipo AAAA na resposta à consulta.

### <a name="can-i-use-traffic-manager-with-more-than-one-web-app-in-the-same-region"></a>Posso usar o Traffic Manager com mais de uma Web App na mesma região?

Normalmente, o Traffic Manager é usado para direcionar o tráfego para aplicações implementadas em diferentes regiões. No entanto, também pode ser utilizado quando uma aplicação tem mais de uma implantação na mesma região. Os pontos finais do Traffic Manager Azure não permitem que mais do que um ponto final da Web App da mesma região azure seja adicionado ao mesmo perfil do Gestor de Tráfego.

### <a name="how-do-i-move-my-traffic-manager-profiles-azure-endpoints-to-a-different-resource-group-or-subscription"></a>Como posso mover os pontos finais do meu perfil de Gestor de Tráfego Azure para um grupo de recursos ou subscrição diferente?

Os pontos finais azure que estão associados a um perfil do Gestor de Tráfego são rastreados usando os seus IDs de recursos. Quando um recurso Azure que está a ser usado como ponto final (por exemplo, IP público, Serviço de Nuvem Clássica, WebApp ou outro perfil do Traffic Manager usado de forma aninhada) é transferido para um grupo de recursos ou subscrição diferente, o seu id de recursos muda. Neste cenário, atualmente, tem de atualizar o perfil do Gestor de Tráfego, apagando primeiro e adicionando os pontos finais ao perfil.

## <a name="traffic-manager-endpoint-monitoring"></a>Monitorização do ponto final do Gestor de Tráfego

### <a name="is-traffic-manager-resilient-to-azure-region-failures"></a>O Gestor de Tráfego é resiliente às falhas da região de Azure?

O Traffic Manager é um componente fundamental da entrega de aplicações altamente disponíveis no Azure.
Para proporcionar uma elevada disponibilidade, o Gestor de Tráfego deve ter um nível excepcionalmente elevado de disponibilidade e ser resiliente à falha regional.

Por design, os componentes do Traffic Manager são resistentes a uma falha completa de qualquer região azure. Esta resiliência aplica-se a todos os componentes do Traffic Manager: os servidores de nome DNS, a API, a camada de armazenamento e o serviço de monitorização do ponto final.

No caso improvável de uma paragem de toda uma região de Azure, espera-se que o Gestor de Tráfego continue a funcionar normalmente. As aplicações implementadas em várias regiões do Azure podem contar com o Gestor de Tráfego para direcionar o tráfego para uma instância disponível da sua aplicação.

### <a name="how-does-the-choice-of-resource-group-location-affect-traffic-manager"></a>Como é que a escolha da localização do grupo de recursos afeta o Gestor de Tráfego?

Traffic Manager é um serviço único e global. Não é regional. A escolha da localização do grupo de recursos não faz diferença para os perfis do Gestor de Tráfego implantados nesse grupo de recursos.

O Gestor de Recursos Azure exige que todos os grupos de recursos especifiquem uma localização, que determina a localização padrão dos recursos implantados nesse grupo de recursos. Quando se cria um perfil de Gestor de Tráfego, é criado num grupo de recursos. Todos os perfis do Traffic Manager usam **globalmente** como sua localização, sobrepondo-se ao padrão do grupo de recursos.

### <a name="how-do-i-determine-the-current-health-of-each-endpoint"></a>Como determino a saúde atual de cada ponto final?

O estado atual de monitorização de cada ponto final, para além do perfil geral, é apresentado no portal Azure. Esta informação também está disponível através do Traffic Monitor [REST API,](https://msdn.microsoft.com/library/azure/mt163667.aspx) [PowerShell cmdlets](https://docs.microsoft.com/powershell/module/az.trafficmanager)e [cross-platform Azure CLI](../cli-install-nodejs.md).

Também pode utilizar o Monitor Azure para acompanhar a saúde dos seus pontos finais e ver uma representação visual dos mesmos. Para mais informações sobre a utilização do Monitor Azure, consulte a documentação de [monitorização do Azure.](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics)

### <a name="can-i-monitor-https-endpoints"></a>Posso monitorizar os pontos finais HTTPS?

Sim. Traffic Manager suporta sondagem sobre HTTPS. Configure **HTTPS** como o protocolo na configuração de monitorização.

O gestor de tráfego não pode fornecer qualquer validação de certificado, incluindo:

* Os certificados do lado do servidor não são validados
* Os certificados do lado do servidor SNI não são validados
* Os certificados de cliente não são suportados

### <a name="do-i-use-an-ip-address-or-a-dns-name-when-adding-an-endpoint"></a>Uso um endereço IP ou um nome DNS ao adicionar um ponto final?

O Traffic Manager suporta a adição de pontos finais utilizando três formas de os encaminhar – como nome DNS, como um endereço IPv4 e como endereço IPv6. Se o ponto final for adicionado como iPv4 ou IPv6, a resposta da consulta será de tipo a disco A ou AAAA, respectivamente. Se o ponto final foi adicionado como um nome DNS, então a resposta de consulta será do tipo cNAME de gravação. A adição de pontos finais como endereço IPv4 ou IPv6 só é permitida se o ponto final for do tipo **Externo**.
Todos os métodos de encaminhamento e definições de monitorização são suportados pelos três tipos de endereçamento de ponto final.

### <a name="what-types-of-ip-addresses-can-i-use-when-adding-an-endpoint"></a>Que tipos de endereços IP posso usar ao adicionar um ponto final?

O Traffic Manager permite-lhe utilizar endereços IPv4 ou IPv6 para especificar pontos finais. Existem algumas restrições que estão listadas abaixo:

- Não são permitidos endereços que correspondam a espaços de endereços IP privados reservados. Estes endereços incluem os chamados em RFC 1918, RFC 6890, RFC 5737, RFC 3068, RFC 2544 e RFC 5771
- O endereço não deve conter quaisquer números de porta (pode especificar as portas a utilizar nas definições de configuração do perfil)
- Não há dois pontos finais no mesmo perfil que podem ter o mesmo endereço IP alvo

### <a name="can-i-use-different-endpoint-addressing-types-within-a-single-profile"></a>Posso usar diferentes tipos de endereços de ponto final dentro de um único perfil?

Não, o Traffic Manager não permite misturar tipos de endereços de ponto final dentro de um perfil, exceto no caso de um perfil com o tipo de encaminhamento MultiValue onde pode misturar tipos de endereços IPv4 e IPv6

### <a name="what-happens-when-an-incoming-querys-record-type-is-different-from-the-record-type-associated-with-the-addressing-type-of-the-endpoints"></a>O que acontece quando o tipo de registo de uma consulta é diferente do tipo de registo associado ao tipo de endereçamento dos pontos finais?

Quando uma consulta é recebida contra um perfil, o Gestor de Tráfego primeiro encontra o ponto final que precisa de ser devolvido de acordo com o método de encaminhamento especificado e o estado de saúde dos pontos finais. Em seguida, olha para o tipo de registo solicitado na consulta de entrada e no tipo de disco associado ao ponto final antes de devolver uma resposta com base na tabela abaixo.

Para perfis com qualquer método de encaminhamento que não seja o MultiValue:

|Pedido de consulta de entrada|     Tipo endpoint|     Resposta Fornecida|
|--|--|--|
|QUALQUER |    A / AAAA / CNAME |    Ponto final do alvo| 
|A |    A / CNAME |    Ponto final do alvo|
|A |    AAAA |    NODATA |
|AAAA |    AAAA / CNAME |    Ponto final do alvo|
|AAAA |    A |    NODATA |
|CNAME |    CNAME |    Ponto final do alvo|
|CNAME     |A / AAAAA |    NODATA |
|

Para perfis com método de encaminhamento definido para MultiValue:

|Pedido de consulta de entrada|     Tipo endpoint |    Resposta Fornecida|
|--|--|--|
|QUALQUER |    Mistura de A e AAAA |    Pontos Finais-Alvo|
|A |    Mistura de A e AAAA |    Apenas pontos finais-alvo do tipo A|
|AAAA    |Mistura de A e AAAA|     Apenas pontos finais de destino do tipo AAAA|
|CNAME |    Mistura de A e AAAA |    NODATA |

### <a name="can-i-use-a-profile-with-ipv4--ipv6-addressed-endpoints-in-a-nested-profile"></a>Posso usar um perfil com iPv4 / IPv6 endereçado pontos finais num perfil aninhado?

Sim, pode com a exceção de que um perfil de tipo MultiValue não pode ser um perfil dos pais num conjunto de perfis aninhado.

### <a name="i-stopped-an-web-application-endpoint-in-my-traffic-manager-profile-but-i-am-not-receiving-any-traffic-even-after-i-restarted-it-how-can-i-fix-this"></a>Parei um ponto final de aplicação web no meu perfil de Traffic Manager, mas não estou a receber nenhum tráfego mesmo depois de o reiniciar. Como posso consertar isto?

Quando um ponto final de aplicação web Azure é parado, o Traffic Manager deixa de verificar a sua saúde e reinicia os controlos de saúde apenas depois de detetar que o ponto final recomeçou. Para evitar este atraso, desative e, em seguida, reative esse ponto final no perfil do Gestor de Tráfego depois de reiniciar o ponto final.

### <a name="can-i-use-traffic-manager-even-if-my-application-does-not-have-support-for-http-or-https"></a>Posso usar o Traffic Manager mesmo que a minha aplicação não tenha suporte para HTTP ou HTTPS?

Sim. Pode especificar o TCP como o protocolo de monitorização e o Traffic Manager pode iniciar uma ligação TCP e esperar por uma resposta a partir do ponto final. Se o ponto final responder ao pedido de ligação com uma resposta para estabelecer a ligação, dentro do prazo, então esse ponto final é marcado como saudável.

### <a name="what-specific-responses-are-required-from-the-endpoint-when-using-tcp-monitoring"></a>Que respostas específicas são necessárias a partir do ponto final quando se utiliza a monitorização do TCP?

Quando a monitorização do TCP é utilizada, o Gestor de Tráfego inicia um aperto de mão de TCP a três vias enviando um pedido de SYN para ponto final na porta especificada. Em seguida, aguarda uma resposta SYN-ACK do ponto final por um período de tempo (especificado nas definições de tempo limite).

- Se uma resposta SYN-ACK for recebida dentro do período de tempo limite especificado nas definições de monitorização, então esse ponto final é considerado saudável. Uma FIN ou FIN-ACK é a resposta esperada do Gestor de Tráfego quando termina regularmente uma tomada.
- Se uma resposta SYN-ACK for recebida após o tempo limite especificado, o Gestor de Tráfego responderá com um RST para redefinir a ligação.

### <a name="how-fast-does-traffic-manager-move-my-users-away-from-an-unhealthy-endpoint"></a>A que velocidade o Traffic Manager afasta os meus utilizadores de um ponto final pouco saudável?

O Traffic Manager fornece várias configurações que podem ajudá-lo a controlar o comportamento de falha do seu perfil de Gestor de Tráfego da seguinte forma:

- pode especificar que o Gestor de Tráfego sonda os pontos finais com mais frequência, definindo o Intervalo de Sondagem em 10 segundos. Isto garante que qualquer ponto final que não seja saudável pode ser detetado o mais rapidamente possível. 
- pode especificar quanto tempo esperar antes de um pedido de verificação de saúde (o valor mínimo de tempo é de 5 seg).
- pode especificar quantas falhas podem ocorrer antes que o ponto final seja marcado como insalubre. Este valor pode ser baixo como 0, caso em que o ponto final é marcado insalubre assim que falha o primeiro exame de saúde. No entanto, a utilização do valor mínimo de 0 para o número tolerado de falhas pode levar a que os pontos finais sejam retirados da rotação devido a quaisquer problemas transitórios que possam ocorrer no momento da sondagem.
- pode especificar o tempo de vida (TTL) para que a resposta dNS seja tão baixa como 0. Fazê-lo significa que os resolvers do DNS não podem cache a resposta e cada nova consulta recebe uma resposta que incorpora a informação de saúde mais atualizada que o Gestor de Tráfego tem.

Ao utilizar estas definições, o Traffic Manager pode fornecer falhas abaixo de 10 segundos após um ponto final ficar insalubre e uma consulta de DNS é feita contra o perfil correspondente.

### <a name="how-can-i-specify-different-monitoring-settings-for-different-endpoints-in-a-profile"></a>Como posso especificar diferentes definições de monitorização para diferentes pontos finais num perfil?

As definições de monitorização do Gestor de Tráfego estão a um nível de perfil. Se precisar de utilizar uma definição de monitorização diferente para apenas um ponto final, pode ser feito tendo esse ponto final como um [perfil aninhado](traffic-manager-nested-profiles.md) cujas definições de monitorização são diferentes do perfil dos pais.

### <a name="how-can-i-assign-http-headers-to-the-traffic-manager-health-checks-to-my-endpoints"></a>Como posso atribuir cabeçalhos HTTP ao Gestor de Tráfego cheques de saúde para os meus pontos finais?

O Traffic Manager permite-lhe especificar cabeçalhos personalizados nos controlos de saúde HTTP(S) que inicia nos seus pontos finais. Se quiser especificar um cabeçalho personalizado, pode fazê-lo ao nível do perfil (aplicável a todos os pontos finais) ou especifique-o ao nível do ponto final. Se um cabeçalho for definido em ambos os níveis, então o especificado no nível final irá sobrepor-se ao nível de perfil um.
Um caso de uso comum para isso é especificar os cabeçalhos do anfitrião para que os pedidos do Gestor de Tráfego possam ser encaminhados corretamente para um ponto final alojado num ambiente multi-inquilino. Outro caso de utilização deste é identificar pedidos do Gestor de Tráfego a partir de registos de pedidos HTTP(S) de um ponto final

### <a name="what-host-header-do-endpoint-health-checks-use"></a>Que cabeçalho de hospedeiro usam os controlos de saúde finais?

Se não for fornecida uma definição personalizada do cabeçalho do anfitrião, o cabeçalho do anfitrião utilizado pelo Traffic Manager é o nome DNS do alvo final configurado no perfil, se estiver disponível.

### <a name="what-are-the-ip-addresses-from-which-the-health-checks-originate"></a>Quais são os endereços IP de que provêm os controlos de saúde?

Clique [aqui](https://azuretrafficmanagerdata.blob.core.windows.net/probes/azure/probe-ip-ranges.json) para ver o ficheiro JSON que lista os endereços IP a partir dos quais os controlos de saúde do Gestor de Tráfego podem originar. Reveja os IPs listados no ficheiro JSON para garantir que as ligações de entrada destes endereços IP são permitidas nos pontos finais para verificar o seu estado de saúde.

### <a name="how-many-health-checks-to-my-endpoint-can-i-expect-from-traffic-manager"></a>Quantos exames de saúde posso esperar do Gerente de Tráfego?

O número de verificações de saúde do Gestor de Tráfego que chegam ao seu ponto final depende do seguinte:

- o valor que definiu para o intervalo de monitorização (intervalo menor significa mais pedidos de aterragem no seu ponto final em qualquer período de tempo).
- o número de locais de onde os controlos de saúde são originados (os endereços IP de onde pode esperar que estes controlos estão listados nas FAQ anteriores).

### <a name="how-can-i-get-notified-if-one-of-my-endpoints-goes-down"></a>Como posso ser notificado se um dos meus pontos finais cai?

Uma das métricas fornecidas pelo Traffic Manager é o estado de saúde dos pontos finais num perfil. Pode ver isto como um agregado de todos os pontos finais dentro de um perfil (por exemplo, 75% dos seus pontos finais são saudáveis), ou, a um nível final. As métricas do Traffic Manager são expostas através do Monitor Azure e pode utilizar as suas [capacidades](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md) de alerta para receber notificações quando houver uma alteração no estado de saúde do seu ponto final. Para mais detalhes, consulte [métricas e alertas do Gestor de Tráfego.](traffic-manager-metrics-alerts.md)  

## <a name="traffic-manager-nested-profiles"></a>Perfis aninhados do Gestor de Tráfego

### <a name="how-do-i-configure-nested-profiles"></a>Como configurar perfis aninhados?

Os perfis do Gestor de Tráfego Aninhado podem ser configurados usando tanto o Gestor de Recursos Azure como os clássicos APIs Azure REST, cmdlets Azure PowerShell e comandos azure CLI de plataforma cruzada. São também apoiados através do novo portal Azure.

### <a name="how-many-layers-of-nesting-does-traffic-manger-support"></a>Quantas camadas de nidificação suporta manger de trânsito?

Pode nidificar perfis até 10 níveis de profundidade. Não são permitidos 'loops'.

### <a name="can-i-mix-other-endpoint-types-with-nested-child-profiles-in-the-same-traffic-manager-profile"></a>Posso misturar outros tipos de pontos finais com perfis infantis aninhados, no mesmo perfil do Traffic Manager?

Sim. Não existem restrições sobre como combinar pontos finais de diferentes tipos dentro de um perfil.

### <a name="how-does-the-billing-model-apply-for-nested-profiles"></a>Como é que o modelo de faturação se aplica aos perfis da Nested?

Não há um impacto negativo dos preços na utilização de perfis aninhados.

A faturação do Gestor de Tráfego tem dois componentes: verificações de saúde de ponto final e milhões de consultas de DNS

* Verificações de saúde de ponto final: Não há nenhum custo para um perfil de criança quando configurado como um ponto final num perfil dos pais. A monitorização dos pontos finais no perfil da criança é faturada da forma habitual.
* Perguntas dNS: Cada consulta só é contada uma vez. Uma consulta contra um perfil dos pais que devolve um ponto final de um perfil de criança é contada apenas contra o perfil dos pais.

Para mais detalhes, consulte a página de [preços do Gestor de Tráfego](https://azure.microsoft.com/pricing/details/traffic-manager/).

### <a name="is-there-a-performance-impact-for-nested-profiles"></a>Há algum impacto no desempenho para perfis aninhados?

Não. Não há impacto no desempenho incorrido na utilização de perfis aninhados.

Os servidores de nome do Gestor de Tráfego atravessam internamente a hierarquia do perfil ao processar cada consulta de DNS. Uma consulta de DNS a um perfil dos pais pode receber uma resposta DNS com um ponto final de um perfil infantil. Um único disco CNAME é usado quer esteja a utilizar um único perfil ou perfis aninhados. Não há necessidade de criar um registo CNAME para cada perfil na hierarquia.

### <a name="how-does-traffic-manager-compute-the-health-of-a-nested-endpoint-in-a-parent-profile"></a>Como é que o Gestor de Tráfego calcula a saúde de um ponto final aninhado num perfil dos pais?

O perfil dos pais não faz exames de saúde da criança diretamente. Em vez disso, a saúde dos pontos finais do perfil da criança é utilizada para calcular a saúde geral do perfil da criança. Esta informação é propagada pela hierarquia do perfil aninhado para determinar a saúde do ponto final aninhado. O perfil dos pais utiliza esta saúde agregada para determinar se o tráfego pode ser direcionado para a criança.

A tabela seguinte descreve o comportamento dos controlos de saúde do Gestor de Tráfego para um ponto final aninhada.

| Estado do Monitor do Perfil Infantil | Estado do Monitor do Ponto Final dos Pais | Notas |
| --- | --- | --- |
| Desativado. O perfil da criança foi desativado. |Parada |O estado final dos pais é parado, não incapacitado. O estado de desativação está reservado para indicar que desativou o ponto final no perfil dos pais. |
| Degradado. Pelo menos um ponto final do perfil infantil está em estado degradado. |Online: o número de pontos finais online no perfil infantil é, pelo menos, o valor dos MinChildEndpoints.<BR>CheckingEndpoint: o número de pontos finais Online plus CheckingEndpoint no perfil infantil é, pelo menos, o valor dos Pontos MinChildEnd.<BR>Degradado: caso contrário. |O tráfego é encaminhado para um ponto final de estado Verificando Endpoint. Se os MinChildEndpoints estiverem demasiado altos, o ponto final está sempre degradado. |
| Online. Pelo menos um ponto final do perfil infantil é um estado online. Nenhum ponto final está no estado degradado. |Veja acima. | |
| Verificando pontos endpoints. Pelo menos um ponto final do perfil infantil é "Checkendpoint". Não há pontos finais 'Online' ou 'Degradados' |O mesmo que acima. | |
| Inativo. Todos os pontos finais do perfil da criança são desativados ou parados, ou este perfil não tem pontos finais. |Parada | |

## <a name="next-steps"></a>Passos seguintes:

- Saiba mais sobre [a monitorização do ponto final](../traffic-manager/traffic-manager-monitoring.md)do Gestor de Tráfego e a falha automática .
- Saiba mais sobre os métodos de [encaminhamento de tráfego](../traffic-manager/traffic-manager-routing-methods.md)do Gestor de Tráfego.
