---
title: FAQ - solução de Monitor de desempenho de rede no Azure | Documentos da Microsoft
description: Este artigo captura as perguntas mais frequentes sobre o Monitor de desempenho de rede no Azure. Ajuda do Monitor de desempenho (NPM) de rede-monitorizar o desempenho das suas redes em tempo real e Deteta e localiza afunilamentos de desempenho de rede.
services: log-analytics
documentationcenter: ''
author: vinynigam
manager: agummadi
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/12/2018
ms.author: vinynigam
ms.openlocfilehash: d573b7ad9edac6b1502744b61e85cba3402a6f68
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/07/2019
ms.locfileid: "65232672"
---
# <a name="network-performance-monitor-solution-faq"></a>FAQ de solução de Monitor de desempenho de rede

![Símbolo de Monitor de desempenho de rede](media/network-performance-monitor-faq/npm-symbol.png)

Este artigo captura as perguntas mais frequentes (FAQ) sobre o Monitor de desempenho de rede (NPM) no Azure

[Monitor de desempenho de rede](/azure/networking/network-monitoring-overview) é uma conta baseada na cloud [a monitorização de rede híbrida](../../azure-monitor/insights/network-performance-monitor-performance-monitor.md) solução que ajuda a monitorizar o desempenho de rede entre vários pontos na sua infraestrutura de rede. Ele também ajuda a monitorizar a conectividade de rede [pontos finais de serviços e de aplicações](../../azure-monitor/insights/network-performance-monitor-service-connectivity.md) e [monitorizar o desempenho do Azure ExpressRoute](../../azure-monitor/insights/network-performance-monitor-expressroute.md). 

Monitor de desempenho de rede Deteta problemas de rede, como blackholing de tráfego, erros de encaminhamento e problemas que os métodos de monitorização de rede convencionais não conseguem detetar. A solução gera alertas e notifica-o quando existir uma falha do limiar de uma ligação de rede. Também garante a deteção atempada de problemas de desempenho de rede e localiza a origem do problema num dispositivo ou segmento de rede específico. 

