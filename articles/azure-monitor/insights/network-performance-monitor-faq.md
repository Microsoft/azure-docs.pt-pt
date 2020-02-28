---
title: FAQs - Solução de Monitor de Desempenho de Rede em Azure Microsoft Docs
description: Este artigo captura as perguntas frequentes sobre o Network Performance Monitor em Azure. O Monitor de Desempenho da Rede (NPM) ajuda-o a monitorizar o desempenho das suas redes em tempo real e a detetar e localizar estrangulamentos de desempenho da rede.
ms.subservice: logs
ms.topic: conceptual
author: vinynigam
ms.author: vinigam
ms.date: 10/12/2018
ms.openlocfilehash: 0ef50dfd4d9c6eb0066e54b76167b9934fbb9cf0
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77654438"
---
# <a name="network-performance-monitor-solution-faq"></a>Solução de Monitor de Desempenho de Rede FAQ

![Símbolo do Monitor de Desempenho da Rede](media/network-performance-monitor-faq/npm-symbol.png)

Este artigo captura as perguntas frequentemente feitas (PERGUNTAS) sobre o Monitor de Desempenho da Rede (NPM) em Azure

[O Network Performance Monitor](/azure/networking/network-monitoring-overview) é uma solução [de monitorização](../../azure-monitor/insights/network-performance-monitor-performance-monitor.md) de rede híbrida baseada na nuvem que o ajuda a monitorizar o desempenho da rede entre vários pontos da sua infraestrutura de rede. Também ajuda a monitorizar a conectividade da rede com [os pontos finais](../../azure-monitor/insights/network-performance-monitor-service-connectivity.md) de serviço e aplicação e [a monitorizar o desempenho do Azure ExpressRoute.](../../azure-monitor/insights/network-performance-monitor-expressroute.md) 

O Network Performance Monitor deteta problemas de rede como blackholing de tráfego, erros de encaminhamento e problemas que os métodos convencionais de monitorização da rede não são capazes de detetar. A solução gera alertas e notifica-o quando existir uma falha do limiar de uma ligação de rede. Também garante a deteção atempada de problemas de desempenho de rede e localiza a origem do problema num dispositivo ou segmento de rede específico. 

