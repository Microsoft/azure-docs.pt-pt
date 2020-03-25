---
title: Exemplos de consulta de log Do Monitor Azure / Microsoft Docs
description: Exemplos de consultas de log no Monitor Azure utilizando a linguagem de consulta Kusto.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/01/2019
ms.openlocfilehash: 9bfadf55e4f68bb7188b27e4ef5bc03e3955f375
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77662053"
---
# <a name="azure-monitor-log-query-examples"></a>Exemplos de consulta de log Do Monitor Azure
Este artigo inclui vários exemplos de [consultas](log-query-overview.md) usando a [linguagem de consulta Kusto](/azure/kusto/query/) para recuperar diferentes tipos de dados de registo do Azure Monitor. Diferentes métodos são usados para consolidar e analisar dados, para que você possa usar estas amostras para identificar diferentes estratégias que você pode usar para os seus próprios requisitos.  

Consulte a [referência linguística kusto](https://docs.microsoft.com/azure/kusto/query/) para obter detalhes sobre as diferentes palavras-chave utilizadas nestas amostras. Passe por uma [lição sobre a criação](get-started-queries.md) de consultas se for novo no Monitor Azure.

## <a name="events"></a>Eventos

### <a name="search-application-level-events-described-as-cryptographic"></a>Eventos de nível de aplicação de pesquisa descritos como "Criptográficos"
Este exemplo procura na tabela **Eventos** registos em que **o EventLog** é _Application_ e **renderedDescription** contém _criptográfico_. Inclui registos das últimas 24 horas.

```Kusto
Event
| where EventLog == "Application" 
| where TimeGenerated > ago(24h) 
| where RenderedDescription contains "cryptographic"
```

### <a name="search-events-related-to-unmarshaling"></a>Eventos de pesquisa relacionados com unmarshaling
Tabelas de pesquisa **Eventos** e **Eventos** de Segurança para registos que _mencionem unmarshaling_.

```Kusto
search in (Event, SecurityEvent) "unmarshaling"
```

## <a name="heartbeat"></a>Heartbeat

### <a name="chart-a-week-over-week-view-of-the-number-of-computers-sending-data"></a>Cartografe uma visão semanal do número de computadores que enviam dados

O exemplo seguinte traça o número de computadores distintos que enviavam batimentos cardíacos, todas as semanas.

```Kusto
Heartbeat
| where TimeGenerated >= startofweek(ago(21d))
| summarize dcount(Computer) by endofweek(TimeGenerated) | render barchart kind=default
```

### <a name="find-stale-computers"></a>Encontre computadores velhos

O exemplo seguinte encontra computadores que estavam ativos no último dia, mas que não enviaram batimentos cardíacos na última hora.

```Kusto
Heartbeat
| where TimeGenerated > ago(1d)
| summarize LastHeartbeat = max(TimeGenerated) by Computer
| where isnotempty(Computer)
| where LastHeartbeat < ago(1h)
```

### <a name="get-the-latest-heartbeat-record-per-computer-ip"></a>Obtenha o mais recente recorde de batimentos cardíacos por IP do computador

Este exemplo devolve o último registo de batimentos cardíacos para cada IP do computador.
```Kusto
Heartbeat
| summarize arg_max(TimeGenerated, *) by ComputerIP
```

### <a name="match-protected-status-records-with-heartbeat-records"></a>Combine registos de estado protegidos com registos de batimentos cardíacos

Este exemplo encontra registos de estado de proteção relacionados e registos de batimentos cardíacos, combinados tanto no Computador como no tempo.
Note que o campo de tempo é arredondado para o minuto mais próximo. Usámos o cálculo do caixote do tempo de execução para o fazer: `round_time=bin(TimeGenerated, 1m)`.

```Kusto
let protection_data = ProtectionStatus
    | project Computer, DetectionId, round_time=bin(TimeGenerated, 1m);
let heartbeat_data = Heartbeat
    | project Computer, Category, round_time=bin(TimeGenerated, 1m);
protection_data | join (heartbeat_data) on Computer, round_time
```

### <a name="server-availability-rate"></a>Taxa de disponibilidade do servidor

Calcular a taxa de disponibilidade do servidor com base em registos de batimentos cardíacos. A disponibilidade é definida como "pelo menos 1 batimento cardíaco por hora".
Então, se um servidor estava disponível 98 de 100 horas, a taxa de disponibilidade é de 98%.

```Kusto
let start_time=startofday(datetime("2018-03-01"));
let end_time=now();
Heartbeat
| where TimeGenerated > start_time and TimeGenerated < end_time
| summarize heartbeat_per_hour=count() by bin_at(TimeGenerated, 1h, start_time), Computer
| extend available_per_hour=iff(heartbeat_per_hour>0, true, false)
| summarize total_available_hours=countif(available_per_hour==true) by Computer 
| extend total_number_of_buckets=round((end_time-start_time)/1h)+1
| extend availability_rate=total_available_hours*100/total_number_of_buckets
```


## <a name="multiple-data-types"></a>Vários tipos de dados

### <a name="chart-the-record-count-per-table"></a>Cartografe a contagem de recordes por tabela
O exemplo seguinte recolhe todos os registos de todas as tabelas das últimas cinco horas e conta quantos registos estavam em cada mesa. Os resultados são mostrados num gráfico de tempo.

```Kusto
union withsource=sourceTable *
| where TimeGenerated > ago(5h) 
| summarize count() by bin(TimeGenerated,10m), sourceTable
| render timechart
```

### <a name="count-all-logs-collected-over-the-last-hour-by-type"></a>Conte todos os registos recolhidos durante a última hora por tipo
O exemplo seguinte pesquisa tudo o que foi relatado na última hora e conta os registos de cada tabela por **Tipo**. Os resultados são apresentados num gráfico de barras.

```Kusto
search *
| where TimeGenerated > ago(1h) 
| summarize CountOfRecords = count() by Type
| render barchart
```

## <a name="azurediagnostics"></a>AzureDiagnostics

### <a name="count-azure-diagnostics-records-per-category"></a>Registos de diagnóstico sinuoso do Conde Azure por categoria
Este exemplo conta todos os registos de diagnóstico do Azure para cada categoria única.

```Kusto
AzureDiagnostics 
| where TimeGenerated > ago(1d)
| summarize count() by Category
```

### <a name="get-a-random-record-for-each-unique-category"></a>Obtenha um recorde aleatório para cada categoria única
Este exemplo obtém um único registo de diagnóstico do Azure para cada categoria única.

```Kusto
AzureDiagnostics
| where TimeGenerated > ago(1d) 
| summarize any(*) by Category
```

### <a name="get-the-latest-record-per-category"></a>Obtenha o mais recente recorde por categoria
Este exemplo obtém o mais recente registo de diagnósticos Azure em cada categoria única.

```Kusto
AzureDiagnostics
| where TimeGenerated > ago(1d) 
| summarize arg_max(TimeGenerated, *) by Category
```

## <a name="network-monitoring"></a>Monitorização de rede

### <a name="computers-with-unhealthy-latency"></a>Computadores com latência pouco saudável
Este exemplo cria uma lista de computadores distintos com latência pouco saudável.

```Kusto
NetworkMonitoring 
| where LatencyHealthState <> "Healthy" 
| where Computer != "" 
| distinct Computer
```

## <a name="performance"></a>Desempenho

### <a name="join-computer-perf-records-to-correlate-memory-and-cpu"></a>Junte-se aos registos de perf do computador para correlacionar a memória e cpU
Este exemplo correlaciona os registos **perf** de um computador específico e cria dois gráficos de tempo, o CPU médio e a memória máxima.

```Kusto
let StartTime = now()-5d;
let EndTime = now()-4d;
Perf
| where CounterName == "% Processor Time"  
| where TimeGenerated > StartTime and TimeGenerated < EndTime
| project TimeGenerated, Computer, cpu=CounterValue 
| join kind= inner (
   Perf
    | where CounterName == "% Used Memory"  
    | where TimeGenerated > StartTime and TimeGenerated < EndTime
    | project TimeGenerated , Computer, mem=CounterValue 
) on TimeGenerated, Computer
| summarize avgCpu=avg(cpu), maxMem=max(mem) by TimeGenerated bin=30m  
| render timechart
```

### <a name="perf-cpu-utilization-graph-per-computer"></a>Gráfico de utilização perf CPU por computador
Este exemplo calcula e traça a utilização do CPU de computadores que começam com _Contoso._

```Kusto
Perf
| where TimeGenerated > ago(4h)
| where Computer startswith "Contoso" 
| where CounterName == @"% Processor Time"
| summarize avg(CounterValue) by Computer, bin(TimeGenerated, 15m) 
| render timechart
```

## <a name="protection-status"></a>Estado de proteção

### <a name="computers-with-non-reporting-protection-status-duration"></a>Computadores com duração do estado de proteção não inquirida
Este exemplo lista computadores que tinham um estado de proteção de _Not Reporting_ e a duração em que estavam neste estado.

```Kusto
ProtectionStatus
| where ProtectionStatus == "Not Reporting"
| summarize count(), startNotReporting = min(TimeGenerated), endNotReporting = max(TimeGenerated) by Computer, ProtectionStatusDetails
| join ProtectionStatus on Computer
| summarize lastReporting = max(TimeGenerated), startNotReporting = any(startNotReporting), endNotReporting = any(endNotReporting) by Computer
| extend durationNotReporting = endNotReporting - startNotReporting
```

### <a name="match-protected-status-records-with-heartbeat-records"></a>Combine registos de estado protegidos com registos de batimentos cardíacos
Este exemplo encontra registos de estado de proteção relacionados e registos de batimentos cardíacos combinados tanto no Computador como no tempo.
O campo de tempo é arredondado para o minuto mais próximo usando **o caixote**do lixo .

```Kusto
let protection_data = ProtectionStatus
    | project Computer, DetectionId, round_time=bin(TimeGenerated, 1m);
let heartbeat_data = Heartbeat
    | project Computer, Category, round_time=bin(TimeGenerated, 1m);
protection_data | join (heartbeat_data) on Computer, round_time
```


## <a name="security-records"></a>Registos de segurança

### <a name="count-security-events-by-activity-id"></a>Contagem de eventos de segurança por ID de atividade


Este exemplo baseia-se na estrutura fixa da coluna **atividade:** \<id\>-\<Nome\>.
Analisa o valor da **Atividade** em duas novas colunas, e conta a ocorrência de cada **id de atividade**.

```Kusto
SecurityEvent
| where TimeGenerated > ago(30m) 
| project Activity 
| parse Activity with activityID " - " activityDesc
| summarize count() by activityID
```

### <a name="count-security-events-related-to-permissions"></a>Contar eventos de segurança relacionados com permissões
Este exemplo mostra o número de registos **de segurançaEvento,** em que a coluna **Atividade** contém todo o termo _Permissões_. A consulta aplica-se a registos criados nos últimos 30 minutos.

```Kusto
SecurityEvent
| where TimeGenerated > ago(30m)
| summarize EventCount = countif(Activity has "Permissions")
```

### <a name="find-accounts-that-failed-to-log-in-from-computers-with-a-security-detection"></a>Encontre contas que não conseguiram fazer login a partir de computadores com uma deteção de segurança
Este exemplo encontra e conta contas que não conseguiram entrar a partir de computadores nos quais identificamos uma deteção de segurança.

```Kusto
let detections = toscalar(SecurityDetection
| summarize makeset(Computer));
SecurityEvent
| where Computer in (detections) and EventID == 4624
| summarize count() by Account
```

### <a name="is-my-security-data-available"></a>Os meus dados de segurança estão disponíveis?
Iniciar a exploração de dados começa frequentemente com a verificação da disponibilidade de dados. Este exemplo mostra o número de registos do **SecurityEvent** nos últimos 30 minutos.

```Kusto
SecurityEvent 
| where TimeGenerated  > ago(30m) 
| count
```

### <a name="parse-activity-name-and-id"></a>Nome e ID da atividade parse
Os dois exemplos abaixo dependem da estrutura fixa da coluna **Atividade:** \<ID\>-\<Nome\>. O primeiro exemplo utiliza o operador **de parse** para atribuir valores a duas novas colunas: **activityID** e **activityDesc**.

```Kusto
SecurityEvent
| take 100
| project Activity 
| parse Activity with activityID " - " activityDesc
```

Este exemplo usa o operador **dividido** para criar uma variedade de valores separados
```Kusto
SecurityEvent
| take 100
| project Activity 
| extend activityArr=split(Activity, " - ") 
| project Activity , activityArr, activityId=activityArr[0]
```

### <a name="explicit-credentials-processes"></a>Processos de credenciais explícitas
O exemplo seguinte mostra um gráfico de tortas de processos que usaram credenciais explícitas na última semana

```Kusto
SecurityEvent
| where TimeGenerated > ago(7d)
// filter by id 4648 ("A logon was attempted using explicit credentials")
| where EventID == 4648
| summarize count() by Process
| render piechart 
```

### <a name="top-running-processes"></a>Processos de execução de topo

O exemplo seguinte mostra uma linha temporal de atividade para os cinco processos mais comuns, nos últimos três dias.

```Kusto
// Find all processes that started in the last three days. ID 4688: A new process has been created.
let RunProcesses = 
    SecurityEvent
    | where TimeGenerated > ago(3d)
    | where EventID == "4688";
// Find the 5 processes that were run the most
let Top5Processes =
RunProcesses
| summarize count() by Process
| top 5 by count_;
// Create a time chart of these 5 processes - hour by hour
RunProcesses 
| where Process in (Top5Processes) 
| summarize count() by bin (TimeGenerated, 1h), Process
| render timechart
```


### <a name="find-repeating-failed-login-attempts-by-the-same-account-from-different-ips"></a>Encontre tentativas de login falhadas repetidas pela mesma conta de diferentes IPs

O exemplo seguinte encontra tentativas falhadas de login pela mesma conta de mais de cinco IPs diferentes nas últimas seis horas.

```Kusto
SecurityEvent 
| where AccountType == "User" and EventID == 4625 and TimeGenerated > ago(6h) 
| summarize IPCount = dcount(IpAddress), makeset(IpAddress)  by Account
| where IPCount > 5
| sort by IPCount desc
```

### <a name="find-user-accounts-that-failed-to-log-in"></a>Encontre contas de utilizador que não conseguiram fazer login 
O exemplo seguinte identifica as contas dos utilizadores que não conseguiram fazer login mais de cinco vezes no último dia, e quando tentaram fazer o login pela última vez.

```Kusto
let timeframe = 1d;
SecurityEvent
| where TimeGenerated > ago(1d)
| where AccountType == 'User' and EventID == 4625 // 4625 - failed log in
| summarize failed_login_attempts=count(), latest_failed_login=arg_max(TimeGenerated, Account) by Account 
| where failed_login_attempts > 5
| project-away Account1
```

Usando **a adesão**, e **deixe** declarações podemos verificar se as mesmas contas suspeitas foram mais tarde capazes de fazer login com sucesso.

```Kusto
let timeframe = 1d;
let suspicious_users = 
    SecurityEvent
    | where TimeGenerated > ago(timeframe)
    | where AccountType == 'User' and EventID == 4625 // 4625 - failed login
    | summarize failed_login_attempts=count(), latest_failed_login=arg_max(TimeGenerated, Account) by Account 
    | where failed_login_attempts > 5
    | project-away Account1;
let suspicious_users_that_later_logged_in = 
    suspicious_users 
    | join kind=innerunique (
        SecurityEvent
        | where TimeGenerated > ago(timeframe)
        | where AccountType == 'User' and EventID == 4624 // 4624 - successful login,
        | summarize latest_successful_login=arg_max(TimeGenerated, Account) by Account
    ) on Account
    | extend was_login_after_failures = iif(latest_successful_login>latest_failed_login, 1, 0)
    | where was_login_after_failures == 1
;
suspicious_users_that_later_logged_in
```

## <a name="usage"></a>Utilização

### <a name="calculate-the-average-size-of-perf-usage-reports-per-computer"></a>Calcular o tamanho médio dos relatórios de utilização perf por computador

Este exemplo calcula o tamanho médio dos relatórios de utilização perf por computador, nas últimas 3 horas.
Os resultados são mostrados num gráfico de barras.
```Kusto
Usage 
| where TimeGenerated > ago(3h)
| where DataType == "Perf" 
| where QuantityUnit == "MBytes" 
| summarize avg(Quantity) by Computer
| sort by avg_Quantity desc nulls last
| render barchart
```

### <a name="timechart-latency-percentiles-50-and-95"></a>Percentilde de latência do timechart 50 e 95

Este exemplo calcula e regista os percentículos 50 e 95 de **avgLatency** reportados por hora nas últimas 24 horas.

```Kusto
Usage
| where TimeGenerated > ago(24h)
| summarize percentiles(AvgLatencyInSeconds, 50, 95) by bin(TimeGenerated, 1h) 
| render timechart
```

### <a name="usage-of-specific-computers-today"></a>Utilização de computadores específicos hoje em dia
Este exemplo recupera dados de **utilização** do último dia para nomes de computador que contêm a corda _ContosoFile_. Os resultados são classificados pela **TimeGenerated**.

```Kusto
Usage
| where TimeGenerated > ago(1d)
| where  Computer contains "ContosoFile" 
| sort by TimeGenerated desc nulls last
```

## <a name="updates"></a>Atualizações

### <a name="computers-still-missing-updates"></a>Computadores ainda faltam atualizações
Este exemplo mostra uma lista de computadores que faltavam uma ou mais atualizações críticas há alguns dias e que ainda faltam atualizações.

```Kusto
let ComputersMissingUpdates3DaysAgo = Update
| where TimeGenerated between (ago(30d)..ago(1h))
| where Classification !has "Critical" and UpdateState =~ "Needed"
| summarize makeset(Computer);
Update
| where TimeGenerated > ago(1d)
| where Classification has "Critical" and UpdateState =~ "Needed"
| where Computer in (ComputersMissingUpdates3DaysAgo)
| summarize UniqueUpdatesCount = dcount(Product) by Computer, OSType
```


## <a name="next-steps"></a>Passos seguintes

- Consulte a [referência linguística kusto](/azure/kusto/query) para obter detalhes sobre a língua.
- Passeie por uma lição sobre escrever consultas de [registo no Monitor Azure.](get-started-queries.md)
