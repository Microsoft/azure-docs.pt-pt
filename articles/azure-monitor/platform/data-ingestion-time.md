---
title: Tempo de ingestão de dados de registo no Azure Monitor | Documentos da Microsoft
description: Explica os diferentes fatores que afetam a latência na coleta de dados de registo no Azure Monitor.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: log-analytics
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/24/2019
ms.author: bwren
ms.openlocfilehash: d508ce217e3a97b3399435cb63295eb28965359a
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/14/2019
ms.locfileid: "65605597"
---
# <a name="log-data-ingestion-time-in-azure-monitor"></a>Tempo de ingestão de dados de registo no Azure Monitor
Monitor do Azure é um serviço de dados de grande escala que serve a milhares de envio de cada mês de terabytes de dados a um ritmo cada vez maior de clientes. Muitas vezes, há perguntas sobre o tempo que demora para dados de registo para ficarão disponíveis assim que são recolhido. Este artigo explica os diferentes fatores que afetam esta latência.

## <a name="typical-latency"></a>Latência típica
Latência refere-se para o tempo que dados são criados no sistema monitorizado e a hora em que se trata de disponível para análise no Azure Monitor. A latência típica para ingerir dados de registo é entre 2 e 5 minutos. A latência específica para quaisquer dados específicos irá variar dependendo de vários fatores explicado abaixo.


## <a name="factors-affecting-latency"></a>Fatores que afetam a latência
O tempo de ingestão total para um determinado conjunto de dados pode ser dividido nas seguintes áreas de alto nível. 

- Hora do agente - tempo para detetar um evento, reuni-los e, em seguida, enviar para o ponto de ingestão de Monitor do Azure como um registro de log. Na maioria dos casos, esse processo é manipulado por um agente.
- Tempo do pipeline – o tempo que o pipeline de ingestão demora a processar o registo. Isto inclui as propriedades do evento de análise e potencialmente adicionar informações calculadas.
- Tempo de indexação – o tempo gasto para ingerir um registro de log no arquivo de macrodados do Azure Monitor.

Obter detalhes sobre a latência de diferente introduzida neste processo estão descritos abaixo.

### <a name="agent-collection-latency"></a>Latência de coleção de agente
Soluções de gestão e agentes use estratégias diferentes para recolher dados de uma máquina virtual, que pode afetar a latência. Seguem-se alguns exemplos específicos:

- Eventos do Windows, eventos do syslog e métricas de desempenho são recolhidas imediatamente. Contadores de desempenho do Linux são consultados a intervalos de 30 segundos.
- Registos do IIS e dos registos personalizados são recolhidos quando seus timestamp é alterado. Para os registos de IIS, isso depende de [agenda de rollover configurada no IIS](data-sources-iis-logs.md). 
- Solução de replicação de diretório Active Directory realiza sua avaliação a cada cinco dias, enquanto a solução de avaliação do Active Directory realiza uma avaliação semanal da infra-estrutura do Active Directory. O agente irá recolher estes registos, apenas quando a avaliação estiver concluída.

### <a name="agent-upload-frequency"></a>Frequência de carregamento do agente
Para garantir que o agente Log Analytics é simples, o agente coloca na memória intermédia registos e carrega periodicamente para o Azure Monitor. Carregar frequência varia entre 30 segundos e dois minutos consoante o tipo de dados. A maioria dos dados são carregados em menos de 1 minuto. Condições de rede podem afetar negativamente a latência destes dados para alcançar o ponto de ingestão do Azure Monitor.

### <a name="azure-activity-logs-diagnostic-logs-and-metrics"></a>Registos de atividades do Azure, os registos de diagnóstico e métricas
Dados do Azure adiciona mais tempo para se tornar disponível no ponto de ingestão do Log Analytics para o processamento:

- Dados de registos de diagnóstico demorar 2 a 15 minutos, dependendo do serviço do Azure. Consulte a [consulta abaixo](#checking-ingestion-time) para examinar esta latência no seu ambiente
- Métricas de plataforma do Azure demorar 3 minutos a serem enviados para o ponto de ingestão do Log Analytics.
- Dados de registo de atividade irão demorar cerca de 10 a 15 minutos a serem enviados para o ponto de ingestão do Log Analytics.

Uma vez disponível no ponto de ingestão, dados demora adicionais 2 a 5 minutos a estar disponível para consulta.

### <a name="management-solutions-collection"></a>Coleção de soluções de gestão
Algumas soluções não recolher os dados de um agente e podem utilizar um método de coleção que cria latência adicional. Algumas soluções de recolhem dados em intervalos regulares, sem tentar coleção quase em tempo real. Exemplos específicos incluem o seguinte:

- Solução do Office 365 consulta os registos de atividades usando a API de atividade do gerenciamento do Office 365, a que atualmente não fornece que garantias de qualquer latência quase em tempo real.
- Dados de soluções (conformidade de atualização, por exemplo) de análise do Windows são recolhidos pela solução a uma frequência diária.

Consulte a documentação para cada solução determinar a frequência de recolha.

### <a name="pipeline-process-time"></a>Tempo do processo de pipeline
Depois de registros de log são ingeridos no pipeline do Azure Monitor, foram escritas para armazenamento temporário para garantir o isolamento de inquilino e certifique-se de que os dados não são perdidos. Este processo adiciona normalmente 5 a 15 segundos. Algumas soluções de gestão implementam algoritmos mais pesados agregar dados e derivam informações como dados de transmissão em fluxo no. Por exemplo, a monitorização de desempenho de rede agrega os dados recebidos em intervalos de 3 minutos, com eficiência, adicionando latência de 3 minutos. Outro processo que adiciona latência é o processo que lida com registos personalizados. Em alguns casos, esse processo pode adicionar alguns minutos de latência para registos que são recolhidos a partir de arquivos pelo agente.

### <a name="new-custom-data-types-provisioning"></a>Novos tipos de dados personalizados de aprovisionamento
Quando é criado um novo tipo de dados personalizados a partir um [log personalizado](data-sources-custom-logs.md) ou o [API de Recoletor de dados](data-collector-api.md), o sistema cria um contentor de armazenamento dedicado. Esta é uma sobrecarga de uso individual que ocorre somente no primeiro aspeto deste tipo de dados.

### <a name="surge-protection"></a>Proteção de surto
A prioridade de segurança do Azure Monitor é garantir que não existem dados de cliente são perdidos, para que o sistema tenha proteção incorporada para surtos de dados. Isto inclui buffers para garantir que até mesmo sob carga grande, o sistema irá manter a funcionar. Sob uma carga normal, esses controles adicionar menos de um minuto, mas em condições extremas e pode adicionar a um tempo significativo, garantindo dados de falhas é seguros.

### <a name="indexing-time"></a>Tempo de indexação
Existe um equilíbrio incorporado para cada plataforma de grandes volumes de dados entre o fornecimento de análise e capacidades de pesquisa avançada em vez de fornecer acesso imediato aos dados. O Azure Monitor permite-lhe executar consultas poderosas em milhares de milhões de registos e obter resultados dentro de alguns segundos. Isso se tornou possível uma vez que a infraestrutura transforma os dados significativamente durante a ingestão e armazena-os em estruturas compactas exclusivas. O sistema coloca na memória intermédia os dados até que o suficiente de está disponível para criar estas estruturas. Isso deve ser concluído antes do Registro de log é apresentada nos resultados da pesquisa.

Este processo atualmente demora cerca de 5 minutos quando existe um volume baixo de dados, mas menos tempo taxas de dados mais altas. Isso parece não intuitivo, mas esse processo permite que a otimização de latência para cargas de trabalho de produção de grande volume.



## <a name="checking-ingestion-time"></a>A verificar o tempo de ingestão
Tempo de ingestão pode variar para diferentes recursos em diferentes circunstâncias. Pode utilizar consultas de registo para identificar o comportamento específico do seu ambiente.

### <a name="ingestion-latency-delays"></a>Atrasos de latência de ingestão
Pode medir a latência de um registo específico ao comparar o resultado do [ingestion_time()](/azure/kusto/query/ingestiontimefunction) funcionar para o _TimeGenerated_ campo. Estes dados podem ser utilizados com várias agregações para localizar a forma como se comporta a latência de ingestão. Examine alguns percentil do tempo de ingestão para obter informações para uma grande quantidade de dados. 

Por exemplo, a seguinte consulta mostrará a quais os computadores que tiveram o maior tempo de ingestão ao longo do dia atual: 

``` Kusto
Heartbeat
| where TimeGenerated > ago(8h) 
| extend E2EIngestionLatency = ingestion_time() - TimeGenerated 
| summarize percentiles(E2EIngestionLatency,50,95) by Computer 
| top 20 by percentile_E2EIngestionLatency_95 desc  
```
 
Se quiser fazer uma busca detalhada sobre o tempo de ingestão de um computador específico durante um período de tempo, utilize a seguinte consulta, que também visualiza os dados num gráfico: 

``` Kusto
Heartbeat 
| where TimeGenerated > ago(24h) and Computer == "ContosoWeb2-Linux"  
| extend E2EIngestionLatencyMin = todouble(datetime_diff("Second",ingestion_time(),TimeGenerated))/60 
| summarize percentiles(E2EIngestionLatencyMin,50,95) by bin(TimeGenerated,30m) 
| render timechart  
```
 
Utilize a seguinte consulta para mostrar a hora de ingestão do computador por país/região que estão localizados no que se baseia no respetivo endereço de IP: 

``` Kusto
Heartbeat 
| where TimeGenerated > ago(8h) 
| extend E2EIngestionLatency = ingestion_time() - TimeGenerated 
| summarize percentiles(E2EIngestionLatency,50,95) by RemoteIPCountry 
```
 
Diferentes tipos de dados provenientes do agente podem ter tempo de latência de ingestão diferentes, para que as consultas anteriores podem ser utilizadas com outros tipos. Utilize a seguinte consulta para examinar o tempo de ingestão de vários serviços do Azure: 

``` Kusto
AzureDiagnostics 
| where TimeGenerated > ago(8h) 
| extend E2EIngestionLatency = ingestion_time() - TimeGenerated 
| summarize percentiles(E2EIngestionLatency,50,95) by ResourceProvider
```

### <a name="resources-that-stop-responding"></a>Recursos que param de responder 
Em alguns casos, um recurso poderia parar o envio de dados. Para compreender se um recurso está a enviar dados ou não, examine o registo mais recente que pode ser identificado pelo padrão de _TimeGenerated_ campo.  

Utilize o _Heartbeat_ tabela para verificar a disponibilidade de uma VM, uma vez que um heartbeat é enviado uma vez a uma minuto pelo agente. Utilize a seguinte consulta para listar os computadores do Active Directory que ainda não comunicou o heartbeat recentemente: 

``` Kusto
Heartbeat  
| where TimeGenerated > ago(1d) //show only VMs that were active in the last day 
| summarize NoHeartbeatPeriod = now() - max(TimeGenerated) by Computer  
| top 20 by NoHeartbeatPeriod desc 
```

## <a name="next-steps"></a>Passos Seguintes
* Leitura a [(SLA) de contrato de nível de serviço](https://azure.microsoft.com/support/legal/sla/log-analytics/v1_1/) para o Azure Monitor.

