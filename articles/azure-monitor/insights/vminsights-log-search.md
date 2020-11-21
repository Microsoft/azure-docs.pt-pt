---
title: Como consultar registos do Azure Monitor para VMs
description: O Azure Monitor para solução VMs recolhe métricas e dados de registo e este artigo descreve os registos e inclui consultas de amostra.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: 6b3fdf052ce7f0d6a5c3497aa1ac971d9249546a
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95015597"
---
# <a name="how-to-query-logs-from-azure-monitor-for-vms"></a>Como consultar registos do Azure Monitor para VMs

O Azure Monitor para VMs recolhe métricas de desempenho e conexão, dados de inventário de computador e processo, e informações do estado de saúde e reencaminha-os para o espaço de trabalho Log Analytics em Azure Monitor.  Estes dados estão disponíveis para [consulta](../log-query/log-query-overview.md) no Azure Monitor. Pode aplicar estes dados em cenários que incluem planeamento de migração, análise de capacidade, descoberta e resolução de problemas de desempenho a pedido.

## <a name="map-records"></a>Mapear registos

Um registo é gerado por hora para cada computador e processo único, além dos registos que são gerados quando um processo ou computador começa ou é embarcado para Azure Monitor para funcionalidade de Mapa de VMs. Estes registos têm as propriedades nas seguintes tabelas. Os campos e valores no mapa de eventos ServiceMapComputer_CL para os campos do recurso Máquina na API do ServiceMap Azure Resource Manager. Os campos e valores no mapa de eventos ServiceMapProcess_CL para os campos do recurso processuais na API do ServiceMap Azure Resource Manager. O campo ResourceName_s corresponde ao campo de nomes no recurso correspondente ao Gestor de Recursos. 

Existem propriedades geradas internamente que pode usar para identificar processos e computadores únicos:

- Computador: Utilize *o ResourceId* ou *ResourceName_s* identificar um computador de forma única dentro de um espaço de trabalho do Log Analytics.
- Processo: Use *o ResourceId* para identificar de forma única um processo dentro de um espaço de trabalho Log Analytics. *ResourceName_s* é único no contexto da máquina em que o processo está em curso (MachineResourceName_s) 

Como podem existir vários registos para um processo especificado e computador num intervalo de tempo especificado, as consultas podem devolver mais do que um registo para o mesmo computador ou processo. Para incluir apenas o registo mais recente, adicione `| summarize arg_max(TimeGenerated, *) by ResourceId` à consulta.

### <a name="connections-and-ports"></a>Ligações e portas

A funcionalidade Connection Metrics introduz duas novas tabelas nos registos do Monitor Azure - VMConnection e VMBoundPort. Estas tabelas fornecem informações sobre as ligações para uma máquina (entrada e saída), bem como as portas do servidor que estão abertas/ativas sobre elas. Conexões Osmétricos também são expostos através de APIs que fornecem os meios para obter uma métrica específica durante uma janela de tempo. As ligações TCP resultantes da *aceitação* numa tomada de audição são de entrada, enquanto as criadas *através da ligação* a um determinado IP e à porta são de saída. A direção de uma ligação é representada pela propriedade Direction, que pode ser definida para **entrada** ou **saída**. 

Os registos nestas tabelas são gerados a partir de dados reportados pelo Agente de Dependência. Cada registo representa uma observação durante um intervalo de 1 minuto. A propriedade TimeGenerated indica o início do intervalo de tempo. Cada registo contém informações para identificar a respetiva entidade, isto é, ligação ou porta, bem como métricas associadas a essa entidade. Atualmente, apenas é reportada a atividade da rede que ocorre utilizando tCP sobre iPv4. 

#### <a name="common-fields-and-conventions"></a>Campos e convenções comuns 

Os seguintes campos e convenções aplicam-se tanto à VMConnection como à VMBoundPort: 

- Computador: Nome de domínio totalmente qualificado da máquina de reporte 
- AgentId: O identificador único para uma máquina com o agente Log Analytics  
- Máquina: Nome do recurso Azure Resource Manager para a máquina exposta pelo ServiceMap. É do formulário *m-{GUID}*, onde *GUID* é o mesmo GUID que o AgentId  
- Processo: Nome do recurso Azure Resource Manager para o processo exposto pelo ServiceMap. É da forma *p-{hex string}*. O processo é único dentro de um âmbito de máquina e para gerar um ID de processo único entre máquinas, combinar campos de máquinas e processos. 
- ProcessoName: Nome executável do processo de reporte.
- Todos os endereços IP são cordas em formato canónico IPv4, por exemplo *13.107.3.160* 

