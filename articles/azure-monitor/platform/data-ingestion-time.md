---
title: Tempo de ingestão de dados de registo no Monitor Azure / Microsoft Docs
description: Explica os diferentes fatores que afetam a latência na recolha de dados de registo no Monitor Azure.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/18/2019
ms.openlocfilehash: 99d5594dd3ebe3750cb0a09ea803065e2aeb5ba2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77666642"
---
# <a name="log-data-ingestion-time-in-azure-monitor"></a>Log data ingestion time in Azure Monitor (Tempo de ingestão de dados de registo no Azure Monitor)
O Azure Monitor é um serviço de dados de alta escala que serve milhares de clientes que enviam terabytes de dados todos os meses a um ritmo crescente. Muitas vezes existem dúvidas sobre o tempo que os dados de registo demoram a ficar disponíveis após a sua recolha. Este artigo explica os diferentes fatores que afetam esta latência.

## <a name="typical-latency"></a>Latência típica
A latência refere-se ao tempo que os dados são criados no sistema monitorizado e ao tempo que estes estão disponíveis para análise no Monitor Azure. A latência típica para ingerir dados de registo é entre 2 e 5 minutos. A latência específica de quaisquer dados específicos variará consoante uma variedade de fatores explicados abaixo.


## <a name="factors-affecting-latency"></a>Fatores que afetam a latência
O tempo total de ingestão de um determinado conjunto de dados pode ser dividido nas seguintes áreas de alto nível. 

- Hora do agente - O tempo para descobrir um evento, recolhê-lo e enviá-lo para o ponto de ingestão do Monitor Azure como registo de registo. Na maioria dos casos, este processo é tratado por um agente.
- Tempo do pipeline – o tempo que o pipeline de ingestão demora a processar o registo. Isto inclui analisar as propriedades do evento e potencialmente adicionar informações calculadas.
- Tempo de indexação – O tempo gasto para ingerir um registo de registo na loja de big data do Azure Monitor.

Os detalhes sobre a latência diferente introduzida neste processo são descritos abaixo.

### <a name="agent-collection-latency"></a>Agent collection latency (Latência de recolha do agente)
Agentes e soluções de gestão usam diferentes estratégias para recolher dados de uma máquina virtual, o que pode afetar a latência. Alguns exemplos específicos incluem o seguinte:

- Eventos windows, eventos syslog e métricas de desempenho são recolhidos imediatamente. Os contadores de desempenho do Linux são sondados em intervalos de 30 segundos.
- Os registos iIS e os registos personalizados são recolhidos assim que o seu carimbo de tempo muda. Para os registos IIS, isto é influenciado pelo calendário de [capotamento configurado no IIS](data-sources-iis-logs.md). 
- A solução de replicação de directórioativo ativo realiza a sua avaliação de cinco em cinco dias, enquanto a solução ative directory Assessment realiza uma avaliação semanal da sua infraestrutura de Diretório Ativo. O agente recolherá estes registos apenas quando a avaliação estiver completa.

### <a name="agent-upload-frequency"></a>Agent upload frequency (Frequência de carregamento do agente)
Para garantir que o agente Log Analytics é leve, o agente regista e envia-os periodicamente para o Monitor Azure. A frequência de upload varia entre 30 segundos e 2 minutos, dependendo do tipo de dados. A maioria dos dados é carregado em menos de 1 minuto. As condições de rede podem afetar negativamente a latência destes dados para chegar ao ponto de ingestão do Monitor Azure.

### <a name="azure-activity-logs-resource-logs-and-metrics"></a>Registos de atividade sinuosos, registos de recursos e métricas
Os dados do Azure adicionam tempo adicional para ficarem disponíveis no ponto de ingestão de Log Analytics para processamento:

