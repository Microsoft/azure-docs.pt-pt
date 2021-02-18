---
title: PERGUNTAS Frequentes - Solução de Monitor de Desempenho de Rede em Azure | Microsoft Docs
description: Este artigo captura as perguntas frequentes sobre o Monitor de Desempenho da Rede em Azure. O Monitor de Desempenho da Rede (NPM) ajuda-o a monitorizar o desempenho das suas redes em tempo real e a detetar e localizar estrangulamentos de desempenho da rede.
ms.subservice: logs
ms.topic: conceptual
author: vinynigam
ms.author: vinigam
ms.date: 10/12/2018
ms.openlocfilehash: c58f94bcdb659eed67ebf023af473545d8cee1a7
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100587272"
---
# <a name="network-performance-monitor-solution-faq"></a>Solução de monitorização de desempenho de rede FAQ

![Símbolo do Monitor de Desempenho da Rede](media/network-performance-monitor-faq/npm-symbol.png)

> [!IMPORTANT]
> A partir de 1 de julho de 2021, não poderá adicionar novos testes num espaço de trabalho existente ou permitir um novo espaço de trabalho no Network Performance Monitor. Pode continuar a utilizar os testes criados antes de 1 de julho de 2021. Para minimizar a perturbação do serviço nas suas cargas de trabalho atuais, [migrar os seus testes do Monitor de Desempenho da Rede para o novo Monitor de Ligação](https://docs.microsoft.com/azure/network-watcher/migrate-to-connection-monitor-from-network-performance-monitor) no Azure Network Watcher antes de 29 de fevereiro de 2024.

Este artigo captura as perguntas frequentes (FAQs) sobre Network Performance Monitor (NPM) em Azure

[O Network Performance Monitor](../../networking/network-monitoring-overview.md) é uma solução de [monitorização de rede híbrida](./network-performance-monitor-performance-monitor.md) baseada na nuvem que o ajuda a monitorizar o desempenho da rede entre vários pontos da sua infraestrutura de rede. Também ajuda a monitorizar a conectividade da rede para [os pontos finais](./network-performance-monitor-service-connectivity.md) de serviço e aplicação e [monitorizar o desempenho do Azure ExpressRoute](./network-performance-monitor-expressroute.md). 

O Monitor de Desempenho da Rede deteta problemas de rede como o blackholing de tráfego, erros de encaminhamento e problemas que os métodos convencionais de monitorização da rede não são capazes de detetar. A solução gera alertas e notifica-o quando existir uma falha do limiar de uma ligação de rede. Também garante a deteção atempada de problemas de desempenho de rede e localiza a origem do problema num dispositivo ou segmento de rede específico. 

Mais informações sobre as várias capacidades suportadas pelo [Network Performance Monitor](../../networking/network-monitoring-overview.md) estão disponíveis online.

## <a name="set-up-and-configure-agents"></a>Configurar e configurar agentes

### <a name="what-are-the-platform-requirements-for-the-nodes-to-be-used-for-monitoring-by-npm"></a>Quais são os requisitos da plataforma para que os nós sejam utilizados para monitorização por NPM?
Listados abaixo estão os requisitos da plataforma para as várias capacidades da NPM:

- As capacidades do Monitor de Desempenho e conectividade de serviço da NPM suportam tanto o servidor Windows como os sistemas operativos Windows desktops/cliente. As versões DE do servidor Windows suportadas são 2008 SP1 ou posterior. As versões windows/cliente suportadas são o Windows 10, Windows 8.1, Windows 8 e Windows 7. 
- A capacidade do Monitor ExpressRoute da NPM suporta apenas o sistema operativo Windows server (2008 SP1 ou posterior).

### <a name="can-i-use-linux-machines-as-monitoring-nodes-in-npm"></a>Posso usar as máquinas Linux como nós de monitorização em NPM?
A capacidade de monitorizar redes utilizando nós baseados em Linux está agora geralmente disponível. Aceda ao agente [aqui.](../../virtual-machines/extensions/oms-linux.md) 

### <a name="what-are-the-size-requirements-of-the-nodes-to-be-used-for-monitoring-by-npm"></a>Quais são os requisitos de tamanho dos nós a utilizar para monitorização por NPM?
Para executar a solução NPM em VMs de nó para monitorizar as redes, os nós devem ter pelo menos 500-MB de memória e um núcleo. Não é preciso usar nós separados para executar NPM. A solução pode funcionar em nós que têm outras cargas de trabalho em execução. A solução tem a capacidade de parar o processo de monitorização se utilizar mais de 5% de CPU.

### <a name="to-use-npm-should-i-connect-my-nodes-as-direct-agent-or-through-system-center-operations-manager"></a>Para utilizar o NPM, devo ligar os meus nós como agente direto ou através do Gestor de Operações do Centro de Sistema?
Tanto o Monitor de Desempenho como as capacidades do Monitor de Conectividade de Serviço suportam nós [ligados como Agentes Diretos](../agents/agent-windows.md) e [ligados através do Gestor de Operações.](../agents/om-agents.md)

Para a capacidade do Monitor ExpressRoute, os nós Azure devem ser ligados apenas como Agentes Diretos. Os nós Azure, que estão ligados através do Gestor de Operações, não são suportados. Para os nós no local, os nós ligados como Agentes Diretos e através do Gestor de Operações são suportados para monitorizar um circuito ExpressRoute.

### <a name="which-protocol-among-tcp-and-icmp-should-be-chosen-for-monitoring"></a>Que protocolo entre a TCP e o ICMP deve ser escolhido para monitorização?
Se estiver a monitorizar a sua rede utilizando nós baseados em servidores do Windows, recomendamos que utilize o TCP como protocolo de monitorização, uma vez que proporciona uma maior precisão. 

O ICMP é recomendado para os nós baseados em computadores windows/clientes baseados no sistema operativo. Esta plataforma não permite que os dados da TCP sejam enviados através de tomadas brutas, que a NPM utiliza para descobrir a topologia da rede.

Pode obter mais detalhes sobre as vantagens relativas de cada protocolo [aqui.](./network-performance-monitor-performance-monitor.md#choose-the-protocol)

### <a name="how-can-i-configure-a-node-to-support-monitoring-using-tcp-protocol"></a>Como posso configurar um nó para apoiar a monitorização utilizando o protocolo TCP?
Para que o nó suporte a monitorização utilizando o protocolo TCP: 
* Certifique-se de que a plataforma de nó é o Windows Server (2008 SP1 ou mais tarde).
* Executar [EnableRules.ps1](https://aka.ms/npmpowershellscript) script PowerShell no nó. Consulte [as instruções](./network-performance-monitor.md#configure-log-analytics-agents-for-monitoring) para mais detalhes.


### <a name="how-can-i-change-the-tcp-port-being-used-by-npm-for-monitoring"></a>Como posso alterar a porta TCP que está a ser utilizada pela NPM para monitorização?
Pode alterar a porta TCP utilizada pela NPM para monitorização, executando o [EnableRules.ps1](https://aka.ms/npmpowershellscript) script. Precisa introduzir o número de porta que pretende utilizar como parâmetro. Por exemplo, para ativar a TCP na porta 8060, corra `EnableRules.ps1 8060` . Certifique-se de que utiliza a mesma porta TCP em todos os nós utilizados para a monitorização.

O script configura apenas o Windows Firewall localmente. Se tiver regras de firewall de rede ou grupo de segurança de rede (NSG), certifique-se de que permitem o tráfego destinado à porta TCP utilizada pela NPM.

### <a name="how-many-agents-should-i-use"></a>Quantos agentes devo usar?
Deve utilizar pelo menos um agente para cada sub-rede que pretende monitorizar.

### <a name="what-is-the-maximum-number-of-agents-i-can-use-or-i-see-error--youve-reached-your-configuration-limit"></a>Qual é o número máximo de agentes que posso usar ou vejo erro".... atingiu o seu limite de configuração"?
O NPM limita o número de IPs a 5000 IPs por espaço de trabalho. Se um nó tiver os endereços IPv4 e IPv6, isto contará como 2 IPs para esse nó. Assim, este limite de 5000 IPs decidiria o limite máximo do número de agentes. Pode eliminar os agentes inativos do separador Nodes no separador NPM >> Configurar. O NPM também mantém a história de todos os IPs que alguma vez foram atribuídos ao VM hospedando o agente e cada um é contado como IP separado contribuindo para esse limite superior de 5000 IPs. Para libertar os IPs para o seu espaço de trabalho, pode utilizar a página Nodes para eliminar os IPs que não estão a ser utilizados.

## <a name="monitoring"></a>Monitorização

### <a name="how-are-loss-and-latency-calculated"></a>Como são calculadas as perdas e a latência
Os agentes de origem enviam pedidos de TCP SYN (se o TCP for escolhido como protocolo de monitorização) ou pedidos de ECHO ICMP (se o ICMP for escolhido como protocolo de monitorização) para destino IP a intervalos regulares para garantir que todos os caminhos entre a combinação IP de destino de origem estão cobertos. A percentagem de pacotes recebidos e o tempo de ida e volta do pacote é medido para calcular a perda e a latência de cada caminho. Estes dados são agregados ao longo do intervalo de votação e em todos os caminhos para obter os valores agregados de perda e latência para a combinação de IP para o intervalo de votação particular.

### <a name="with-what-frequency-does-the-source-agent-send-packets-to-the-destination-for-monitoring"></a>Com que frequência o agente de origem envia pacotes para o destino para monitorização?
Para as capacidades do Monitor de Desempenho e do Monitor ExpressRoute, a fonte envia pacotes a cada 5 segundos e regista as medições da rede. Estes dados são agregados ao longo de um intervalo de 3 minutos de votação para calcular os valores médios e máximos de perda e latência. Para a capacidade do Monitor de Conectividade de Serviço, a frequência de envio dos pacotes para a medição da rede é determinada pela frequência inserida pelo utilizador para o teste específico enquanto configura o teste.

### <a name="how-many-packets-are-sent-for-monitoring"></a>Quantos pacotes são enviados para monitorização?
O número de pacotes enviados pelo agente de origem para o destino numa sondagem é adaptável e é decidido pelo nosso algoritmo proprietário, que pode ser diferente para diferentes topologias de rede. Mais o número de caminhos de rede entre a combinação IP de destino de origem, mais é o número de pacotes que são enviados. O sistema garante que todos os caminhos entre a combinação IP de destino de origem estão cobertos.

### <a name="how-does-npm-discover-network-topology-between-source-and-destination"></a>Como é que a NPM descobre a topologia da rede entre a origem e o destino?
O NPM usa um algoritmo proprietário baseado em Traceroute para descobrir todos os caminhos e lúpulo entre a fonte e o destino.

### <a name="does-npm-provide-routing-and-switching-level-info"></a>O NPM fornece informações de nível de encaminhamento e de comutação 
Embora a NPM possa detetar todas as rotas possíveis entre o agente de origem e o destino, não dá visibilidade em que rota foi tomada pelos pacotes enviados pelas suas cargas de trabalho específicas. A solução pode ajudá-lo a identificar os caminhos e o lúpulo subjacente da rede, que estão a adicionar mais latência do que esperava.

### <a name="why-are-some-of-the-paths-unhealthy"></a>Por que alguns dos caminhos não são saudáveis?
Diferentes caminhos de rede podem existir entre os IPs de origem e destino e cada caminho pode ter um valor diferente de perda e latência. O NPM marca esses caminhos como pouco saudáveis (denotados com cor vermelha) para os quais os valores de perda e/ou latência são superiores ao respetivo limiar estabelecido na configuração de monitorização.

### <a name="what-does-a-hop-in-red-color-signify-in-the-network-topology-map"></a>O que significa um salto de cor vermelha no mapa da topologia da rede?
Se um salto é vermelho, significa que faz parte de pelo menos um caminho pouco saudável. O NPM só marca os caminhos como insalubres, não segrega o estado de saúde de cada caminho. Para identificar o lúpulo problemático, você pode ver a latência hop-by-hop e segregar os que adicionam mais do que o esperado latência.

### <a name="how-does-fault-localization-in-performance-monitor-work"></a>Como funciona a localização por falhas no Monitor de Desempenho?
O NPM usa um mecanismo probabilístico para atribuir probabilidades de falhas a cada caminho de rede, segmento de rede e o lúpulo de rede constituinte com base no número de caminhos insalubres dos quais fazem parte. À medida que os segmentos de rede e o lúpulo se tornam parte de um maior número de caminhos insalubres, a probabilidade de falha associada a eles aumenta. Este algoritmo funciona melhor quando você tem muitos nós com o agente NPM ligados uns aos outros, uma vez que isso aumenta os pontos de dados para calcular as probabilidades de falha.

### <a name="how-can-i-create-alerts-in-npm"></a>Como posso criar alertas no NPM?
Atualmente, a criação de alertas da NPM UI está a falhar devido a um problema conhecido. Por favor, [crie alertas manualmente.](../alerts/alerts-log.md)

### <a name="what-are-the-default-log-analytics-queries-for-alerts"></a>Quais são as consultas padrão de Log Analytics para alertas
Consulta do monitor de desempenho

```kusto
NetworkMonitoring
 | where (SubType == "SubNetwork" or SubType == "NetworkPath") 
 | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy") and RuleName == "<<your rule name>>"
```

Consulta do monitor de conectividade de serviço

```kusto
NetworkMonitoring
 | where (SubType == "EndpointHealth" or SubType == "EndpointPath")
 | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy" or ServiceResponseHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy") and TestName == "<<your test name>>"
```

Consultas de monitor expressRoute: Consulta de circuitos

```kusto
NetworkMonitoring
 | where (SubType == "ERCircuitTotalUtilization") and (UtilizationHealthState == "Unhealthy") and CircuitResourceId == "<<your circuit resource ID>>"
```

Peering privado

```kusto
NetworkMonitoring
 | where (SubType == "ExpressRoutePeering" or SubType == "ERVNetConnectionUtilization" or SubType == "ExpressRoutePath")   
 | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy" or UtilizationHealthState == "Unhealthy") and CircuitName == "<<your circuit name>>" and VirtualNetwork == "<<vnet name>>"
```

Peering da Microsoft

```kusto
NetworkMonitoring
 | where (SubType == "ExpressRoutePeering" or SubType == "ERMSPeeringUtilization" or SubType == "ExpressRoutePath")
 | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy" or UtilizationHealthState == "Unhealthy") and CircuitName == ""<<your circuit name>>" and PeeringType == "MicrosoftPeering"
```

Consulta comum

```kusto
NetworkMonitoring
 | where (SubType == "ExpressRoutePeering" or SubType == "ERVNetConnectionUtilization" or SubType == "ERMSPeeringUtilization" or SubType == "ExpressRoutePath")
 | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy" or UtilizationHealthState == "Unhealthy")
```

### <a name="can-npm-monitor-routers-and-servers-as-individual-devices"></a>O NPM pode monitorizar os routers e servidores como dispositivos individuais?
O NPM apenas identifica o nome IP e anfitrião do lúpulo de rede subjacente (comutadores, routers, servidores, etc.) entre os IPs de origem e destino. Também identifica a latência entre estes lúpulos identificados. Não monitoriza individualmente estes lúpulos subjacentes.

### <a name="can-npm-be-used-to-monitor-network-connectivity-between-azure-and-aws"></a>O NPM pode ser utilizado para monitorizar a conectividade da rede entre o Azure e o AWS?
Sim. Consulte as [redes monitor Azure, AWS e no local utilizando NPM](/archive/blogs/msoms/monitor-on-premises-cloud-iaas-and-hybrid-networks-using-oms-network-performance-monitor) para obter mais detalhes.

### <a name="is-the-expressroute-bandwidth-usage-incoming-or-outgoing"></a>O uso da largura de banda ExpressRoute está a chegar ou a sair?
O uso da largura de banda é o total de largura de banda de entrada e saída. É expressa em Bits/seg.

### <a name="can-we-get-incoming-and-outgoing-bandwidth-information-for-the-expressroute"></a>Podemos obter informações de largura de banda para o ExpressRoute?
Os valores de entrada e saída para largura de banda primária e secundária podem ser capturados.

Para obter informações de nível de estojo de MS, utilize a consulta abaixo mencionada na Pesquisa de Registo

```kusto
NetworkMonitoring
 | where SubType == "ERMSPeeringUtilization"
 | project CircuitName,PeeringName,BitsInPerSecond,BitsOutPerSecond 
```

Para obter informações de nível de estojo privado, utilize a consulta abaixo mencionada na Pesquisa de Registo

```kusto
NetworkMonitoring
 | where SubType == "ERVNetConnectionUtilization"
 | project CircuitName,PeeringName,BitsInPerSecond,BitsOutPerSecond
```

Para obter informações sobre o nível de circuito, utilize a consulta abaixo mencionada na Pesquisa de Registo

```kusto
NetworkMonitoring
 | where SubType == "ERCircuitTotalUtilization"
 | project CircuitName, BitsInPerSecond, BitsOutPerSecond
```

### <a name="which-regions-are-supported-for-npms-performance-monitor"></a>Que regiões são apoiadas para o Monitor de Desempenho da NPM?
O NPM pode monitorizar a conectividade entre redes em qualquer parte do mundo, a partir de um espaço de trabalho que é hospedado numa das [regiões apoiadas](./network-performance-monitor.md#supported-regions)

### <a name="which-regions-are-supported-for-npms-service-connectivity-monitor"></a>Que regiões são apoiadas para o Monitor de Conectividade de Serviços da NPM?
O NPM pode monitorizar a conectividade com os serviços em qualquer parte do mundo, a partir de um espaço de trabalho que é hospedado numa das [regiões apoiadas](./network-performance-monitor.md#supported-regions)

### <a name="which-regions-are-supported-for-npms-expressroute-monitor"></a>Que regiões são apoiadas para o Monitor ExpressRoute da NPM?
A NPM pode monitorizar os seus circuitos ExpressRoute localizados em qualquer região de Azure. Para embarcar no NPM, você precisará de um espaço de trabalho Log Analytics que deve ser hospedado em uma das [regiões apoiadas](../../expressroute/how-to-npm.md)

## <a name="troubleshoot"></a>Resolução de problemas

### <a name="why-are-some-of-the-hops-marked-as-unidentified-in-the-network-topology-view"></a>Porque é que alguns dos lúpulos são marcados como não identificados na vista da topologia da rede?
O NPM usa uma versão modificada de rastreá-lo para descobrir a topologia do agente de origem para o destino. Um lúpulo não identificado representa que o salto de rede não respondeu ao pedido de rastreio do agente de origem. Se três lúpulos de rede consecutivos não responderem ao rasto do agente, a solução marca o lúpulo sem resposta como não identificado e não tenta descobrir mais lúpulo.

Um lúpulo pode não responder a um rasto de um ou mais dos cenários abaixo:

* Os routers foram configurados para não revelar a sua identidade.
* Os dispositivos de rede não permitem ICMP_TTL_EXCEEDED tráfego.
* Uma firewall está a bloquear a resposta ICMP_TTL_EXCEEDED do dispositivo de rede.

Quando qualquer um dos pontos finais está em Azure, o traceroute mostra lúpulo não identificado, uma vez que a Infraestrutura Azure não revela identidade para rastrear. 

### <a name="i-get-alerts-for-unhealthy-tests-but-i-do-not-see-the-high-values-in-npms-loss-and-latency-graph-how-do-i-check-what-is-unhealthy"></a>Recebo alertas para testes insalubres, mas não vejo os valores elevados no gráfico de perda e latência da NPM. Como verifico o que não é saudável?
NPM levanta um alerta se o fim da latência entre a fonte e o destino cruza o limiar para qualquer caminho entre eles. Algumas redes têm múltiplos caminhos que ligam a mesma fonte e destino. NPM levanta um alerta é que qualquer caminho não é saudável. A perda e latência observadas nos gráficos é o valor médio para todos os caminhos, pelo que pode não mostrar o valor exato de um único caminho. Para entender onde o limiar foi violado, procure a coluna "SubType" no alerta. Se o problema for causado por um caminho, o valor do SubTip será NetworkPath (para testes de Monitor de Desempenho), EndpointPath (para testes de Monitor de Conectividade de Serviço) e ExpressRoutePath (para testes ExpressRotue Monitor). 

A consulta da amostra para encontrar é o caminho não é saudável:

```kusto
NetworkMonitoring
 | where ( SubType == "ExpressRoutePath")
 | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy" or UtilizationHealthState == "Unhealthy") and CircuitResourceID =="<your ER circuit ID>" and ConnectionResourceId == "<your ER connection resource id>"
 | project SubType, LossHealthState, LatencyHealthState, MedianLatency
```

### <a name="why-does-my-test-show-unhealthy-but-the-topology-does-not"></a>Por que o meu teste mostra pouco saudável, mas a topologia não 
O NPM monitoriza a perda, latência e topologia de ponta a ponta em intervalos diferentes. A perda e a latência são medidas uma vez a cada 5 segundos e agregadas a cada três minutos (para Monitor de Desempenho e Monitor de Rota Expresso) enquanto a topologia é calculada usando o percurso de rastreá-lo uma vez a cada 10 minutos. Por exemplo, entre 3:44 e 4:04, a topologia pode ser atualizada três vezes (3:44, 3:54, 4:04), mas a perda e a latência são atualizadas cerca de sete vezes (3:44, 3:47, 3:50, 3:53, 3:56, 3:59, 4:52). A topologia gerada às 3:54 será prestada pela perda e latência que é calculada às 3:56, 3:59 e 4:02. Suponha que receba um alerta de que o seu circuito ereiro não era saudável às 3:59. Entras no NPM e tentas definir a hora da topologia para 3:59. NPM tornará a topologia gerada às 3:54. Para compreender a última topologia conhecida da sua rede, compare os campos TimeProcessed (tempo em que a perda e a latência foram calculadas) e TracerouteCompletedTime (tempo em que a topologia foi calculada). 

### <a name="what-is-the-difference-between-the-fields-e2emedianlatency-and-avghoplatencylist-in-the-networkmonitoring-table"></a>Qual é a diferença entre os campos E2EMedianLatency e AvgHopLatencyList na tabela NetworkMonitoring
E2EMedianLatency é a latência atualizada a cada três minutos após agregar os resultados dos testes de ping tcp, enquanto o AvgHopLatencyList é atualizado a cada 10 minutos com base no rastreamento. Para compreender a hora exata em que a E2EMedianLatency foi calculada, utilize o tempo de campo Processado. Para entender a hora exata em que o traceroute completou e atualizou a AvgHopLatencyList, utilize o campo TracerouteCompletedTime

### <a name="why-does-hop-by-hop-latency-numbers-differ-from-hoplatencyvalues"></a>Por que os números de latência hop-by-hop diferem de HopLatencyValues 
HopLatencyValues são fonte para o ponto final.
Por exemplo: Lúpulo - A,B,C. AvgHopLatency - 10,15,20. Isto significa que a fonte para Uma latência = 10, fonte para a latência B = 15 e fonte para latência C é de 20. UI calculará latência de salto A-B como 5 na topologia

### <a name="the-solution-shows-100-loss-but-there-is-connectivity-between-the-source-and-destination"></a>A solução mostra perda de 100%, mas há conectividade entre a fonte e o destino
Isto pode acontecer se a firewall do anfitrião ou a firewall intermédia (firewall de rede ou Azure NSG) estiverem bloqueando a comunicação entre o agente de origem e o destino sobre a porta que está sendo utilizado para monitorização por NPM (por defeito a porta é 8084, a menos que o cliente tenha alterado isso).

* Para verificar se a firewall do anfitrião não está a bloquear a comunicação na porta requerida, consulte o estado de saúde dos nós de origem e destino a partir da seguinte vista: Monitor de desempenho de rede -> Configuração -> Nós. 
  Se não forem saudáveis, consulte as instruções e tome medidas corretivas. Se os nós estiverem saudáveis, mova-se para o passo B. abaixo.
* Para verificar se uma firewall de rede intermédia ou Azure NSG não bloqueia a comunicação na porta necessária, utilize o utilitário PsPing de terceiros utilizando as instruções abaixo:
  * utilitário psping está disponível para download [aqui](/sysinternals/downloads/psping) 
  * Corra seguindo o comando do nó de origem.
    * psping -n 15 \<destination node IPAddress\> :p ortNumber Por defeito NPM usa porta 8084. Caso tenha alterado explicitamente esta introdução utilizando o EnableRules.ps1 script, insira o número de porta personalizado que está a utilizar). Este é um ping da máquina Azure para o local
* Verifique se os pings são bem sucedidos. Caso contrário, indica que uma firewall de rede intermédia ou a Azure NSG está a bloquear o tráfego nesta porta.
* Agora, executar o comando do nó de destino para o nó de origem IP.


### <a name="there-is-loss-from-node-a-to-b-but-not-from-node-b-to-a-why"></a>Há perda do nó A para B, mas não do nó B para A. Porquê?
Como os caminhos de rede entre A e B podem ser diferentes dos caminhos de rede entre B e A, podem observar-se diferentes valores de perda e latência.

### <a name="why-are-all-my-expressroute-circuits-and-peering-connections-not-being-discovered"></a>Porque é que todos os meus circuitos ExpressRoute e conexões de observação não estão a ser descobertos?
A NPM descobre agora os circuitos ExpressRoute e as ligações de observação em todas as subscrições a que o utilizador tem acesso. Escolha todas as subscrições onde os recursos da Rota Expresso estão ligados e permita a monitorização de cada recurso descoberto. O NPM procura objetos de ligação ao descobrir um espremiado privado, por isso, verifique se um VNET está associado ao seu espreitamento. A NPM não deteta circuitos e espreitamentos que se encontram num inquilino diferente do espaço de trabalho Log Analytics.

### <a name="the-er-monitor-capability-has-a-diagnostic-message-traffic-is-not-passing-through-any-circuit-what-does-that-mean"></a>A capacidade do Monitor ER tem uma mensagem de diagnóstico "O tráfego não está a passar por nenhum circuito". O que significa?

Pode haver um cenário em que haja uma ligação saudável entre os nós no local e os nós Azure, mas o tráfego não está a passar pelo circuito ExpressRoute configurado para ser monitorizado pela NPM. 

Este problema pode ocorrer se:

* O circuito das Urgências está em baixo.
* Os filtros de rota são configurados de forma a darem prioridade a outras rotas (como uma ligação VPN ou outro circuito ExpressRoute) sobre o circuito ExpressRoute pretendido. 
* Os nós no local e os nós Azure escolhidos para monitorizar o circuito ExpressRoute na configuração de monitorização, não têm conectividade uns com os outros durante o circuito ExpressRoute pretendido. Certifique-se de que escolheu os nós corretos que têm conectividade uns com os outros durante o circuito ExpressRoute que pretende monitorizar.

### <a name="why-does-expressroute-monitor-report-my-circuitpeering-as-unhealthy-when-it-is-available-and-passing-data"></a>Porque é que o ExpressRoute Monitor reporta o meu circuito/espreitar como pouco saudável quando está disponível e a passar dados.
O ExpressRoute Monitor compara os valores de desempenho da rede (perda, latência e utilização da largura de banda) reportados pelos agentes/serviço com os limiares definidos durante a Configuração. Para um circuito, se a utilização da largura de banda reportada for maior do que o limiar definido na Configuração, o circuito está marcado como pouco saudável. Para os seus pares, se a perda, latência ou utilização da largura de banda reportada for maior do que o limiar estabelecido na Configuração, o espreitamento é marcado como insalubre. A NPM não utiliza métricas ou qualquer outra forma de dados para decidir o estado de saúde.

### <a name="why-does-expressroute-monitorbandwidth-utilization-report-a-value-different-from-metrics-bits-inout"></a>Porque é que a utilização da largura de banda do ExpressRoute Monitor reporta um valor diferente das métricas de bits dentro/fora
Para o ExpressRoute Monitor, a utilização da largura de banda é a média de largura de banda de entrada e saída nos últimos 20 minutos é expressa em Bits/seg. Para as métricas da Rota Expressa, os pontos de informação de entrada/saída são por minuto. Internamente, o conjunto de dados utilizado para ambos é o mesmo, mas a agregação varia entre as métricas de NPM e ER. Para monitorização granular, minuto a minuto e alertas rápidos, recomendamos a definição de alertas diretamente nas métricas er

### <a name="while-configuring-monitoring-of-my-expressroute-circuit-the-azure-nodes-are-not-being-detected"></a>Enquanto configura a monitorização do meu circuito ExpressRoute, os nós Azure não estão a ser detetados.
Isto pode acontecer se os nós Azure estiverem ligados através do Gestor de Operações. A capacidade do Monitor ExpressRoute suporta apenas os nós Azure que estão ligados como Agentes Diretos.

### <a name="i-cannot-discover-by-expressroute-circuits-in-the-oms-portal"></a>I can't Discover by ExpressRoute circuits in the OMS portal
Embora o NPM possa ser utilizado tanto a partir do portal Azure como do portal OMS, a descoberta do circuito na capacidade do ExpressRoute Monitor funciona apenas através do portal Azure. Uma vez descobertos os circuitos através do portal Azure, pode então utilizar a capacidade em qualquer um dos dois portais. 

### <a name="in-the-service-connectivity-monitor-capability-the-service-response-time-network-loss-as-well-as-latency-are-shown-as-na"></a>Na capacidade do Monitor de Conectividade de Serviço, o tempo de resposta do serviço, a perda de rede, bem como a latência são mostrados como NA
Isto pode acontecer se um ou mais for verdade:

* O serviço está em baixo.
* O nó utilizado para verificar a conectividade da rede ao serviço está em baixo.
* O alvo introduzido na configuração do teste está incorreto.
* O nó não tem conectividade de rede.

### <a name="in-the-service-connectivity-monitor-capability-a-valid-service-response-time-is-shown-but-network-loss-as-well-as-latency-are-shown-as-na"></a>Na capacidade do Monitor de Conectividade de Serviço, é mostrado um tempo de resposta de serviço válido, mas a perda de rede, bem como a latência, são mostradas como NA
 Isto pode acontecer se um ou mais for verdade:

* Se o nó utilizado para verificar a conectividade da rede ao serviço for uma máquina cliente Windows, ou o serviço-alvo está a bloquear pedidos de ICMP ou uma firewall de rede está a bloquear pedidos de ICMP originários do nó.
* A caixa de verificação de medições de rede Perform está em branco na configuração do teste.

### <a name="in-the-service-connectivity-monitor-capability-the-service-response-time-is-na-but-network-loss-as-well-as-latency-are-valid"></a>Na capacidade do Monitor de Conectividade de Serviço, o tempo de resposta do serviço é NA, mas a perda de rede, bem como a latência, são válidas
Isto pode acontecer se o serviço alvo não for uma aplicação web, mas o teste é configurado como um teste Web. Edite a configuração do teste e escolha o tipo de teste como Rede em vez de Web.

## <a name="miscellaneous"></a>Diversos

### <a name="is-there-a-performance-impact-on-the-node-being-used-for-monitoring"></a>Existe um impacto de desempenho no nó utilizado para monitorização?
O processo de NPM está configurado para parar se utilizar mais de 5% dos recursos de CPU hospedeiros. Isto é para garantir que pode continuar a usar os nós para as suas cargas de trabalho habituais sem afetar o desempenho.

### <a name="does-npm-edit-firewall-rules-for-monitoring"></a>A NPM edita regras de firewall para monitorização?
O NPM apenas cria uma regra local do Windows Firewall sobre os nós em que é executado o script powerShell EnableRules.ps1 para permitir que os agentes criem ligações TCP entre si na porta especificada. A solução não modifica nenhuma firewall de rede ou regras do Grupo de Segurança de Rede (NSG).

### <a name="how-can-i-check-the-health-of-the-nodes-being-used-for-monitoring"></a>Como posso verificar a saúde dos nós que estão a ser utilizados para a monitorização?
Pode visualizar o estado de saúde dos nós utilizados para a monitorização a partir da seguinte vista: Monitor de desempenho de rede -> Configuração -> Nós. Se um nó não for saudável, pode ver os detalhes do erro e tomar as medidas sugeridas.

### <a name="can-npm-report-latency-numbers-in-microseconds"></a>A NPM pode reportar números de latência em microsegundos?
O NPM completa os números de latência na UI e em milissegundos. Os mesmos dados são armazenados numa granularidade mais elevada (às vezes até quatro casas decimais).

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre o Monitor de Desempenho da Rede referindo-se à [solução Monitor de Desempenho da Rede em Azure](./network-performance-monitor.md).