Mais informações sobre as várias capacidades suportadas pelo [Network Performance Monitor](https://docs.microsoft.com/azure/networking/network-monitoring-overview) estão disponíveis online.

## <a name="set-up-and-configure-agents"></a>Configurar e configurar agentes

### <a name="what-are-the-platform-requirements-for-the-nodes-to-be-used-for-monitoring-by-npm"></a>Quais são os requisitos da plataforma para que os nós sejam utilizados para monitorização por NPM?
Abaixo estão os requisitos da plataforma para as várias capacidades da NPM:

- As capacidades do Monitor de Desempenho e do Monitor de Conectividade do Serviço da NPM suportam tanto o servidor do Windows como os sistemas operativos Windows/cliente. As versões SISTEMA do servidor windows suportadas são 2008 SP1 ou posteriores. Os desktops/versões do cliente suportados pelo Windows 10, Windows 8.1, Windows 8 e Windows 7. 
- A capacidade do Monitor ExpressRoute da NPM suporta apenas o sistema operativo Windows server (2008 SP1 ou posterior).

### <a name="can-i-use-linux-machines-as-monitoring-nodes-in-npm"></a>Posso usar as máquinas Linux como narizes de monitorização na NPM?
A capacidade de monitorizar redes utilizando nódosos baseados em Linux está atualmente em pré-visualização. Contacte o seu Gestor de Contas para saber mais. Os agentes Linux fornecem capacidade de monitorização apenas para a capacidade do Monitor de Desempenho da NPM, e não estão disponíveis para as capacidades do Monitor de Conectividade do Serviço e do Monitor expressRoute

### <a name="what-are-the-size-requirements-of-the-nodes-to-be-used-for-monitoring-by-npm"></a>Quais são os requisitos de tamanho dos nós a utilizar para monitorização por NPM?
Para executar a solução NPM em VMs de nó para monitorizar redes, os nós devem ter pelo menos 500 MB de memória e um núcleo. Não precisas de usar nódosos separados para gerir a NPM. A solução pode funcionar em nódosos que têm outras cargas de trabalho em funcionamento. A solução tem a capacidade de parar o processo de monitorização se utilizar mais de 5% de CPU.

### <a name="to-use-npm-should-i-connect-my-nodes-as-direct-agent-or-through-system-center-operations-manager"></a>Para usar o NPM, devo ligar os meus nós como agente direto ou através do System Center Operations Manager?
Tanto o Monitor de Desempenho como as capacidades do Monitor de Conectividade do Serviço suportam nós [ligados como Agentes Diretos](../../azure-monitor/platform/agent-windows.md) e ligados através do Gestor de [Operações.](../../azure-monitor/platform/om-agents.md)

Para a capacidade do Monitor ExpressRoute, os nós Azure devem ser ligados apenas como Agentes Diretos. Os nós azure, que estão ligados através do Gestor de Operações, não são suportados. Para nós no local, os nós ligados como Agentes Diretos e através do Gestor de Operações são suportados para monitorizar um circuito ExpressRoute.

### <a name="which-protocol-among-tcp-and-icmp-should-be-chosen-for-monitoring"></a>Qual o protocolo entre o TCP e o ICMP que deve ser escolhido para monitorização?
Se estiver a monitorizar a sua rede utilizando nós baseados no servidor do Windows, recomendamos que utilize o TCP como protocolo de monitorização, uma vez que proporciona uma maior precisão. 

O ICMP é recomendado para desktops/nódodes baseados em sistemas operativos windows. Esta plataforma não permite que os dados do TCP sejam enviados através de tomadas cruas, que a NPM utiliza para descobrir a topologia da rede.

Pode obter mais detalhes sobre as vantagens relativas de cada protocolo [aqui.](../../azure-monitor/insights/network-performance-monitor-performance-monitor.md#choose-the-protocol)

### <a name="how-can-i-configure-a-node-to-support-monitoring-using-tcp-protocol"></a>Como posso configurar um nó para apoiar a monitorização usando o protocolo TCP?
Para o nó apoiar a monitorização utilizando o protocolo TCP: 
* Certifique-se de que a plataforma do nó é o Windows Server (2008 SP1 ou mais tarde).
* Executar o script [Powershell EnableRules.ps1](https://aka.ms/npmpowershellscript) no nó. Consulte [as instruções](../../azure-monitor/insights/network-performance-monitor.md#configure-log-analytics-agents-for-monitoring) para mais detalhes.


### <a name="how-can-i-change-the-tcp-port-being-used-by-npm-for-monitoring"></a>Como posso alterar a porta TCP que está a ser utilizada pela NPM para monitorização?
Pode alterar a porta TCP utilizada pela NPM para monitorização, executando o script [EnableRules.ps1.](https://aka.ms/npmpowershellscript) Precisa de introduzir o número da porta que pretende utilizar como parâmetro. Por exemplo, para ativar o TCP na porta 8060, executar `EnableRules.ps1 8060`. Certifique-se de que utiliza a mesma porta TCP em todos os nós utilizados para monitorização.

O script configura apenas o Windows Firewall localmente. Se tiver regras de firewall de rede ou network security group (NSG), certifique-se de que permitem o tráfego destinado à porta TCP utilizada pela NPM.

### <a name="how-many-agents-should-i-use"></a>Quantos agentes devo usar?
Deve utilizar pelo menos um agente para cada sub-rede que queira monitorizar.

### <a name="what-is-the-maximum-number-of-agents-i-can-use-or-i-see-error--youve-reached-your-configuration-limit"></a>Qual é o número máximo de agentes que posso usar ou vejo erro ".... atingiu o seu limite de configuração"?
O NPM limita o número de IPs a 5000 IPs por espaço de trabalho. Se um nó tiver endereços IPv4 e IPv6, isto contará como 2 IPs para esse nó. Assim, este limite de 5000 IPs decidiria o limite máximo do número de agentes. Pode eliminar os agentes inativos do separador Nósem NPM >> Configure. O NPM também mantém a história de todos os IPs que alguma vez foram atribuídos ao VM que acolhe o agente e cada um é contado como IP separado contribuindo para esse limite superior de 5000 IPs. Para libertar os IPs para o seu espaço de trabalho, pode utilizar a página Nós para eliminar os IPs que não estão a ser utilizados.

## <a name="monitoring"></a>Monitorização

### <a name="how-are-loss-and-latency-calculated"></a>Como são calculadas perdas e latências
Os agentes de origem enviam pedidos de TCP SYN (se o TCP for escolhido como protocolo de monitorização) ou pedidos do ICMP ECHO (se o ICMP for escolhido como protocolo de monitorização) para o destino IP a intervalos regulares para garantir que todos os caminhos entre o IP de destino-fonte combinação são cobertos. A percentagem de pacotes recebidos e pacotes de ida e volta é medida para calcular a perda e a latência de cada caminho. Estes dados são agregados ao longo do intervalo de votação e de todos os caminhos para obter os valores agregados de perda e latência para a combinação IP para o intervalo de votação particular.

### <a name="with-what-frequency-does-the-source-agent-send-packets-to-the-destination-for-monitoring"></a>Com que frequência o agente de origem envia pacotes para o destino para monitorização?
Para as capacidades do Monitor de Desempenho e do Monitor ExpressRoute, a fonte envia pacotes a cada 5 segundos e regista as medições da rede. Estes dados são agregados ao longo de um intervalo de sondagens de 3 minutos para calcular os valores médios e máximos de perda e latência. Para a capacidade do Monitor de Conectividade do Serviço, a frequência de envio dos pacotes para a medição da rede é determinada pela frequência introduzida pelo utilizador para o teste específico enquanto configura o teste.

### <a name="how-many-packets-are-sent-for-monitoring"></a>Quantos pacotes são enviados para monitorização?
O número de pacotes enviados pelo agente de origem para o destino numa sondagem é adaptativo e é decidido pelo nosso algoritmo proprietário, que pode ser diferente para diferentes topologs de rede. Mais o número de caminhos de rede entre a combinação IP de destino-origem, mais é o número de pacotes que são enviados. O sistema garante que todos os caminhos entre a combinação IP de destino-origem estão cobertos.

### <a name="how-does-npm-discover-network-topology-between-source-and-destination"></a>Como é que a NPM descobre a topologia da rede entre fonte e destino?
O NPM usa um algoritmo proprietário baseado na Traceroute para descobrir todos os caminhos e lúpulo entre fonte e destino.

### <a name="does-npm-provide-routing-and-switching-level-info"></a>O NPM fornece informações de encaminhamento e comutação de nível 
Embora a NPM possa detetar todas as rotas possíveis entre o agente de origem e o destino, não fornece visibilidade em que rota foi tomada pelos pacotes enviados pelas suas cargas de trabalho específicas. A solução pode ajudá-lo a identificar os caminhos e o lúpulo subjacente à rede, que estão a adicionar mais latência do que esperava.

### <a name="why-are-some-of-the-paths-unhealthy"></a>Por que alguns dos caminhos não são saudáveis?
Podem existir diferentes caminhos de rede entre a fonte e os IPs de destino e cada caminho pode ter um valor diferente de perda e latência. O NPM marca esses caminhos como insalubres (denotados com cor vermelha) para os quais os valores de perda e/ou latência são superiores ao respetivo limiar estabelecido na configuração de monitorização.

### <a name="what-does-a-hop-in-red-color-signify-in-the-network-topology-map"></a>O que significa um salto de cor vermelha no mapa de topologia da rede?
Se um lúpulo é vermelho, significa que faz parte de pelo menos um caminho pouco saudável. O NPM apenas marca os caminhos como insalubres, não segrega o estado de saúde de cada caminho. Para identificar o lúpulo problemático, você pode ver a latência hop-by-hop e segregar os que adicionam mais do que o esperado latência.

### <a name="how-does-fault-localization-in-performance-monitor-work"></a>Como funciona a localização por falhas no Monitor de Desempenho?
O NPM usa um mecanismo probabilístico para atribuir probabilidades de falhas a cada caminho de rede, segmento de rede e lúpulo de rede constituinte com base no número de caminhos pouco saudáveis em que fazem parte. À medida que os segmentos de rede e lúpulo se tornam parte de mais um número de caminhos pouco saudáveis, a probabilidade de avaria associada a eles aumenta. Este algoritmo funciona melhor quando se tem muitos nós com agente NPM ligados uns aos outros, uma vez que isso aumenta os pontos de dados para calcular as probabilidades de avaria.

### <a name="how-can-i-create-alerts-in-npm"></a>Como posso criar alertas na NPM?
Consulte a secção de [alertas na documentação](https://docs.microsoft.com/azure/log-analytics/log-analytics-network-performance-monitor#alerts) para obter instruções passo a passo.

### <a name="what-are-the-default-log-analytics-queries-for-alerts"></a>Quais são as consultas padrão de Log Analytics para alertas
Consulta de monitor de desempenho

    NetworkMonitoring 
     | where (SubType == "SubNetwork" or SubType == "NetworkPath") 
     | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy") and RuleName == "<<your rule name>>"
    
Consulta de monitor de conectividade de serviço

    NetworkMonitoring                 
     | where (SubType == "EndpointHealth" or SubType == "EndpointPath")
     | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy" or ServiceResponseHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy") and TestName == "<<your test name>>"
    
Consultas de monitor ExpressRoute: Consulta de circuitos

    NetworkMonitoring
    | where (SubType == "ERCircuitTotalUtilization") and (UtilizationHealthState == "Unhealthy") and CircuitResourceId == "<<your circuit resource ID>>"

Peering privado

    NetworkMonitoring 
     | where (SubType == "ExpressRoutePeering" or SubType == "ERVNetConnectionUtilization" or SubType == "ExpressRoutePath")   
    | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy" or UtilizationHealthState == "Unhealthy") and CircuitName == "<<your circuit name>>" and VirtualNetwork == "<<vnet name>>"

Peering da Microsoft

    NetworkMonitoring 
     | where (SubType == "ExpressRoutePeering" or SubType == "ERMSPeeringUtilization" or SubType == "ExpressRoutePath")
    | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy" or UtilizationHealthState == "Unhealthy") and CircuitName == ""<<your circuit name>>" and PeeringType == "MicrosoftPeering"

Consulta comum   

    NetworkMonitoring
    | where (SubType == "ExpressRoutePeering" or SubType == "ERVNetConnectionUtilization" or SubType == "ERMSPeeringUtilization" or SubType == "ExpressRoutePath")
    | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy" or UtilizationHealthState == "Unhealthy") 

### <a name="can-npm-monitor-routers-and-servers-as-individual-devices"></a>O NPM pode monitorizar os routers e servidores como dispositivos individuais?
O NPM identifica apenas o nome IP e anfitrião do lúpulo de rede subjacente (interruptores, routers, servidores, etc.) entre a fonte e os IPs de destino. Também identifica a latência entre estes lúpulos identificados. Não monitoriza individualmente estes lúpulos subjacentes.

### <a name="can-npm-be-used-to-monitor-network-connectivity-between-azure-and-aws"></a>O NPM pode ser utilizado para monitorizar a conectividade da rede entre o Azure e o AWS?
Sim. Consulte o artigo [Monitor Azure, AWS e redes no local utilizando nPM](https://blogs.technet.microsoft.com/msoms/2016/08/30/monitor-on-premises-cloud-iaas-and-hybrid-networks-using-oms-network-performance-monitor/) para mais detalhes.

### <a name="is-the-expressroute-bandwidth-usage-incoming-or-outgoing"></a>O uso da largura de banda ExpressRoute está a entrar ou a sair?
O uso da largura de banda é o total de largura de banda de entrada e saída. Expressa-se em Bits/seg.

### <a name="can-we-get-incoming-and-outgoing-bandwidth-information-for-the-expressroute"></a>Podemos obter informações de entrada e saída de largura de banda para o ExpressRoute?
Os valores de entrada e saída para a largura de banda primária e secundária podem ser capturados.

Para obter informações sobre o nível de ms, use a consulta abaixo mencionada em Pesquisa de Registo

    NetworkMonitoring 
     | where SubType == "ERMSPeeringUtilization"
     | project  CircuitName,PeeringName,PrimaryBytesInPerSecond,PrimaryBytesOutPerSecond,SecondaryBytesInPerSecond,SecondaryBytesOutPerSecond
    
Para informações privadas de nível de peering, use a consulta abaixo mencionada em Pesquisa de Registo

    NetworkMonitoring 
     | where SubType == "ERVNetConnectionUtilization"
     | project  CircuitName,PeeringName,PrimaryBytesInPerSecond,PrimaryBytesOutPerSecond,SecondaryBytesInPerSecond,SecondaryBytesOutPerSecond
  
Para obter informações sobre o nível do circuito, utilize a consulta abaixo mencionada na Pesquisa de Registos

    NetworkMonitoring 
        | where SubType == "ERCircuitTotalUtilization"
        | project CircuitName, PrimaryBytesInPerSecond, PrimaryBytesOutPerSecond,SecondaryBytesInPerSecond,SecondaryBytesOutPerSecond

### <a name="which-regions-are-supported-for-npms-performance-monitor"></a>Que regiões são apoiadas para o Monitor de Desempenho da NPM?
A NPM pode monitorizar a conectividade entre redes em qualquer parte do mundo, a partir de um espaço de trabalho que está hospedado numa das [regiões apoiadas](../../azure-monitor/insights/network-performance-monitor.md#supported-regions)

### <a name="which-regions-are-supported-for-npms-service-connectivity-monitor"></a>Quais as regiões apoiadas para o Monitor de Conectividade de Serviço da NPM?
A NPM pode monitorizar a conectividade com serviços em qualquer parte do mundo, a partir de um espaço de trabalho que está alojado numa das [regiões apoiadas](../../azure-monitor/insights/network-performance-monitor.md#supported-regions)

### <a name="which-regions-are-supported-for-npms-expressroute-monitor"></a>Quais as regiões apoiadas para o Monitor ExpressRoute da NPM?
A NPM pode monitorizar os circuitos ExpressRoute localizados em qualquer região do Azure. Para bordo para npm, você precisará de um espaço de trabalho Log Analytics que deve ser hospedado em uma das [regiões apoiadas](/azure/expressroute/how-to-npm)

## <a name="troubleshoot"></a>Resolução de problemas

### <a name="why-are-some-of-the-hops-marked-as-unidentified-in-the-network-topology-view"></a>Porque é que alguns dos lúpulos são marcados como não identificados na vista de topologia da rede?
O NPM usa uma versão modificada da traceroute para descobrir a topologia do agente de origem para o destino. Um salto não identificado representa que o salto de rede não respondeu ao pedido de rastreio do agente de origem. Se três saltos consecutivos de rede não responderem à rota do agente, a solução marca o lúpulo sem resposta como não identificado e não tenta descobrir mais lúpulo.

Um salto não pode responder a uma rota de rastreio em um ou mais dos cenários abaixo:

* Os routers foram configurados para não revelar a sua identidade.
* Os dispositivos de rede não permitem ICMP_TTL_EXCEEDED tráfego.
* Uma firewall está a bloquear a resposta ICMP_TTL_EXCEEDED do dispositivo de rede.

Quando um dos pontos finais se encontra em Azure, a traceroute aparece em lúpulo não identificado, uma vez que a ndrastructure azure não revela identidade para rastrear a rota. 

### <a name="i-get-alerts-for-unhealthy-tests-but-i-do-not-see-the-high-values-in-npms-loss-and-latency-graph-how-do-i-check-what-is-unhealthy"></a>Recebo alertas para testes pouco saudáveis, mas não vejo os valores elevados no gráfico de perda e latência da NPM. Como posso verificar o que não é saudável?
O NPM levanta um alerta se o fim da latência entre a fonte e o destino cruzar o limiar para qualquer caminho entre eles. Algumas redes têm múltiplos caminhos que ligam a mesma fonte e destino. NPM levanta um alerta é que qualquer caminho não é saudável. A perda e latência observada nos gráficos é o valor médio para todos os caminhos, pelo que pode não mostrar o valor exato de um único caminho. Para entender onde o limiar foi violado, procure a coluna "SubType" no alerta. Se o problema for causado por um caminho, o valor subTipo será NetworkPath (para testes de Monitor de Desempenho), EndpointPath (para testes de Monitor de Conectividade de Serviço) e ExpressRoutePath (para testes expressRotue Monitor). 

A amostra de consulta para encontrar é o caminho não é saudável:

    NetworkMonitoring 
    | where ( SubType == "ExpressRoutePath")
    | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy" or UtilizationHealthState == "Unhealthy") and          CircuitResourceID =="<your ER circuit ID>" and ConnectionResourceId == "<your ER connection resource id>"
    | project SubType, LossHealthState, LatencyHealthState, MedianLatency 

### <a name="why-does-my-test-show-unhealthy-but-the-topology-does-not"></a>Por que o meu teste mostra insalubre, mas a topologia não 
O NPM monitoriza a perda, latência e topologia em intervalos diferentes. A perda e a latência são medidas uma vez a cada 5 segundos e agregadas a cada três minutos (para Monitor de Desempenho e Monitor de Rota Expresso) enquanto a topologia é calculada usando a traceroute uma vez a cada 10 minutos. Por exemplo, entre 3:44 e 4:04, a topologia pode ser atualizada três vezes (3:44, 3:54, 4:04), mas a perda e a latência são atualizadas cerca de sete vezes (3:44, 3:47, 3:50, 3:53, 3:56, 3:59, 4:02). A topologia gerada às 3:54 será prestada para a perda e latência que é calculada em 3:56, 3:59 e 4:02. Suponha que receba um alerta de que o seu circuito de Urgência não estava saudável às 3:59. Aceda à NPM e tente definir o tempo de topologia para 3:59. A NPM tornará a topologia gerada às 3:54. Para compreender a última topologia conhecida da sua rede, compare os campos TimeProcessed (tempo em que a perda e a latência foram calculadas) e tracerouteCompletedTime (tempo em que a topologia foi calculada). 

### <a name="what-is-the-difference-between-the-fields-e2emedianlatency-and-avghoplatencylist-in-the-networkmonitoring-table"></a>Qual é a diferença entre os campos E2EMedianLatency e AvgHopLatencyList na tabela NetworkMonitoring
E2EMedianLatency é a latência atualizada de três em três minutos após a agregação dos resultados dos testes de ping tCP, enquanto o AvgHopLatencyList é atualizado a cada 10 minutos com base na traceroute. Para compreender a hora exata em que o E2EMedianLatency foi calculado, utilize o campo TimeProcessed. Para compreender a hora exata em que a traceroute completou e atualizou a AvgHopLatencyList, utilize o campo TracerouteCompletedTime

### <a name="why-does-hop-by-hop-latency-numbers-differ-from-hoplatencyvalues"></a>Porque é que os números de latência hop-by-hop diferem dos HopLatencyValues 
HopLatencyValues são fonte para ponto final.
Por exemplo: Lúpulo - A,B,C. AvgHopLatency - 10,15,20. Isto significa fonte para Uma latência = 10, fonte para latência B = 15 e fonte para latência C é de 20. UI calculará latência de lúpulo A-B como 5 na topologia

### <a name="the-solution-shows-100-loss-but-there-is-connectivity-between-the-source-and-destination"></a>A solução mostra perda de 100% mas há conectividade entre a fonte e o destino
Isto pode acontecer se a firewall do hospedeiro ou a firewall intermédia (firewall de rede ou Azure NSG) estiver bloqueando a comunicação entre o agente de origem e o destino sobre a porta que está sendo usada para monitorização por NPM (por padrão a porta é 8084, a menos que o cliente mudou isto).

* Para verificar se a firewall do hospedeiro não está a bloquear a comunicação sobre a porta requerida, veja o estado de saúde dos nós de origem e destino a partir da seguinte vista: Network Performance Monitor -> Configuração -> Nós. 
  Se não forem saudáveis, veja as instruções e tome medidas corretivas. Se os nós estiverem saudáveis, mova-se para o passo b. abaixo.
* Para verificar se uma firewall de rede intermédia ou o Azure NSG não está a bloquear a comunicação na porta requerida, utilize o utilitário PsPing de terceiros utilizando as instruções abaixo:
  * utilitário de psping está disponível para download [aqui](https://technet.microsoft.com/sysinternals/psping.aspx) 
  * Corra seguindo o comando do nó de origem.
    * psping -n 15 \<nó de destino IPAddress\>:portNumber Por padrão NPM utiliza porta 8084. Caso tenha alterado explicitamente isto utilizando o script EnableRules.ps1, introduza o número de porta personalizado que está a utilizar). Este é um ping da máquina Azure para o local
* Verifique se os pings são bem sucedidos. Caso contrário, indica que uma firewall de rede intermédia ou o AZURE NSG está a bloquear o tráfego nesta porta.
* Agora, corra o comando do nó de destino para fonte do nó IP.


### <a name="there-is-loss-from-node-a-to-b-but-not-from-node-b-to-a-why"></a>Há perda do nó A para B, mas não do nó B para A. Porquê?
Como os caminhos de rede entre A a B podem ser diferentes dos caminhos de rede entre B e A, podem observar-se diferentes valores de perda e latência.

### <a name="why-are-all-my-expressroute-circuits-and-peering-connections-not-being-discovered"></a>Porque é que todos os meus circuitos expressRoute e ligações de observação não estão a ser descobertos?
A NPM descobre agora circuitos ExpressRoute e ligações de pares em todas as subscrições a que o utilizador tem acesso. Escolha todas as subscrições onde os seus recursos da Rota Expresso estão ligados e permita a monitorização de cada recurso descoberto. A NPM procura objetos de ligação ao descobrir um epeering privado, por isso, verifique se um VNET está associado ao seu olhar.

### <a name="the-er-monitor-capability-has-a-diagnostic-message-traffic-is-not-passing-through-any-circuit-what-does-that-mean"></a>A capacidade do Er Monitor tem uma mensagem de diagnóstico "O tráfego não está a passar por qualquer circuito". O que é que isso significa?

Pode haver um cenário em que haja uma ligação saudável entre os nós no local e os nós de Azure, mas o tráfego não está a passar pelo circuito ExpressRoute configurado para ser monitorizado pela NPM. 

Este problema pode ocorrer se:

* O circuito das Urgências está avariado.
* Os filtros de rota são configurados de forma a dar prioridade a outras rotas (como uma ligação VPN ou outro circuito ExpressRoute) sobre o circuito ExpressRoute pretendido. 
* Os nós no local e azure escolhidos para monitorizar o circuito ExpressRoute na configuração de monitorização, não têm conectividade entre si sobre o circuito ExpressRoute pretendido. Certifique-se de que escolheu nós corretos que têm conectividade entre si durante o circuito ExpressRoute que pretende monitorizar.

### <a name="while-configuring-monitoring-of-my-expressroute-circuit-the-azure-nodes-are-not-being-detected"></a>Enquanto configura a monitorização do meu circuito ExpressRoute, os nós azure não estão a ser detetados.
Isto pode acontecer se os nós azure estiverem ligados através do Gestor de Operações. A capacidade do ExpressRoute Monitor suporta apenas os nódos o Azure que estão ligados como Agentes Diretos.

### <a name="i-cannot-discover-by-expressroute-circuits-in-the-oms-portal"></a>Não posso descobrir por circuitos ExpressRoute no portal OMS
Embora o NPM possa ser utilizado tanto a partir do portal Azure como do portal OMS, a descoberta do circuito na capacidade do Monitor ExpressRoute funciona apenas através do portal Azure. Uma vez descobertos os circuitos através do portal Azure, pode utilizar a capacidade em qualquer um dos dois portais. 

### <a name="in-the-service-connectivity-monitor-capability-the-service-response-time-network-loss-as-well-as-latency-are-shown-as-na"></a>Na capacidade do Monitor de Conectividade de Serviços, o tempo de resposta ao serviço, a perda de rede, bem como a latência são mostrados como NA
Isto pode acontecer se um ou mais for verdade:

* O serviço está em baixo.
* O nó usado para verificar a conectividade da rede com o serviço está em baixo.
* O alvo introduzido na configuração do teste está incorreto.
* O nó não tem conectividade de rede.

### <a name="in-the-service-connectivity-monitor-capability-a-valid-service-response-time-is-shown-but-network-loss-as-well-as-latency-are-shown-as-na"></a>Na capacidade do Monitor de Conectividade de Serviço, é demonstrado um tempo de resposta de serviço válido, mas a perda de rede e a latência são mostradas como NA
 Isto pode acontecer se um ou mais for verdade:

* Se o nó utilizado para verificar a conectividade da rede com o serviço for uma máquina cliente Do Windows, ou o serviço alvo está a bloquear pedidos do ICMP ou uma firewall de rede está a bloquear pedidos do ICMP originários do nó.
* A caixa de verificação de medições de rede Executar está em branco na configuração do teste.

### <a name="in-the-service-connectivity-monitor-capability-the-service-response-time-is-na-but-network-loss-as-well-as-latency-are-valid"></a>Na capacidade do Monitor de Conectividade de Serviço, o tempo de resposta ao serviço é NA, mas a perda de rede, bem como a latência são válidas
Isto pode acontecer se o serviço alvo não for uma aplicação web, mas o teste estiver configurado como um teste Web. Editar a configuração do teste e escolher o tipo de teste como Rede em vez de Web.

## <a name="miscellaneous"></a>Diverso

### <a name="is-there-a-performance-impact-on-the-node-being-used-for-monitoring"></a>Existe um impacto no desempenho no nó que está a ser utilizado para a monitorização?
O processo NPM está configurado para parar se utilizar mais de 5% dos recursos cpu hospedeiros. Isto é para garantir que pode continuar a usar os nós para as suas cargas de trabalho habituais sem afetar o desempenho.

### <a name="does-npm-edit-firewall-rules-for-monitoring"></a>A NPM edita regras de firewall para monitorização?
O NPM apenas cria uma regra local do Windows Firewall nos nós em que o script EnableRules.ps1 Powershell é executado para permitir que os agentes criem ligações TCP entre si na porta especificada. A solução não modifica nenhuma firewall de rede ou regras do Network Security Group (NSG).

### <a name="how-can-i-check-the-health-of-the-nodes-being-used-for-monitoring"></a>Como posso verificar a saúde dos nós que estão a ser usados para monitorização?
Pode ver o estado de saúde dos nós que estão a ser utilizados para monitorização a partir da seguinte vista: Network Performance Monitor -> Configuração -> Nós. Se um nó não for saudável, pode ver os detalhes do erro e tomar as medidas sugeridas.

### <a name="can-npm-report-latency-numbers-in-microseconds"></a>A NPM pode reportar números de latência em microsegundos?
NPM ronda os números de latência na UI e em milissegundos. Os mesmos dados são armazenados numa granularidade mais elevada (por vezes até quatro casas decimais).

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre o Monitor de Desempenho da Rede referindo-se à solução de Monitor de [Desempenho da Rede em Azure](../../azure-monitor/insights/network-performance-monitor.md).