- Os dados dos registos de recursos demoram 2 a 15 minutos, dependendo do serviço Azure. Veja a [consulta abaixo](#checking-ingestion-time) para examinar esta latência no seu ambiente
- As métricas da plataforma Azure demoram 3 minutos a ser enviadas para o ponto de ingestão de Log Analytics.
- Os dados do registo de atividade levarão cerca de 10-15 minutos a serem enviados para o ponto de ingestão de Log Analytics.

Uma vez disponíveis no ponto de ingestão, os dados demoram 2 a 5 minutos adicionais para estarem disponíveis para consulta.

### <a name="management-solutions-collection"></a>Coleção de soluções de gestão
Algumas soluções não recolhem os seus dados de um agente e podem utilizar um método de recolha que introduz latência adicional. Algumas soluções recolhem dados em intervalos regulares sem tentar a recolha em tempo quase real. Exemplos específicos incluem:

- O Office 365 solution polls activity logs using the Office 365 Management Activity API, which currently não fornece quaisquer garantias de latência em tempo quase real.
- As soluções Windows Analytics (Atualização de Conformidade, por exemplo) são recolhidas pela solução numa frequência diária.

Consulte a documentação para cada solução para determinar a sua frequência de recolha.

### <a name="pipeline-process-time"></a>Tempo de processo de gasoduto
Uma vez que os registos de registo são ingeridos no oleoduto Do Monitor Azure (identificado na [propriedade _TimeReceived),](log-standard-properties.md#_timereceived) são escritos para armazenamento temporário para garantir o isolamento dos inquilinos e para garantir que os dados não se perdem. Este processo normalmente adiciona 5-15 segundos. Algumas soluções de gestão implementam algoritmos mais pesados para agregar dados e obter insights à medida que os dados estão a ser transmitidos. Por exemplo, o Monitor de Desempenho da Rede agrega dados de entrada em intervalos de 3 minutos, adicionando efetivamente latência de 3 minutos. Outro processo que adiciona latência é o processo que trata os registos personalizados. Em alguns casos, este processo pode adicionar alguns minutos de latência aos registos que são recolhidos a partir de ficheiros pelo agente.

### <a name="new-custom-data-types-provisioning"></a>Novos tipos de dados personalizados que aprovisionam
Quando um novo tipo de dados personalizados é criado a partir de um [registo personalizado](data-sources-custom-logs.md) ou da API do [Coletor](data-collector-api.md)de Dados, o sistema cria um recipiente de armazenamento dedicado. Esta é uma sobrecarga única que ocorre apenas na primeira aparição deste tipo de dados.

### <a name="surge-protection"></a>Proteção contra ondas
A principal prioridade do Monitor Azure é garantir que não se percam dados dos clientes, pelo que o sistema tem uma proteção incorporada para picos de dados. Isto inclui tampões para garantir que, mesmo sob uma carga imensa, o sistema continuará a funcionar. Sob carga normal, estes controlos adicionam menos de um minuto, mas em condições extremas e falhas podem adicionar tempo significativo, garantindo que os dados são seguros.

### <a name="indexing-time"></a>Tempo de indexação
Existe um equilíbrio incorporado para todas as grandes plataformas de dados entre o fornecimento de análises e capacidades de pesquisa avançadas, em oposição ao acesso imediato aos dados. O Azure Monitor permite-lhe fazer consultas poderosas em biliões de registos e obter resultados em poucos segundos. Isto é possível porque a infraestrutura transforma os dados dramaticamente durante a sua ingestão e armazena-os em estruturas compactas únicas. O sistema tampona os dados até que esteja disponível o suficiente para criar estas estruturas. Isto deve ser concluído antes que o registo de registo apareça nos resultados da pesquisa.

Este processo demora atualmente cerca de 5 minutos quando há um baixo volume de dados, mas menos tempo a taxas de dados mais elevadas. Isto parece contraintuitivo, mas este processo permite a otimização da latência para cargas de trabalho de produção de alto volume.



## <a name="checking-ingestion-time"></a>Verificação do tempo de ingestão
O tempo de ingestão pode variar para diferentes recursos em circunstâncias diferentes. Pode utilizar consultas de registo para identificar comportamentos específicos do seu ambiente. A tabela seguinte especifica como pode determinar os diferentes tempos para um registo, uma vez que é criado e enviado para o Monitor Azure.

| Passo | Propriedade ou Função | Comentários |
|:---|:---|:---|
| Registo criado na fonte de dados | [TimeGenerated](log-standard-properties.md#timegenerated-and-timestamp) <br>Se a fonte de dados não definir este valor, então será definido ao mesmo tempo que _TimeReceived. |
| Recorde recebido pelo ponto final de ingestão da Monitora Azure | [_TimeReceived](log-standard-properties.md#_timereceived) | |
| Registo armazenado no espaço de trabalho e disponível para consultas | [ingestion_time()](/azure/kusto/query/ingestiontimefunction) | |

### <a name="ingestion-latency-delays"></a>Atrasos na latência da ingestão
Pode medir a latência de um registo específico comparando o resultado da função [ingestion_time()](/azure/kusto/query/ingestiontimefunction) com a propriedade _TimeGenerated._ Estes dados podem ser utilizados com várias agregações para descobrir como a latência de ingestão se comporta. Examine algum percentil do tempo de ingestão para obter informações para uma grande quantidade de dados. 

Por exemplo, a seguinte consulta mostrar-lhe-á quais os computadores que tiveram o maior tempo de ingestão nas 8 horas anteriores: 

``` Kusto
Heartbeat
| where TimeGenerated > ago(8h) 
| extend E2EIngestionLatency = ingestion_time() - TimeGenerated 
| extend AgentLatency = _TimeReceived - TimeGenerated 
| summarize percentiles(E2EIngestionLatency,50,95), percentiles(AgentLatency,50,95) by Computer 
| top 20 by percentile_E2EIngestionLatency_95 desc
```

Os controlos de percentil anteriores são bons para encontrar tendências gerais na latência. Para identificar um pico de latência a`max()`curto prazo, usar o máximo pode ser mais eficaz.

Se pretender aprofundar o tempo de ingestão durante um determinado computador durante um período de tempo, utilize a seguinte consulta, que também visualiza os dados do dia passado num gráfico: 


``` Kusto
Heartbeat 
| where TimeGenerated > ago(24h) //and Computer == "ContosoWeb2-Linux"  
| extend E2EIngestionLatencyMin = todouble(datetime_diff("Second",ingestion_time(),TimeGenerated))/60 
| extend AgentLatencyMin = todouble(datetime_diff("Second",_TimeReceived,TimeGenerated))/60 
| summarize percentiles(E2EIngestionLatencyMin,50,95), percentiles(AgentLatencyMin,50,95) by bin(TimeGenerated,30m) 
| render timechart
```
 
Utilize a seguinte consulta para mostrar o tempo de ingestão informática pelo país/região em que se encontram localizados com base no seu endereço IP: 

``` Kusto
Heartbeat 
| where TimeGenerated > ago(8h) 
| extend E2EIngestionLatency = ingestion_time() - TimeGenerated 
| extend AgentLatency = _TimeReceived - TimeGenerated 
| summarize percentiles(E2EIngestionLatency,50,95),percentiles(AgentLatency,50,95) by RemoteIPCountry 
```
 
Diferentes tipos de dados originários do agente podem ter um tempo de latência diferente, pelo que as consultas anteriores podem ser usadas com outros tipos. Utilize a seguinte consulta para examinar o tempo de ingestão de vários serviços Azure: 

``` Kusto
AzureDiagnostics 
| where TimeGenerated > ago(8h) 
| extend E2EIngestionLatency = ingestion_time() - TimeGenerated 
| extend AgentLatency = _TimeReceived - TimeGenerated 
| summarize percentiles(E2EIngestionLatency,50,95), percentiles(AgentLatency,50,95) by ResourceProvider
```

### <a name="resources-that-stop-responding"></a>Recursos que deixam de responder 
Em alguns casos, um recurso pode parar de enviar dados. Para entender se um recurso está ou não a enviar dados, veja o seu registo mais recente que pode ser identificado pelo campo _timegenerated_ padrão.  

Utilize a tabela _Heartbeat_ para verificar a disponibilidade de um VM, uma vez que um batimento cardíaco é enviado uma vez por minuto pelo agente. Use a seguinte consulta para listar os computadores ativos que não reportaram batimentos cardíacos recentemente: 

``` Kusto
Heartbeat  
| where TimeGenerated > ago(1d) //show only VMs that were active in the last day 
| summarize NoHeartbeatPeriod = now() - max(TimeGenerated) by Computer  
| top 20 by NoHeartbeatPeriod desc 
```

## <a name="next-steps"></a>Passos seguintes
* Leia o Acordo de [Nível de Serviço (SLA)](https://azure.microsoft.com/support/legal/sla/log-analytics/v1_1/) para o Monitor Azure.

