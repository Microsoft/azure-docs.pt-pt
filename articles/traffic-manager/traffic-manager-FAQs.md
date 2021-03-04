---
title: Gestor de Tráfego Azure - PERGUNTAS Frequentes
description: Este artigo fornece respostas a perguntas frequentes sobre Gestor de Tráfego
services: traffic-manager
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/03/2021
ms.author: duau
ms.openlocfilehash: 163436ad82ea6f5067ad41b7fdd7e315db6dc29a
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102095022"
---
# <a name="traffic-manager-frequently-asked-questions-faq"></a>Gestor de tráfego frequentemente perguntas (FAQ)

## <a name="traffic-manager-basics"></a>Básicos do Gestor de Tráfego

### <a name="what-ip-address-does-traffic-manager-use"></a>Que endereço IP utiliza o Gestor de Tráfego?

Como explicado em [Como funciona o Gestor de Tráfego,](../traffic-manager/traffic-manager-how-it-works.md)o Gestor de Tráfego trabalha ao nível do DNS. Envia respostas DNS para direcionar os clientes para o ponto final de serviço apropriado. Os clientes ligam-se então ao ponto final do serviço diretamente, não através do Gestor de Tráfego.

Portanto, o Traffic Manager não fornece um ponto final ou endereço IP para os clientes se conectarem. Se deseja um endereço IP estático para o seu serviço, este deve ser configurado no serviço, não no Gestor de Tráfego.

