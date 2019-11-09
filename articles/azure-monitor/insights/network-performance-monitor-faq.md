---
title: Perguntas frequentes-Monitor de Desempenho de Rede solução no Azure | Microsoft Docs
description: Este artigo captura as perguntas frequentes sobre Monitor de Desempenho de Rede no Azure. O Monitor de Desempenho de Rede (NPM) ajuda a monitorar o desempenho de suas redes quase em tempo real e detectar e localizar afunilamentos de desempenho de rede.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: vinynigam
ms.author: vinigam
ms.date: 10/12/2018
ms.openlocfilehash: ce0b917f34cab31227e721e119c72cd5d1f99bff
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73832008"
---
# <a name="network-performance-monitor-solution-faq"></a>Perguntas frequentes sobre a solução Monitor de Desempenho de Rede

![Símbolo de Monitor de Desempenho de Rede](media/network-performance-monitor-faq/npm-symbol.png)

Este artigo captura as perguntas frequentes sobre Monitor de Desempenho de Rede (NPM) no Azure

O [Monitor de desempenho de rede](/azure/networking/network-monitoring-overview) é uma solução de [monitoramento de rede híbrida](../../azure-monitor/insights/network-performance-monitor-performance-monitor.md) baseada em nuvem que ajuda a monitorar o desempenho de rede entre vários pontos em sua infraestrutura de rede. Ele também ajuda a monitorar a conectividade de rede para [pontos de extremidade de serviço e de aplicativo](../../azure-monitor/insights/network-performance-monitor-service-connectivity.md) e [monitorar o desempenho do Azure ExpressRoute](../../azure-monitor/insights/network-performance-monitor-expressroute.md). 

Monitor de Desempenho de Rede detecta problemas de rede, como tráfego blackholing, erros de roteamento e problemas que os métodos de monitoramento de rede convencionais não conseguem detectar. A solução gera alertas e notifica-o quando existir uma falha do limiar de uma ligação de rede. Também garante a deteção atempada de problemas de desempenho de rede e localiza a origem do problema num dispositivo ou segmento de rede específico. 