Para gerir custos e complexidade, os registos de ligação não representam ligações individuais de rede física. Múltiplas ligações de rede física são agrupadas numa ligação lógica, que é então refletida na respetiva tabela.  Ou seja, os registos na tabela *VMConnection* representam um agrupamento lógico e não as ligações físicas individuais que estão a ser observadas. A ligação física da rede que partilha o mesmo valor para os seguintes atributos durante um determinado intervalo de um minuto, são agregadas num único registo lógico em *VMConnection*. 

| Propriedade | Descrição |
|:--|:--|
|Direção |Direção da ligação, valor é *entrada* ou *saída* |
|Máquina |O computador FQDN |
|Processo |Identidade do processo ou grupos de processos, iniciando/aceitando a ligação |
|SourceIp |Endereço IP da fonte |
|DestinationIp |Endereço IP do destino |
|DestinationPort |Número do porto do destino |
|Protocolo |Protocolo usado para a ligação.  Valores é *tcp*. |

Para ter em conta o impacto do agrupamento, as informações sobre o número de ligações físicas agrupadas são fornecidas nas seguintes propriedades do registo:

| Propriedade | Descrição |
|:--|:--|
|LinksEstablished |O número de ligações de rede física que foram estabelecidas durante a janela de tempo de reporte |
|LinksTerminados |O número de ligações de rede física que foram encerradas durante a janela de tempo de reporte |
|LinksFailed |O número de ligações de rede física que falharam durante a janela de tempo de reporte. Atualmente, esta informação está disponível apenas para ligações de saída. |
|LinksLive |O número de ligações de rede física que foram abertas no final da janela de tempo de reporte|

#### <a name="metrics"></a>Métricas

Para além das métricas de contagem de ligação, as informações sobre o volume de dados enviados e recebidos numa determinada ligação lógica ou porta de rede também estão incluídas nas seguintes propriedades do registo:

| Propriedade | Descrição |
|:--|:--|
|BytesSent |Número total de bytes enviados durante a janela de tempo de reporte |
|BytesReceived |Número total de bytes que foram recebidos durante a janela de tempo de reporte |
|Respostas |O número de respostas observadas durante a janela de tempo de reporte. 
|ResponseTimeMax |O maior tempo de resposta (milissegundos) observado durante a janela de tempo de reporte. Se não tiver valor, a propriedade está em branco.|
|ResponseTimeMin |O menor tempo de resposta (milissegundos) observado durante a janela de tempo de reporte. Se não tiver valor, a propriedade está em branco.|
|Tempos de Resposta |A soma de todos os tempos de resposta (milissegundos) observados durante a janela de tempo de reporte. Se não tiver valor, a propriedade está em branco.|

O terceiro tipo de dados que estão a ser reportados é o tempo de resposta - quanto tempo é que um chamador gasta à espera de um pedido enviado através de uma ligação a ser processado e respondido pelo ponto final remoto. O tempo de resposta reportado é uma estimativa do verdadeiro tempo de resposta do protocolo de aplicação subjacente. É calculado utilizando heurística com base na observação do fluxo de dados entre a fonte e a extremidade de destino de uma ligação de rede física. Conceptualmente, é a diferença entre o último byte de um pedido deixa o remetente, e o momento em que o último byte da resposta volta a ele. Estes dois picos de tempo são usados para delinear eventos de pedido e resposta numa determinada ligação física. A diferença entre eles representa o tempo de resposta de um único pedido. 

Nesta primeira versão desta funcionalidade, o nosso algoritmo é uma aproximação que pode funcionar com um grau de sucesso variado, dependendo do protocolo de aplicação real usado para uma determinada ligação de rede. Por exemplo, a abordagem atual funciona bem para protocolos baseados em resposta a pedidos, como HTTP(S), mas não funciona com protocolos unidirecionais ou baseados em filas de mensagens.

Eis alguns pontos importantes a ter em conta:

