---
title: Como consultar os registos de Monitor do Azure para VMs | Documentos da Microsoft
description: Monitor do Azure para a solução de VMs reencaminha as métricas e dados de registo para o Log Analytics e este artigo descreve os registos e incluem exemplos de consultas.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/20/2018
ms.author: magoedte
ms.openlocfilehash: 4a5f3178ad4d4152bb29e6c313b3fd332124c154
ms.sourcegitcommit: f58fc4748053a50c34a56314cf99ec56f33fd616
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/04/2018
ms.locfileid: "48269399"
---
# <a name="how-to-query-logs-from-azure-monitor-for-vms"></a>Como consultar os registos do Azure Monitor para VMs
O Monitor para VMs do Azure recolhe métricas de desempenho e a ligação, o computador e o processamento de dados de inventário e informações de estado de funcionamento e encaminhá-la para o arquivo de dados do Log Analytics no Azure Monitor.  Estes dados estão disponíveis para [pesquisa](../log-analytics/log-analytics-log-searches.md) no Log Analytics. Pode aplicar esses dados para cenários que incluem planos de migração, análise de capacidade, deteção e resolução de problemas de desempenho a pedido.

## <a name="map-records"></a>Registos de mapa
Um registo é gerado por hora para cada computador exclusivo e o processo, além dos registos que são gerados quando um processo ou o computador inicia ou é integrado para o Azure Monitor para a funcionalidade de mapa de VMs. Estes registos têm as propriedades nas tabelas seguintes. Os campos e os valores nos eventos ServiceMapComputer_CL mapeiam para campos do recurso de máquina na API ServiceMap do Azure Resource Manager. Os campos e os valores nos eventos ServiceMapProcess_CL mapeiam os campos do recurso de processo na API ServiceMap do Azure Resource Manager. O campo de ResourceName_s corresponda o campo de nome do recurso de Gestor de recursos correspondente. 

Existem propriedades internamente geradas, pode usar para identificar processos exclusivos e computadores:

- Computador: Utilizar *ResourceId* ou *ResourceName_s* para identificar exclusivamente um computador dentro de uma área de trabalho do Log Analytics.
- Processo: Utilizar o *ResourceId* para identificar exclusivamente um processo numa área de trabalho do Log Analytics. *ResourceName_s* é exclusivo dentro do contexto da máquina em que o processo está em execução (MachineResourceName_s) 

Uma vez que vários registos podem existir num processo especificado e o computador num intervalo de tempo especificado, as consultas podem devolver mais de um registo para o mesmo computador ou processo. Para incluir apenas o registo mais recente, adicione "| eliminação de duplicados ResourceId"para a consulta.

### <a name="connections"></a>Ligações
Métricas de ligação são escritas para uma nova tabela no Log Analytics - VMConnection. Esta tabela fornece informações sobre as ligações para uma máquina (entrada e saída). Métricas de ligação também são expostas com APIs que fornecem os meios para obter uma métrica específica durante uma janela de tempo.  Ligações de TCP resultantes de "*aceitar*ing - num soquete de escuta são entrada, enquanto os criados por *ligar*, ing para um determinado IP e porta são saída. A direção de uma conexão é representada pela propriedade de direção, o que pode ser definida para o **entrada** ou **saída**. 

Registos nestas tabelas são gerados a partir de dados reportados pelo agente de dependência. Cada registo representa uma observação sobre um intervalo de tempo de um minuto. A propriedade TimeGenerated indica o início do intervalo de tempo. Cada registro contém informações para identificar a entidade respectiva, ou seja, ligação ou porta, bem como as métricas associadas com essa entidade. Atualmente, apenas as atividades de rede que ocorre usando TCP sobre IPv4 é comunicada.