### <a name="what-types-of-traffic-can-be-routed-using-traffic-manager"></a>Que tipo de tráfego pode ser encaminhado usando o Gestor de Tráfego?
Como explicado em [Como o Gestor de Tráfego funciona,](../traffic-manager/traffic-manager-how-it-works.md)um ponto final do Traffic Manager pode ser qualquer serviço de internet virado para a internet hospedado dentro ou fora de Azure. Assim, o Traffic Manager pode encaminhar o tráfego que se origina da internet pública para um conjunto de pontos finais que também estão virados para a Internet. Se tiver pontos finais que estejam dentro de uma rede privada (por exemplo, uma versão interna do [Azure Load Balancer](../load-balancer/components.md#frontend-ip-configurations)) ou tiver utilizadores a fazer pedidos de DNS a partir dessas redes internas, então não pode utilizar o Gestor de Tráfego para encaminhar este tráfego.

### <a name="does-traffic-manager-support-sticky-sessions"></a>O Traffic Manager suporta sessões "pegajosas"?

Como explicado em [Como funciona o Gestor de Tráfego,](../traffic-manager/traffic-manager-how-it-works.md)o Gestor de Tráfego trabalha ao nível do DNS. Utiliza respostas DNS para direcionar os clientes para o ponto final de serviço apropriado. Os clientes conectam-se diretamente ao ponto final do serviço, não através do Gestor de Tráfego. Portanto, o Traffic Manager não vê o tráfego HTTP entre o cliente e o servidor.

Adicionalmente, o endereço IP de origem da consulta DNS recebida pelo Traffic Manager pertence ao serviço DE DNS recursivo, não ao cliente. Por isso, o Traffic Manager não tem forma de rastrear clientes individuais e não pode implementar sessões 'pegajosas'. Esta limitação é comum a todos os sistemas de gestão de tráfego baseados em DNS e não é específica para o Gestor de Tráfego.

### <a name="why-am-i-seeing-an-http-error-when-using-traffic-manager"></a>Porque estou a ver um erro HTTP ao usar o Traffic Manager?

Como explicado em [Como funciona o Gestor de Tráfego,](../traffic-manager/traffic-manager-how-it-works.md)o Gestor de Tráfego trabalha ao nível do DNS. Utiliza respostas DNS para direcionar os clientes para o ponto final de serviço apropriado. Os clientes ligam-se então ao ponto final do serviço diretamente, não através do Gestor de Tráfego. O Gestor de Tráfego não vê o tráfego HTTP entre o cliente e o servidor. Portanto, qualquer erro HTTP que veja deve vir da sua aplicação. Para que o cliente se conecte à aplicação, todos os passos de resolução DNS estão completos. Isto inclui qualquer interação que o Gestor de Tráfego tenha no fluxo de tráfego de aplicações.

Por conseguinte, a investigação complementar deverá centrar-se no pedido.

O cabeçalho de anfitrião HTTP enviado do navegador do cliente é a fonte de problemas mais comum. Certifique-se de que a aplicação está configurada para aceitar o cabeçalho de anfitrião correto para o nome de domínio que está a utilizar. Para pontos finais utilizando o Serviço de Aplicações Azure, consulte [configurar um nome de domínio personalizado para uma aplicação web no Azure App Service utilizando](../app-service/configure-domain-traffic-manager.md)o Traffic Manager .

### <a name="what-is-the-performance-impact-of-using-traffic-manager"></a>Qual é o impacto de desempenho da utilização do Gestor de Tráfego?

Como explicado em [Como funciona o Gestor de Tráfego,](../traffic-manager/traffic-manager-how-it-works.md)o Gestor de Tráfego trabalha ao nível do DNS. Uma vez que os clientes se ligam diretamente aos seus pontos finais de serviço, não há nenhum impacto de desempenho incorrido ao utilizar o Traffic Manager uma vez que a ligação é estabelecida.

Uma vez que o Traffic Manager se integra com aplicações ao nível do DNS, requer uma procura adicional de DNS para ser inserida na cadeia de resolução de DNS. O impacto do Gestor de Tráfego no tempo de resolução do DNS é mínimo. O Traffic Manager utiliza uma rede global de servidores de nomes e utiliza qualquer rede [de rede](https://en.wikipedia.org/wiki/Anycast) para garantir que as consultas de DNS são sempre encaminhada para o servidor de nomes mais próximo disponível. Além disso, o caching de respostas DNS significa que a latência adicional de DNS incorrida pela utilização do Traffic Manager se aplica apenas a uma fração de sessões.

O método Performance encaminha o tráfego para o ponto final mais próximo disponível. O resultado líquido é que o impacto global do desempenho associado a este método deve ser mínimo. Qualquer aumento da latência do DNS deve ser compensado pela latência da rede mais baixa para o ponto final.

### <a name="what-application-protocols-can-i-use-with-traffic-manager"></a>Que protocolos de aplicação posso usar com o Gestor de Tráfego?

Como explicado em [Como funciona o Gestor de Tráfego,](../traffic-manager/traffic-manager-how-it-works.md)o Gestor de Tráfego trabalha ao nível do DNS. Uma vez concluída a procura do DNS, os clientes conectam-se diretamente ao ponto final da aplicação, e não através do Traffic Manager. Portanto, a ligação pode usar qualquer protocolo de aplicação. Se selecionar o TCP como protocolo de monitorização, a monitorização de saúde do TrafficPoint pode ser feita sem utilizar quaisquer protocolos de aplicação. Se optar por ter a saúde verificada através de um protocolo de aplicação, o ponto final tem de ser capaz de responder a pedidos HTTP ou HTTPS GET.

### <a name="can-i-use-traffic-manager-with-a-naked-domain-name"></a>Posso usar o Traffic Manager com um nome de domínio "nu"?

Sim. Para aprender a criar um registo de pseudónimo para o seu nome de domínio apex para fazer referência a um perfil do Gestor de Tráfego Azure, consulte [um registo de pseudónimo para suportar nomes de domínio apex com Gestor de Tráfego](../dns/tutorial-alias-tm.md).

### <a name="does-traffic-manager-consider-the-client-subnet-address-when-handling-dns-queries"></a>O Gestor de Tráfego considera o endereço da sub-rede do cliente ao manusear consultas de DNS? 

Sim, para além do endereço IP de origem da consulta DNS que recebe (que normalmente é o endereço IP do DNS resolver), ao efetuar pesquisas para métodos de encaminhamento Geográfico, Performance e Subnet, o gestor de tráfego também considera o endereço de sub-rede do cliente se for incluído na consulta pela resolução fazendo o pedido em nome do utilizador final.  
Especificamente, [RFC 7871 – Sub-rede de clientes em Consultas DNS](https://tools.ietf.org/html/rfc7871) que fornece um [Mecanismo de Extensão para DNS (EDNS0)](https://tools.ietf.org/html/rfc2671) que pode passar o endereço de sub-rede do cliente a partir de resolver os que o suportam.

### <a name="what-is-dns-ttl-and-how-does-it-impact-my-users"></a>O que é o DNS TTL e como é que afeta os meus utilizadores?

Quando uma consulta de DNS aterra no Traffic Manager, define um valor na resposta chamada time-to-live (TTL). Este valor, cuja unidade está em segundos, indica ao DNS que resolve a jusante quanto tempo para cache esta resposta. Embora os problemas dns não estejam garantidos para cache este resultado, o cache permite-lhes responder a quaisquer perguntas subsequentes fora da cache em vez de ir para os servidores DNS do Traffic Manager. Isto afeta as respostas da seguinte forma:

- um TTL mais elevado reduz o número de consultas que aterram nos servidores DNS do Gestor de Tráfego, o que pode reduzir o custo para um cliente uma vez que o número de consultas servidas é uma utilização faturada.
- um TTL mais alto pode potencialmente reduzir o tempo que leva para fazer uma procura de DNS.
- um TTL mais elevado também significa que os seus dados não refletem as últimas informações de saúde que o Traffic Manager obteve através dos seus agentes de sondagem.

### <a name="how-high-or-low-can-i-set-the-ttl-for-traffic-manager-responses"></a>Até que ponto posso definir o TTL para respostas do Gestor de Tráfego?

Pode definir, a um nível de perfil, o DNS TTL para ser tão baixo como 0 segundos e até 2.147.483,647 segundos (o alcance máximo em conformidade com [RFC-1035).](https://www.ietf.org/rfc/rfc1035.txt ) Um TTL de 0 significa que os dns a jusante não fazem respostas de consulta em cache e todas as consultas devem chegar aos servidores DNS do Gestor de Tráfego para resolução.

### <a name="how-can-i-understand-the-volume-of-queries-coming-to-my-profile"></a>Como posso entender o volume de consultas que chegam ao meu perfil? 

Uma das métricas fornecidas pelo Traffic Manager é o número de consultas respondidas por um perfil. Você pode obter esta informação em uma agregação de nível de perfil ou você pode dividi-lo ainda mais para ver o volume de consultas onde os pontos finais específicos foram devolvidos. Além disso, pode configurar alertas para o notificar se o volume de resposta de consulta cruzar as condições definidas. Para mais detalhes, [métricas e alertas do Gestor de Tráfego.](traffic-manager-metrics-alerts.md)

## <a name="traffic-manager-geographic-traffic-routing-method"></a>Método de encaminhamento de tráfego do gestor de tráfego de tráfego do gestor de tráfego

### <a name="what-are-some-use-cases-where-geographic-routing-is-useful"></a>Quais são alguns casos de uso em que o encaminhamento geográfico é útil?

O tipo de encaminhamento geográfico pode ser usado em qualquer cenário em que um cliente Azure precise distinguir os seus utilizadores com base em regiões geográficas. Por exemplo, utilizando o método de encaminhamento de tráfego Geográfico, pode dar aos utilizadores de regiões específicas uma experiência de utilizador diferente das de outras regiões. Outro exemplo é o cumprimento dos mandatos locais de soberania de dados que exigem que os utilizadores de uma região específica sejam servidos apenas por pontos finais nessa região.

### <a name="how-do-i-decide-if-i-should-use-performance-routing-method-or-geographic-routing-method"></a>Como decido se devo utilizar o método de encaminhamento de desempenho ou o método de encaminhamento geográfico?

A diferença fundamental entre estes dois métodos de encaminhamento populares é que no método de encaminhamento de desempenho o seu objetivo principal é enviar tráfego para o ponto final que pode fornecer a latência mais baixa para o chamador, enquanto, no encaminhamento geográfico o objetivo principal é impor uma cerca geo para os seus chamadores para que você possa deliberadamente encaminhá-los para um ponto final específico. A sobreposição acontece uma vez que existe uma correlação entre a proximidade geográfica e a menor latência, embora isso nem sempre seja verdade. Pode haver um ponto final numa geografia diferente que possa proporcionar uma melhor experiência de latência para o chamador e nesse caso o encaminhamento de desempenho enviará o utilizador para esse ponto final, mas o encaminhamento geográfico irá sempre enviá-los para o ponto final que mapeou para a sua região geográfica. Para deixar mais claro, considere o seguinte exemplo - com o encaminhamento geográfico você pode fazer mapeamentos incomuns, como enviar todo o tráfego da Ásia para pontos finais nos EUA e todo o tráfego dos EUA para pontos finais na Ásia. Nesse caso, o encaminhamento da Geographic fará deliberadamente o que você configurado para fazer e a otimização de desempenho não é uma consideração. 
>[!NOTE]
>Pode haver cenários em que você pode precisar tanto de capacidades de desempenho como de encaminhamento geográfico, pois estes cenários os perfis aninhados podem ser uma ótima escolha. Por exemplo, pode configurar um perfil de pai com encaminhamento geográfico para onde envia todo o tráfego da América do Norte para um perfil aninhado que tenha pontos finais nos EUA e use o encaminhamento de desempenho para enviar esse tráfego para o melhor ponto final dentro desse conjunto. 

### <a name="what-are-the-regions-that-are-supported-by-traffic-manager-for-geographic-routing"></a>Quais são as regiões que são apoiadas pelo Gestor de Tráfego para o encaminhamento geográfico?

A hierarquia país/região que é utilizada pelo Traffic Manager pode ser consultada [aqui.](traffic-manager-geographic-regions.md) Enquanto esta página é mantida atualizada com quaisquer alterações, também pode obter programáticamente as mesmas informações utilizando a API do [Gestor de Tráfego Azure](/rest/api/trafficmanager/). 

### <a name="how-does-traffic-manager-determine-where-a-user-is-querying-from"></a>Como é que o gestor de tráfego determina de onde um utilizador está a consultar?

O Gestor de Tráfego olha para a origem IP da consulta (isto provavelmente é um DNS local resolver fazer a consulta em nome do utilizador) e usa um IP interno para o mapa da região para determinar a localização. Este mapa é atualizado numa base contínua para responder a alterações na internet. 

### <a name="is-it-guaranteed-that-traffic-manager-can-correctly-determine-the-exact-geographic-location-of-the-user-in-every-case"></a>É garantido que o Traffic Manager pode determinar corretamente a localização geográfica exata do utilizador em todos os casos?

Não, o Gestor de Tráfego não pode garantir que a região geográfica que inferimos a partir do endereço IP de origem de uma consulta de DNS corresponderá sempre à localização do utilizador devido às seguintes razões:

- Em primeiro lugar, como descrito nas FAQ anteriores, o endereço IP de origem que vemos é o de um DNS resolver fazer a pesquisa em nome do utilizador. Embora a localização geográfica do DNS resolver seja um bom representante para a localização geográfica do utilizador, também pode ser diferente dependendo da pegada do serviço DNS resolver e do serviço específico DNS resolver que um cliente escolheu utilizar. Como exemplo, um cliente localizado na Malásia poderia especificar nas definições do seu dispositivo utilizar um serviço DNS resolver cujo servidor DNS em Singapura pode ser escolhido para lidar com as resoluções de consulta para esse utilizador/dispositivo. Nesse caso, o Traffic Manager só pode ver o endereço IP do resolver que corresponde à localização de Singapura. Consulte também as FAQ anteriores sobre o suporte ao endereço do cliente nesta página.

- Em segundo lugar, o Gestor de Tráfego utiliza um mapa interno para fazer o endereço IP para a tradução da região geográfica. Embora este mapa seja validado e atualizado de forma contínua para aumentar a sua precisão e contabilização da natureza evolutiva da internet, existe ainda a possibilidade de as nossas informações não constem uma representação exata da localização geográfica de todos os endereços IP.

###  <a name="does-an-endpoint-need-to-be-physically-located-in-the-same-region-as-the-one-it-is-configured-with-for-geographic-routing"></a>Um ponto final precisa de ser fisicamente localizado na mesma região com que está configurado para o encaminhamento geográfico?

Não, a localização do ponto final não impõe restrições às regiões que lhe podem ser mapeadas. Por exemplo, um ponto final na região de US-Central Azure pode ter todos os utilizadores da Índia direcionados para ele.

### <a name="can-i-assign-geographic-regions-to-endpoints-in-a-profile-that-is-not-configured-to-do-geographic-routing"></a>Posso atribuir regiões geográficas a pontos finais num perfil que não está configurado para fazer o encaminhamento geográfico?

Sim, se o método de encaminhamento de um perfil não for geográfico, pode utilizar o API do [Gestor de Tráfego Azure](/rest/api/trafficmanager/) para atribuir regiões geográficas a pontos finais nesse perfil. No caso de perfis de tipo de encaminhamento não geográficos, esta configuração é ignorada. Se alterar este perfil para o tipo de encaminhamento geográfico mais tarde, o Traffic Manager pode utilizar esses mapeamentos.

### <a name="why-am-i-getting-an-error-when-i-try-to-change-the-routing-method-of-an-existing-profile-to-geographic"></a>Porque estou a cometer um erro quando tento mudar o método de encaminhamento de um perfil existente para a Geographic?

Todos os pontos finais sob um perfil com encaminhamento geográfico precisam de ter pelo menos uma região mapeada para ele. Para converter um perfil existente para o tipo de encaminhamento geográfico, primeiro é necessário associar as regiões geográficas a todos os seus pontos finais utilizando o [API REST, gestor de tráfego azure,](/rest/api/trafficmanager/) antes de alterar o tipo de encaminhamento para geográfico. Se utilizar o portal, primeiro elimine os pontos finais, altere o método de encaminhamento do perfil para geográfico e, em seguida, adicione os pontos finais juntamente com o mapeamento da região geográfica.

### <a name="why-is-it-strongly-recommended-that-customers-create-nested-profiles-instead-of-endpoints-under-a-profile-with-geographic-routing-enabled"></a>Por que razão é fortemente recomendado que os clientes criem perfis aninhados em vez de pontos finais sob um perfil com encaminhamento geográfico ativado?

Uma região pode ser atribuída a apenas um ponto final dentro de um perfil se estiver a utilizar o método de encaminhamento geográfico. Se esse ponto final não for um tipo aninhado com um perfil de criança ligado a ele, se esse ponto final não for saudável, o Traffic Manager continua a enviar-lhe tráfego, uma vez que a alternativa de não enviar nenhum tráfego não é melhor. O Gestor de Tráfego não falha noutro ponto final, mesmo quando a região atribuída é um "pai" da região afetada ao ponto final que não ficou saudável (por exemplo, se um ponto final que tem região espanha não for saudável, não falhamos em outro ponto final que tem a região da Europa a lhe atribuir). Isto é feito para garantir que o Traffic Manager respeita os limites geográficos que um cliente tem no seu perfil. Para obter o benefício de falhar para outro ponto final quando um ponto final não é saudável, recomenda-se que as regiões geográficas sejam atribuídas a perfis aninhados com múltiplos pontos finais dentro dele em vez de pontos finais individuais. Desta forma, se um ponto final no perfil da criança aninhado falhar, o tráfego pode falhar para outro ponto final dentro do mesmo perfil de criança aninhado.

### <a name="are-there-any-restrictions-on-the-api-version-that-supports-this-routing-type"></a>Existem restrições à versão API que suporte este tipo de encaminhamento?

Sim, apenas a versão API 2017-03-01 e mais recente suporta o tipo de encaminhamento Geographic. Quaisquer versões API mais antigas não podem ser utilizadas para criar perfis do tipo de encaminhamento Geográfico ou atribuir regiões geográficas a pontos finais. Se uma versão API mais antiga for utilizada para recuperar perfis de uma subscrição do Azure, qualquer perfil do tipo de encaminhamento Geographic não é devolvido. Além disso, ao utilizar versões API mais antigas, qualquer perfil devolvido que tenha pontos finais com uma atribuição de região geográfica, não tem a sua atribuição de região geográfica mostrada.

## <a name="traffic-manager-subnet-traffic-routing-method"></a>Método de encaminhamento de tráfego sub-rede do gestor de tráfego de tráfego de gestor de tráfego

### <a name="what-are-some-use-cases-where-subnet-routing-is-useful"></a>Quais são alguns casos de utilização em que o encaminhamento de sub-redes é útil?

O encaminhamento de sub-redes permite-lhe diferenciar a experiência que proporciona para conjuntos específicos de utilizadores identificados pela origem IP do seu endereço IP de pedidos dns. Um exemplo seria mostrar diferentes conteúdos se os utilizadores estiverem a ligar-se a um website a partir do seu QG corporativo. Outra seria restringir os utilizadores de certos ISPs a apenas aceder a pontos finais que suportam apenas ligações IPv4 se esses ISPs tiverem desempenho sub-par quando o IPv6 for utilizado.
Outra razão para utilizar o método de encaminhamento sub-rede é em conjunto com outros perfis num conjunto de perfis aninhados. Por exemplo, se quiser utilizar o método de encaminhamento Geográfico para a geo-esgrima dos seus utilizadores, mas para um ISP específico, pretender fazer um método de encaminhamento diferente, pode ter um perfil com o método de encaminhamento subnet como o perfil principal e sobrepor-se a esse ISP para usar um perfil específico da criança e ter o perfil Geográfico padrão para todos os outros.

### <a name="how-does-traffic-manager-know-the-ip-address-of-the-end-user"></a>Como é que o Gestor de Tráfego conhece o endereço IP do utilizador final?

Os dispositivos finais do utilizador normalmente usam um dns resolver para fazer a pesquisa de DNS em seu nome. O IP de saída de tais resolvers é o que o Gestor de Tráfego vê como o IP de origem. Além disso, o método de encaminhamento da Subnet também procura ver se existe informação da Subneta de ClienteS Estendidos EDNS0 (ECS) que foi passada com o pedido. Se estiverem presentes informações da CES, este é o endereço utilizado para determinar o encaminhamento. Na ausência de informações relativas à ECS, o IP de origem da consulta é utilizado para efeitos de encaminhamento.

### <a name="how-can-i-specify-ip-addresses-when-using-subnet-routing"></a>Como posso especificar endereços IP ao utilizar o encaminhamento sub-rede?

Os endereços IP para associar a um ponto final podem ser especificados de duas formas. Em primeiro lugar, pode utilizar a notação de octetos decimais quad pontilhados com endereços de início e fim para especificar o intervalo (por exemplo, 1.2.3.4-5.6.7.8 ou 3.4.5.6-3.4.5.6). Em segundo lugar, pode utilizar a notação CIDR para especificar o intervalo (por exemplo, 1.2.3.0/24). Pode especificar vários intervalos e utilizar ambos os tipos de notação num conjunto de gama. Algumas restrições aplicam-se.

-    Não é possível ter sobreposição de intervalos de endereços, uma vez que cada IP precisa de ser mapeado para apenas um único ponto final
-    O endereço de partida não pode ser mais do que o endereço final
-    No caso da notação CIDR, o endereço IP antes do '/' deve ser o endereço de partida desse intervalo (por exemplo, 1.2.3.0/24 é válido, mas 1.2.3.4.4/24 NÃO é válido)

### <a name="how-can-i-specify-a-fallback-endpoint-when-using-subnet-routing"></a>Como posso especificar um ponto final de recuo ao utilizar o encaminhamento sub-rede?

Num perfil com o encaminhamento subnet, se tiver um ponto final sem sub-redes mapeadas, qualquer pedido que não corresponda a outros pontos finais será direcionado para aqui. É altamente recomendável que tenha um ponto final de recuo no seu perfil, uma vez que o Traffic Manager devolverá uma resposta NXDOMAIN se um pedido chegar e não for mapeado para nenhum ponto final ou se estiver mapeado para um ponto final, mas esse ponto final não é saudável.

### <a name="what-happens-if-an-endpoint-is-disabled-in-a-subnet-routing-type-profile"></a>O que acontece se um ponto final for desativado num perfil do tipo de encaminhamento sub-rede?

Num perfil com o encaminhamento subnet, se tiver um ponto final com o que está desativado, o Traffic Manager comportar-se-á como se esse ponto final e os mapeamentos da sub-rede que tem não existissem. Se uma consulta que teria combinado com o seu mapeamento de endereço IP for recebida e o ponto final for desativado, o Gestor de Tráfego devolverá um ponto final de recuo (um sem mapeamento) ou se tal ponto final não estiver presente, devolverá uma resposta NXDOMAIN.

## <a name="traffic-manager-multivalue-traffic-routing-method"></a>Método de encaminhamento de tráfego multiValue do gestor de tráfego

### <a name="what-are-some-use-cases-where-multivalue-routing-is-useful"></a>Quais são alguns casos de uso em que o encaminhamento MultiValue é útil?

O encaminhamento MultiValue devolve vários pontos finais saudáveis numa única resposta de consulta. A principal vantagem disto é que, se um ponto final não é saudável, o cliente tem mais opções para voltar a tentar sem fazer outra chamada de DNS (que pode devolver o mesmo valor de uma cache a montante). Isto é aplicável para aplicações sensíveis à disponibilidade que pretendem minimizar o tempo de inatividade.
Outra utilização para o método de encaminhamento MultiValue é se um ponto final for "casa dupla" para os endereços IPv4 e IPv6 e pretende dar ao chamador ambas as opções para escolher quando iniciar uma ligação ao ponto final.

### <a name="how-many-endpoints-are-returned-when-multivalue-routing-is-used"></a>Quantos pontos finais são devolvidos quando o roteamento MultiValue é usado?

Pode especificar o número máximo de pontos finais a devolver e o MultiValue não devolverá mais do que muitos pontos finais saudáveis quando uma consulta é recebida. O valor máximo possível para esta configuração é de 10.

### <a name="will-i-get-the-same-set-of-endpoints-when-multivalue-routing-is-used"></a>Terei o mesmo conjunto de pontos finais quando o roteamento MultiValue for usado?

Não podemos garantir que o mesmo conjunto de pontos finais será devolvido em cada consulta. Isto também é afetado pelo facto de alguns dos pontos finais poderem não ficar saudáveis, altura em que não serão incluídos na resposta.

## <a name="real-user-measurements"></a>Medidas Reais de Utilizadores

### <a name="what-are-the-benefits-of-using-real-user-measurements"></a>Quais são os benefícios da utilização de Medidas Reais do Utilizador?

Quando utiliza o método de encaminhamento de desempenho, o Traffic Manager escolhe a melhor região Azure para o seu utilizador final ligar-se inspecionando a sub-rede IP de origem e EDNS (se transmitida) e verificando-a contra a inteligência de latência da rede que o serviço mantém. As Verdadeiras Medições do Utilizador melhoram-no para a sua base de utilizador final, tendo a sua experiência contribuído para esta tabela de latência, além de garantir que esta tabela abrange adequadamente as redes de utilizadores finais de onde os seus utilizadores finais se ligam ao Azure. Isto leva a uma maior precisão no encaminhamento do seu utilizador final.

### <a name="can-i-use-real-user-measurements-with-non-azure-regions"></a>Posso utilizar as medidas reais do utilizador com regiões não-Azure?

As medidas e relatórios reais das medições dos utilizadores apenas sobre a latência para chegar às regiões de Azure. Se estiver a utilizar o encaminhamento baseado no desempenho com pontos finais alojados em regiões não-Azure, ainda pode beneficiar desta funcionalidade, tendo informações de latência acrescidas sobre a região representativa de Azure que selecionou para ser associado a este ponto final.

### <a name="which-routing-method-benefits-from-real-user-measurements"></a>Que método de encaminhamento beneficia das Medições Reais do Utilizador?

As informações adicionais obtidas através de Medições Reais do Utilizador são aplicáveis apenas para perfis que utilizem o método de encaminhamento de desempenho. O link Real User Measurements está disponível em todos os perfis quando o vê através do portal Azure.

### <a name="do-i-need-to-enable-real-user-measurements-each-profile-separately"></a>Preciso de ativar as medições reais do utilizador separadamente?

Não, só precisa de o ativar uma vez por subscrição e todas as informações de latência medidas e comunicadas estão disponíveis para todos os perfis.

### <a name="how-do-i-turn-off-real-user-measurements-for-my-subscription"></a>Como desativar as Medidas reais do Utilizador para a minha subscrição?

Pode parar de acumular encargos relacionados com as Medições Reais do Utilizador quando deixar de recolher e enviar de volta as medições de latência da sua aplicação do cliente. Por exemplo, quando a medição do JavaScript incorporada nas páginas web, pode parar de utilizar esta funcionalidade removendo o JavaScript ou desligando a sua invocação quando a página é renderizada.

Também pode desligar as Medições reais do utilizador eliminando a sua chave. Uma vez eliminada a tecla, quaisquer medições enviadas ao Gestor de Tráfego com esta tecla são descartadas.

### <a name="can-i-use-real-user-measurements-with-client-applications-other-than-web-pages"></a>Posso utilizar as Medidas reais do utilizador com aplicações de clientes que não as páginas web?

Sim, As Medições reais do Utilizador são concebidas para ingerir dados recolhidos através de diferentes tipos de clientes de utilizador final. Este FAQ será atualizado à medida que novos tipos de aplicações de clientes são suportados.

### <a name="how-many-measurements-are-made-each-time-my-real-user-measurements-enabled-web-page-is-rendered"></a>Quantas medições são feitas cada vez que a minha página web ativada por Medições de Utilizador é feita?

Quando as Medições Reais do Utilizador são utilizadas com a medição JavaScript fornecida, cada renderização de página resulta em seis medições a serem efetuadas. Estes são então comunicados de volta ao serviço de Gerente de Tráfego. É cobrado por esta funcionalidade com base no número de medições reportadas ao serviço De Gerente de Tráfego. Por exemplo, se o utilizador navegar para longe da sua página web enquanto as medições estão a ser efetuadas, mas antes de ser reportada, essas medições não são consideradas para efeitos de faturação.

### <a name="is-there-a-delay-before-real-user-measurements-script-runs-in-my-webpage"></a>Existe um atraso antes de o script real de medições do utilizador ser executado na minha página web?

Não, não há atraso programado antes do guião ser invocado.

### <a name="can-i-use-real-user-measurements-with-only-the-azure-regions-i-want-to-measure"></a>Posso utilizar as medidas reais do utilizador apenas com as regiões Azure que quero medir?

Não, cada vez que é invocado, o script real de medições do utilizador mede um conjunto de seis regiões Azure, conforme determinado pelo serviço. Este conjunto muda entre diferentes invocações e quando um grande número de tais invocações acontecem, a cobertura de medição estende-se por diferentes regiões do Azure.

### <a name="can-i-limit-the-number-of-measurements-made-to-a-specific-number"></a>Posso limitar o número de medições efetuadas a um número específico?

A medição JavaScript está incorporada na sua página web e está em total controlo sobre quando iniciar e parar de usá-lo. Desde que o serviço de Gerente de Tráfego receba um pedido de menção de uma lista das regiões de Azure, um conjunto de regiões são devolvidas.

### <a name="can-i-see-the-measurements-taken-by-my-client-application-as-part-of-real-user-measurements"></a>Posso ver as medidas feitas pela aplicação do meu cliente como parte das Medições Reais do Utilizador?

Uma vez que a lógica de medição é executada a partir da aplicação do seu cliente, você está em pleno controlo do que acontece, incluindo ver as medições de latência. O Gestor de Tráfego não reporta uma visão agregada das medições recebidas na chave ligada à sua subscrição.

### <a name="can-i-modify-the-measurement-script-provided-by-traffic-manager"></a>Posso modificar o script de medição fornecido pelo Traffic Manager?

Enquanto controla o que está incorporado na sua página web, desencorajamo-lo fortemente de fazer quaisquer alterações no script de medição para garantir que mede e reporta corretamente as latências.

### <a name="will-it-be-possible-for-others-to-see-the-key-i-use-with-real-user-measurements"></a>Será possível que outros vejam a chave que uso com as medidas reais do utilizador?

Quando incorporar o script de medição numa página web, será possível que outros vejam o script e a tecla Real User Measurements (RUM). Mas é importante saber que esta chave é diferente da sua subscrição e é gerada pelo Traffic Manager para ser usada apenas para este fim. Conhecer a sua chave RUM não comprometerá a segurança da sua conta Azure.

### <a name="can-others-abuse-my-rum-key"></a>Outros podem abusar da minha chave RUM?

Embora seja possível que outros utilizem a sua chave para enviar informações erradas para o Azure, algumas medições erradas não alterarão o encaminhamento, uma vez que são tidas em conta juntamente com todas as outras medições que recebemos. Se precisar de alterar as teclas, pode gerar novamente a chave, altura em que a tecla antiga é descartada.

### <a name="do-i-need-to-put-the-measurement-javascript-in-all-my-web-pages"></a>Preciso de colocar o JavaScript de medição em todas as minhas páginas web?

As Medições Reais do Utilizador proporcionam mais valor à medida que o número de medições aumenta. Dito isto, é sua decisão se precisa colocá-lo em todas as suas páginas web ou em alguns selecionados. A nossa recomendação é começar por colocá-la na sua página mais visitada, onde se espera que um utilizador permaneça nessa página cinco segundos ou mais.

### <a name="can-information-about-my-end-users-be-identified-by-traffic-manager-if-i-use-real-user-measurements"></a>As informações sobre os meus utilizadores finais podem ser identificadas pelo Gestor de Tráfego se eu utilizar as Medidas reais do Utilizador?

Quando a medição fornecida JavaScript for utilizada, o Gestor de Tráfego terá visibilidade no endereço IP do cliente do utilizador final e no endereço IP de origem do DNS local que utilizam. O Gestor de Tráfego utiliza o endereço IP do cliente apenas depois de o ter truncado para não conseguir identificar o utilizador final específico que enviou as medições.

### <a name="does-the-webpage-measuring-real-user-measurements-need-to-be-using-traffic-manager-for-routing"></a>A página web que mede as medições reais do utilizador precisa de estar a utilizar o Gestor de Tráfego para encaminhamento?

Não, não precisa de usar o Diretor de Trânsito. O lado de encaminhamento do Traffic Manager funciona separadamente da parte de Medição do Utilizador Real e embora seja uma boa ideia tê-los ambos na mesma propriedade web, eles não precisam de ser.

### <a name="do-i-need-to-host-any-service-on-azure-regions-to-use-with-real-user-measurements"></a>Preciso de hospedar algum serviço nas regiões Azure para utilizar com as medidas reais do utilizador?

Não, não precisa de hospedar nenhum componente do lado do servidor no Azure para que as medições reais do utilizador funcionem. A imagem de pixel única descarregada pela medição JavaScript e o serviço que o executa em diferentes regiões do Azure é hospedado e gerido pela Azure. 

### <a name="will-my-azure-bandwidth-usage-increase-when-i-use-real-user-measurements"></a>O meu uso de largura de banda Azure aumentará quando utilizar as Medições reais do utilizador?

Como mencionado na resposta anterior, os componentes do lado do servidor das Medições reais do utilizador são propriedade e geridos pelo Azure. Isto significa que a sua utilização de largura de banda Azure não aumentará porque utiliza As Medições reais do Utilizador. Isto não inclui qualquer utilização de largura de banda fora do que a Azure cobra. Minimizamos a largura de banda utilizada através do download de apenas uma única imagem de pixel para medir a latência para uma região de Azure. 

## <a name="traffic-view"></a>Vista de Tráfego

### <a name="what-does-traffic-view-do"></a>O que faz a Traffic View?

Traffic View é uma funcionalidade do Traffic Manager que o ajuda a entender mais sobre os seus utilizadores e como é a sua experiência. Utiliza as consultas recebidas pelo Traffic Manager e as tabelas de inteligência de latência da rede que o serviço mantém para lhe fornecer as seguintes:

- As regiões de onde os seus utilizadores estão a ligar-se aos seus pontos finais em Azure.
- O volume de utilizadores que se ligam a partir destas regiões.
- As regiões de Azure para onde estão a ser encaminhados.
- A sua experiência de latência nestas regiões de Azure.

Estas informações estão disponíveis para que possa consumir através de sobreposição de mapas geográficos e vistas tabulares no portal, além de estar disponível como dados brutos para descarregar.

### <a name="how-can-i-benefit-from-using-traffic-view"></a>Como posso beneficiar-me da utilização da Traffic View?

A Vista de Tráfego dá-lhe a visão geral do tráfego que os perfis do Gestor de Tráfego recebem. Em particular, pode ser usado para entender de onde a sua base de utilizadores se conecta e igualmente importante qual é a sua experiência média de latência. Em seguida, pode utilizar esta informação para encontrar áreas em que precisa de se concentrar, por exemplo, expandindo a sua pegada Azure para uma região que pode servir esses utilizadores com menor latência. Outra visão que você pode derivar da utilização do Traffic View é ver os padrões de tráfego para diferentes regiões que, por sua vez, podem ajudá-lo a tomar decisões sobre o aumento ou diminuição do invento nessas regiões.

### <a name="how-is-traffic-view-different-from-the-traffic-manager-metrics-available-through-azure-monitor"></a>Como é que a Vista de Tráfego é diferente das métricas do Gestor de Tráfego disponível através do monitor Azure?

O Azure Monitor pode ser utilizado para compreender a um nível agregado o tráfego recebido pelo seu perfil e pelos seus pontos finais. Também permite acompanhar o estado de saúde dos pontos finais expondo os resultados do exame de saúde. Quando precisa de ir além destes e entender a experiência do utilizador final conectando-se ao Azure a nível regional, o Traffic View pode ser usado para o conseguir.

### <a name="does-traffic-view-use-edns-client-subnet-information"></a>A Traffic View utiliza informações de sub-rede do cliente EDNS?

As consultas dns servidas pelo Azure Traffic Manager consideram as informações da ECS para aumentar a precisão do encaminhamento. Mas ao criar o conjunto de dados que mostra de onde os utilizadores estão a ligar-se, o Traffic View está a utilizar apenas o endereço IP do DNS resolver.

### <a name="how-many-days-of-data-does-traffic-view-use"></a>Quantos dias de dados utiliza a Traffic View?

O Traffic View cria a sua produção através do processamento dos dados dos sete dias anteriores ao dia anterior, quando é visto por si. Esta é uma janela em movimento e os dados mais recentes serão usados cada vez que visitar.

### <a name="how-does-traffic-view-handle-external-endpoints"></a>Como é que a Traffic View lida com pontos finais externos?

Quando utiliza pontos finais externos alojados fora das regiões de Azure num perfil de Gestor de Tráfego, pode optar por mapear para uma região do Azure, que é uma procuração pelas suas características de latência (isto é, de facto, necessário se utilizar o método de encaminhamento de desempenho). Se tiver este mapeamento da região de Azure, as métricas de latência da região de Azure serão usadas na criação da produção de Traffic View. Se nenhuma região de Azure for especificada, as informações de latência ficarão vazias nos dados relativos a esses pontos finais externos.

### <a name="do-i-need-to-enable-traffic-view-for-each-profile-in-my-subscription"></a>Preciso de ativar a Traffic View para cada perfil da minha assinatura?

Durante o período de pré-visualização, a Traffic View foi ativada a nível de subscrição. Como parte das melhorias que fizemos antes da disponibilidade geral, pode agora ativar a Vista de Tráfego a um nível de perfil, permitindo-lhe ter uma ativação mais granular desta funcionalidade. Por predefinição, a Vista de Tráfego será desativada para um perfil.

>[!NOTE]
>Se ativou a Traffic View a nível de subscrição durante o tempo de pré-visualização, tem agora de o reabilitar para cada um dos perfis nessa subscrição.
 
### <a name="how-can-i-turn-off-traffic-view"></a>Como posso desligar a Vista de Trânsito?

Pode desligar a Vista de Tráfego para qualquer perfil utilizando o Portal ou a API REST. 

### <a name="how-does-traffic-view-billing-work"></a>Como funciona a faturação da Traffic View?

Os preços de Visualização de Tráfego baseiam-se no número de pontos de dados utilizados para criar a saída. Atualmente, o único tipo de dados suportado são as consultas que o seu perfil recebe. Além disso, só é cobrado o processamento que foi feito quando tem a Vista de Tráfego ativada. Isto significa que, se ativar a Traffic View durante algum período de tempo num mês e desligá-lo durante outros tempos, apenas os pontos de dados processados enquanto tinha a conta ativada para a sua conta.

## <a name="traffic-manager-endpoints"></a>Pontos finais do Gestor de Tráfego

### <a name="can-i-use-traffic-manager-with-endpoints-from-multiple-subscriptions"></a>Posso utilizar o Traffic Manager com pontos finais de várias subscrições?

A utilização de pontos finais de várias subscrições não é possível com as Aplicações Web Azure. As Aplicações Web Azure exigem que qualquer nome de domínio personalizado usado com Web Apps seja utilizado apenas numa única subscrição. Não é possível utilizar aplicações web a partir de várias subscrições com o mesmo nome de domínio.

Para outros tipos de pontos finais, é possível utilizar o Traffic Manager com pontos finais de mais de uma subscrição. No Gestor de Recursos, os pontos finais de qualquer subscrição podem ser adicionados ao Gestor de Tráfego, desde que a pessoa que configura o perfil do Gestor de Tráfego tenha lido o acesso ao ponto final. Estas permissões podem ser concedidas através do [controlo de acesso baseado em funções Azure (Azure RBAC)](../role-based-access-control/role-assignments-portal.md). Os pontos finais de outras subscrições podem ser adicionados utilizando [a Azure PowerShell](/powershell/module/az.trafficmanager/new-aztrafficmanagerendpoint) ou o [Azure CLI](/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-create).

### <a name="can-i-use-traffic-manager-with-cloud-service-staging-slots"></a>Posso utilizar o Gestor de Tráfego com slots de 'Staging' do Serviço de Nuvem?

Sim. As ranhuras de 'staging' do Serviço de Nuvem podem ser configuradas no Gestor de Tráfego como pontos finais externos. Os controlos de saúde ainda são cobrados à taxa Azure Endpoints.

### <a name="does-traffic-manager-support-ipv6-endpoints"></a>O Gestor de Tráfego suporta pontos finais IPv6?

O Gestor de Tráfego não fornece atualmente servidores de nome iPv6 endereçadores. No entanto, o Traffic Manager ainda pode ser utilizado por clientes IPv6 que se conectam aos pontos finais do IPv6. Um cliente não faz pedidos dns diretamente ao Gestor de Tráfego. Em vez disso, o cliente utiliza um serviço DE DNS recursivo. Um cliente exclusivo do IPv6 envia pedidos para o serviço DNS recursivo via IPv6. Em seguida, o serviço recursivo deverá ser capaz de contactar os servidores de nome do Gestor de Tráfego utilizando o IPv4.

O Gestor de Tráfego responde com o nome DNS ou endereço IP do ponto final. Para suportar um ponto final do IPv6, existem duas opções. Pode adicionar o ponto final como um nome DNS que tem um registo AAAA associado e o Traffic Manager verificará esse ponto final e devolverá-o como um tipo de registo CNAME na resposta de consulta. Também pode adicionar esse ponto final usando diretamente o endereço IPv6 e o Traffic Manager devolverá um registo do tipo AAAA na resposta de consulta.

### <a name="can-i-use-traffic-manager-with-more-than-one-web-app-in-the-same-region"></a>Posso usar o Traffic Manager com mais de uma Web App na mesma região?

Normalmente, o Traffic Manager é usado para direcionar o tráfego para aplicações implantadas em diferentes regiões. No entanto, também pode ser utilizado quando uma aplicação tem mais de uma implantação na mesma região. Os pontos finais do Gestor de Tráfego Azure não permitem que mais do que um ponto final da Web App da mesma região de Azure seja adicionado ao mesmo perfil de Gestor de Tráfego.

### <a name="how-do-i-move-my-traffic-manager-profiles-azure-endpoints-to-a-different-resource-group-or-subscription"></a>Como posso mover os pontos finais do Azure do meu perfil de Gestor de Tráfego para um grupo de recursos ou subscrição diferente?

Os pontos finais Azure que estão associados a um perfil de Gestor de Tráfego são rastreados usando os seus IDs de recursos. Quando um recurso Azure que está a ser usado como ponto final (por exemplo, IP Público, Classic Cloud Service, WebApp ou outro perfil de Traffic Manager usado de forma aninhada) é transferido para um grupo de recursos diferente ou subscrição, o seu ID de recursos muda. Neste cenário, atualmente, deve atualizar o perfil de Gestor de Tráfego, eliminando primeiro e depois adicionando os pontos finais ao perfil.

## <a name="traffic-manager-endpoint-monitoring"></a>Monitorização do ponto final do Gestor de Tráfego

### <a name="is-traffic-manager-resilient-to-azure-region-failures"></a>O Gestor de Tráfego é resiliente às falhas da região de Azure?

O Traffic Manager é um componente fundamental da entrega de aplicações altamente disponíveis no Azure.
Para proporcionar uma elevada disponibilidade, o Gestor de Tráfego deve ter um nível excepcionalmente elevado de disponibilidade e ser resiliente à falha regional.

Por design, os componentes do Traffic Manager são resistentes a uma falha completa de qualquer região do Azure. Esta resiliência aplica-se a todos os componentes do Gestor de Tráfego: os servidores de nome DNS, a API, a camada de armazenamento e o serviço de monitorização do ponto final.

No caso improvável de uma paragem de toda uma região de Azure, espera-se que o Gestor de Tráfego continue a funcionar normalmente. As aplicações implementadas em várias regiões do Azure podem contar com o Gestor de Tráfego para direcionar o tráfego para uma instância disponível da sua aplicação.

### <a name="how-does-the-choice-of-resource-group-location-affect-traffic-manager"></a>Como é que a escolha da localização do grupo de recursos afeta o Gestor de Tráfego?

O Traffic Manager é um serviço único e global. Não é regional. A escolha da localização do grupo de recursos não faz diferença para os perfis do Gestor de Tráfego implantados nesse grupo de recursos.

O Gestor de Recursos Azure exige que todos os grupos de recursos especifiquem uma localização, que determina a localização padrão dos recursos implantados nesse grupo de recursos. Quando cria um perfil de Gestor de Tráfego, é criado num grupo de recursos. Todos os perfis do Traffic Manager usam **o global** como sua localização, sobreprimando o padrão do grupo de recursos.

### <a name="how-do-i-determine-the-current-health-of-each-endpoint"></a>Como determino a saúde atual de cada ponto final?

O estado atual de monitorização de cada ponto final, para além do perfil geral, é apresentado no portal Azure. Esta informação também está disponível através do Traffic Monitor [REST API,](/rest/api/trafficmanager/) [PowerShell cmdlets,](/powershell/module/az.trafficmanager)e [cross-platform Azure CLI](/cli/azure/install-classic-cli).

Também pode utilizar o Azure Monitor para acompanhar a saúde dos seus pontos finais e ver uma representação visual dos mesmos. Para obter mais informações sobre a utilização do Azure Monitor, consulte a [documentação de monitorização do Azure](../azure-monitor/data-platform.md).

### <a name="can-i-monitor-https-endpoints"></a>Posso monitorizar os pontos finais do HTTPS?

Sim. O Gestor de Tráfego suporta sondagens sobre HTTPS. Configurar **HTTPS** como o protocolo na configuração de monitorização.

O gestor de tráfego não pode fornecer qualquer validação de certificado, incluindo:

* Os certificados do lado do servidor não são validados
* Os certificados do lado do servidor SNI não são validados
* Os certificados de cliente não são suportados

### <a name="do-i-use-an-ip-address-or-a-dns-name-when-adding-an-endpoint"></a>Uso um endereço IP ou um nome DNS ao adicionar um ponto final?

O Traffic Manager suporta a adição de pontos finais utilizando três formas de os referir – como um nome DNS, como um endereço IPv4 e como um endereço IPv6. Se o ponto final for adicionado como endereço IPv4 ou IPv6, a resposta de consulta será do tipo A ou AAAA, respectivamente. Se o ponto final foi adicionado como um nome DNS, então a resposta de consulta será do tipo de registo CNAME. A adição de pontos finais como endereço IPv4 ou IPv6 só é permitida se o ponto final for do tipo **Externo**.
Todos os métodos de encaminhamento e definições de monitorização são suportados pelos três tipos de endpoint.

### <a name="what-types-of-ip-addresses-can-i-use-when-adding-an-endpoint"></a>Que tipos de endereços IP posso usar ao adicionar um ponto final?

O Gestor de Tráfego permite-lhe utilizar endereços IPv4 ou IPv6 para especificar pontos finais. Existem algumas restrições listadas abaixo:

- Não são permitidos endereços que correspondam a espaços de endereços IP privados reservados. Estes endereços incluem os chamados RFC 1918, RFC 6890, RFC 5737, RFC 3068, RFC 2544 e RFC 5771
- O endereço não deve conter quaisquer números de porta (pode especificar as portas a utilizar nas definições de configuração do perfil)
- Nenhum dois pontos finais no mesmo perfil podem ter o mesmo endereço IP alvo

### <a name="can-i-use-different-endpoint-addressing-types-within-a-single-profile"></a>Posso utilizar diferentes tipos de endpoint num único perfil?

Não, o Traffic Manager não permite misturar tipos de endpoint de endereço dentro de um perfil, exceto no caso de um perfil com o tipo de encaminhamento MultiValue onde pode misturar tipos de endpoint iPv4 e IPv6

### <a name="what-happens-when-an-incoming-querys-record-type-is-different-from-the-record-type-associated-with-the-addressing-type-of-the-endpoints"></a>O que acontece quando o tipo de registo de uma consulta de entrada é diferente do tipo de registo associado ao tipo de endereço dos pontos finais?

Quando uma consulta é recebida contra um perfil, o Gestor de Tráfego encontra primeiro o ponto final que precisa de ser devolvido de acordo com o método de encaminhamento especificado e o estado de saúde dos pontos finais. Em seguida, olha para o tipo de registo solicitado na consulta de entrada e o tipo de registo associado ao ponto final antes de devolver uma resposta com base na tabela abaixo.

Para perfis com qualquer método de encaminhamento diferente do MultiValue:

|Pedido de consulta de entrada|     Tipo de ponto final|     Resposta Fornecida|
|--|--|--|
|QUALQUER |    A / AAAA / CNAME |    Ponto final alvo| 
|A |    A / CNAME |    Ponto final alvo|
|A |    AAAA |    NODATA |
|AAAA |    AAAA / CNAME |    Ponto final alvo|
|AAAA |    A |    NODATA |
|CNAME |    CNAME |    Ponto final alvo|
|CNAME     |A / AAAA |    NODATA |
|

Para perfis com método de encaminhamento definido para MultiValue:

|Pedido de consulta de entrada|     Tipo de ponto final |    Resposta Fornecida|
|--|--|--|
|QUALQUER |    Mistura de A e AAAA |    Pontos finais alvo|
|A |    Mistura de A e AAAA |    Apenas pontos finais alvo do tipo A|
|AAAA    |Mistura de A e AAAA|     Apenas pontos finais alvo do tipo AAAA|
|CNAME |    Mistura de A e AAAA |    NODATA |

### <a name="can-i-use-a-profile-with-ipv4--ipv6-addressed-endpoints-in-a-nested-profile"></a>Posso usar um perfil com iPv4/ IPv6 endpoints endpoints num perfil aninhado?

Sim, pode, com a exceção, que um perfil do tipo MultiValue não pode ser um perfil-mãe num conjunto de perfil aninhado.

### <a name="i-stopped-an-web-application-endpoint-in-my-traffic-manager-profile-but-i-am-not-receiving-any-traffic-even-after-i-restarted-it-how-can-i-fix-this"></a>Parei um ponto final de aplicação web no meu perfil de Gerente de Tráfego, mas não estou a receber nenhum tráfego mesmo depois de o ter reiniciado. Como posso resolver isto?

Quando um ponto final de aplicação web Azure é interrompido O Gestor de Tráfego deixa de verificar a sua saúde e reinicia os controlos de saúde apenas depois de detetar que o ponto final foi reiniciado. Para evitar este atraso, desative e, em seguida, reensibiliar esse ponto final no perfil de Gestor de Tráfego depois de reiniciar o ponto final.

### <a name="can-i-use-traffic-manager-even-if-my-application-does-not-have-support-for-http-or-https"></a>Posso utilizar o Traffic Manager mesmo que a minha aplicação não tenha suporte para HTTP ou HTTPS?

Sim. Pode especificar o TCP como o protocolo de monitorização e o Gestor de Tráfego pode iniciar uma ligação TCP e aguardar uma resposta a partir do ponto final. Se o ponto final responder ao pedido de ligação com uma resposta para estabelecer a ligação, dentro do período de tempo limite, então esse ponto final é marcado como saudável.

### <a name="what-specific-responses-are-required-from-the-endpoint-when-using-tcp-monitoring"></a>Que respostas específicas são necessárias a partir do ponto final durante a utilização do controlo TCP?

Quando a monitorização da TCP é utilizada, o Gestor de Tráfego inicia um aperto de mão TCP de três vias enviando um pedido SYN para o ponto final na porta especificada. Em seguida, aguarda uma resposta SYN-ACK do ponto final por um período de tempo (especificado nas definições de tempo limite).

- Se uma resposta SYN-ACK for recebida dentro do período de tempo especificado nas definições de monitorização, esse ponto final é considerado saudável. Uma FIN ou FIN-ACK é a resposta esperada do Gestor de Tráfego quando encerra regularmente uma tomada.
- Se uma resposta SYN-ACK for recebida após o intervalo especificado, o Gestor de Tráfego responderá com um RST para reiniciar a ligação.

### <a name="how-fast-does-traffic-manager-move-my-users-away-from-an-unhealthy-endpoint"></a>Quão rápido o Traffic Manager afasta os meus utilizadores de um ponto final pouco saudável?

O Traffic Manager fornece várias configurações que podem ajudá-lo a controlar o comportamento de falha do seu perfil de Gestor de Tráfego da seguinte forma:

- pode especificar que o Gestor de Tráfego sonda os pontos finais com mais frequência, definindo o Intervalo de Sondagem em 10 segundos. Isto garante que qualquer ponto final que não seja saudável pode ser detetado o mais rapidamente possível. 
- pode especificar quanto tempo esperar antes de um pedido de verificação de saúde esgotar os tempos (o valor mínimo de tempo de saída é de 5 segundos).
- pode especificar quantas falhas podem ocorrer antes que o ponto final seja marcado como insalubre. Este valor pode ser baixo como 0, caso em que o ponto final é marcado como insalubre assim que falha o primeiro exame de saúde. No entanto, a utilização do valor mínimo de 0 para o número tolerado de falhas pode levar a que os pontos finais sejam retirados da rotação devido a quaisquer problemas transitórios que possam ocorrer no momento da sondagem.
- pode especificar o tempo de vida (TTL) para que a resposta ao DNS seja tão baixa como 0. Ao fazê-lo, os dns não conseguem cache a resposta e cada nova consulta obtém uma resposta que incorpora a informação de saúde mais atualizada que o Gestor de Tráfego tem.

Ao utilizar estas definições, o Traffic Manager pode fornecer falhas abaixo de 10 segundos após um ponto final não ficar saudável e uma consulta de DNS é feita contra o perfil correspondente.

### <a name="how-can-i-specify-different-monitoring-settings-for-different-endpoints-in-a-profile"></a>Como posso especificar diferentes definições de monitorização para diferentes pontos finais num perfil?

As definições de monitorização do Gestor de Tráfego estão a um nível de perfil. Se precisar de utilizar uma definição de monitorização diferente para apenas um ponto final, pode ser feito tendo esse ponto final como um [perfil aninhado](traffic-manager-nested-profiles.md) cujas definições de monitorização são diferentes do perfil dos pais.

### <a name="how-can-i-assign-http-headers-to-the-traffic-manager-health-checks-to-my-endpoints"></a>Como posso atribuir cabeçalhos HTTP aos controlos de saúde do Gestor de Tráfego para os meus pontos finais?

O Traffic Manager permite-lhe especificar cabeçalhos personalizados nos controlos de saúde HTTP(S) que inicia nos seus pontos finais. Se quiser especificar um cabeçalho personalizado, pode fazê-lo ao nível do perfil (aplicável a todos os pontos finais) ou especi-lo no nível do ponto final. Se um cabeçalho for definido em ambos os níveis, então o especificado no nível do ponto final irá sobrepor-se ao nível de perfil um.
Um caso de uso comum para isso é especificar cabeçalhos hospedeiros para que os pedidos do Traffic Manager possam ser encaminhados corretamente para um ponto final hospedado em um ambiente multi-inquilino. Outro caso de utilização deste caso é identificar os pedidos do Gestor de Tráfego a partir dos registos de pedidos http(s) de um ponto final

### <a name="what-host-header-do-endpoint-health-checks-use"></a>Que cabeçalho de anfitrião usam os controlos de saúde do ponto final?

Se não for fornecida nenhuma definição personalizada do cabeçalho do anfitrião, o cabeçalho do anfitrião utilizado pelo Traffic Manager é o nome DNS do alvo do ponto final configurado no perfil, se estiver disponível.

### <a name="what-are-the-ip-addresses-from-which-the-health-checks-originate"></a>Quais são os endereços IP de onde provêm os controlos sanitários?

Clique [aqui](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) para saber como recuperar as listas de endereços IP a partir dos quais os controlos de saúde do Traffic Manager podem ser originados. Pode utilizar rest API, Azure CLI ou Azure PowerShell para recuperar a lista mais recente. Reveja os IPs listados para garantir que as ligações recebidas destes endereços IP sejam permitidas nos pontos finais para verificar o seu estado de saúde.

Exemplo utilizando a Azure PowerShell:

```azurepowershell-interactive
$serviceTags = Get-AzNetworkServiceTag -Location eastus
$result = $serviceTags.Values | Where-Object { $_.Name -eq "AzureTrafficManager" }
$result.Properties.AddressPrefixes
```

> [!NOTE]
> Os endereços IP públicos podem ser alterados sem aviso prévio. Certifique-se de recuperar as informações mais recentes usando o ficheiro API da Tag de Serviço ou descarregamento JSON.

### <a name="how-many-health-checks-to-my-endpoint-can-i-expect-from-traffic-manager"></a>Quantos cheques de saúde para o meu ponto final posso esperar do Gerente de Tráfego?

O número de verificações de saúde do Traffic Manager que chegam ao seu ponto final depende do seguinte:

- o valor que definiu para o intervalo de monitorização (intervalo menor significa mais pedidos de aterragem no seu ponto final em qualquer período de tempo).
- o número de locais de onde os controlos de saúde são originários (os endereços IP de onde se pode esperar que esses controlos sejam listados nas FAQ anteriores).

### <a name="how-can-i-get-notified-if-one-of-my-endpoints-goes-down"></a>Como posso ser notificado se um dos meus pontos finais vai para baixo?

Uma das métricas fornecidas pelo Traffic Manager é o estado de saúde dos pontos finais num perfil. Pode ver isto como um agregado de todos os pontos finais dentro de um perfil (por exemplo, 75% dos seus pontos finais são saudáveis), ou, a um nível por ponto final. As métricas do Traffic Manager são expostas através do Azure Monitor e pode utilizar as suas [capacidades de alerta](../azure-monitor/alerts/alerts-metric.md) para receber notificações quando há uma alteração no estado de saúde do seu ponto final. Para mais detalhes, consulte [as métricas e alertas do Gestor de Tráfego.](traffic-manager-metrics-alerts.md)  

## <a name="traffic-manager-nested-profiles"></a>Perfis aninhados do Gestor de Tráfego

### <a name="how-do-i-configure-nested-profiles"></a>Como posso configurar perfis aninhados?

Os perfis de Gerente de Tráfego Aninhado podem ser configurados usando tanto o Azure Resource Manager como os clássicos apis Azure REST, cmdlets Azure PowerShell e comandos Azure CLI de plataforma cruzada. Também são apoiados através do novo portal Azure.

### <a name="how-many-layers-of-nesting-does-traffic-manger-support"></a>Quantas camadas de nidificação a Traffic Manger suporta?

Pode nidificar perfis até 10 níveis de profundidade. Não são permitidos "loops".

### <a name="can-i-mix-other-endpoint-types-with-nested-child-profiles-in-the-same-traffic-manager-profile"></a>Posso misturar outros tipos de pontos finais com perfis infantis aninhados, no mesmo perfil de Gerente de Tráfego?

Sim. Não existem restrições na forma como combina pontos finais de diferentes tipos dentro de um perfil.

### <a name="how-does-the-billing-model-apply-for-nested-profiles"></a>Como é que o modelo de faturação se aplica aos perfis de Nested?

Não existe um impacto negativo nos preços da utilização de perfis aninhados.

A faturação do Gestor de Tráfego tem dois componentes: cheques de saúde de ponta e milhões de consultas de DNS

* Verificações de saúde endpoint: Não há qualquer custo para um perfil de criança quando configurado como um ponto final em um perfil de pai. A monitorização dos pontos finais no perfil da criança é faturada da forma habitual.
* Consultas DNS: Cada consulta é contada apenas uma vez. Uma consulta contra um perfil dos pais que retorna um ponto final de um perfil de criança é contada apenas contra o perfil dos pais.

Para mais detalhes, consulte a [página de preços do Gestor de Tráfego.](https://azure.microsoft.com/pricing/details/traffic-manager/)

### <a name="is-there-a-performance-impact-for-nested-profiles"></a>Existe um impacto de desempenho para perfis aninhados?

N.º Não há impacto de desempenho incorrido ao utilizar perfis aninhados.

Os servidores de nome do Gestor de Tráfego atravessam internamente a hierarquia do perfil ao processar cada consulta DNS. Uma consulta de DNS a um perfil dos pais pode receber uma resposta DNS com um ponto final a partir de um perfil infantil. É utilizado um único registo CNAME quer esteja a utilizar um único perfil ou perfis aninhados. Não há necessidade de criar um registo CNAME para cada perfil na hierarquia.

### <a name="how-does-traffic-manager-compute-the-health-of-a-nested-endpoint-in-a-parent-profile"></a>Como é que o Traffic Manager calcula a saúde de um ponto final aninhado num perfil de pai?

O perfil dos pais não faz verificações de saúde diretamente na criança. Em vez disso, a saúde dos pontos finais do perfil da criança é utilizada para calcular a saúde geral do perfil da criança. Esta informação é propagada pela hierarquia do perfil aninhado para determinar a saúde do ponto final aninhado. O perfil dos pais utiliza esta saúde agregada para determinar se o tráfego pode ser direcionado para a criança.

A tabela seguinte descreve o comportamento dos controlos de saúde do Traffic Manager para um ponto final aninhado.

| Estado do monitor do perfil da criança | Estado do Monitor de Ponto Final dos pais | Notas |
| --- | --- | --- |
| Desativado. O perfil da criança foi desativado. |Parada |O estado do ponto final dos pais está parado, não incapacitado. O estado de deficiente está reservado para indicar que desativou o ponto final no perfil dos pais. |
| Degradado. Pelo menos um ponto final do perfil da criança está num estado degradado. |Online: o número de pontos finais online no perfil da criança é pelo menos o valor dos Pontos MinChildEndpoints.<BR>CheckingEndpoint: o número de pontos finais Online plus CheckingEndpoint no perfil da criança é pelo menos o valor dos pontos MinChildEndpoints.<BR>Degradado: caso contrário. |O tráfego é encaminhado para um ponto final de estado CheckingEndpoint. Se os pontos MinChildEndpoints estiverem demasiado altos, o ponto final está sempre degradado. |
| Online. Pelo menos um ponto final de perfil infantil é um estado online. Nenhum ponto final está no estado degradado. |Veja acima. | |
| Verificação de Pontos de Final. Pelo menos um ponto final do perfil da criança é "CheckingEndpoint". Nenhum ponto final é 'Online' ou 'Degradado' |Mesmo que acima. | |
| Inativo. Todos os pontos finais do perfil da criança são desativado ou parados, ou este perfil não tem pontos finais. |Parada | |

## <a name="next-steps"></a>Passos seguintes:

- Saiba mais sobre [a monitorização do ponto final](../traffic-manager/traffic-manager-monitoring.md)do Traffic Manager e a falha automática.
- Saiba mais sobre [os métodos de encaminhamento de tráfego](../traffic-manager/traffic-manager-routing-methods.md)do Traffic Manager .