1. Se um processo aceitar ligações no mesmo endereço IP, mas em várias interfaces de rede, será reportado um registo separado para cada interface. 
2. Os registos com o WILDCARD IP não contêm qualquer atividade. Estão incluídos para representar o facto de que um porto da máquina está aberto ao tráfego de entrada.
3. Para reduzir a verbosidade e o volume de dados, os registos com o WILDCARD IP serão omitidos quando houver um registo correspondente (para o mesmo processo, porta e protocolo) com um endereço IP específico. Quando um registo IP wildcard é omitido, a propriedade de gravação IsWildcardBind com o endereço IP específico, será definida como "True" para indicar que a porta está exposta em todas as interfaces da máquina de reporte.
4. As portas que estão ligadas apenas numa interface específica têm IsWildcardBind definido para *Falso*.

#### <a name="naming-and-classification"></a>Nomeação e Classificação

Por conveniência, o endereço IP da extremidade remota de uma ligação está incluído na propriedade RemoteIp. Para ligações de entrada, o RemoteIp é o mesmo que o SourceIp, enquanto para as ligações de saída, é o mesmo que o DestinationIp. A propriedade RemoteDnsCanonicalNames representa os nomes canónicos DNS relatados pela máquina para RemoteIp. As propriedades RemoteDnsQuestions e RemoteClassification estão reservadas para uso futuro. 

#### <a name="geolocation"></a>Geolocalização

*A VMConnection* também inclui informações de geolocalização para a extremidade remota de cada registo de ligação nas seguintes propriedades do registo: 

| Propriedade | Descrição |
|:--|:--|
|Remoto País |O nome do país/região que acolhe o RemoteIp.  Por exemplo, *Estados Unidos* |
|RemotaZatitude |A latitude de geolocalização. Por exemplo, *47.68* |
|Remota Longitude |A longitude da geolocalização. Por exemplo, *-122.12* |

#### <a name="malicious-ip"></a>IP malicioso

Todas as propriedades RemoteIp na tabela *VMConnection* são verificadas contra um conjunto de IPs com atividade maliciosa conhecida. Se o RemoteIp for identificado como malicioso, as seguintes propriedades serão povoadas (estão vazias, quando o IP não for considerado malicioso) nas seguintes propriedades do registo:

| Propriedade | Descrição |
|:--|:--|
|MaliciousIp |O endereço RemoteIp |
|IndicadorThreadType |O indicador de ameaça detetado é um dos seguintes valores, *Botnet*, *C2*, *CryptoMining,* *Darknet*, *DDos,* *MaliciousUrl,* *Malware*, *Phishing,* *Proxy,* *PUA,* *Watchlist*.   |
|Descrição |Descrição da ameaça observada. |
|TLPLevel |O nível do Protocolo de Semáforos (TLP) é um dos valores definidos, *Branco,* *Verde,* *Âmbar,* *Vermelho.* |
|Confiança |Os valores são *0 a 100.* |
|Gravidade |Os valores são *0 - 5,* onde *5* é o mais grave e *0* não é nada grave. O valor predefinido é *de 3*.  |
|FirstReportedDateTime |A primeira vez que o fornecedor reportou o indicador. |
|Última ReportagemDateTime |A última vez que o indicador foi visto pela Interflow. |
|IsActive |Indica que os indicadores são desativados com valor *verdadeiro* ou *falso.* |
|ReportReferenceLink |Ligações a relatórios relacionados com um dado observável. |
|Informação Adicional |Fornece informações adicionais, se aplicável, sobre a ameaça observada. |

### <a name="ports"></a>Portas 

As portas de uma máquina que aceite ativamente o tráfego de entrada ou que possam potencialmente aceitar o tráfego, mas que estão inativas durante a janela de tempo de reporte, são escritas para a tabela VMBoundPort.  

Todos os registos em VMBoundPort são identificados pelos seguintes campos: 

| Propriedade | Descrição |
|:--|:--|
|Processo | Identidade do processo (ou grupos de processos) com os quais a porta está associada.|
|Ip | Endereço IP portuário (pode ser wildcard IP, *0.0.0.0*) |
|Porta |O número do porto |
|Protocolo | O protocolo.  Exemplo, *tcp* ou *UDP* (apenas *a TCP* está atualmente suportada).|
 
A identidade de uma porta é derivada dos cinco campos acima e está armazenada na propriedade PortId. Esta propriedade pode ser usada para encontrar rapidamente registos para uma porta específica através do tempo. 

#### <a name="metrics"></a>Métricas 

Os registos portuários incluem métricas que representam as ligações associadas a eles. Atualmente, são reportadas as seguintes métricas (os detalhes de cada métrica são descritos na secção anterior): 

