---
title: Exemplos de consulta de log de Azure Monitor | Microsoft Docs
description: Exemplos de consultas de log no Azure Monitor usando a linguagem de consulta Kusto.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/01/2019
ms.openlocfilehash: a5a19910d101f3f30afcafa049056c78cd976f75
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72933068"
---
# <a name="azure-monitor-log-query-examples"></a>Exemplos de consulta de log de Azure Monitor
Este artigo inclui vários exemplos de [consultas](log-query-overview.md) que usam a [linguagem de consulta Kusto](/azure/kusto/query/) para recuperar diferentes tipos de dados de log de Azure monitor. Métodos diferentes são usados para consolidar e analisar dados, para que você possa usar esses exemplos para identificar estratégias diferentes que podem ser usadas para seus próprios requisitos.  

Consulte a [referência de linguagem Kusto](https://docs.microsoft.com/azure/kusto/query/) para obter detalhes sobre as diferentes palavras-chave usadas nesses exemplos. Siga uma [lição sobre como criar consultas](get-started-queries.md) se você for novo no Azure monitor.

## <a name="events"></a>Eventos

### <a name="search-application-level-events-described-as-cryptographic"></a>Pesquisar eventos no nível do aplicativo descritos como "criptografia"
Este exemplo pesquisa a tabela de **eventos** em busca de registros nos quais **EventLog** é _Application_ e **RenderedDescription** contém _criptografia_. Inclui registros das últimas 24 horas.

```Kusto
Event
| where EventLog == "Application" 
| where TimeGenerated > ago(24h) 
| where RenderedDescription contains "cryptographic"
```

### <a name="search-events-related-to-unmarshaling"></a>Eventos de pesquisa relacionados ao desempacotamento
**Evento** de tabelas de pesquisa e **SecurityEvents** para registros que mencionam o _desempacotamento_.

```Kusto
search in (Event, SecurityEvent) "unmarshaling"
```

## <a name="heartbeat"></a>Batida

### <a name="chart-a-week-over-week-view-of-the-number-of-computers-sending-data"></a>Gráfico uma exibição semana a semana do número de computadores que enviam dados

O exemplo a seguir mostra o número de computadores distintos que enviaram pulsações, a cada semana.

```Kusto
Heartbeat
| where TimeGenerated >= startofweek(ago(21d))
| summarize dcount(Computer) by endofweek(TimeGenerated) | render barchart kind=default
```

### <a name="find-stale-computers"></a>Localizar computadores obsoletos

O exemplo a seguir localiza computadores que estavam ativos no último dia, mas que não enviaram pulsações na última hora.

```Kusto
Heartbeat
| where TimeGenerated > ago(1d)
| summarize LastHeartbeat = max(TimeGenerated) by Computer
| where isnotempty(Computer)
| where LastHeartbeat < ago(1h)
```

### <a name="get-the-latest-heartbeat-record-per-computer-ip"></a>Obter o registro de pulsação mais recente por IP do computador

Este exemplo retorna o último registro de pulsação para cada IP do computador.
```Kusto
Heartbeat
| summarize arg_max(TimeGenerated, *) by ComputerIP
```

### <a name="match-protected-status-records-with-heartbeat-records"></a>Corresponder registros de status protegidos com registros de pulsação

Este exemplo localiza registros de status de proteção relacionados e registros de pulsação, com correspondência no computador e no horário.
Observe que o campo de tempo é arredondado para o minuto mais próximo. Usamos o cálculo do compartimento de tempo de execução para fazer isso: `round_time=bin(TimeGenerated, 1m)`.

```Kusto
let protection_data = ProtectionStatus
    | project Computer, DetectionId, round_time=bin(TimeGenerated, 1m);
let heartbeat_data = Heartbeat
    | project Computer, Category, round_time=bin(TimeGenerated, 1m);
protection_data | join (heartbeat_data) on Computer, round_time
```

### <a name="server-availability-rate"></a>Taxa de disponibilidade do servidor

Calcule a taxa de disponibilidade do servidor com base nos registros de pulsação. A disponibilidade é definida como "pelo menos 1 pulsação por hora".
Portanto, se um servidor estava disponível 98 de 100 horas, a taxa de disponibilidade é 98%.

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

### <a name="chart-the-record-count-per-table"></a>Gráfico do registro-contagem por tabela
O exemplo a seguir coleta todos os registros de todas as tabelas das últimas cinco horas e conta o número de registros em cada tabela. Os resultados são mostrados em um gráfico de time.

```Kusto
union withsource=sourceTable *
| where TimeGenerated > ago(5h) 
| summarize count() by bin(TimeGenerated,10m), sourceTable
| render timechart
```

### <a name="count-all-logs-collected-over-the-last-hour-by-type"></a>Contar todos os logs coletados pela última hora por tipo
O exemplo a seguir pesquisa tudo relatado na última hora e conta os registros de cada tabela por **tipo**. Os resultados são exibidos em um gráfico de barras.

```Kusto
search *
| where TimeGenerated > ago(1h) 
| summarize CountOfRecords = count() by Type
| render barchart
```

## <a name="azurediagnostics"></a>AzureDiagnostics

### <a name="count-azure-diagnostics-records-per-category"></a>Contar registros de diagnóstico do Azure por categoria
Este exemplo conta todos os registros de diagnóstico do Azure para cada categoria exclusiva.

```Kusto
AzureDiagnostics 
| where TimeGenerated > ago(1d)
| summarize count() by Category
```

### <a name="get-a-random-record-for-each-unique-category"></a>Obter um registro aleatório para cada categoria exclusiva
Este exemplo obtém um único registro aleatório do diagnóstico do Azure para cada categoria exclusiva.

```Kusto
AzureDiagnostics
| where TimeGenerated > ago(1d) 
| summarize any(*) by Category
```

### <a name="get-the-latest-record-per-category"></a>Obter o registro mais recente por categoria
Este exemplo obtém o registro mais recente do diagnóstico do Azure em cada categoria exclusiva.

```Kusto
AzureDiagnostics
| where TimeGenerated > ago(1d) 
| summarize arg_max(TimeGenerated, *) by Category
```

## <a name="network-monitoring"></a>Monitorização de rede

### <a name="computers-with-unhealthy-latency"></a>Computadores com latência não íntegra
Este exemplo cria uma lista de computadores distintos com latência não íntegra.

```Kusto
NetworkMonitoring 
| where LatencyHealthState <> "Healthy" 
| where Computer != "" 
| distinct Computer
```

## <a name="performance"></a>Desempenho

### <a name="join-computer-perf-records-to-correlate-memory-and-cpu"></a>Ingresse os registros de desempenho do computador para correlacionar a memória e a CPU
Este exemplo correlaciona os registros de **desempenho** de um computador específico e cria dois gráficos de tempo, a média de CPU e máximo de memória.

```Kusto
let StartTime = now()-5d;
let EndTime = now()-4d;
Perf
| where CounterName == "% Processor Time"  
| where TimeGenerated > StartTime and TimeGenerated < EndTime
and TimeGenerated < EndTime
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

### <a name="perf-cpu-utilization-graph-per-computer"></a>Grafo de utilização de CPU de desempenho por computador
Este exemplo calcula e gráficos a utilização da CPU de computadores que começam com _contoso_.

```Kusto
Perf
| where TimeGenerated > ago(4h)
| where Computer startswith "Contoso" 
| where CounterName == @"% Processor Time"
| summarize avg(CounterValue) by Computer, bin(TimeGenerated, 15m) 
| render timechart
```

## <a name="protection-status"></a>Status de proteção

### <a name="computers-with-non-reporting-protection-status-duration"></a>Computadores com duração de status de proteção sem relatórios
Este exemplo lista os computadores que tiveram um status de proteção de _não relatório_ e a duração em que estavam nesse status.

```Kusto
ProtectionStatus
| where ProtectionStatus == "Not Reporting"
| summarize count(), startNotReporting = min(TimeGenerated), endNotReporting = max(TimeGenerated) by Computer, ProtectionStatusDetails
| join ProtectionStatus on Computer
| summarize lastReporting = max(TimeGenerated), startNotReporting = any(startNotReporting), endNotReporting = any(endNotReporting) by Computer
| extend durationNotReporting = endNotReporting - startNotReporting
```

### <a name="match-protected-status-records-with-heartbeat-records"></a>Corresponder registros de status protegidos com registros de pulsação
Este exemplo localiza registros de status de proteção relacionados e registros de pulsação correspondentes no computador e no horário.
O campo de tempo é arredondado para o minuto mais próximo usando **bin**.

```Kusto
let protection_data = ProtectionStatus
    | project Computer, DetectionId, round_time=bin(TimeGenerated, 1m);
let heartbeat_data = Heartbeat
    | project Computer, Category, round_time=bin(TimeGenerated, 1m);
protection_data | join (heartbeat_data) on Computer, round_time
```


## <a name="security-records"></a>Registros de segurança

### <a name="count-security-events-by-activity-id"></a>Contar eventos de segurança por ID de atividade


Este exemplo se baseia na estrutura fixa da coluna **atividade** : \<ID\>-nome\<\>.
Ele analisa o valor da **atividade** em duas novas colunas e conta a ocorrência de cada **ActivityId**.

```Kusto
SecurityEvent
| where TimeGenerated > ago(30m) 
| project Activity 
| parse Activity with activityID " - " activityDesc
| summarize count() by activityID
```

### <a name="count-security-events-related-to-permissions"></a>Contar eventos de segurança relacionados a permissões
Este exemplo mostra o número de registros **securityEvent** , no qual a coluna **atividade** contém as _permissões_de termo inteiro. A consulta se aplica aos registros criados nos últimos 30 minutos.

```Kusto
SecurityEvent
| where TimeGenerated > ago(30m)
| summarize EventCount = countif(Activity has "Permissions")
```

### <a name="find-accounts-that-failed-to-log-in-from-computers-with-a-security-detection"></a>Localizar contas que falharam ao fazer logon de computadores com uma detecção de segurança
Este exemplo localiza e conta contas que falharam ao fazer logon de computadores nos quais identificamos uma detecção de segurança.

```Kusto
let detections = toscalar(SecurityDetection
| summarize makeset(Computer));
SecurityEvent
| where Computer in (detections) and EventID == 4624
| summarize count() by Account
```

### <a name="is-my-security-data-available"></a>Meus dados de segurança estão disponíveis?
Iniciar a exploração de dados geralmente começa com a verificação de disponibilidade de dados. Este exemplo mostra o número de registros **SecurityEvent** nos últimos 30 minutos.

```Kusto
SecurityEvent 
| where TimeGenerated  > ago(30m) 
| count
```

### <a name="parse-activity-name-and-id"></a>Nome e ID da atividade de análise
Os dois exemplos abaixo dependem da estrutura fixa da coluna **atividade** : \<ID\>-nome\<\>. O primeiro exemplo usa o operador **Parse** para atribuir valores a duas novas colunas: **ActivityId** e **activityDesc**.

```Kusto
SecurityEvent
| take 100
| project Activity 
| parse Activity with activityID " - " activityDesc
```

Este exemplo usa o operador **Split** para criar uma matriz de valores separados
```Kusto
SecurityEvent
| take 100
| project Activity 
| extend activityArr=split(Activity, " - ") 
| project Activity , activityArr, activityId=activityArr[0]
```

### <a name="explicit-credentials-processes"></a>Processos de credenciais explícitas
O exemplo a seguir mostra um gráfico de pizza de processos que usaram credenciais explícitas na última semana

```Kusto
SecurityEvent
| where TimeGenerated > ago(7d)
// filter by id 4648 ("A logon was attempted using explicit credentials")
| where EventID == 4648
| summarize count() by Process
| render piechart 
```

### <a name="top-running-processes"></a>Principais processos em execução

O exemplo a seguir mostra uma linha de tempo de atividade para os cinco processos mais comuns, nos últimos três dias.

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


### <a name="find-repeating-failed-login-attempts-by-the-same-account-from-different-ips"></a>Localizar tentativas de logon com falha repetidas pela mesma conta de IPs diferentes

O exemplo a seguir localiza tentativas de logon com falha pela mesma conta de mais de cinco IPs diferentes nas últimas seis horas.

```Kusto
SecurityEvent 
| where AccountType == "User" and EventID == 4625 and TimeGenerated > ago(6h) 
| summarize IPCount = dcount(IpAddress), makeset(IpAddress)  by Account
| where IPCount > 5
| sort by IPCount desc
```

### <a name="find-user-accounts-that-failed-to-log-in"></a>Localizar contas de usuário que falharam ao fazer logon 
O exemplo a seguir identifica as contas de usuário que falharam ao fazer logon mais de cinco vezes no último dia e a última tentativa de fazer logon.

```Kusto
let timeframe = 1d;
SecurityEvent
| where TimeGenerated > ago(1d)
| where AccountType == 'User' and EventID == 4625 // 4625 - failed log in
| summarize failed_login_attempts=count(), latest_failed_login=arg_max(TimeGenerated, Account) by Account 
| where failed_login_attempts > 5
| project-away Account1
```

Usando **Join**e **Let** de instruções, podemos verificar se as mesmas contas suspeitas foram mais tarde capazes de fazer logon com êxito.

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

### <a name="calculate-the-average-size-of-perf-usage-reports-per-computer"></a>Calcular o tamanho médio dos relatórios de uso de desempenho por computador

Este exemplo calcula o tamanho médio dos relatórios de uso de desempenho por computador, nas últimas 3 horas.
Os resultados são mostrados em um gráfico de barras.
```Kusto
Usage 
| where TimeGenerated > ago(3h)
| where DataType == "Perf" 
| where QuantityUnit == "MBytes" 
| summarize avg(Quantity) by Computer
| sort by avg_Quantity desc nulls last
| render barchart
```

### <a name="timechart-latency-percentiles-50-and-95"></a>Latência do gráfico de timepercentils 50 e 95

Este exemplo calcula e gráficos os 50 º e 95 º percentils de **avgLatency** relatados por hora nas últimas 24 horas.

```Kusto
Usage
| where TimeGenerated > ago(24h)
| summarize percentiles(AvgLatencyInSeconds, 50, 95) by bin(TimeGenerated, 1h) 
| render timechart
```

### <a name="usage-of-specific-computers-today"></a>Uso de computadores específicos hoje
Este exemplo recupera dados de **uso** do último dia para nomes de computador que contêm a cadeia de caracteres _contosofile_. Os resultados são classificados por **TimeGenerated**.

```Kusto
Usage
| where TimeGenerated > ago(1d)
| where  Computer contains "ContosoFile" 
| sort by TimeGenerated desc nulls last
```

## <a name="updates"></a>Atualizações

### <a name="computers-still-missing-updates"></a>Os computadores ainda estão com atualizações ausentes
Este exemplo mostra uma lista de computadores que estavam sem uma ou mais atualizações críticas há alguns dias e ainda estão com atualizações ausentes.

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

- Consulte a [referência de linguagem Kusto](/azure/kusto/query) para obter detalhes sobre o idioma.
- Percorra uma [lição sobre como escrever consultas de log em Azure monitor](get-started-queries.md).