Mais informações sobre os vários recursos com suporte do [Monitor de desempenho de rede](https://docs.microsoft.com/azure/networking/network-monitoring-overview) estão disponíveis online.

## <a name="set-up-and-configure-agents"></a>Configurar e configurar agentes

### <a name="what-are-the-platform-requirements-for-the-nodes-to-be-used-for-monitoring-by-npm"></a>Quais são os requisitos de plataforma para os nós a serem usados para monitoramento pelo NPM?
Abaixo estão listados os requisitos de plataforma para os vários recursos do NPM:

- Os recursos do monitor de conectividade do serviço e do monitor de desempenho do NPM dão suporte a sistemas operacionais Windows Server e Windows desktops/cliente. As versões do sistema operacional do Windows Server com suporte são 2008 SP1 ou posterior. As versões de desktop/cliente do Windows com suporte são Windows 10, Windows 8.1, Windows 8 e Windows 7. 
- A funcionalidade de monitor do ExpressRoute do NPM dá suporte apenas ao sistema operacional Windows Server (2008 SP1 ou posterior).

### <a name="can-i-use-linux-machines-as-monitoring-nodes-in-npm"></a>Posso usar computadores Linux como nós de monitoramento no NPM?
No momento, a capacidade de monitorar redes usando nós baseados em Linux está em versão prévia. Entre em contato com seu gerente de conta para saber mais. Os agentes do Linux fornecem capacidade de monitoramento somente para o recurso de monitor de desempenho do NPM e não estão disponíveis para os recursos do monitor de conectividade de serviço e do monitor do ExpressRoute

### <a name="what-are-the-size-requirements-of-the-nodes-to-be-used-for-monitoring-by-npm"></a>Quais são os requisitos de tamanho dos nós a serem usados para monitoramento pelo NPM?
Para executar a solução NPM em VMs de nó para monitorar redes, os nós devem ter pelo menos 500 MB de memória e um núcleo. Você não precisa usar nós separados para executar o NPM. A solução pode ser executada em nós que têm outras cargas de trabalho em execução. A solução tem a capacidade de interromper o processo de monitoramento se usar mais de 5% da CPU.

### <a name="to-use-npm-should-i-connect-my-nodes-as-direct-agent-or-through-system-center-operations-manager"></a>Para usar o NPM, devo conectar meus nós como agente direto ou por meio de System Center Operations Manager?
O monitor de desempenho e os recursos do monitor de conectividade de serviço dão suporte a nós [conectados como agentes diretos](../../azure-monitor/platform/agent-windows.md) e [conectados por meio de Operations Manager](../../azure-monitor/platform/om-agents.md).

Para o recurso de monitor do ExpressRoute, os nós do Azure devem ser conectados somente como agentes diretos. Não há suporte para nós do Azure conectados por meio de Operations Manager. Para nós locais, os nós conectados como agentes diretos e por meio de Operations Manager têm suporte para monitorar um circuito de ExpressRoute.

### <a name="which-protocol-among-tcp-and-icmp-should-be-chosen-for-monitoring"></a>Qual protocolo entre TCP e ICMP deve ser escolhido para monitoramento?
Se você estiver monitorando sua rede usando nós baseados no Windows Server, recomendamos o uso de TCP como o protocolo de monitoramento, pois ele fornece maior precisão. 

O ICMP é recomendado para nós baseados no sistema operacional Windows desktop/cliente. Essa plataforma não permite que dados TCP sejam enviados por soquetes brutos, que o NPM usa para descobrir a topologia de rede.

Você pode obter mais detalhes sobre as vantagens relativas de cada protocolo [aqui](../../azure-monitor/insights/network-performance-monitor-performance-monitor.md#choose-the-protocol).

### <a name="how-can-i-configure-a-node-to-support-monitoring-using-tcp-protocol"></a>Como configurar um nó para dar suporte ao monitoramento usando o protocolo TCP?
Para o nó oferecer suporte ao monitoramento usando o protocolo TCP: 
* Verifique se a plataforma de nó é o Windows Server (2008 SP1 ou posterior).
* Execute o script do PowerShell [EnableRules. ps1](https://aka.ms/npmpowershellscript) no nó. Consulte [as instruções](../../azure-monitor/insights/network-performance-monitor.md#configure-log-analytics-agents-for-monitoring) para obter mais detalhes.


### <a name="how-can-i-change-the-tcp-port-being-used-by-npm-for-monitoring"></a>Como posso alterar a porta TCP usada pelo NPM para monitoramento?
Você pode alterar a porta TCP usada pelo NPM para monitoramento, executando o script [EnableRules. ps1](https://aka.ms/npmpowershellscript) . Você precisa inserir o número da porta que pretende usar como parâmetro. Por exemplo, para habilitar o TCP na porta 8060, execute `EnableRules.ps1 8060`. Certifique-se de usar a mesma porta TCP em todos os nós que estão sendo usados para monitoramento.

O script configura apenas o Firewall do Windows localmente. Se você tiver regras de firewall de rede ou grupo de segurança de rede (NSG), certifique-se de que elas permitam o tráfego destinado à porta TCP usada pelo NPM.

### <a name="how-many-agents-should-i-use"></a>Quantos agentes devo usar?
Você deve usar pelo menos um agente para cada sub-rede que deseja monitorar.

### <a name="what-is-the-maximum-number-of-agents-i-can-use-or-i-see-error--youve-reached-your-configuration-limit"></a>Qual é o número máximo de agentes que posso usar ou que eu vejo o erro ".... Você atingiu seu limite de configuração "?
NPM limita o número de IPs a 5000 IPs por espaço de trabalho. Se um nó tiver endereços IPv4 e IPv6, isso contará como 2 IPs para esse nó. Portanto, esse limite de 5000 IPs decidiria o limite superior do número de agentes. Você pode excluir os agentes inativos da guia nós em NPM > > Configurar. O NPM também mantém o histórico de todos os IPs que já foram atribuídos à VM que hospeda o agente e cada um é contado como um IP separado, contribuindo para esse limite superior de 5000 IPs. Para liberar IPs para seu espaço de trabalho, você pode usar a página nós para excluir os IPs que não estão em uso.

## <a name="monitoring"></a>Monitorização

### <a name="how-are-loss-and-latency-calculated"></a>Como a perda e a latência são calculadas
Os agentes de origem enviam solicitações TCP SYN (se o TCP for escolhido como o protocolo para monitoramento) ou solicitações de eco ICMP (se o ICMP for escolhido como o protocolo para monitoramento) para o IP de destino em intervalos regulares para garantir que todos os caminhos entre o IP de destino de origem a combinação é coberta. O percentual de pacotes recebidos e o tempo de ida e volta do pacote são medidos para calcular a perda e a latência de cada caminho. Esses dados são agregados no intervalo de sondagem e em todos os caminhos para obter os valores agregados de perda e latência para a combinação de IP para o intervalo de sondagem específico.

### <a name="with-what-frequency-does-the-source-agent-send-packets-to-the-destination-for-monitoring"></a>Com que frequência o agente de origem envia pacotes para o destino para monitoramento?
Para os recursos do monitor de desempenho e do monitor do ExpressRoute, a origem envia pacotes a cada 5 segundos e registra as medidas de rede. Esses dados são agregados em um intervalo de sondagem de 3 minutos para calcular os valores médio e máximo de perda e latência. Para o recurso de monitor de conectividade de serviço, a frequência de envio dos pacotes para a medição de rede é determinada pela frequência inserida pelo usuário para o teste específico durante a configuração do teste.

### <a name="how-many-packets-are-sent-for-monitoring"></a>Quantos pacotes são enviados para monitoramento?
O número de pacotes enviados pelo agente de origem para o destino em uma sondagem é adaptável e é decidido pelo nosso algoritmo proprietário, que pode ser diferente para diferentes topologias de rede. Mais o número de caminhos de rede entre a combinação de IP de origem/destino, mais é o número de pacotes que são enviados. O sistema garante que todos os caminhos entre a combinação de IP de origem/destino sejam cobertos.

### <a name="how-does-npm-discover-network-topology-between-source-and-destination"></a>Como o NPM detecta a topologia de rede entre a origem e o destino?
O NPM usa um algoritmo proprietário com base em traceroute para descobrir todos os caminhos e saltos entre a origem e o destino.

### <a name="does-npm-provide-routing-and-switching-level-info"></a>O NPM fornece informações de roteamento e de nível de alternância 
Embora NPM possa detectar todas as rotas possíveis entre o agente de origem e o destino, ele não fornece visibilidade de qual rota foi tomada pelos pacotes enviados por suas cargas de trabalho específicas. A solução pode ajudá-lo a identificar os caminhos e os saltos de rede subjacentes, que estão adicionando mais latência do que o esperado.

### <a name="why-are-some-of-the-paths-unhealthy"></a>Por que alguns dos caminhos não estão íntegros?
Diferentes caminhos de rede podem existir entre os IPs de origem e de destino e cada caminho pode ter um valor diferente de perda e latência. NPM marca esses caminhos como não íntegros (indicados com cor vermelha) para os quais os valores de perda e/ou latência são maiores que o respectivo limite definido na configuração de monitoramento.

### <a name="what-does-a-hop-in-red-color-signify-in-the-network-topology-map"></a>O que um salto na cor vermelha significa no mapa de topologia de rede?
Se um salto for vermelho, significa que ele faz parte de pelo menos um caminho não íntegro. NPM marca os caminhos como não íntegros, não separa o status de integridade de cada caminho. Para identificar os saltos problemáticos, você pode exibir a latência de salto a salto e separar aquelas que adicionam mais do que a latência esperada.

### <a name="how-does-fault-localization-in-performance-monitor-work"></a>Como funciona a localização de falhas no monitor de desempenho?
O NPM usa um mecanismo probabilística para atribuir probabilidades de falha a cada caminho de rede, segmento de rede e os saltos de rede constituintes com base no número de caminhos não íntegros dos quais fazem parte. Como os segmentos de rede e os saltos se tornam parte de um número maior de caminhos não íntegros, a probabilidade de falha associada a eles aumenta. Esse algoritmo funciona melhor quando você tem muitos nós com o agente NPM conectado entre si, pois isso aumenta os pontos de dados para calcular as probabilidades de falha.

### <a name="how-can-i-create-alerts-in-npm"></a>Como posso criar alertas no NPM?
Consulte a [seção alertas na documentação](https://docs.microsoft.com/azure/log-analytics/log-analytics-network-performance-monitor#alerts) para obter instruções passo a passo.

### <a name="what-are-the-default-log-analytics-queries-for-alerts"></a>Quais são as consultas de Log Analytics padrão para alertas
Consulta do monitor de desempenho

    NetworkMonitoring 
     | where (SubType == "SubNetwork" or SubType == "NetworkPath") 
     | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy") and RuleName == "<<your rule name>>"
    
Consulta do monitor de conectividade de serviço

    NetworkMonitoring                 
     | where (SubType == "EndpointHealth" or SubType == "EndpointPath")
     | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy" or ServiceResponseHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy") and TestName == "<<your test name>>"
    
Consultas do monitor do ExpressRoute: consulta de circuitos

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

### <a name="can-npm-monitor-routers-and-servers-as-individual-devices"></a>Pode NPM monitorar roteadores e servidores como dispositivos individuais?
NPM identifica apenas o IP e o nome do host dos saltos de rede subjacentes (comutadores, roteadores, servidores, etc.) entre os IPs de origem e de destino. Ele também identifica a latência entre esses saltos identificados. Ele não monitora individualmente esses saltos subjacentes.

### <a name="can-npm-be-used-to-monitor-network-connectivity-between-azure-and-aws"></a>NPM pode ser usado para monitorar a conectividade de rede entre o Azure e o AWS?
Sim. Consulte o artigo [monitorar o Azure, AWS e redes locais usando o NPM](https://blogs.technet.microsoft.com/msoms/2016/08/30/monitor-on-premises-cloud-iaas-and-hybrid-networks-using-oms-network-performance-monitor/) para obter detalhes.

### <a name="is-the-expressroute-bandwidth-usage-incoming-or-outgoing"></a>O uso de largura de banda do ExpressRoute é de entrada ou de saída?
O uso da largura de banda é o total da largura de banda de entrada e saída. Ele é expresso em bits/s.

### <a name="can-we-get-incoming-and-outgoing-bandwidth-information-for-the-expressroute"></a>Podemos obter informações de largura de banda de entrada e saída para o ExpressRoute?
Os valores de entrada e saída para a largura de banda primária e secundária podem ser capturados.

Para informações de nível de emparelhamento MS, use a consulta abaixo mencionada na pesquisa de logs

    NetworkMonitoring 
     | where SubType == "ERMSPeeringUtilization"
     | project  CircuitName,PeeringName,PrimaryBytesInPerSecond,PrimaryBytesOutPerSecond,SecondaryBytesInPerSecond,SecondaryBytesOutPerSecond
    
Para obter informações de nível de emparelhamento privado, use a consulta abaixo mencionada na pesquisa de logs

    NetworkMonitoring 
     | where SubType == "ERVNetConnectionUtilization"
     | project  CircuitName,PeeringName,PrimaryBytesInPerSecond,PrimaryBytesOutPerSecond,SecondaryBytesInPerSecond,SecondaryBytesOutPerSecond
  
Para informações de nível de circuito, use a consulta abaixo mencionada na pesquisa de logs

    NetworkMonitoring 
        | where SubType == "ERCircuitTotalUtilization"
        | project CircuitName, PrimaryBytesInPerSecond, PrimaryBytesOutPerSecond,SecondaryBytesInPerSecond,SecondaryBytesOutPerSecond

### <a name="which-regions-are-supported-for-npms-performance-monitor"></a>Quais regiões têm suporte para o monitor de desempenho do NPM?
O NPM pode monitorar a conectividade entre redes em qualquer parte do mundo, de um espaço de trabalho hospedado em uma das [regiões com suporte](../../azure-monitor/insights/network-performance-monitor.md#supported-regions)

### <a name="which-regions-are-supported-for-npms-service-connectivity-monitor"></a>Quais regiões têm suporte para o monitor de conectividade de serviço do NPM?
O NPM pode monitorar a conectividade com os serviços em qualquer parte do mundo, de um espaço de trabalho hospedado em uma das [regiões com suporte](../../azure-monitor/insights/network-performance-monitor.md#supported-regions)

### <a name="which-regions-are-supported-for-npms-expressroute-monitor"></a>Quais regiões têm suporte para o monitor do ExpressRoute do NPM?
O NPM pode monitorar os circuitos do ExpressRoute localizados em qualquer região do Azure. Para carregar o NPM, você precisará de um espaço de trabalho Log Analytics que deve ser hospedado em uma das [regiões com suporte](/azure/expressroute/how-to-npm)

## <a name="troubleshoot"></a>Resolução de problemas

### <a name="why-are-some-of-the-hops-marked-as-unidentified-in-the-network-topology-view"></a>Por que alguns dos saltos marcados como não identificados no modo de exibição de topologia de rede?
O NPM usa uma versão modificada de traceroute para descobrir a topologia do agente de origem para o destino. Um salto não identificado representa que o salto de rede não respondeu à solicitação de traceroute do agente de origem. Se três saltos de rede consecutivos não responderem aos traceroute do agente, a solução marcará os saltos sem resposta como não sendo identificados e não tentará descobrir mais saltos.

Um salto pode não responder a um traceroute em um ou mais dos cenários abaixo:

* Os roteadores foram configurados para não revelar sua identidade.
* Os dispositivos de rede não estão permitindo o tráfego de ICMP_TTL_EXCEEDED.
* Um firewall está bloqueando a resposta ICMP_TTL_EXCEEDED do dispositivo de rede.

### <a name="i-get-alerts-for-unhealthy-tests-but-i-do-not-see-the-high-values-in-npms-loss-and-latency-graph-how-do-i-check-what-is-unhealthy"></a>Obtenho alertas para testes não íntegros, mas não vejo os valores altos no grafo de perda e latência do NPM. Como fazer verificar o que não está íntegro?
O NPM emitirá um alerta se a latência de ponta a ponta entre a origem e o destino cruzar o limite de qualquer caminho entre eles. Algumas redes têm vários caminhos conectando a mesma origem e destino. NPM gera um alerta é que qualquer caminho não está íntegro. A perda e a latência vistas nos grafos são o valor médio de todos os caminhos, portanto, ele pode não mostrar o valor exato de um único caminho. Para entender onde o limite foi violado, procure a coluna "subtipo" no alerta. Se o problema for causado por um caminho, o valor do subtipo será NetworkPath (para testes do monitor de desempenho), EndpointPath (para testes do monitor de conectividade de serviço) e ExpressRoutePath (para testes do monitor ExpressRotue). 

A consulta de exemplo para localizar o caminho não está íntegra:

    NetworkMonitoring 
    | where ( SubType == "ExpressRoutePath")
    | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy" or UtilizationHealthState == "Unhealthy") and          CircuitResourceID =="<your ER circuit ID>" and ConnectionResourceId == "<your ER connection resource id>"
    | project SubType, LossHealthState, LatencyHealthState, MedianLatency 

### <a name="why-does-my-test-show-unhealthy-but-the-topology-does-not"></a>Por que meu teste mostra não íntegro, mas a topologia não 
O NPM monitora a perda, a latência e a topologia de ponta a ponta em intervalos diferentes. A perda e a latência são medidas uma vez a cada 5 segundos e agregadas a cada três minutos (para o monitor de desempenho e monitor de rota expressa) enquanto a topologia é calculada usando traceroute uma vez a cada 10 minutos. Por exemplo, entre 3:44 e 4:04, a topologia pode ser atualizada três vezes (3:44, 3:54, 4:04), mas a perda e a latência são atualizadas cerca de sete vezes (3:44, 3:47, 3:50, 3:53, 3:56, 3:59, 4:02). A topologia gerada em 3:54 será renderizada para a perda e a latência calculadas às 3:56, 3:59 e 4:02. Suponha que você receba um alerta de que o circuito ER não estava íntegro em 3:59. Faça logon no NPM e tente definir o tempo de topologia como 3:59. NPM renderizará a topologia gerada em 3:54. Para entender a última topologia conhecida de sua rede, compare os campos timeprocessod (tempo em que a perda e a latência foram calculadas) e TracerouteCompletedTime (hora em que a topologia foi calculada). 

### <a name="what-is-the-difference-between-the-fields-e2emedianlatency-and-avghoplatencylist-in-the-networkmonitoring-table"></a>Qual é a diferença entre os campos E2EMedianLatency e AvgHopLatencyList na tabela NetworkMonitoring
E2EMedianLatency é a latência atualizada a cada três minutos após a agregação dos resultados de testes de ping TCP, enquanto o AvgHopLatencyList é atualizado a cada 10 minutos com base em traceroute. Para entender a hora exata em que o E2EMedianLatency foi calculado, use o campo tempo processado. Para entender a hora exata em que os traceroutes foram concluídos e atualizados AvgHopLatencyList, use o campo TracerouteCompletedTime

### <a name="why-does-hop-by-hop-latency-numbers-differ-from-hoplatencyvalues"></a>Por que os números de latência de salto por salto são diferentes de HopLatencyValues 
HopLatencyValues são de origem para ponto de extremidade.
Por exemplo: Hops-A, B, C. AvgHopLatency-10, 15, 20. Isso significa origem para uma latência = 10, latência de origem para B = 15 e a latência de origem para C é 20. A interface do usuário calculará A latência de salto-B como 5 na topologia

### <a name="the-solution-shows-100-loss-but-there-is-connectivity-between-the-source-and-destination"></a>A solução mostra a perda de 100%, mas há conectividade entre a origem e o destino
Isso pode acontecer se o Firewall do host ou o firewall intermediário (firewall de rede ou NSG do Azure) estiver bloqueando a comunicação entre o agente de origem e o destino pela porta que está sendo usada para monitoramento pelo NPM (por padrão, a porta é 8084, a menos que o o cliente alterou isso).

* Para verificar se o Firewall do host não está bloqueando a comunicação na porta necessária, exiba o status de integridade dos nós de origem e de destino no seguinte modo de exibição: configuração de Monitor de Desempenho de Rede->-nós de >. 
  Se eles não estiverem íntegros, exiba as instruções e execute uma ação corretiva. Se os nós estiverem íntegros, vá para a etapa b. abaixo.
* Para verificar se um firewall de rede intermediário ou o Azure NSG não está bloqueando a comunicação na porta necessária, use o utilitário PsPing de terceiros usando as instruções abaixo:
  * o utilitário psping está disponível para download [aqui](https://technet.microsoft.com/sysinternals/psping.aspx) 
  * Execute o comando a seguir no nó de origem.
    * psping-n 15 \<o IPAddress do nó de destino\>:p ortNumber por padrão NPM usa a porta 8084. Caso você tenha alterado explicitamente isso usando o script EnableRules. ps1, insira o número da porta personalizada que você está usando). Este é um ping do computador do Azure para o local
* Verifique se os pings foram bem-sucedidos. Caso contrário, ele indica que um firewall de rede intermediário ou o Azure NSG está bloqueando o tráfego nessa porta.
* Agora, execute o comando do nó de destino para o IP do nó de origem.


### <a name="there-is-loss-from-node-a-to-b-but-not-from-node-b-to-a-why"></a>Há perda do nó A para B, mas não do nó B para um. Por?
Como os caminhos de rede entre a a B podem ser diferentes dos caminhos de rede entre B e a, os valores diferentes para perda e latência podem ser observados.

### <a name="why-are-all-my-expressroute-circuits-and-peering-connections-not-being-discovered"></a>Por que todos os meus circuitos do ExpressRoute e conexões de emparelhamento não estão sendo descobertos?
O NPM agora descobre circuitos do ExpressRoute e conexões de emparelhamento em todas as assinaturas às quais o usuário tem acesso. Escolha todas as assinaturas nas quais os recursos de rota expressa estão vinculados e habilite o monitoramento para cada recurso descoberto. O NPM procura objetos de conexão ao descobrir um emparelhamento privado, portanto, verifique se uma VNET está associada ao seu emparelhamento.

### <a name="the-er-monitor-capability-has-a-diagnostic-message-traffic-is-not-passing-through-any-circuit-what-does-that-mean"></a>O recurso de monitor de ER tem uma mensagem de diagnóstico "o tráfego não está passando por nenhum circuito". O que é que isto significa?

Pode haver um cenário em que há uma conexão íntegra entre os nós locais e do Azure, mas o tráfego não está passando pelo circuito de ExpressRoute configurado para ser monitorado pelo NPM. 

Este problema pode ocorrer se:

* O circuito ER está inoperante.
* Os filtros de rota são configurados de forma que eles forneçam prioridade a outras rotas (como uma conexão VPN ou outro circuito de ExpressRoute) no circuito de ExpressRoute pretendido. 
* Os nós locais e do Azure escolhidos para monitorar o circuito de ExpressRoute na configuração de monitoramento não têm conectividade entre si no circuito de ExpressRoute pretendido. Verifique se você escolheu os nós corretos que têm conectividade entre si no circuito do ExpressRoute que pretende monitorar.

### <a name="while-configuring-monitoring-of-my-expressroute-circuit-the-azure-nodes-are-not-being-detected"></a>Ao configurar o monitoramento do meu circuito do ExpressRoute, os nós do Azure não estão sendo detectados.
Isso pode acontecer se os nós do Azure estiverem conectados por meio de Operations Manager. O recurso de monitor do ExpressRoute dá suporte apenas aos nós do Azure que estão conectados como agentes diretos.

### <a name="i-cannot-discover-by-expressroute-circuits-in-the-oms-portal"></a>Não consigo descobrir por circuitos do ExpressRoute no portal do OMS
Embora NPM possa ser usado tanto da portal do Azure quanto do portal do OMS, a descoberta de circuito no recurso de monitor do ExpressRoute funciona apenas por meio do portal do Azure. Depois que os circuitos forem descobertos por meio do portal do Azure, você poderá usar o recurso em qualquer um dos dois portais. 

### <a name="in-the-service-connectivity-monitor-capability-the-service-response-time-network-loss-as-well-as-latency-are-shown-as-na"></a>No recurso do monitor de conectividade de serviço, o tempo de resposta do serviço, a perda de rede, bem como a latência são mostrados como NA
Isso pode acontecer se um ou mais for verdadeiro:

* O serviço está inoperante.
* O nó usado para verificar a conectividade de rede com o serviço está inoperante.
* O destino inserido na configuração de teste está incorreto.
* O nó não tem nenhuma conectividade de rede.

### <a name="in-the-service-connectivity-monitor-capability-a-valid-service-response-time-is-shown-but-network-loss-as-well-as-latency-are-shown-as-na"></a>No recurso do monitor de conectividade de serviço, um tempo de resposta de serviço válido é mostrado, mas a perda de rede, bem como a latência, é mostrada como NA
 Isso pode acontecer se um ou mais for verdadeiro:

* Se o nó usado para verificar a conectividade de rede com o serviço for um computador cliente Windows, o serviço de destino está bloqueando solicitações ICMP ou um firewall de rede está bloqueando solicitações ICMP originadas do nó.
* A caixa de seleção executar medidas de rede está em branco na configuração de teste.

### <a name="in-the-service-connectivity-monitor-capability-the-service-response-time-is-na-but-network-loss-as-well-as-latency-are-valid"></a>No recurso do monitor de conectividade de serviço, o tempo de resposta do serviço é NA, mas a perda de rede, bem como a latência, são válidas
Isso pode acontecer se o serviço de destino não for um aplicativo Web, mas o teste estiver configurado como um teste na Web. Edite a configuração de teste e escolha o tipo de teste como rede em vez de Web.

## <a name="miscellaneous"></a>Diversos

### <a name="is-there-a-performance-impact-on-the-node-being-used-for-monitoring"></a>Há um impacto no desempenho no nó que está sendo usado para monitoramento?
O processo NPM é configurado para parar se utilizar mais de 5% dos recursos de CPU do host. Isso é para garantir que você possa continuar usando os nós para suas cargas de trabalho usuais sem afetar o desempenho.

### <a name="does-npm-edit-firewall-rules-for-monitoring"></a>NPM Editar regras de firewall para monitoramento?
O NPM cria apenas uma regra de firewall do Windows local nos nós em que o script do PowerShell EnableRules. ps1 é executado para permitir que os agentes criem conexões TCP entre si na porta especificada. A solução não modifica nenhuma regra de firewall de rede ou grupo de segurança de rede (NSG).

### <a name="how-can-i-check-the-health-of-the-nodes-being-used-for-monitoring"></a>Como posso verificar a integridade dos nós que estão sendo usados para monitoramento?
Você pode exibir o status de integridade dos nós que estão sendo usados para monitoramento na seguinte exibição: Monitor de Desempenho de Rede-> Configuration-> nós. Se um nó não estiver íntegro, você poderá exibir os detalhes do erro e executar a ação sugerida.

### <a name="can-npm-report-latency-numbers-in-microseconds"></a>É possível NPM números de latência de relatório em microssegundos?
NPM arredonda os números de latência na interface do usuário e em milissegundos. Os mesmos dados são armazenados em uma granularidade mais alta (às vezes, até quatro casas decimais).

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre Monitor de Desempenho de Rede fazendo referência à [solução de monitor de desempenho de rede no Azure](../../azure-monitor/insights/network-performance-monitor.md).
