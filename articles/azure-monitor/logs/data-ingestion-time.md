---
title: Registar tempo de ingestão de dados em Azure Monitor | Microsoft Docs
description: Explica os diferentes fatores que afetam a latência na recolha de dados de registo no Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/18/2019
ms.openlocfilehash: 56ef6563982c315d34cfeb87070b9ebfa3d27a30
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102500432"
---
# <a name="log-data-ingestion-time-in-azure-monitor"></a>Log data ingestion time in Azure Monitor (Tempo de ingestão de dados de registo no Azure Monitor)
O Azure Monitor é um serviço de dados de alta escala que serve milhares de clientes que enviam terabytes de dados todos os meses a um ritmo crescente. Muitas vezes existem dúvidas sobre o tempo que os dados de registo demoram a ficar disponíveis após a sua recolha. Este artigo explica os diferentes fatores que afetam esta latência.

## <a name="typical-latency"></a>Latência típica
Latência refere-se ao tempo em que os dados são criados no sistema monitorizado e no momento em que ele vem disponível para análise no Azure Monitor. A latência típica para ingerir dados de registo é entre 20 seg e 3 minutos. No entanto, a latência específica para dados específicos variará consoante uma variedade de fatores explicados abaixo.


## <a name="factors-affecting-latency"></a>Fatores que afetam a latência
O tempo total de ingestão de um determinado conjunto de dados pode ser dividido nas seguintes áreas de alto nível. 

- Tempo do agente - A hora de descobrir um evento, recolhê-lo e, em seguida, enviá-lo para o ponto de ingestão de registos do Monitor Azure como registo de registo. Na maioria dos casos, este processo é tratado por um agente. A latência adicional pode ser introduzida por rede.
- Tempo do pipeline – o tempo que o pipeline de ingestão demora a processar o registo. Isto inclui analisar as propriedades do evento e potencialmente adicionar informações calculadas.
- Tempo de indexação – O tempo gasto para ingerir um registo registado na loja de big data do Azure Monitor.

Os detalhes sobre a latência diferente introduzido neste processo são descritos abaixo.

### <a name="agent-collection-latency"></a>Agent collection latency (Latência de recolha do agente)
Os agentes e as soluções de gestão utilizam diferentes estratégias para recolher dados de uma máquina virtual, o que pode afetar a latência. Alguns exemplos específicos incluem:

- Eventos windows, eventos de syslog e métricas de desempenho são recolhidos imediatamente. Os contadores de desempenho do Linux são sondados em intervalos de 30 segundos.
- Os registos iIS e os registos personalizados são recolhidos assim que o seu calendário muda. Para os registos IIS, este é influenciado pelo [calendário de capotamento configurado no IIS](../agents/data-sources-iis-logs.md). 
- A solução de replicação do Diretório Ativo realiza a sua avaliação de cinco em cinco dias, enquanto a solução de Avaliação do Diretório Ativo realiza uma avaliação semanal da sua infraestrutura de Diretório Ativo. O agente recolherá estes registos apenas quando a avaliação estiver completa.

### <a name="agent-upload-frequency"></a>Agent upload frequency (Frequência de carregamento do agente)
Para garantir que o agente Log Analytics é leve, o agente faz o buffers e envia-os periodicamente para o Azure Monitor. A frequência de upload varia entre 30 segundos e 2 minutos, dependendo do tipo de dados. A maioria dos dados é enviado em menos de 1 minuto. 

### <a name="network"></a>Rede
As condições da rede podem afetar negativamente a latência destes dados para chegar ao ponto de ingestão de registos do Monitor Azure.

### <a name="azure-activity-logs-resource-logs-and-metrics"></a>Registos de atividades azure, registos de recursos e métricas
Os dados do Azure acrescentam tempo adicional para se disponibilizarem no ponto de ingestão de Registos do Monitor Azure para o processamento:

- Os registos de recursos normalmente adicionam 30-90 segundos, dependendo do serviço Azure. Alguns serviços Azure (especificamente, Azure SQL Database e Azure Virtual Network) reportam atualmente os seus registos com intervalos de 5 min. Estão em curso trabalhos para melhorar ainda mais este assunto. Consulte a [consulta abaixo](#checking-ingestion-time) para examinar esta latência no seu ambiente
- As métricas da plataforma Azure demoram mais 3 minutos a serem exportadas para o ponto de ingestão de registos do Monitor Azure.
- Os dados do registo de atividade podem demorar 10 a 15 minutos adicionais, se for utilizada a integração do legado. Recomendamos a utilização de definições de diagnóstico ao nível da subscrição para ingerir Registos de Atividade em Registos monitores Azure, que incorrem numa latência adicional de cerca de 30 segundos.

### <a name="management-solutions-collection"></a>Recolha de soluções de gestão
Algumas soluções não recolhem os seus dados de um agente e podem utilizar um método de recolha que introduz latência adicional. Algumas soluções recolhem dados a intervalos regulares sem tentar a recolha em tempo real. Exemplos específicos incluem:

- A Microsoft 365 regista registos de atividade de sondagens utilizando a API de Atividade de Gestão, que atualmente não fornece quaisquer garantias de latência em tempo real.
- As soluções do Windows Analytics (Update Compliance, por exemplo) são recolhidas pela solução numa frequência diária.

Consulte a documentação de cada solução para determinar a sua frequência de recolha.

### <a name="pipeline-process-time"></a>Tempo de processo do gasoduto

Uma vez disponíveis no ponto de ingestão, os dados demoram 30-60 segundos adicionais a estar disponíveis para consulta.

Uma vez que os registos de registos são ingeridos no oleoduto Azure Monitor (conforme identificado na propriedade [_TimeReceived),](./log-standard-columns.md#_timereceived) são escritos para armazenamento temporário para garantir o isolamento do inquilino e para garantir que os dados não são perdidos. Este processo normalmente adiciona 5-15 segundos. Algumas soluções de gestão implementam algoritmos mais pesados para agregar dados e obter insights à medida que os dados estão a ser transmitidos. Por exemplo, a Monitorização do Desempenho da Rede agrega dados de entrada em intervalos de 3 minutos, adicionando efetivamente latência de 3 minutos. Outro processo que adiciona latência é o processo que lida com registos personalizados. Em alguns casos, este processo pode adicionar poucos minutos de latência aos registos que são recolhidos a partir de ficheiros pelo agente.

### <a name="new-custom-data-types-provisioning"></a>Novos tipos de dados personalizados
Quando um novo tipo de dados personalizados é criado a partir de um [registo personalizado](../agents/data-sources-custom-logs.md) ou da [API do Colecionador de Dados,](../logs/data-collector-api.md)o sistema cria um recipiente de armazenamento dedicado. Esta é uma sobrecarga única que ocorre apenas na primeira apresentação deste tipo de dados.

### <a name="surge-protection"></a>Proteção contra ondas
A principal prioridade do Azure Monitor é garantir que nenhum dado do cliente seja perdido, pelo que o sistema tem proteção incorporada para aumentos de dados. Isto inclui tampão para garantir que mesmo sob uma carga imensa, o sistema continuará a funcionar. Sob carga normal, estes controlos adicionam menos de um minuto, mas em condições extremas e falhas podem adicionar tempo significativo, garantindo que os dados são seguros.

### <a name="indexing-time"></a>Tempo de indexação
Existe um equilíbrio incorporado para cada grande plataforma de dados entre fornecer analíticos e capacidades de pesquisa avançadas em oposição a fornecer acesso imediato aos dados. O Azure Monitor permite-lhe executar perguntas poderosas sobre biliões de registos e obter resultados em poucos segundos. Isto é possível porque a infraestrutura transforma os dados dramaticamente durante a sua ingestão e armazena-os em estruturas compactas únicas. O sistema tampona os dados até que o suficiente esteja disponível para criar estas estruturas. Isto deve ser concluído antes que o registo de registo apareça nos resultados da pesquisa.

Este processo demora atualmente cerca de 5 minutos quando há um baixo volume de dados, mas menos tempo a taxas de dados mais elevadas. Isto parece contraintuitivo, mas este processo permite a otimização da latência para cargas de trabalho de produção de grande volume.



## <a name="checking-ingestion-time"></a>Verificar o tempo de ingestão
O tempo de ingestão pode variar para diferentes recursos em diferentes circunstâncias. Pode utilizar consultas de registo para identificar comportamentos específicos do seu ambiente. A tabela a seguir especifica como pode determinar os diferentes tempos para um registo à medida que é criado e enviado para o Azure Monitor.

| Passo | Propriedade ou Função | Comentários |
|:---|:---|:---|
| Record criado na fonte de dados | [TimeGenerated](./log-standard-columns.md#timegenerated-and-timestamp) <br>Se a fonte de dados não definir este valor, então será definido ao mesmo tempo que _TimeReceived. |
| Record recebido pelo Azure Monitor no ponto final | [_TimeReceived](./log-standard-columns.md#_timereceived) | |
| Disco armazenado no espaço de trabalho e disponível para consultas | [ingestion_time()](/azure/kusto/query/ingestiontimefunction) | |

### <a name="ingestion-latency-delays"></a>Atrasos na latência da ingestão
Pode medir a latência de um registo específico comparando o resultado da função [ingestion_time()](/azure/kusto/query/ingestiontimefunction) com a propriedade _TimeGenerated._ Estes dados podem ser usados com várias agregações para descobrir como a latência da ingestão se comporta. Examine algum percentil do tempo de ingestão para obter insights para uma grande quantidade de dados. 

Por exemplo, a seguinte consulta irá mostrar-lhe quais os computadores que tiveram o maior tempo de ingestão nas 8 horas anteriores: 

``` Kusto
Heartbeat
| where TimeGenerated > ago(8h) 
| extend E2EIngestionLatency = ingestion_time() - TimeGenerated 
| extend AgentLatency = _TimeReceived - TimeGenerated 
| summarize percentiles(E2EIngestionLatency,50,95), percentiles(AgentLatency,50,95) by Computer 
| top 20 by percentile_E2EIngestionLatency_95 desc
```

Os controlos percentil anteriores são bons para encontrar tendências gerais de latência. Identificar um pico de latência a curto prazo, usar o máximo `max()` pode ser mais eficaz.

Se quiser aprofundar o tempo de ingestão de um computador específico durante um período de tempo, utilize a seguinte consulta, que também visualiza os dados do dia anterior num gráfico: 


``` Kusto
Heartbeat 
| where TimeGenerated > ago(24h) //and Computer == "ContosoWeb2-Linux"  
| extend E2EIngestionLatencyMin = todouble(datetime_diff("Second",ingestion_time(),TimeGenerated))/60 
| extend AgentLatencyMin = todouble(datetime_diff("Second",_TimeReceived,TimeGenerated))/60 
| summarize percentiles(E2EIngestionLatencyMin,50,95), percentiles(AgentLatencyMin,50,95) by bin(TimeGenerated,30m) 
| render timechart
```
 
Utilize a seguinte consulta para mostrar o tempo de ingestão de computador pelo país/região em que estão localizados, no qual se baseia no seu endereço IP: 

``` Kusto
Heartbeat 
| where TimeGenerated > ago(8h) 
| extend E2EIngestionLatency = ingestion_time() - TimeGenerated 
| extend AgentLatency = _TimeReceived - TimeGenerated 
| summarize percentiles(E2EIngestionLatency,50,95),percentiles(AgentLatency,50,95) by RemoteIPCountry 
```
 
Diferentes tipos de dados originários do agente podem ter diferentes tempos de latência de ingestão, pelo que as consultas anteriores poderiam ser usadas com outros tipos. Utilize a seguinte consulta para examinar o tempo de ingestão de vários serviços da Azure: 

``` Kusto
AzureDiagnostics 
| where TimeGenerated > ago(8h) 
| extend E2EIngestionLatency = ingestion_time() - TimeGenerated 
| extend AgentLatency = _TimeReceived - TimeGenerated 
| summarize percentiles(E2EIngestionLatency,50,95), percentiles(AgentLatency,50,95) by ResourceProvider
```

### <a name="resources-that-stop-responding"></a>Recursos que param de responder 
Em alguns casos, um recurso poderia parar de enviar dados. Para entender se um recurso está a enviar dados ou não, olhe para o seu registo mais recente que pode ser identificado pelo campo _timegenerated_ padrão.  

Utilize a tabela _Heartbeat_ para verificar a disponibilidade de um VM, uma vez que um batimento cardíaco é enviado uma vez por minuto pelo agente. Utilize a seguinte consulta para listar os computadores ativos que não reportaram batimentos cardíacos recentemente: 

``` Kusto
Heartbeat  
| where TimeGenerated > ago(1d) //show only VMs that were active in the last day 
| summarize NoHeartbeatPeriod = now() - max(TimeGenerated) by Computer  
| top 20 by NoHeartbeatPeriod desc 
```

## <a name="next-steps"></a>Passos seguintes
* Leia o [Acordo de Nível de Serviço (SLA)](https://azure.microsoft.com/en-us/support/legal/sla/monitor/v1_3/) para O Monitor Azure.