- BytesSent e BytesReceived 
- LinksEstablished, LinksTerminated, LinksLive 
- ResposeTime, ResponseTimeMin, ResponseTimeMax, ResponseTimeSum 

Eis alguns pontos importantes a ter em conta:

- Se um processo aceitar ligações no mesmo endereço IP, mas em várias interfaces de rede, será reportado um registo separado para cada interface.  
- Os registos com o WILDCARD IP não contêm qualquer atividade. Estão incluídos para representar o facto de que um porto da máquina está aberto ao tráfego de entrada. 
- Para reduzir a verbosidade e o volume de dados, os registos com o WILDCARD IP serão omitidos quando houver um registo correspondente (para o mesmo processo, porta e protocolo) com um endereço IP específico. Quando um registo IP wildcard é omitido, a propriedade *IsWildcardBind* para o registo com o endereço IP específico, será definida como *True*.  Isto indica que a porta está exposta em todas as interfaces da máquina de comunicação. 
- As portas que estão ligadas apenas numa interface específica têm IsWildcardBind definido para *Falso*. 

### <a name="vmcomputer-records"></a>VMComputer registros

Os registos com um tipo de *VMComputer* têm dados de inventário para servidores com o agente Dependency. Estes registos têm as propriedades na tabela seguinte:

| Propriedade | Descrição |
|:--|:--|
|TenantId | O identificador único para o espaço de trabalho |
|SourceSystem | *Informações* | 
|TimeGenerated | Calendário do registo (UTC) |
|Computador | O computador FQDN | 
|AgentId | O ID único do agente Log Analytics |
|Máquina | Nome do recurso Azure Resource Manager para a máquina exposta pelo ServiceMap. É do formulário *m-{GUID}*, onde *guid* é o mesmo GUID que o AgentId. | 
|DisplayName | Nome a apresentar | 
|FullDisplayName | Nome de exibição completo | 
|Nome do Anfitrião | O nome da máquina sem nome de domínio |
|Horário de arranque | O tempo de arranque da máquina (UTC) |
|TimeZone | O fuso horário normalizado |
|Estado de Virtualização | *virtual,* *hipervisor,* *físico* |
|Ipv4Addresses | Matriz de endereços IPv4 | 
|Ipv4SubnetMasks | Matriz de máscaras de sub-rede IPv4 (na mesma ordem que Ipv4Addresses). |
|Ipv4DefaultGateways | Matriz de gateways IPv4 | 
|Ipv6Addresses | Matriz de endereços IPv6 | 
|MacAddresses | Matriz de endereços MAC | 
|DnsNames | Conjunto de nomes DNS associados à máquina. |
|DependênciaAgentVer | A versão do agente de dependência a funcionar na máquina. | 
|FuncionamentoSystemFamily | *Linux,* *Janelas* |
|Nome operativo Do Sistema Operativo | O nome completo do sistema operativo | 
|PhysicalMemoryMB | A memória física em megabytes | 
|Cpus | O número de processadores | 
|CpuSpeed | A velocidade do CPU em MHz | 
|VirtualMachineType | *hiperv,* *vmware,* *xen* |
|VirtualMachinenativeId | O VM ID atribuído pelo seu hipervisor | 
|Nome virtual denacinanative | O nome do VM |
|VirtualMachineHypervisorId | O identificador único do hipervisor que hospeda o VM |
|HipervisorType | *hiperv* |
|HipervisorId | O ID único do hipervisor | 
|HospedagemProvider | *azul* |
|_ResourceId | O identificador único para um recurso Azure |
|AzureSubscriptionId | Um identificador globalmente único que identifica a sua subscrição | 
|Grupo AzureResource | O nome do grupo de recursos Azure da máquina é membro. |
|Nome AzureResource | O nome do recurso Azure |
|AzureLocation | A localização do recurso Azure |
|AzureUpdateDomain | O nome do domínio de atualização Azure |
|AzureFaultDomain | O nome do domínio da falha de Azure |
|AzureVmId | O identificador único da máquina virtual Azure |
|Azuresize | O tamanho do Azure VM |
|AzureImagePublisher | O nome da editora Azure VM |
|AzureImageOffering | O nome do tipo de oferta Azure VM | 
|AzureImageSku | O SKU da imagem Azure VM | 
|AzureImageVersion | A versão da imagem Azure VM | 
|Nome AzureCloudService | O nome do serviço de nuvem Azure |
|AzureCloudServiceDeployment | ID de implementação para o Serviço cloud |
|Nome AzureCloudServiceRole | Nome da função do Serviço de Nuvem |
|AzureCloudServiceRoleType | Tipo de função de Serviço de Nuvem: *trabalhador* ou *web* |
|AzureCloudServiceInstanceId | ID de instância de função de serviço de nuvem |
|Nome AzureVmScaleSetName | O nome do conjunto de escala de máquina virtual |
|AzureVmScaleSetDeployment | ID de conjunto de escala de máquina virtual |
|AzureVmScaleSetResourceId | O identificador único do recurso de conjunto de escala de máquina virtual.|
|AzureVmScaleSetInstanceId | O identificador único do conjunto de escala de máquina virtual |
|AzureServiceFabricClusterId | O identificador único do cluster de tecido de serviço Azure | 
|AzureServiceFabricClusterName | O nome do cluster de tecido de serviço Azure |