Para gerir o custo e a complexidade, registos de ligação não representam conexões de rede físico individuais. Várias ligações de rede física são agrupadas numa conexão lógica, que, em seguida, é refletida na respetiva tabela.  Significado, regista na *VMConnection* tabela representam um agrupamento lógico e não as individuais físicas ligações que estão a ser observadas. Ligação de rede física que partilham o mesmo valor para os seguintes atributos durante um determinado intervalo de um minuto, são agregados num único registo lógico na *VMConnection*. 

| Propriedade | Descrição |
|:--|:--|
|Direção |Direção da conexão, o valor é *entrada* ou *saída* |
|Máquina |O FQDN do computador |
|Processo |Identidade de processo ou grupos de processos, iniciar/aceitar a ligação |
|SourceIp |Endereço IP de origem |
|DestinationIp |Endereço IP de destino |
|DestinationPort |Número de porta de destino |
|Protocolo |Protocolo utilizado para a ligação.  É de valores *tcp*. |

Para levar em conta o impacto de agrupamento, são fornecidas informações sobre o número de ligações físicos agrupados nas seguintes propriedades do registo:

| Propriedade | Descrição |
|:--|:--|
|LinksEstablished |O número de ligações de rede física que foram estabelecidas durante a janela de tempo de criação de relatórios |
|LinksTerminated |O número de ligações de rede física que forem terminadas durante a janela de tempo de criação de relatórios |
|LinksFailed |O número de ligações de rede física que falharam durante a janela de tempo de criação de relatórios. Estas informações estão atualmente disponíveis apenas para ligações de saída. |
|LinksLive |O número de ligações de rede física que foram abertas no final da janela de tempo de criação de relatórios|

#### <a name="metrics"></a>Métricas

Para além das métricas de contagem de ligação, informações sobre o volume de dados enviados e receberam numa determinada ligação lógica ou a porta de rede também inclui as seguintes propriedades do registo:

| Propriedade | Descrição |
|:--|:--|
|BytesSent |Número total de bytes que foram enviados durante a janela de tempo de criação de relatórios |
|BytesReceived |Número total de bytes que foram recebidos durante a janela de tempo de criação de relatórios |
|Respostas |O número de respostas foi observada durante a janela de tempo de criação de relatórios. 
|ResponseTimeMax |O maior tempo de resposta (milissegundos) foi observado durante a janela de tempo de criação de relatórios. Se nenhum valor, a propriedade está em branco.|
|ResponseTimeMin |O menor tempo de resposta (milissegundos) foi observado durante a janela de tempo de criação de relatórios. Se nenhum valor, a propriedade está em branco.|
|ResponseTimeSum |A soma de todos os tempos de resposta (milissegundos) foi observada durante a janela de tempo de criação de relatórios. Se nenhum valor, a propriedade está em branco.|

O terceiro tipo de dados está a ser comunicados é o tempo de resposta - quanto um chamador passa aguardando para um pedido enviado através de uma ligação para serem processados e emitida por ponto final remoto. O tempo de resposta reportado é uma estimativa do tempo de resposta verdadeiro do protocolo subjacente do aplicativo. É calculada usando a heurística com base na observação do fluxo de dados entre o final de origem e de destino de uma ligação de rede física. Conceitualmente, ele é a diferença entre a hora que do último byte de um pedido deixa o remetente e a hora ao último byte de resposta receber retorne a ele. Estes dois carimbos de data / é usados para delinear a pedido e resposta a eventos numa determinada ligação física. A diferença entre eles representa o tempo de resposta de um único pedido. 

Neste primeiro lançamento desse recurso, o nosso algoritmo é uma aproximação que poderá funcionar com variados graus de sucesso, dependendo do protocolo de real do aplicativo usado para uma ligação de rede fornecido. Por exemplo, a abordagem atual funciona bem para protocolos de solicitação-resposta com base, como HTTP (S), mas não funcionam com unidirecional ou protocolos baseada na fila de mensagens.

Aqui estão alguns pontos importantes a considerar:

1. Se um processo aceitar ligações no mesmo endereço IP, mas ao longo de várias interfaces de rede, será reportado um registo separado para cada interface. 
2. Registos com o IP de caráter universal não irão conter nenhuma atividade. Eles estão incluídos para representar o fato de que uma porta na máquina é aberto a entrada de tráfego.
3. Para reduzir verbosidade e volume de dados, registos com o IP de caráter universal serão omitidos quando existe um registro correspondente (para o mesmo processo, porta e protocolo) com um endereço IP específico. Quando um registo IP de caráter universal for omitido, a propriedade de registo de IsWildcardBind com o endereço IP específico, será de ser definida como "Verdadeiro" para indicar que a porta está exposta através de cada interface da máquina de geração de relatórios.
4. Portas que estão vinculadas apenas numa interface específica ter IsWildcardBind definido como "False".

#### <a name="naming-and-classification"></a>Nomenclatura e classificação
Para sua comodidade, o endereço IP do final de uma conexão remota está incluído na propriedade RemoteIp. Para ligações de entrada, RemoteIp é o mesmo como SourceIp, enquanto para ligações de saída, é o mesmo que DestinationIp. A propriedade RemoteDnsCanonicalNames representa os nomes de canônicos de DNS comunicados pela máquina para RemoteIp. As propriedades RemoteDnsQuestions e RemoteClassification estão reservadas para utilização futura. 

#### <a name="geolocation"></a>Geolocalização
*VMConnection* também inclui informações de localização geográfica para o final de cada registo de ligação remota nas seguintes propriedades do registo: 

| Propriedade | Descrição |
|:--|:--|
|RemoteCountry |O nome do país RemoteIp de alojamento.  Por exemplo, *dos Estados Unidos* |
|RemoteLatitude |A latitude da localização geográfica. Por exemplo, *47.68* |
|RemoteLongitude |A longitude da localização geográfica. Por exemplo, *-122.12* |

#### <a name="malicious-ip"></a>IP malicioso
Cada propriedade RemoteIp *VMConnection* tabela é comparada com um conjunto de IPs com atividades maliciosas conhecidas. Se o RemoteIp é identificado como malicioso as seguintes propriedades serão preenchidas (elas estiverem vazias, quando o IP não é considerado malicioso) nas seguintes propriedades do registo:

| Propriedade | Descrição |
|:--|:--|
|MaliciousIp |O endereço de RemoteIp |
|IndicatorThreadType |Indicador de ameaça detetada é um dos seguintes valores *Botnet*, *C2*, *CryptoMining*, *Darknet*, *DDos* , *MaliciousUrl*, *software maligno*, *Phishing*, *Proxy*, *PUA*, *Lista de observação*.   |
|Descrição |Descrição da ameaça observada. |
|TLPLevel |Nível de protocolo de semáforo (TLP) é um dos valores definidos, *White*, *verde*, *Amber*, *Red*. |
|Confiança |Os valores são *0 – 100*. |
|Gravidade |Os valores são *0 – 5*, onde *5* é o mais grave e *0* não for grave em todos os. Valor predefinido é *3*.  |
|FirstReportedDateTime |Na primeira vez que o fornecedor reportou o indicador. |
|LastReportedDateTime |A última vez que o indicador foi visto por Interflow. |
|IsActive |Indica a indicadores são desativados com *True* ou *falso* valor. |
|ReportReferenceLink |Links para relatórios relacionados com um determinado observable. |
|AdditionalInformation |Fornece informações adicionais, se aplicável, sobre a ameaça observada. |

### <a name="servicemapcomputercl-records"></a>Registos de ServiceMapComputer_CL
Registos com um tipo de *ServiceMapComputer_CL* tiver dados de inventário para servidores com o agente de dependência. Estes registos têm as propriedades na tabela a seguir:

| Propriedade | Descrição |
|:--|:--|
| Tipo | *ServiceMapComputer_CL* |
| SourceSystem | *OpsManager* |
| ResourceId | O identificador exclusivo para o computador da área de trabalho |
| ResourceName_s | O identificador exclusivo para o computador da área de trabalho |
| ComputerName_s | O FQDN do computador |
| Ipv4Addresses_s | Endereços de uma lista de IPv4 do servidor |
| Ipv6Addresses_s | Endereços de uma lista de IPv6 do servidor |
| DnsNames_s | Uma matriz de nomes de DNS |
| OperatingSystemFamily_s | Windows ou Linux |
| OperatingSystemFullName_s | O nome completo do sistema operativo  |
| Bitness_s | O número de bits da máquina (32 bits ou 64 bits)  |
| PhysicalMemory_d | A memória física em MB |
| Cpus_d | O número de CPUs |
| CpuSpeed_d | A velocidade de CPU em MHz|
| VirtualizationState_s | *desconhecido*, *físico*, *virtual*, *hipervisor* |
| VirtualMachineType_s | *Hyper-v*, *vmware*e assim por diante |
| VirtualMachineNativeMachineId_g | O ID da VM como atribuído pelo seu hipervisor |
| VirtualMachineName_s | O nome da VM |
| BootTime_t | O tempo de inicialização |

### <a name="servicemapprocesscl-type-records"></a>Registos do tipo de ServiceMapProcess_CL
Registos com um tipo de *ServiceMapProcess_CL* tiver dados de inventário para processos ligados por TCP em servidores com o agente de dependência. Estes registos têm as propriedades na tabela a seguir:

| Propriedade | Descrição |
|:--|:--|
| Tipo | *ServiceMapProcess_CL* |
| SourceSystem | *OpsManager* |
| ResourceId | O identificador exclusivo para um processo dentro da área de trabalho |
| ResourceName_s | O identificador exclusivo para um processo na máquina em que está em execução|
| MachineResourceName_s | O nome do recurso da máquina |
| ExecutableName_s | O nome do executável de processo |
| StartTime_t | A hora de início do conjunto de processo |
| FirstPid_d | O PID primeiro no conjunto de processos |
| Description_s | A descrição de processo |
| CompanyName_s | O nome da empresa |
| InternalName_s | O nome interno |
| ProductName_s | O nome do produto |
| ProductVersion_s | A versão do produto |
| FileVersion_s | A versão do ficheiro |
| CommandLine_s | A linha de comandos |
| ExecutablePath _s | O caminho para o ficheiro executável |
| WorkingDirectory_s | O diretório de trabalho |
| Nome de Utilizador | A conta sob a qual o processo está em execução |
| UserDomain | O domínio em que o processo está em execução |

## <a name="sample-log-searches"></a>Pesquisas de registo de exemplo

### <a name="list-all-known-machines"></a>Listar todas as máquinas conhecidas
`ServiceMapComputer_CL | summarize arg_max(TimeGenerated, *) by ResourceId`

### <a name="list-the-physical-memory-capacity-of-all-managed-computers"></a>Liste a capacidade de memória física de todos os computadores geridos.
`ServiceMapComputer_CL | summarize arg_max(TimeGenerated, *) by ResourceId | project PhysicalMemory_d, ComputerName_s`

### <a name="list-computer-name-dns-ip-and-os"></a>Nome de computador da lista, o DNS, o IP e o sistema operacional.
`ServiceMapComputer_CL | summarize arg_max(TimeGenerated, *) by ResourceId | project ComputerName_s, OperatingSystemFullName_s, DnsNames_s, Ipv4Addresses_s`

### <a name="find-all-processes-with-sql-in-the-command-line"></a>Localizar todos os processos com "sql" na linha de comandos
`ServiceMapProcess_CL | where CommandLine_s contains_cs "sql" | summarize arg_max(TimeGenerated, *) by ResourceId`

