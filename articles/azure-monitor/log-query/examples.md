---
title: Exemplos de consulta do log de Monitor do Azure | Documentos da Microsoft
description: Exemplos de consultas de registo no Azure Monitor utilizando a linguagem de consulta de Kusto.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/03/2018
ms.author: bwren
ms.openlocfilehash: 2c35bc4026c81cbc8b95225e688a3922bc320554
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60759916"
---
# <a name="azure-monitor-log-query-examples"></a>Exemplos de consulta de registo do Azure Monitor
Este artigo inclui vários exemplos de [consultas](log-query-overview.md) utilizando o [linguagem de consulta de Kusto](/azure/kusto/query/) para recuperar os diferentes tipos de dados de registo do Azure Monitor. Métodos diferentes são utilizados para consolidar e analisar os dados, pelo que pode utilizar estes exemplos para identificar estratégias diferentes que poderia usar em seus próprios requisitos.  

Consulte a [referência de linguagem de Kusto](https://docs.microsoft.com/azure/kusto/query/) para obter detalhes sobre as diferentes palavras-chave usadas nesses exemplos. Aceda através de um [lição sobre a criação de consultas](get-started-queries.md) se estiver familiarizado com o Azure Monitor.

## <a name="events"></a>Eventos

### <a name="search-application-level-events-described-as-cryptographic"></a>Pesquisar eventos de nível de aplicativo descritos como "Criptográficos utilizados"
Este exemplo pesquisa a tabela **Events** para encontrar registos em que **EventLog** é _Application_ e **RenderedDescription** contém _cryptographic_. Inclui registos das últimas 24 horas.

```Kusto
Event
| where EventLog == "Application" 
| where TimeGenerated > ago(24h) 
| where RenderedDescription == "cryptographic"
```

### <a name="search-events-related-to-unmarshaling"></a>Pesquisar eventos relacionados com unmarshaling
Procurar tabelas **evento** e **SecurityEvents** para regista essa menção _unmarshaling_.

```Kusto
search in (Event, SecurityEvent) "unmarshaling"
```

## <a name="heartbeat"></a>Heartbeat

### <a name="chart-a-week-over-week-view-of-the-number-of-computers-sending-data"></a>Uma vista de semana-over-semana do número de computadores a enviar dados de gráfico

O exemplo seguinte mostra graficamente o número de computadores distintos que enviam heartbeats, todas as semanas.

```Kusto
Heartbeat
| where TimeGenerated >= startofweek(ago(21d))
| summarize dcount(Computer) by endofweek(TimeGenerated) | render barchart kind=default
```

### <a name="find-stale-computers"></a>Localizar os computadores obsoletos

O exemplo a seguir encontra os computadores que estavam ativos no último dia, mas não enviar heartbeats na última hora.

```Kusto
Heartbeat
| where TimeGenerated > ago(1d)
| summarize LastHeartbeat = max(TimeGenerated) by Computer
| where isnotempty(Computer)
| where LastHeartbeat < ago(1h)
```

### <a name="get-the-latest-heartbeat-record-per-computer-ip"></a>Obter o registo mais recente do heartbeat por computador IP

Neste exemplo devolve o último registo de heartbeat para cada IP de computador.
```Kusto
Heartbeat
| summarize arg_max(TimeGenerated, *) by ComputerIP
```

### <a name="match-protected-status-records-with-heartbeat-records"></a>Corresponder os registos de estado protegido com registos de heartbeat

Neste exemplo localiza registos de estado de proteção relacionados e os registos de heartbeat, correspondidos no computador e a hora.
Tenha em atenção de que o campo de hora é arredondado para o minuto mais próximo. Usamos o cálculo de reciclagem de tempo de execução para fazer isso: `round_time=bin(TimeGenerated, 1m)`.

```Kusto
let protection_data = ProtectionStatus
    | project Computer, DetectionId, round_time=bin(TimeGenerated, 1m);
let heartbeat_data = Heartbeat
    | project Computer, Category, round_time=bin(TimeGenerated, 1m);
protection_data | join (heartbeat_data) on Computer, round_time
```

### <a name="server-availability-rate"></a>Tarifa de disponibilidade de servidor

Calcule a taxa de disponibilidade de servidor com base nos registos de heartbeat. Disponibilidade é definida como ", pelo menos, 1 heartbeat por hora".
Portanto, se um servidor estava disponível 98 entre 100 horas, a tarifa de disponibilidade é 98%.

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

### <a name="chart-the-record-count-per-table"></a>A contagem de registros por tabela de gráfico
O exemplo seguinte recolhe todos os registos de todas as tabelas das últimas cinco horas e conta o número de registos foram em cada tabela. Os resultados são mostrados na Pego.

```Kusto
union withsource=sourceTable *
| where TimeGenerated > ago(5h) 
| summarize count() by bin(TimeGenerated,10m), sourceTable
| render timechart
```

### <a name="count-all-logs-collected-over-the-last-hour-by-type"></a>Contagem de todos os registos recolhidos na última hora por tipo
O exemplo seguinte procura tudo relatados na última hora e conta os registos de cada tabela por **tipo**. Os resultados são exibidos num gráfico de barras.

```Kusto
search *
| where TimeGenerated > ago(1h) 
| summarize CountOfRecords = count() by Type
| render barchart
```

## <a name="azurediagnostics"></a>AzureDiagnostics

### <a name="count-azure-diagnostics-records-per-category"></a>Contagem de registos de diagnóstico do Azure por categoria
Neste exemplo conta todos os registos de diagnóstico do Azure para cada categoria exclusivo.

```Kusto
AzureDiagnostics 
| where TimeGenerated > ago(1d)
| summarize count() by Category
```

### <a name="get-a-random-record-for-each-unique-category"></a>Obter um registro aleatório para cada categoria exclusiva
Neste exemplo obtém um registo de diagnóstico do Azure de aleatório único para cada categoria exclusivo.

```Kusto
AzureDiagnostics
| where TimeGenerated > ago(1d) 
| summarize any(*) by Category
```

### <a name="get-the-latest-record-per-category"></a>Obter o registo mais recente por categoria
Neste exemplo obtém o registo de diagnóstico do Azure mais recente em cada categoria exclusiva.

```Kusto
AzureDiagnostics
| where TimeGenerated > ago(1d) 
| summarize arg_max(TimeGenerated, *) by Category
```

## <a name="network-monitoring"></a>Monitorização de rede

### <a name="computers-with-unhealthy-latency"></a>Computadores com uma latência de mau estado de funcionamento
Este exemplo cria uma lista de computadores distintos, com latência de mau estado de funcionamento.

```Kusto
NetworkMonitoring 
| where LatencyHealthState <> "Healthy" 
| where Computer != "" 
| distinct Computer
```

## <a name="performance"></a>Desempenho

### <a name="join-computer-perf-records-to-correlate-memory-and-cpu"></a>Junte-se a registos de desempenho do computador para correlacionar a memória e CPU
Este exemplo liga um determinado computador **perf** regista e cria dois gráficos de tempo, a CPU média e memória máxima.

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

### <a name="perf-cpu-utilization-graph-per-computer"></a>Gráfico de desempenho de utilização da CPU por computador
Neste exemplo calcula e gráficos de computadores que começam com a utilização de CPU _Contoso_.

```Kusto
Perf
| where TimeGenerated > ago(4h)
| where Computer startswith "Contoso" 
| where CounterName == @"% Processor Time"
| summarize avg(CounterValue) by Computer, bin(TimeGenerated, 15m) 
| render timechart
```

## <a name="protection-status"></a>Estado da proteção

### <a name="computers-with-non-reporting-protection-status-duration"></a>Computadores com a duração do Estado de proteção sem relatórios
Este exemplo apresenta uma lista dos computadores que tiveram o estado de proteção _Not Reporting_ e durante quanto tempo estiveram nesse estado.

```Kusto
ProtectionStatus
| where ProtectionStatus == "Not Reporting"
| summarize count(), startNotReporting = min(TimeGenerated), endNotReporting = max(TimeGenerated) by Computer, ProtectionStatusDetails
| join ProtectionStatus on Computer
| summarize lastReporting = max(TimeGenerated), startNotReporting = any(startNotReporting), endNotReporting = any(endNotReporting) by Computer
| extend durationNotReporting = endNotReporting - startNotReporting
```

### <a name="match-protected-status-records-with-heartbeat-records"></a>Corresponder os registos de estado protegido com registos de heartbeat
Neste exemplo localiza registos de estado de proteção relacionados e os registos heartbeat correspondidos no computador e a hora.
O campo de hora é arredondado para o com minuto mais próximo **bin**.

```Kusto
let protection_data = ProtectionStatus
    | project Computer, DetectionId, round_time=bin(TimeGenerated, 1m);
let heartbeat_data = Heartbeat
    | project Computer, Category, round_time=bin(TimeGenerated, 1m);
protection_data | join (heartbeat_data) on Computer, round_time
```


## <a name="security-records"></a>Registos de segurança

### <a name="count-security-events-by-activity-id"></a>Contagem de eventos de segurança por ID de atividade


Este exemplo baseia-se à estrutura fixa do **atividade** coluna: \<ID\>-\<nome\>.
Ele analisa o **atividade** valor em duas novas colunas e a conta a ocorrência de cada **activityID**.

```Kusto
SecurityEvent
| where TimeGenerated > ago(30m) 
| project Activity 
| parse Activity with activityID " - " activityDesc
| summarize count() by activityID
```

### <a name="count-security-events-related-to-permissions"></a>Contagem de eventos de segurança relacionados com as permissões
Este exemplo mostra o número de registos **securityEvent** em que a coluna **Activity** contém o termo _Permissions_ completo. A consulta aplica-se a registos criados nos últimos 30 minutos.

```Kusto
SecurityEvent
| where TimeGenerated > ago(30m)
| summarize EventCount = countif(Activity has "Permissions")
```

### <a name="find-accounts-that-failed-to-log-in-from-computers-with-a-security-detection"></a>Encontrar contas que não conseguiu iniciar sessão a partir de computadores com uma deteção de segurança
Neste exemplo localiza e contagens de contas que falha ao iniciar sessão a partir de computadores em que podemos identificar uma deteção de segurança.

```Kusto
let detections = toscalar(SecurityDetection
| summarize makeset(Computer));
SecurityEvent
| where Computer in (detections) and EventID == 4624
| summarize count() by Account
```

### <a name="is-my-security-data-available"></a>Meus dados de segurança está disponível?
A partir de dados de exploração, muitas vezes, começa com verificação de disponibilidade de dados. Este exemplo mostra o número de **SecurityEvent** registos nos últimos 30 minutos.

```Kusto
SecurityEvent 
| where TimeGenerated  > ago(30m) 
| count
```

### <a name="parse-activity-name-and-id"></a>Analisar ID e o nome da atividade
Os dois exemplos abaixo baseiam-se à estrutura fixa do **atividade** coluna: \<ID\>-\<nome\>. O primeiro exemplo utiliza a **analisar** operador para atribuir valores a duas novas colunas: **activityID** e **activityDesc**.

```Kusto
SecurityEvent
| take 100
| project Activity 
| parse Activity with activityID " - " activityDesc
```

Este exemplo utiliza a **dividir** operador para criar uma matriz de valores separados
```Kusto
SecurityEvent
| take 100
| project Activity 
| extend activityArr=split(Activity, " - ") 
| project Activity , activityArr, activityId=activityArr[0]
```

### <a name="explicit-credentials-processes"></a>Processos de credenciais explícitas
O exemplo seguinte mostra um gráfico de pizza de processos que utilizavam credenciais explícitas na última semana

```Kusto
SecurityEvent
| where TimeGenerated > ago(7d)
// filter by id 4648 ("A logon was attempted using explicit credentials")
| where EventID == 4648
| summarize count() by Process
| render piechart 
```

### <a name="top-running-processes"></a>Parte superior de processos em execução

O exemplo seguinte mostra uma linha de tempo de atividade para os cinco processos mais comuns, nos últimos três dias.

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


### <a name="find-repeating-failed-login-attempts-by-the-same-account-from-different-ips"></a>Encontre a repetição de tentativas de início de sessão, a mesma conta de diferentes IPs

O exemplo a seguir encontra tentativas de início de sessão, a mesma conta de mais de cinco diferentes IPs nas últimas seis horas.

```Kusto
SecurityEvent 
| where AccountType == "User" and EventID == 4625 and TimeGenerated > ago(6h) 
| summarize IPCount = dcount(IpAddress), makeset(IpAddress)  by Account
| where IPCount > 5
| sort by IPCount desc
```

### <a name="find-user-accounts-that-failed-to-log-in"></a>Localizar contas de utilizador que não conseguiu iniciar sessão 
O exemplo seguinte identifica as contas de utilizador que não conseguiu iniciar sessão mais do que cinco vezes no último dia, e quando eles última tentaram de iniciar sessão.

```Kusto
let timeframe = 1d;
SecurityEvent
| where TimeGenerated > ago(1d)
| where AccountType == 'User' and EventID == 4625 // 4625 - failed log in
| summarize failed_login_attempts=count(), latest_failed_login=arg_max(TimeGenerated, Account) by Account 
| where failed_login_attempts > 5
| project-away Account1
```

Usando **associação**, e **permitem** instruções podemos verificar se as mesmas contas suspeitas mais tarde tiveram a oportunidade de iniciar sessão com êxito.

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

### <a name="calculate-the-average-size-of-perf-usage-reports-per-computer"></a>Calcular o tamanho médio de relatórios de utilização de desempenho por computador

Neste exemplo calcula o tamanho médio de relatórios de utilização de desempenho por computador, durante as últimas 3 horas.
Os resultados são apresentados num gráfico de barras.
```Kusto
Usage 
| where TimeGenerated > ago(3h)
| where DataType == "Perf" 
| where QuantityUnit == "MBytes" 
| summarize avg(Quantity) by Computer
| sort by avg_Quantity desc nulls last
| render barchart
```

### <a name="timechart-latency-percentiles-50-and-95"></a>Pego percentis da latência 50 e 95

Neste exemplo calcula e percentis gráficos os percentis 50 º e 95 de comunicado **avgLatency** por hora nas últimas 24 horas.

```Kusto
Usage
| where TimeGenerated > ago(24h)
| summarize percentiles(AvgLatencyInSeconds, 50, 95) by bin(TimeGenerated, 1h) 
| render timechart
```

### <a name="usage-of-specific-computers-today"></a>Utilização de computadores específicos hoje mesmo
Este exemplo obtém **utilização** data do último dia para nomes de computador que contém a cadeia de caracteres _ContosoFile_. Os resultados são ordenados por **TimeGenerated**.

```Kusto
Usage
| where TimeGenerated > ago(1d)
| where  Computer contains "ContosoFile" 
| sort by TimeGenerated desc nulls last
```

## <a name="updates"></a>Atualizações

### <a name="computers-still-missing-updates"></a>Atualizações ainda falta de computadores
Este exemplo mostra uma lista de computadores que estavam em falta um ou mais atualizações críticas há alguns dias e ainda nos faltam atualizações.

```Kusto
let ComputersMissingUpdates3DaysAgo = Update
| where TimeGenerated between (ago(3d)..ago(2d))
| where  Classification == "Critical Updates" and UpdateState != "Not needed" and UpdateState != "NotNeeded"
| summarize makeset(Computer);

Update
| where TimeGenerated > ago(1d)
| where  Classification == "Critical Updates" and UpdateState != "Not needed" and UpdateState != "NotNeeded"
| where Computer in (ComputersMissingUpdates3DaysAgo)
| summarize UniqueUpdatesCount = dcount(Product) by Computer, OSType
```


## <a name="next-steps"></a>Passos Seguintes

- Consulte a [referência de linguagem de Kusto](/azure/kusto/query) para obter detalhes sobre a linguagem.
- Percorrer um [lição sobre como escrever consultas de registo no Azure Monitor](get-started-queries.md).