### <a name="vmprocess-records"></a>Registos VMProcess

Os registos com um tipo de *VMProcess* têm dados de inventário para processos ligados à TCP em servidores com o agente Dependency. Estes registos têm as propriedades na tabela seguinte:

| Propriedade | Descrição |
|:--|:--|
|TenantId | O identificador único para o espaço de trabalho |
|SourceSystem | *Informações* | 
|TimeGenerated | Calendário do registo (UTC) |
|Computador | O computador FQDN | 
|AgentId | O ID único do agente Log Analytics |
|Máquina | Nome do recurso Azure Resource Manager para a máquina exposta pelo ServiceMap. É do formulário *m-{GUID}*, onde *guid* é o mesmo GUID que o AgentId. | 
|Processo | O identificador único do processo do Mapa de Serviço. É na forma de *p-{GUID}*. 
|Nome executável | O nome do processo executável | 
|DisplayName | Nome de exibição de processo |
|Função | Função de processo: *webserver,* *appServer,* *databaseServer,* *ldapServer,* *smbServer* |
|Grupo | Processar nome de grupo. Os processos no mesmo grupo estão logicamente relacionados, por exemplo, parte do mesmo produto ou componente do sistema. |
|StartTime | A hora de início da piscina do processo |
|FirstPid | O primeiro PID na piscina de processos |
|Descrição | A descrição do processo |
|CompanyName | O nome da empresa |
|Nome interno | O nome interno |
|NomeDoProduto | O nome do produto |
|ProductVersion | A versão do produto |
|Versão de Ficheiros | A versão do ficheiro |
|Caminho executável |O caminho do executável |
|Linha de Comando | A linha de comando |
|WorkingDirectory | O diretório de trabalho |
|Serviços | Uma série de serviços em que o processo está a executar |
|Nome de Utilizador | A conta em que o processo está a executar |
|UserDomain | O domínio sob o qual o processo está a executar |
|_ResourceId | O identificador único para um processo dentro do espaço de trabalho |


## <a name="sample-map-queries"></a>Consultas de mapa de amostras

### <a name="list-all-known-machines"></a>Listar todas as máquinas conhecidas

```kusto
VMComputer | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="when-was-the-vm-last-rebooted"></a>Quando foi o VM pela última vez reiniciado

```kusto
let Today = now(); VMComputer | extend DaysSinceBoot = Today - BootTime | summarize by Computer, DaysSinceBoot, BootTime | sort by BootTime asc
```

### <a name="summary-of-azure-vms-by-image-location-and-sku"></a>Resumo dos VMs Azure por imagem, localização e SKU

```kusto
VMComputer | where AzureLocation != "" | summarize by Computer, AzureImageOffering, AzureLocation, AzureImageSku
```

### <a name="list-the-physical-memory-capacity-of-all-managed-computers"></a>Listar a capacidade de memória física de todos os computadores geridos

```kusto
VMComputer | summarize arg_max(TimeGenerated, *) by _ResourceId | project PhysicalMemoryMB, Computer
```

### <a name="list-computer-name-dns-ip-and-os"></a>Lista de nomes de computador, DNS, IP e OS

```kusto
VMComputer | summarize arg_max(TimeGenerated, *) by _ResourceId | project Computer, OperatingSystemFullName, DnsNames, Ipv4Addresses
```

### <a name="find-all-processes-with-sql-in-the-command-line"></a>Encontre todos os processos com "sql" na linha de comando

```kusto
VMProcess | where CommandLine contains_cs "sql" | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="find-a-machine-most-recent-record-by-resource-name"></a>Encontre uma máquina (registo mais recente) pelo nome de recurso