### <a name="find-a-machine-most-recent-record-by-resource-name"></a>Encontrar uma máquina (registo mais recente) ao nome do recurso
`search in (ServiceMapComputer_CL) "m-4b9c93f9-bc37-46df-b43c-899ba829e07b" | summarize arg_max(TimeGenerated, *) by ResourceId`

### <a name="find-a-machine-most-recent-record-by-ip-address"></a>Localizar uma máquina (registo mais recente) por endereço IP
`search in (ServiceMapComputer_CL) "10.229.243.232" | summarize arg_max(TimeGenerated, *) by ResourceId`

### <a name="list-all-known-processes-on-a-specified-machine"></a>Lista de todos os processos num computador especificado
`ServiceMapProcess_CL | where MachineResourceName_s == "m-559dbcd8-3130-454d-8d1d-f624e57961bc" | summarize arg_max(TimeGenerated, *) by ResourceId`

### <a name="list-all-computers-running-sql"></a>Lista de todos os computadores que executam o SQL
`ServiceMapComputer_CL | where ResourceName_s in ((search in (ServiceMapProcess_CL) "\*sql\*" | distinct MachineResourceName_s)) | distinct ComputerName_s`

### <a name="list-all-unique-product-versions-of-curl-in-my-datacenter"></a>Listar todas as versões de produto exclusiva de curl em meu datacenter
`ServiceMapProcess_CL | where ExecutableName_s == "curl" | distinct ProductVersion_s`

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>Criar um grupo de computadores de todos os computadores em execução no CentOS
`ServiceMapComputer_CL | where OperatingSystemFullName_s contains_cs "CentOS" | distinct ComputerName_s`

### <a name="summarize-the-outbound-connections-from-a-group-of-machines"></a>Resumir as ligações de saída de um grupo de máquinas
```
// the machines of interest
let machines = datatable(m: string) ["m-82412a7a-6a32-45a9-a8d6-538354224a25"];
// map of ip to monitored machine in the environment
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
// all connections to/from the machines of interest
let out=materialize(VMConnection
| where Machine in (machines)
| summarize arg_max(TimeGenerated, *) by ConnectionId);
// connections to localhost augmented with RemoteMachine
let local=out
| where RemoteIp startswith "127."
| project ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine=Machine;
// connections not to localhost augmented with RemoteMachine
let remote=materialize(out
| where RemoteIp !startswith "127."
| join kind=leftouter (ips) on $left.RemoteIp == $right.ips
| summarize by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine=MonitoredMachine);
// the remote machines to/from which we have connections
let remoteMachines = remote | summarize by RemoteMachine;
// all augmented connections
(local)
| union (remote)
//Take all outbound records but only inbound records that come from either //unmonitored machines or monitored machines not in the set for which we are computing dependencies.
| where Direction == 'outbound' or (Direction == 'inbound' and RemoteMachine !in (machines))
| summarize by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine
// identify the remote port
| extend RemotePort=iff(Direction == 'outbound', DestinationPort, 0)
// construct the join key we'll use to find a matching port
| extend JoinKey=strcat_delim(':', RemoteMachine, RemoteIp, RemotePort, Protocol)
// find a matching port
| join kind=leftouter (VMBoundPort 
| where Machine in (remoteMachines) 
| summarize arg_max(TimeGenerated, *) by PortId 
| extend JoinKey=strcat_delim(':', Machine, Ip, Port, Protocol)) on JoinKey
// aggregate the remote information
| summarize Remote=makeset(iff(isempty(RemoteMachine), todynamic('{}'), pack('Machine', RemoteMachine, 'Process', Process1, 'ProcessName', ProcessName1))) by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol
```

## <a name="next-steps"></a>Passos Seguintes
* Se estiver familiarizado com a criação de consultas do Log Analytics, consulte [como utilizar a página do Log Analytics](../log-analytics/query-language/get-started-analytics-portal.md) no portal do Azure para escrever consultas do Log Analytics.
* Saiba mais sobre [escrever consultas de pesquisa](../log-analytics/query-language/search-queries.md).