Obter mais informações sobre os vários recursos suportados pelo [Monitor de desempenho de rede](https://docs.microsoft.com/azure/networking/network-monitoring-overview) está disponível online.

## <a name="set-up-and-configure-agents"></a>Definir e configurar agentes

### <a name="what-are-the-platform-requirements-for-the-nodes-to-be-used-for-monitoring-by-npm"></a>Quais são os requisitos de plataforma para os nós a ser utilizada para monitorização pelo NPM?
Abaixo encontram-se os requisitos de plataforma para de NPM vários recursos:

- Monitor de desempenho e capacidades do Monitor de conectividade do serviço de NPM suportam o servidor do Windows e sistemas de operativos de áreas de trabalho/cliente Windows. Versões de SO do servidor do Windows suportadas são 2008 SP1 ou posterior. Versões de áreas de trabalho/cliente Windows suportadas são o Windows 10, Windows 8.1, Windows 8 e Windows 7. 
- Apenas servidor de Windows oferece suporte a capacidade de Monitor do ExpressRoute do NPM (2008 SP1 ou posterior) sistema operacional.

### <a name="can-i-use-linux-machines-as-monitoring-nodes-in-npm"></a>Pode utilizar máquinas Linux como nós no NPM de monitorização?
A capacidade de monitorizar redes utilizando nós baseado em Linux está atualmente em pré-visualização. Entrar em contacto ao seu Gestor de conta para saber mais. Agentes do Linux fornecem a capacidade de monitorização apenas para a capacidade de Monitor de desempenho do NPM e não estão disponíveis para os recursos de Monitor de conectividade do serviço e Monitor do ExpressRoute

### <a name="what-are-the-size-requirements-of-the-nodes-to-be-used-for-monitoring-by-npm"></a>Quais são os requisitos de tamanho de nós para ser utilizada para monitorização pelo NPM?
Para executar a solução NPM no nó VMs para monitorizar as redes, os nós devem ter pelo menos de 500 MB de memória e um núcleo. Não precisa de utilizar nós separados para a execução de NPM. A solução pode ser executado em nós que têm outras cargas de trabalho em execução no mesmo. A solução tem a capacidade de parar o processo de monitorização, se utilizar mais do que 5% da CPU.

### <a name="to-use-npm-should-i-connect-my-nodes-as-direct-agent-or-through-system-center-operations-manager"></a>Para utilizar o NPM, deve ligar meus nós como agente direto ou por meio do System Center Operations Manager?
O Monitor de desempenho e as capacidades de Monitor de conectividade do serviço de suporte nós [ligado como agentes diretos](../../azure-monitor/platform/agent-windows.md) e [ligado através do Operations Manager](../../azure-monitor/platform/om-agents.md).

Para capacidade de Monitor do ExpressRoute, os nós do Azure devem estar conectados como agentes diretos apenas. Os nós do Azure, que se encontram ligados através do Operations Manager não são suportados. Para nós no local, os nós como agentes diretos e através do Operations Manager ligados são suportados para monitorização de um circuito do ExpressRoute.

### <a name="which-protocol-among-tcp-and-icmp-should-be-chosen-for-monitoring"></a>Qual protocolo entre TCP e ICMP deve ser selecionado para a monitorização?
Se estiver a monitorizar sua rede através de nós com base no servidor do Windows, recomendamos que utilizar TCP como protocolo de monitorização, uma vez que ele fornece uma melhor precisão. 

ICMP é recomendado para nós de com base no sistema operativo de áreas de trabalho/cliente do Windows. Does'nt esta plataforma permitem que os dados TCP enviado através de sockets não processados, que utiliza o NPM para detetar a topologia de rede.

Pode obter mais detalhes sobre as vantagens relativas de cada protocolo [aqui](../../azure-monitor/insights/network-performance-monitor-performance-monitor.md#choose-the-protocol).

### <a name="how-can-i-configure-a-node-to-support-monitoring-using-tcp-protocol"></a>Como posso configurar um nó para suportar a monitorização utilizando o protocolo TCP?
Para o nó suportar a monitorização através do protocolo TCP: 
* Certifique-se de que a plataforma de nó é o Windows Server (2008 SP1 ou posterior).
* Execute [EnableRules.ps1](https://aka.ms/npmpowershellscript) script do Powershell no nó. Ver [instruções](../../azure-monitor/insights/network-performance-monitor.md#configure-log-analytics-agents-for-monitoring) para obter mais detalhes.


### <a name="how-can-i-change-the-tcp-port-being-used-by-npm-for-monitoring"></a>Como posso alterar a porta TCP que está a ser utilizada pelo NPM para monitorização?
Pode alterar a porta TCP utilizada pelo NPM para monitorização, ao executar o [EnableRules.ps1](https://aka.ms/npmpowershellscript) script. Tem de introduzir o número da porta que pretende utilizar como um parâmetro. Por exemplo, para ativar o TCP na porta 8060, execute `EnableRules.ps1 8060`. Certifique-se de que utilizam a mesma porta TCP em todos os nós serem utilizados para monitorização.

O script configura apenas Windows Firewall localmente. Se tiver regras de grupo de segurança de rede (NSG) ou de firewall da rede, certifique-se de que eles permitem que o tráfego destinado a porta TCP utilizada pelo NPM.

### <a name="how-many-agents-should-i-use"></a>Número de agentes devo utilizar?
Deve utilizar, pelo menos, um agente para cada sub-rede que pretende monitorizar.

### <a name="what-is-the-maximum-number-of-agents-i-can-use-or-i-see-error--youve-reached-your-configuration-limit"></a>O que é o número máximo de agentes que posso usar ou vejo o erro em "... atingiu o limite de configuração"?
NPM limita o número de IPs para 5000 IPs por área de trabalho. Se um nó tiver endereços IPv4 e IPv6, esta será contabilizada como IPs de 2 para esse nó. Por conseguinte, este limite de 5000 IPs seria decidir o limite superior no número de agentes. Pode eliminar os agentes Inativos do separador de nós no NPM >> configurar. NPM também mantém o histórico de todos os IPs que nunca foram atribuídos à VM que aloja o agente e cada um é contabilizado como IP separado que contribuem para esse limite superior de IPs de 5000. Para libertar IPs sua área de trabalho, pode utilizar a página de nós para eliminar os IPs que não estão em utilização.

## <a name="monitoring"></a>Monitorização

### <a name="how-are-loss-and-latency-calculated"></a>Como são calculadas as perdas e latência
Agentes de origem enviam qualquer um dos SYN TCP pedidos (se o TCP é escolhida como o protocolo de monitorização) ou pedidos de eco ICMP (se o ICMP é escolhido como o protocolo de monitorização) para o IP de destino em intervalos regulares para se certificar de que todos os caminhos entre o IP de origem-destino combinação são abordados. A percentagem de pacotes recebidos e a hora de ida e volta do pacote é medida para calcular a perda e latência de cada caminho. Estes dados são agregados ao longo do intervalo de consulta e ao longo de todos os caminhos para obter os valores agregados de perda e latência para a combinação de IP para o intervalo de consulta específica.

### <a name="with-what-frequency-does-the-source-agent-send-packets-to-the-destination-for-monitoring"></a>Com que freqüência o agente de origem enviar pacotes para o destino para a monitorização?
Recursos de Monitor de desempenho e Monitor do ExpressRoute, a origem envia pacotes em 5 segundos e regista as medidas de rede. Estes dados são agregados ao longo de um intervalo de consulta de 3 minutos para calcular os valores médios e horas de pico de perda e latência. Para a capacidade de Monitor de conectividade do serviço, a frequência de envio de pacotes para medição de rede é determinada pela frequência inserida pelo usuário para o teste específico ao configurar o teste.

### <a name="how-many-packets-are-sent-for-monitoring"></a>Quantos pacotes são enviados para a monitorização?
O número de pacotes enviados pelo agente de origem para destino numa consulta é adaptável e é decidido por nosso algoritmo proprietário, que pode ser diferente para topologias de rede diferentes. Mais o número de caminhos de rede entre a combinação de IP de origem-destino, mais é o número de pacotes que são enviados. O sistema garante que todos os caminhos entre a combinação de IP de origem-destino são abordados.

### <a name="how-does-npm-discover-network-topology-between-source-and-destination"></a>Como é que o NPM detetar a topologia de rede entre a origem e destino?
NPM utiliza um algoritmo proprietário com base em Traceroute para detetar todos os caminhos e saltos entre a origem e destino.

### <a name="does-npm-provide-routing-and-switching-level-info"></a>O NPM fornece informações sobre nível de encaminhamento e a mudar 
Embora o NPM pode detetar todas as rotas possíveis entre o agente de origem e destino, ele não fornece visibilidade para o qual foi adotada uma rota por pacotes enviados por suas cargas de trabalho específicas. A solução pode ajudá-lo a identificar os caminhos e saltos de rede subjacente, que estão a adicionar latência mais do que o esperado.

### <a name="why-are-some-of-the-paths-unhealthy"></a>Por que motivo são alguns dos caminhos de mau estado de funcionamento?
Caminhos de rede diferentes podem existir entre a origem e destino IPs e cada caminho pode ter um valor diferente de perda e latência. NPM marca esses caminhos como mau estado de funcionamento (denoted com cor vermelha) para a qual os valores de perda de e/ou latência é superior ao limiar respectivo definido na configuração da monitorização.

### <a name="what-does-a-hop-in-red-color-signify-in-the-network-topology-map"></a>O que indicar um salto em cor vermelha no mapa de topologia de rede?
Se um salto é vermelho, ele significa que ele faz parte de um caminho mau estado de funcionamento de um mínimo. NPM marca apenas os caminhos como mau estado de funcionamento, ele não segregar o estado de funcionamento de cada caminho. Para identificar os saltos problemáticos, pode ver a latência de salto por salto e segregar os que adicionar mais do que a latência esperada.

### <a name="how-does-fault-localization-in-performance-monitor-work"></a>Como funciona a localização de falhas no Monitor de desempenho?
NPM utiliza um mecanismo de probabilístico para atribuir as probabilidades de falha para cada caminho de rede, o segmento de rede, e os saltos de rede constituintes com base no número de caminhos de mau estado de funcionamento são uma parte. À medida que os segmentos de rede e saltos se tornam parte do número mais elevado de caminhos danificados, aumenta a probabilidade de falhas associada a eles. Esse algoritmo funciona melhor quando tem muitos nós com o agente NPM ligado entre si como isso aumenta os pontos de dados para calcular as probabilidades de falha.

### <a name="how-can-i-create-alerts-in-npm"></a>Como criar alertas em NPM?
Consulte a [seção na documentação de alertas](https://docs.microsoft.com/azure/log-analytics/log-analytics-network-performance-monitor#alerts) para obter instruções passo a passo.

### <a name="can-npm-monitor-routers-and-servers-as-individual-devices"></a>Pode o NPM monitorizar routers e de servidores como dispositivos individuais?
NPM identifica apenas o nome IP e o anfitrião subjacente de saltos de rede (comutadores, routers, servidores, etc.) entre a origem e destino IPs. Ele também identifica a latência entre estes saltos identificados. Ele não individualmente monitorizar estes saltos subjacentes.

### <a name="can-npm-be-used-to-monitor-network-connectivity-between-azure-and-aws"></a>NPM pode ser utilizado para monitorizar a conectividade de rede entre o Azure e AWS?
Sim. Veja o artigo [monitorizar o Azure, AWS e redes no local utilizando NPM](https://blogs.technet.microsoft.com/msoms/2016/08/30/monitor-on-premises-cloud-iaas-and-hybrid-networks-using-oms-network-performance-monitor/) para obter detalhes.

### <a name="is-the-expressroute-bandwidth-usage-incoming-or-outgoing"></a>É a utilização de largura de banda do ExpressRoute entrada ou de saída?
Utilização de largura de banda é o total de largura de banda de entrada e saída. Ele é expresso em Bits/seg.

### <a name="can-we-get-incoming-and-outgoing-bandwidth-information-for-the-expressroute"></a>Podemos obter informações de largura de banda de entrada e saída para o ExpressRoute?
Valores de entrada e saídos para a largura de banda primária e secundária podem ser capturados.

Para informações do nível de peering, utilize o abaixo consulta mencionada na pesquisa de registos

    NetworkMonitoring 
    | where SubType == "ExpressRoutePeeringUtilization"
    | project CircuitName,PeeringName,PrimaryBytesInPerSecond,PrimaryBytesOutPerSecond,SecondaryBytesInPerSecond,SecondaryBytesOutPerSecond
  
Para informações de nível de circuito, utilize o abaixo mencionada consulta 

    NetworkMonitoring 
    | where SubType == "ExpressRouteCircuitUtilization"
    | project CircuitName,PrimaryBytesInPerSecond, PrimaryBytesOutPerSecond,SecondaryBytesInPerSecond,SecondaryBytesOutPerSecond

### <a name="which-regions-are-supported-for-npms-performance-monitor"></a>Que regiões são suportadas para o Monitor de desempenho do NPM?
NPM pode monitorizar a conectividade entre as redes em qualquer parte do mundo, a partir de uma área de trabalho que está alojado do [regiões suportadas](../../azure-monitor/insights/network-performance-monitor.md#supported-regions)

### <a name="which-regions-are-supported-for-npms-service-connectivity-monitor"></a>Que regiões são suportadas para o Monitor de conectividade do serviço de NPM?
NPM pode monitorizar a conectividade a serviços em qualquer parte do mundo, a partir de uma área de trabalho que está alojada do [regiões suportadas](../../azure-monitor/insights/network-performance-monitor.md#supported-regions)

### <a name="which-regions-are-supported-for-npms-expressroute-monitor"></a>Que regiões são suportadas para o Monitor do ExpressRoute do NPM?
NPM pode monitorizar os circuitos do ExpressRoute localizados em qualquer região do Azure. A carregar para o NPM, vai precisar de uma área de trabalho do Log Analytics que tem de estar alojada do [regiões suportadas](/azure/expressroute/how-to-npm)

## <a name="troubleshoot"></a>Resolução de problemas

### <a name="why-are-some-of-the-hops-marked-as-unidentified-in-the-network-topology-view"></a>Por que motivo são saltos marcados como não identificado na vista de topologia de rede?
NPM utiliza uma versão modificada do traceroute para detetar a topologia do agente de origem para o destino. Um salto não identificado representa que o salto de rede não respondeu ao pedido de traceroute do agente de origem. Se a três saltos de rede consecutivos não respondem às traceroute do agente, a solução marca os sem resposta saltos não identificados como e não tenta descobrir mais saltos.

Um salto não pode responder a um traceroute num ou mais do abaixo cenários:

* Os routers tenham sido configurados para não revelar sua identidade.
* Os dispositivos de rede não estão a permitir o tráfego ICMP_TTL_EXCEEDED.
* Existe uma firewall a bloquear a resposta ICMP_TTL_EXCEEDED do dispositivo de rede.

### <a name="why-does-my-link-show-unhealthy-but-the-topology-does-not"></a>Por que motivo, mas a topologia de meu show de ligação em mau estado de funcionamento não 
NPM monitoriza a perda de ponto-a-ponto, a latência e a topologia em intervalos diferentes. Perda e latência são medidos uma vez a cada cinco segundos e agregadas a cada três minutos (para o Monitor de desempenho e Monitor de rota Express) enquanto topologia é calculada usando o traceroute a cada 10 minutos. Por exemplo, entre 3:44 e 4:04, topologia poderão ser atualizada três vezes (3:44, 3:54, 4:04), mas a perda e latência são atualizados aproximadamente sete vezes (3:44, 3:47, 3:50, 3:53, 3:56, 3:59, 4:02). A topologia gerada em 3 minutos e 54 será renderizada para a perda e latência que obtém calculada em 3:56, 3:59 e 4:02. Suponha que recebe um alerta se o circuito de ER foi mau estado de funcionamento 3:59. Inicie sessão no NPM e tente definir o tempo de topologia para 3:59. NPM processará a topologia gerada em 3 minutos e 54. Para compreender a topologia último conhecida da sua rede, comparar os campos TimeProcessed (tempo em que perda e latência foi calculado) e TracerouteCompletedTime (tempo em que topologia foi calculada). 

### <a name="what-is-the-difference-between-the-fields-e2emedianlatency-and-avghoplatencylist-in-the-networkmonitoring-table"></a>O que é a diferença entre os campos E2EMedianLatency e AvgHopLatencyList na tabela NetworkMonitoring
E2EMedianLatency é a latência atualizada a cada três minutos depois de agregar os resultados de testes de ping de tcp, ao passo que AvgHopLatencyList é atualizado a cada 10 minutos, com base em traceroute. Para compreender o tempo exato em que foi calculado E2EMedianLatency, utilize o campo TimeProcessed. Para compreender o tempo exato em que o traceroute concluída e AvgHopLatencyList atualizada, utilize o campo TracerouteCompletedTime

### <a name="why-does-hop-by-hop-latency-numbers-differ-from-hoplatencyvalues"></a>Por que motivo é que os números de latência de salto por salto difere do HopLatencyValues 
HopLatencyValues são de origem para o ponto final.
Por exemplo: Saltos - A, B, C. AvgHopLatency - 10,15,20. Isso significa que a origem para uma latência = 10, origem a latência de B = 15 e origem a latência de C é 20. Interface do Usuário irá calcular a latência de salto de A-B como 5 na topologia

### <a name="the-solution-shows-100-loss-but-there-is-connectivity-between-the-source-and-destination"></a>A solução mostra a perda de 100%, mas não existe conectividade entre a origem e destino
Isto pode acontecer se a firewall do anfitrião ou o intermediário firewall (firewall de rede ou NSG do Azure) que está a bloquear a comunicação entre o agente de origem e o destino através da porta a ser utilizada para monitorização pelo NPM (por predefinição a porta é 8084, a menos que o cliente mudou isso).

* Para verificar que a firewall do anfitrião não está a bloquear a comunicação na porta necessária, ver o estado de funcionamento de nós de origem e de destino da vista do seguinte: Monitor de desempenho de rede -> configuração -> nós. 
  Se estiverem em mau estado de funcionamento, veja as instruções e tomar medidas corretivas. Se os nós estão em bom Estados, mova para o passo b. abaixo.
* Para verificar que um firewall de rede intermediários ou o NSG do Azure não está a bloquear a comunicação na porta necessária, utilize a através do utilitário de terceiros PsPing as instruções abaixo:
  * utilitário de psping está disponível para download [aqui](https://technet.microsoft.com/sysinternals/psping.aspx) 
  * Execute o seguinte comando do nó de origem.
    * psping -n 15 \<nó de destino IPAddress\>: portNumber por predefinição NPM utiliza a porta de 8084. Caso explicitamente mudaram isso usando o script EnableRules.ps1, introduza o número de porta personalizada que está a utilizar). Este é um ping de máquina do Azure para o local
* Verifique se os pings estão com êxito. Caso contrário, em seguida, ele indica que um firewall de rede intermediários ou o NSG do Azure está a bloquear o tráfego nesta porta.
* Agora, execute o comando a partir do nó de destino para o IP do nó de origem.


### <a name="there-is-loss-from-node-a-to-b-but-not-from-node-b-to-a-why"></a>Não há perda do nó A para B, mas não a partir do nó B para r. Por quê?
Como os caminhos de rede entre A para B podem ser diferentes dos caminhos de rede entre B para R, podem ser observados valores diferentes para perda e latência.

### <a name="why-are-all-my-expressroute-circuits-and-peering-connections-not-being-discovered"></a>Por que são todos os meus circuitos do ExpressRoute e ligações de peering não detetadas?
NPM agora Deteta circuitos do ExpressRoute e ligações de peering em todas as subscrições a que o utilizador tem acesso. Escolha todas as subscrições em que os seus recursos de Express Route estão ligados e ative a monitorização para cada recurso detetado. NPM procura objetos de conexão quando detetar um peering privado, portanto, não se uma VNET está associada a seu modo de peering.

### <a name="the-er-monitor-capability-has-a-diagnostic-message-traffic-is-not-passing-through-any-circuit-what-does-that-mean"></a>A capacidade de ER Monitor tem uma mensagem de diagnóstico "Tráfego não passa através de qualquer circuito". O que é que isto significa?

É possível um cenário onde existe uma ligação de bom estado de funcionamento entre no local e nós do Azure, mas o tráfego não será o circuito de ExpressRoute configurados para serem monitorizados pelo NPM. 

Isto pode acontecer se:

* O circuito de ER está inativo.
* Os filtros de rota são configurados de forma que eles derem prioridade a outras rotas (como uma ligação VPN ou outro circuito do ExpressRoute), o circuito de ExpressRoute pretendido. 
* Os locais e os nós do Azure escolhidos para o circuito do ExpressRoute na configuração de monitorização, de monitorização não têm conectividade entre si através de circuito do ExpressRoute pretendido. Certifique-se de que optou por nós corretos que têm conectividade entre si através de circuito ExpressRoute que pretende monitorizar.

### <a name="while-configuring-monitoring-of-my-expressroute-circuit-the-azure-nodes-are-not-being-detected"></a>Ao configurar a monitorização do meu circuito do ExpressRoute, os nós do Azure não estão a ser detetados.
Isto pode acontecer se os nós do Azure estiverem ligados através do Operations Manager. A capacidade de Monitor do ExpressRoute suporta apenas os nós do Azure que estiver ligados como agentes diretos.

### <a name="i-cannot-discover-by-expressroute-circuits-in-the-oms-portal"></a>Eu não é possível detectar por circuitos do ExpressRoute no portal do OMS
Apesar do NPM pode ser utilizado tanto a partir do portal do Azure, bem como o portal do OMS, a deteção de circuito a capacidade de Monitor do ExpressRoute funciona apenas através do portal do Azure. Depois do circuitos é detetado através do portal do Azure, em seguida, pode utilizar a capacidade em qualquer um dos dois portais. 

### <a name="in-the-service-connectivity-monitor-capability-the-service-response-time-network-loss-as-well-as-latency-are-shown-as-na"></a>Na capacidade de Monitor de conectividade do serviço, o tempo de resposta do serviço, perda de rede, bem como latência é apresentada como ND
Isto pode acontecer se um ou mais for verdadeira:

* O serviço está desativado.
* O nó utilizado para a verificar a conectividade de rede para o serviço está inativo.
* O destino introduzido na configuração de teste está incorreto.
* O nó não tem qualquer conectividade de rede.

### <a name="in-the-service-connectivity-monitor-capability-a-valid-service-response-time-is-shown-but-network-loss-as-well-as-latency-are-shown-as-na"></a>A capacidade de Monitor de conectividade do serviço, é mostrado um tempo de resposta de serviço válido, mas a perda de rede, bem como a latência, é apresentada como NA
 Isto pode acontecer se um ou mais for verdadeira:

* Se o nó utilizado para a verificar a conectividade de rede para o serviço for uma máquina de cliente do Windows, o serviço de destino está a bloquear pedidos ICMP ou uma firewall de rede está a bloquear pedidos ICMP que têm a partir do nó de origem.
* A caixa de verificação Executar medições de rede está em branco na configuração do teste.

### <a name="in-the-service-connectivity-monitor-capability-the-service-response-time-is-na-but-network-loss-as-well-as-latency-are-valid"></a>A capacidade de Monitor de conectividade do serviço, o tempo de resposta do serviço é ND mas perda de rede, bem como latência é válida
Isto pode acontecer se o serviço de destino não é um aplicativo web, mas o teste é configurado como um teste Web. Editar a configuração de teste e escolha o tipo de teste como rede em vez do Web.

## <a name="miscellaneous"></a>Diversos

### <a name="is-there-a-performance-impact-on-the-node-being-used-for-monitoring"></a>Existe um impacto no desempenho no nó que está a ser utilizado para monitorização?
Processo NPM está configurado para ser interrompida se ele utiliza mais do que 5% dos recursos de CPU do anfitrião. Trata-se para se certificar de que pode continuar a utilizar os nós para as cargas de trabalho normais sem afetar o desempenho.

### <a name="does-npm-edit-firewall-rules-for-monitoring"></a>O NPM editar regras de firewall para a monitorização?
NPM apenas cria uma regra de Firewall do Windows local em nós de onde é executado o script do EnableRules.ps1 Powershell para permitir que os agentes criar conexões TCP entre si na porta especificada. A solução não modifica qualquer firewall da rede ou regras do grupo de segurança de rede (NSG).

### <a name="how-can-i-check-the-health-of-the-nodes-being-used-for-monitoring"></a>Como posso verificar o estado de funcionamento de nós serem utilizados para monitorização?
Pode ver o estado de funcionamento de nós ser utilizado para a monitorização a partir da vista seguinte: Monitor de desempenho de rede -> configuração -> nós. Se um nó está danificado, pode ver os detalhes do erro e tome as ações sugeridas.

### <a name="can-npm-report-latency-numbers-in-microseconds"></a>NPM reportam números de latência em microssegundos
NPM arredonda os números de latência na interface de Usuário e, em milissegundos. Os mesmos dados são armazenados numa maior granularidade (casas decimais, por vezes, até quatro).

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre o Monitor de desempenho de rede fazendo referência a [solução Monitor de desempenho de rede no Azure](../../azure-monitor/insights/network-performance-monitor.md).
