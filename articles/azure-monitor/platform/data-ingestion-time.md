---
title: Tempo de ingestão de dados de log em Azure Monitor | Microsoft Docs
description: Explica os diferentes fatores que afetam a latência na coleta de dados de log em Azure Monitor.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: log-analytics
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/18/2019
ms.author: bwren
ms.openlocfilehash: e07a436ee18a216bab569d299e534e729996db19
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68990162"
---
# <a name="log-data-ingestion-time-in-azure-monitor"></a>Tempo de ingestão de dados de log no Azure Monitor
Azure Monitor é um serviço de dados de alta escala que atende a milhares de clientes enviando terabytes de dados por mês em um ritmo crescente. Geralmente, há perguntas sobre o tempo que leva para que os dados de log fiquem disponíveis depois de coletados. Este artigo explica os diferentes fatores que afetam essa latência.

## <a name="typical-latency"></a>Latência típica
Latência refere-se à hora em que os dados são criados no sistema monitorado e a hora em que ele fica disponível para análise no Azure Monitor. A latência típica para ingerir dados de log está entre 2 e 5 minutos. A latência específica para qualquer dado específico variará dependendo de uma variedade de fatores explicados abaixo.


## <a name="factors-affecting-latency"></a>Fatores que afetam a latência
O tempo total de ingestão para um determinado conjunto de dados pode ser dividido nas seguintes áreas de alto nível. 

- Tempo do agente-o tempo para descobrir um evento, coletá-lo e, em seguida, enviá-lo para Azure Monitor ponto de ingestão como um registro de log. Na maioria dos casos, esse processo é tratado por um agente.
- Tempo do pipeline – o tempo que o pipeline de ingestão demora a processar o registo. Isso inclui analisar as propriedades do evento e potencialmente adicionar informações calculadas.
- Tempo de indexação – o tempo gasto para ingerir um registro de log em Azure Monitor Big Data Store.

Os detalhes sobre a latência diferente introduzida nesse processo são descritos abaixo.

### <a name="agent-collection-latency"></a>Latência de coleção de agentes
Os agentes e as soluções de gerenciamento usam estratégias diferentes para coletar dados de uma máquina virtual, o que pode afetar a latência. Alguns exemplos específicos incluem o seguinte:

- Os eventos do Windows, os eventos de syslog e as métricas de desempenho são coletados imediatamente. Os contadores de desempenho do Linux são sondados em intervalos de 30 segundos.
- Logs do IIS e logs personalizados são coletados quando o carimbo de data/hora é alterado. Para logs do IIS, isso é influenciado pela [agenda de substituição configurada no IIS](data-sources-iis-logs.md). 
- Active Directory solução de replicação executa sua avaliação a cada cinco dias, enquanto a solução de Avaliação do Active Directory executa uma avaliação semanal de sua infraestrutura de Active Directory. O agente coletará esses logs somente quando a avaliação for concluída.

### <a name="agent-upload-frequency"></a>Frequência de carregamento do agente
Para garantir que o agente de Log Analytics seja leve, o agente armazena em log os logs e os carrega periodicamente no Azure Monitor. A frequência de carregamento varia entre 30 segundos e 2 minutos, dependendo do tipo de dados. A maioria dos dados é carregada em menos de um minuto. As condições de rede podem afetar negativamente a latência desses dados para alcançar Azure Monitor ponto de ingestão.

### <a name="azure-activity-logs-diagnostic-logs-and-metrics"></a>Logs de atividades do Azure, logs de diagnóstico e métricas
Os dados do Azure adicionam mais tempo para ficarem disponíveis em Log Analytics ponto de ingestão para processamento:

- Os dados dos logs de diagnóstico levam 2-15 minutos, dependendo do serviço do Azure. Consulte a [consulta abaixo](#checking-ingestion-time) para examinar essa latência em seu ambiente
- As métricas da plataforma Azure levam 3 minutos para serem enviadas para Log Analytics ponto de ingestão.
- Os dados do log de atividades levarão cerca de 10-15 minutos para serem enviados para Log Analytics ponto de ingestão.

Uma vez disponível no ponto de ingestão, os dados levam 2-5 minutos adicionais para estar disponíveis para consulta.

### <a name="management-solutions-collection"></a>Coleção de soluções de gerenciamento
Algumas soluções não coletam seus dados de um agente e podem usar um método de coleta que apresente latência adicional. Algumas soluções coletam dados em intervalos regulares sem tentar uma coleta quase em tempo real. Exemplos específicos incluem o seguinte:

- A solução Office 365 sonda os logs de atividade usando a API de atividade de gerenciamento do Office 365, que atualmente não fornece nenhuma garantia de latência quase em tempo real.
- Os dados das soluções do Windows Analytics (Conformidade de Atualizações, por exemplo) são coletados pela solução a uma frequência diária.

Consulte a documentação de cada solução para determinar sua frequência de coleta.

### <a name="pipeline-process-time"></a>Pipeline – tempo de processo
Depois que os registros de log são ingeridos no pipeline de Azure Monitor (conforme identificado na propriedade [_TimeReceived](log-standard-properties.md#_timereceived) ), eles são gravados no armazenamento temporário para garantir o isolamento do locatário e para garantir que os dados não sejam perdidos. Esse processo normalmente adiciona 5-15 segundos. Algumas soluções de gerenciamento implementam algoritmos mais pesados para agregar dados e derivam informações à medida que os dados são transmitidos. Por exemplo, o monitoramento de desempenho de rede agrega dados de entrada em intervalos de 3 minutos, adicionando a latência de 3 minutos com eficiência. Outro processo que adiciona latência é o processo que manipula logs personalizados. Em alguns casos, esse processo pode adicionar alguns minutos de latência aos logs coletados dos arquivos pelo agente.

### <a name="new-custom-data-types-provisioning"></a>Novo provisionamento de tipos de dados personalizados
Quando um novo tipo de dados personalizados é criado a partir de um [log personalizado](data-sources-custom-logs.md) ou da [API do coletor de dados](data-collector-api.md), o sistema cria um contêiner de armazenamento dedicado. Essa é uma sobrecarga única que ocorre apenas na primeira aparência desse tipo de dados.

### <a name="surge-protection"></a>Proteção contra surtos
A prioridade mais alta do Azure Monitor é garantir que nenhum dado do cliente seja perdido, portanto, o sistema tem proteção interna para sobretensões de dados. Isso inclui buffers para garantir que, mesmo sob grande carga, o sistema continuará funcionando. Sob carga normal, esses controles adicionam menos de um minuto, mas em condições e falhas extremas poderiam adicionar um tempo significativo, garantindo que os dados estejam seguros.

### <a name="indexing-time"></a>Tempo de indexação
Há um saldo interno para cada plataforma de Big Data entre fornecer recursos de análise e pesquisa avançada em vez de fornecer acesso imediato aos dados. Azure Monitor permite que você execute consultas poderosas em bilhões de registros e obtenha resultados em alguns segundos. Isso é possível porque a infraestrutura transforma os dados drasticamente durante sua ingestão e os armazena em estruturas compactas exclusivas. O sistema armazena em buffer os dados até que sejam suficientes para criar essas estruturas. Isso deve ser concluído antes de o registro de log aparecer nos resultados da pesquisa.

Atualmente, esse processo leva cerca de 5 minutos quando há pouco volume de dados, mas menos tempo em taxas de dados mais altas. Isso parece um pouco intuitivo, mas esse processo permite a otimização da latência para cargas de trabalho de produção de alto volume.



## <a name="checking-ingestion-time"></a>Verificando a hora da ingestão
O tempo de ingestão pode variar para diferentes recursos em circunstâncias diferentes. Você pode usar consultas de log para identificar o comportamento específico do seu ambiente. A tabela a seguir especifica como você pode determinar as diferentes horas para um registro conforme ele é criado e enviado para Azure Monitor.

| Passo | Propriedade ou função | Comentários |
|:---|:---|:---|
| Registro criado na fonte de dados | [TimeGenerated](log-standard-properties.md#timegenerated-and-timestamp) <br>Se a fonte de dados não definir esse valor, ela será definida para o mesmo horário que _TimeReceived. |
| Registro recebido por Azure Monitor ponto de extremidade de ingestão | [_TimeReceived](log-standard-properties.md#_timereceived) | |
| Registro armazenado no espaço de trabalho e disponível para consultas | [ingestion_time()](/azure/kusto/query/ingestiontimefunction) | |

### <a name="ingestion-latency-delays"></a>Atrasos de latência de ingestão
Você pode medir a latência de um registro específico comparando o resultado da função [ingestion_time ()](/azure/kusto/query/ingestiontimefunction) com a Propriedade TimeGenerated. Esses dados podem ser usados com várias agregações para encontrar a forma como a latência de ingestão se comporta. Examine algum percentil do tempo de ingestão para obter informações sobre uma grande quantidade de dados. 

Por exemplo, a consulta a seguir mostrará quais computadores tiveram o maior tempo de ingestão durante as 8 horas anteriores: 

``` Kusto
Heartbeat
| where TimeGenerated > ago(8h) 
| extend E2EIngestionLatency = ingestion_time() - TimeGenerated 
| extend AgentLatency = _TimeReceived - TimeGenerated 
| summarize percentiles(E2EIngestionLatency,50,95), percentiles(AgentLatency,50,95) by Computer 
| top 20 by percentile_E2EIngestionLatency_95 desc
```
 
Se você quiser fazer uma busca detalhada no tempo de ingestão de um computador específico durante um período de tempo, use a seguinte consulta que também visualiza os dados do dia anterior em um grafo: 

``` Kusto
Heartbeat 
| where TimeGenerated > ago(24h) //and Computer == "ContosoWeb2-Linux"  
| extend E2EIngestionLatencyMin = todouble(datetime_diff("Second",ingestion_time(),TimeGenerated))/60 
| extend AgentLatencyMin = todouble(datetime_diff("Second",_TimeReceived,TimeGenerated))/60 
| summarize percentiles(E2EIngestionLatencyMin,50,95), percentiles(AgentLatencyMin,50,95) by bin(TimeGenerated,30m) 
| render timechart
```
 
Use a consulta a seguir para mostrar o tempo de ingestão do computador pelo país/região em que eles estão localizados, com base em seu endereço IP: 

``` Kusto
Heartbeat 
| where TimeGenerated > ago(8h) 
| extend E2EIngestionLatency = ingestion_time() - TimeGenerated 
| extend AgentLatency = _TimeReceived - TimeGenerated 
| summarize percentiles(E2EIngestionLatency,50,95),percentiles(AgentLatency,50,95) by RemoteIPCountry 
```
 
Tipos de dados diferentes provenientes do agente podem ter tempo de latência de ingestão diferente, portanto, as consultas anteriores poderiam ser usadas com outros tipos. Use a consulta a seguir para examinar o tempo de ingestão de vários serviços do Azure: 

``` Kusto
AzureDiagnostics 
| where TimeGenerated > ago(8h) 
| extend E2EIngestionLatency = ingestion_time() - TimeGenerated 
| extend AgentLatency = _TimeReceived - TimeGenerated 
| summarize percentiles(E2EIngestionLatency,50,95), percentiles(AgentLatency,50,95) by ResourceProvider
```

### <a name="resources-that-stop-responding"></a>Recursos que param de responder 
Em alguns casos, um recurso pode parar de enviar dados. Para entender se um recurso está enviando dados ou não, examine o registro mais recente que pode ser identificado pelo campo TimeGenerated padrão.  

Use a tabela de pulsação para verificar a disponibilidade de uma VM, já que uma pulsação é enviada uma vez por minuto pelo agente. Use a consulta a seguir para listar os computadores ativos que não relataram a pulsação recentemente: 

``` Kusto
Heartbeat  
| where TimeGenerated > ago(1d) //show only VMs that were active in the last day 
| summarize NoHeartbeatPeriod = now() - max(TimeGenerated) by Computer  
| top 20 by NoHeartbeatPeriod desc 
```

## <a name="next-steps"></a>Passos Seguintes
* Leia o [contrato de nível de serviço (SLA)](https://azure.microsoft.com/support/legal/sla/log-analytics/v1_1/) para Azure monitor.

