---
title: Solução de dados de fio em Azure Monitor | Microsoft Docs
description: Os dados de fio são dados consolidados de rede e desempenho de computadores com agentes do Log Analytics. Os dados de rede são combinados com os seus dados de registo para ajudá-lo a correlacionar os dados.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/26/2021
ms.openlocfilehash: 1a9ea544419ef5c688e78a25eeb0eb444b196ec9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105732028"
---
# <a name="wire-data-20-preview-solution-in-azure-monitor-retired"></a>Solução de Dados de Fio 2.0 (Pré-visualização) no Monitor Azure (Aposentado)

![Símbolo do Wire Data](media/wire-data/wire-data2-symbol.png)

>[!NOTE]
>A solução Dados de Fio foi substituída por [insights VM](../vm/vminsights-overview.md) e [solução de Mapa de Serviço.](../vm/service-map.md)  Ambos utilizam o agente Log Analytics e o agente De dependência para recolher dados de ligação à rede no Azure Monitor.
>
>O suporte para a solução de Dados de Fios terminará a **31 de março de 2022**.  Até à data de aposentadoria, os clientes existentes que utilizam a solução Wire Data 2.0 (pré-visualização) podem continuar a usá-la.
>
>Os clientes novos e existentes devem instalar os [insights VM](../vm/vminsights-enable-overview.md) ou [a solução De Mapa de Serviços.](../vm/service-map.md)  O conjunto de dados do Mapa que recolhem é comparável ao conjunto de dados de dados de dados de dados de fio 2.0 (pré-visualização).  Os insights vM incluem o conjunto de dados do Mapa de Serviço, juntamente com dados de desempenho adicionais e funcionalidades para análise. Ambas as ofertas têm [ligações com a Azure Sentinel.](https://docs.microsoft.com/azure/sentinel/connect-data-sources#map-data-types-with-azure-sentinel-connection-options)
 

Os dados de fio são dados consolidados de rede e desempenho recolhidos a partir de computadores ligados ao Windows e ligados ao Linux com o agente Log Analytics, incluindo os monitorizados pelo Gestor de Operações no seu ambiente. Os dados de rede são combinados com os seus outros dados de registo para ajudá-lo a correlacionar os dados.

Além do agente Log Analytics, a solução Wire Data utiliza agentes de dependência da Microsoft que instala em computadores na sua infraestrutura de TI. Os Agentes de Dependência monitorizam os dados de rede enviados para e a partir dos seus computadores para níveis de rede 2-3 no [modelo OSI](https://en.wikipedia.org/wiki/OSI_model), incluindo os vários protocolos e portas utilizados. Os dados são então enviados para o Azure Monitor utilizando agentes.

## <a name="migrate-to-azure-monitor-vm-insights-or-service-map"></a>Migrar para Azure Monitor VM insights ou mapa de serviço

Em muitos casos, vemos que os clientes têm frequentemente dados de arame 2.0 (pré-visualização) e  [informações VM](../vm/vminsights-overview.md) ou [solução de Mapa de Serviço](../vm/service-map.md) já ativada nos mesmos VMs.  Isto significa que tem a oferta de substituição ativada no seu VM.  Pode simplesmente [remover a solução Data 2.0 (pré-visualização) do seu espaço de trabalho Log Analytics](https://docs.microsoft.com/azure/azure-monitor/insights/solutions?tabs=portal#remove-a-monitoring-solution).

Se tiver VMs que tenham apenas Dados de Fio 2.0 (pré-visualização) ativados neles, então pode embarcar nos VMs para [insights VM](../vm/vminsights-enable-overview.md) ou [solução de Mapa de Serviço](../vm/service-map.md) e, em seguida, remover a [solução de Dados de Fio 2.0 (pré-visualização) do seu espaço de trabalho Log Analytics](https://docs.microsoft.com/azure/azure-monitor/insights/solutions?tabs=portal#remove-a-monitoring-solution).

## <a name="migrate-your-queries-to-the-vmconnection-table-from-azure-monitor-vm-insights"></a>Migrar as suas consultas para a tabela VMConnection a partir de insights VM do Monitor Azure

### <a name="agents-providing-data"></a>Agentes que fornecem dados

#### <a name="wire-data-20-query"></a>Consulta de Dados de Fio 2.0

```
WireData
| summarize AggregatedValue = sum(TotalBytes) by Computer
| limit 500000
```

#### <a name="vm-insights-and-service-map-query"></a>Introspeções VM e consulta do Mapa de Serviços

```
VMConnection
| summarize AggregatedValue = sum(BytesReceived + BytesSent) by Computer
| limit 500000
```

### <a name="ip-addresses-of-the-agents-providing-data"></a>Endereços IP dos agentes que fornecem dados

#### <a name="wire-data-20-query"></a>Consulta de Dados de Fio 2.0

```
WireData
| summarize AggregatedValue = count() by LocalIP
```

#### <a name="vm-insights-and-service-map-query"></a>Introspeções VM e consulta do Mapa de Serviços

```
VMComputer
| distinct Computer, tostring(Ipv4Addresses)
```

### <a name="all-outbound-communications-by-remote-ip-address"></a>Todas as comunicações de saída por endereço IP remoto

#### <a name="wire-data-20-query"></a>Consulta de Dados de Fio 2.0

```
WireData
| where Direction == "Outbound"
| summarize AggregatedValue = count() by RemoteIP
```

#### <a name="vm-insights-and-service-map-query"></a>Introspeções VM e consulta do Mapa de Serviços

```
VMConnection
| where Direction == "outbound"
| summarize AggregatedValue = count() by RemoteIp
```

### <a name="bytes-received-by-protocol-name"></a>Bytes recebidos pelo Nome do Protocolo

#### <a name="wire-data-20-query"></a>Consulta de Dados de Fio 2.0

```
WireData 
| where Direction == "Inbound"
| summarize AggregatedValue = sum(ReceivedBytes) by ProtocolName
```

#### <a name="vm-insights-and-service-map-query"></a>Introspeções VM e consulta do Mapa de Serviços

```
VMConnection
| where Direction == "inbound"
| summarize AggregatedValue = sum(BytesReceived) by Protocol
```

### <a name="amount-of-network-traffic-in-bytes-by-process"></a>Quantidade de Tráfego de Rede (em Bytes) por Processo

#### <a name="wire-data-20-query"></a>Consulta de Dados de Fio 2.0

```
WireData
| summarize AggregatedValue = sum(TotalBytes) by ProcessName
```

#### <a name="vm-insights-and-service-map-query"></a>Introspeções VM e consulta do Mapa de Serviços

```
VMConnection
| summarize sum(BytesReceived), sum(BytesSent) by ProcessName
```

### <a name="more-examples-queries"></a>Mais consultas de exemplos

Consulte a documentação de [pesquisa de registo de registo de informações VM](https://docs.microsoft.com/azure/azure-monitor/vm/vminsights-log-search) e a documentação de alerta de [informações VM](https://docs.microsoft.com/azure/azure-monitor/vm/vminsights-alerts#sample-alert-queries) para consultas adicionais de exemplo.

## <a name="uninstall-wire-data-20-solution"></a>Desinstalar dados de fio 2.0 solução

Para desinstalar os dados do fio 2.0 basta remover a solução do espaço de trabalho do Log Analytics.  Isto resultará no seguinte:

* o pacote de gestão de dados de fio sendo removido dos VMs que estão ligados ao espaço de trabalho 
* o tipo de dados de fio já não aparece no seu espaço de trabalho

Siga [estas instruções](https://docs.microsoft.com/azure/azure-monitor/insights/solutions?tabs=portal#remove-a-monitoring-solution) para remover a solução dados de fio.

>[!NOTE]
>Se tiver a solução de insights do Mapa de Serviço ou VM no seu espaço de trabalho, então o pacote de gestão não será removido, uma vez que estas soluções também utilizam este pacote de gestão.

### <a name="wire-data-20-management-packs"></a>Pacotes de gestão de dados de fios 2.0

Quando o Wire Data é ativado numa área de trabalho do Log Analytics, é enviado um pacote de gestão de 300 KB para todos os servidores Windows nessa área de trabalho. Se estiver a utilizar agentes do System Center Operations Manager num [grupo de gestão ligado](../agents/om-agents.md), o pacote de gestão do Dependency Monitor será implementado a partir do System Center Operations Manager. Se os agentes estiverem diretamente ligados, o Azure Monitor entrega o pacote de gestão.

O pacote de gestão é designado Microsoft.IntelligencePacks.ApplicationDependencyMonitor. O mesmo é escrito em: %Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs. A origem de dados que o pacote de gestão utiliza é: %Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources&lt;AutoGeneratedID&gt;\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll.

## <a name="uninstall-the-dependency-agent"></a>Desinstalar o agente de dependência

>[!NOTE]
>Se planeia substituir os Dados de Fio por insights de Mapa de Serviço ou VM, não deverá remover o agente Desagravativo.

Utilize as seguintes secções para ajudá-lo a remover o agente Dependency.  

### <a name="uninstall-the-dependency-agent-on-windows"></a>Desinstalar o agente de dependência no Windows

Um administrador pode desinstalar o agente de dependência do Windows através do Painel de Controlo.

Um administrador também pode executar %Programfiles%\Microsoft Dependency Agent\Uninstall.exe de desinstalar o agente Dependency.

### <a name="uninstall-the-dependency-agent-on-linux"></a>Desinstalar o agente de dependência no Linux

Para desinstalar completamente o agente de Dependência do Linux, deve remover o próprio agente e o conector, que é instalado automaticamente com o agente. Pode desinstalar ambos com o seguinte comando único:

```
rpm -e dependency-agent dependency-agent-connector
```

## <a name="using-the-wire-data-20-solution"></a>Utilizar a solução Wire Data 2.0

Na página **Descrição Geral** da área de trabalho do Log Analytics no portal do Azure, clique no mosaico **Wire Data 2.0** para abrir o dashboard do Wire Data. O dashboard inclui os painéis na tabela seguinte. Cada painel apresenta uma lista com um máximo de 10 itens que correspondem aos critérios do âmbito e do intervalo de tempo especificados. Pode executar uma pesquisa de registos que devolve todos os registos ao clicar em **Ver tudo** na parte inferior do painel ou ao clicar no cabeçalho do painel.

| **Painel** | **Descrição** |
| --- | --- |
| Agentes que capturam tráfego de rede | Mostra o número de agentes que está a capturar o tráfego de rede e apresenta uma lista dos 10 principais computadores que estão a capturar o tráfego. Clique no número para executar uma pesquisa de registos para <code>WireData \| summarize sum(TotalBytes) by Computer \| take 500000</code>. Clique num computador na lista para executar uma pesquisa de registos para devolver o número total de bytes capturados. |
| Sub-redes Locais | Mostra o número de sub-redes locais que os agentes detetaram.  Clique no número para executar uma pesquisa de registos para <code>WireData \| summarize sum(TotalBytes) by LocalSubnet</code>, que apresenta uma lista de todas as sub-redes com o número de bytes enviados através de cada um deles. Clique numa sub-rede na lista para executar uma pesquisa de registos para devolver o número total de bytes enviados pela sub-rede. |
| Protocolos de Nível das Aplicações | Mostra o número de protocolos ao nível das aplicações em utilização, tal como detetado pelos agentes. Clique no número para executar uma pesquisa de registos para <code>WireData \| summarize sum(TotalBytes) by ApplicationProtocol</code>. Clique num protocolo para executar uma pesquisa de registos para devolver o número total de bytes com o protocolo. |

![Dashboard do Wire Data](./media/wire-data/wire-data-dash.png)

Pode utilizar o painel **Agentes a capturar tráfego de rede** para determinar a quantidade de largura de banda de rede que está a ser consumida pelos computadores. Este painel pode ajudá-lo a encontrar facilmente o computador _mais ativo_ no seu ambiente. Esses computadores podem estar sobrecarregados, a agir de forma anormal ou a utilizar mais recursos de rede do que o habitual.

![Screenshot dos Agentes que captam a lâmina de tráfego de rede no painel De dados de arame 2.0 mostrando a largura de banda da rede consumida por cada computador.](./media/wire-data/log-search-example01.png)

Da mesma forma, pode utilizar o painel **Sub-redes Locais** para determinar a quantidade de tráfego de rede que é movida através das suas sub-redes. Frequentemente, os utilizadores definem as sub-redes em torno das áreas críticas das suas aplicações. Este painel oferece uma vista dessas áreas.

![Screenshot da lâmina de sub-redes local no painel De dados de fio 2.0 mostrando a largura de banda da rede consumida por cada LocalSubnet.](./media/wire-data/log-search-example02.png)

O painel **Protocolos ao Nível das Aplicações** é útil porque ajuda a saber quais os protocolos em utilização. Por exemplo, poderia esperar que o SSH não estivesse em utilização no seu ambiente de rede. A visualização das informações disponíveis no painel pode rapidamente confirmar ou refutar essa expectativa.

![Screenshot da lâmina de protocolos de nível de aplicação no painel de instrumentos De fio 2.0 mostrando a largura de banda da rede consumida por cada protocolo.](./media/wire-data/log-search-example03.png)

Também é útil para saber se o tráfego do protocolo aumenta ou diminui ao longo do tempo. Por exemplo, se a quantidade de dados a ser transmitida por uma aplicação estiver a aumentar, pode ser algo a que deve estar atento ou que possa considerar significativo.

## <a name="input-data"></a>Dados de entrada

Os dados por fio recolhem metadados sobre o tráfego de rede com os agentes que tiver ativado. Cada agente envia dados em intervalos de cerca de 15 segundos.

## <a name="output-data"></a>Dados de saída

É criado um registo com um tipo de _WireData_ para cada tipo de dados de entrada. Os registos do WireData têm as propriedades mostradas na tabela seguinte:

| Propriedade | Descrição |
|---|---|
| Computador | Nome do computador onde os dados foram recolhidos |
| TimeGenerated | Hora do registo |
| LocalIP | Endereço IP do computador local |
| SessionState | Ligado ou desligado |
| ReceivedBytes | Quantidade de bytes recebidos |
| ProtocolName | Nome do protocolo de rede utilizado |
| IPVersion | Versão do IP |
| Direção | Entrada ou saída |
| MaliciousIP | Endereço IP de uma origem maliciosa conhecida |
| Gravidade | Gravidade do potencial malware |
| RemoteIPCountry | País/região do endereço IP remoto |
| ManagementGroupName | Nome do grupo de gestão do Operations Manager |
| SourceSystem | Origem onde os dados foram recolhidos |
| SessionStartTime | Hora de início da sessão |
| SessionEndTime | Hora de fim da sessão |
| LocalSubnet | Sub-rede onde os dados foram recolhidos |
| LocalPortNumber | Número de porta local |
| RemoteIP | Endereço IP remoto utilizado pelo computador remoto |
| RemotePortNumber | Número de porta utilizado pelo endereço IP remoto |
| SessionID | Um valor exclusivo que identifica a sessão de comunicação entre dois endereços IP |
| SentBytes | Número de bytes enviados |
| TotalBytes | Número total de bytes enviados durante a sessão |
| ApplicationProtocol | Tipo de protocolo de rede utilizado   |
| ProcessID | ID do processo do Windows |
| ProcessName | Caminho e nome do processo |
| RemoteIPLongitude | Valor da longitude do IP |
| RemoteIPLatitude | Valor da latitude do IP |

## <a name="next-steps"></a>Passos seguintes

- Consulte [introspeções em VM sobre](./vminsights-enable-overview.md) os requisitos e métodos que permitam a monitorização das suas máquinas virtuais.
- [Registos de pesquisa](../logs/log-query-overview.md) para ver registos detalhados da pesquisa dos dados por fio.