```kusto
search in (VMComputer) "m-4b9c93f9-bc37-46df-b43c-899ba829e07b" | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="find-a-machine-most-recent-record-by-ip-address"></a>Encontre uma máquina (registo mais recente) por endereço IP

```kusto
search in (VMComputer) "10.229.243.232" | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="list-all-known-processes-on-a-specified-machine"></a>Listar todos os processos conhecidos numa máquina especificada

```kusto
VMProcess | where Machine == "m-559dbcd8-3130-454d-8d1d-f624e57961bc" | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="list-all-computers-running-sql-server"></a>Listar todos os computadores que executam o SQL Server

```kusto
VMComputer | where AzureResourceName in ((search in (VMProcess) "*sql*" | distinct Machine)) | distinct Computer
```

### <a name="list-all-unique-product-versions-of-curl-in-my-datacenter"></a>Listar todas as versões únicas do produto do curl no meu datacenter

```kusto
VMProcess | where ExecutableName == "curl" | distinct ProductVersion
```

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>Criar um grupo de computador de todos os computadores que executam o CentOS

```kusto
VMComputer | where OperatingSystemFullName contains_cs "CentOS" | distinct Computer
```

### <a name="bytes-sent-and-received-trends"></a>Bytes enviados e recebidos tendências

```kusto
VMConnection | summarize sum(BytesSent), sum(BytesReceived) by bin(TimeGenerated,1hr), Computer | order by Computer desc | render timechart
```

### <a name="which-azure-vms-are-transmitting-the-most-bytes"></a>Quais VMs Azure estão transmitindo mais bytes

```kusto
VMConnection | join kind=fullouter(VMComputer) on $left.Computer == $right.Computer | summarize count(BytesSent) by Computer, AzureVMSize | sort by count_BytesSent desc
```

### <a name="link-status-trends"></a>Tendências de estado de ligação

```kusto
VMConnection | where TimeGenerated >= ago(24hr) | where Computer == "acme-demo" | summarize dcount(LinksEstablished), dcount(LinksLive), dcount(LinksFailed), dcount(LinksTerminated) by bin(TimeGenerated, 1h) | render timechart
```

### <a name="connection-failures-trend"></a>Tendência de falhas de ligação

```kusto
VMConnection | where Computer == "acme-demo" | extend bythehour = datetime_part("hour", TimeGenerated) | project bythehour, LinksFailed | summarize failCount = count() by bythehour | sort by bythehour asc | render timechart
```

### <a name="bound-ports"></a>Portos Encadernados

```kusto
VMBoundPort
| where TimeGenerated >= ago(24hr)
| where Computer == 'admdemo-appsvr'
| distinct Port, ProcessName
```

### <a name="number-of-open-ports-across-machines"></a>Número de portas abertas através de máquinas

```kusto
VMBoundPort
| where Ip != "127.0.0.1"
| summarize by Computer, Machine, Port, Protocol
| summarize OpenPorts=count() by Computer, Machine
| order by OpenPorts desc
```

### <a name="score-processes-in-your-workspace-by-the-number-of-ports-they-have-open"></a>Marque processos no seu espaço de trabalho pelo número de portas que têm aberto

```kusto
VMBoundPort
| where Ip != "127.0.0.1"
| summarize by ProcessName, Port, Protocol
| summarize OpenPorts=count() by ProcessName
| order by OpenPorts desc
```

### <a name="aggregate-behavior-for-each-port"></a>Comportamento agregado para cada porta

Esta consulta pode então ser usada para pontuar portos por atividade, por exemplo, portos com maior tráfego de entrada/saída, portos com a maioria das ligações
```kusto
// 
VMBoundPort
| where Ip != "127.0.0.1"
| summarize BytesSent=sum(BytesSent), BytesReceived=sum(BytesReceived), LinksEstablished=sum(LinksEstablished), LinksTerminated=sum(LinksTerminated), arg_max(TimeGenerated, LinksLive) by Machine, Computer, ProcessName, Ip, Port, IsWildcardBind
| project-away TimeGenerated
| order by Machine, Computer, Port, Ip, ProcessName
```

### <a name="summarize-the-outbound-connections-from-a-group-of-machines"></a>Resumir as ligações de saída de um grupo de máquinas

```kusto
// the machines of interest
let machines = datatable(m: string) ["m-82412a7a-6a32-45a9-a8d6-538354224a25"];
// map of ip to monitored machine in the environment
let ips=materialize(VMComputer
| summarize ips=makeset(todynamic(Ipv4Addresses)) by MonitoredMachine=AzureResourceName
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

## <a name="performance-records"></a>Registos de desempenho
Os registos com um tipo de *InsightsMetrics* têm dados de desempenho do sistema operativo convidado da máquina virtual. Estes registos têm as propriedades na tabela seguinte:


| Propriedade | Descrição |
|:--|:--|
|TenantId | Identificador único para o espaço de trabalho |
|SourceSystem | *Informações* | 
|TimeGenerated | Tempo de recolha do valor (UTC) |
|Computador | O computador FQDN | 
|Origem | *vm.azm.ms* |
|Espaço de Nomes | Categoria do contador de desempenho | 
|Nome | Nome do balcão de performance |
|Val | Valor recolhido | 
|Etiquetas | Detalhes relacionados sobre o registo. Consulte a tabela abaixo para obter etiquetas utilizadas com diferentes tipos de registo.  |
|AgentId | Identificador único para o agente de cada computador |
|Tipo | *InsightsMetrics* |
|_ResourceId_ | ID de recursos da máquina virtual |

Os contadores de desempenho atualmente recolhidos na tabela *InsightsMetrics* estão listados na tabela seguinte:

| Espaço de Nomes | Nome | Descrição | Unidade | Etiquetas |
|:---|:---|:---|:---|:---|
| Computador    | Heartbeat             | Batimento cardíaco do computador                        | | |
| Memória      | DisponívelMB           | Bytes disponíveis para memória                    | Megabytes      | memorySizeMB - Tamanho total da memória|
| Rede     | WriteBytesPerSecond   | Bytes de escrita de rede por segundo            | BytesPerSecond | NetworkDeviceId - ID do dispositivo<br>bytes - Bytes totais enviados |
| Rede     | ReadBytesPerSecond    | Rede Ler Bytes por segundo             | BytesPerSecond | networkDeviceId - Id do dispositivo<br>bytes - Total recebido bytes |
| Processador   | UtilizaçãoPercentagem | Percentagem de Utilização do Processador          | Percentagem        | totalCpus - CpUs totais |
| LógicaDisk | WritesPerSecond       | Disco lógico escreve por segundo            | CondePerSecond | mountId - ID do monte do dispositivo |
| LógicaDisk | WriteLatencyMs        | Disco lógico escrever Latência Miliscond    | MilliSeconds   | mountId - ID do monte do dispositivo |
| LógicaDisk | WriteBytesPerSecond   | Bytes de escrita de disco lógico por segundo       | BytesPerSecond | mountId - ID do monte do dispositivo |
| LógicaDisk | TransfersPerSecond    | Transferências lógicas de disco por segundo         | CondePerSecond | mountId - ID do monte do dispositivo |
| LógicaDisk | TransferLatencyMs     | Latência de transferência lógica de disco Millisecond | MilliSeconds   | mountId - ID do monte do dispositivo |
| LógicaDisk | ReadsPerSecond        | Leituras lógicas do disco por segundo             | CondePerSecond | mountId - ID do monte do dispositivo |
| LógicaDisk | ReadLatencyMs         | Disco lógico Ler Latência Milissegundo     | MilliSeconds   | mountId - ID do monte do dispositivo |
| LógicaDisk | ReadBytesPerSecond    | Discos lógicos ler bytes por segundo        | BytesPerSecond | mountId - ID do monte do dispositivo |
| LógicaDisk | FreeSpacePercentage   | Percentagem lógica de espaço livre de disco        | Percentagem        | mountId - ID do monte do dispositivo |
| LógicaDisk | FreeSpaceMB           | Bytes de espaço livre de disco lógico             | Megabytes      | mountId - ID do monte do dispositivo<br>diskSizeMB - Tamanho total do disco |
| LógicaDisk | BytesPerSecond        | Bytes de disco lógico por segundo             | BytesPerSecond | mountId - ID do monte do dispositivo |


## <a name="next-steps"></a>Próximos passos

* Se é novo a escrever consultas de registo no Azure Monitor, [reveja como utilizar](../log-query/get-started-portal.md) o Log Analytics no portal Azure para escrever consultas de registo.

* Saiba mais sobre [escrever consultas de pesquisa.](/azure/azure-monitor/log-query/get-started-queries)

