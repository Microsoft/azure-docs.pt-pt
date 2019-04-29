---
title: Uniformizar a vários recursos do Azure Monitor Application Insights | Documentos da Microsoft
description: Este artigo fornece detalhes sobre como utilizar uma função nos Logs de Monitor do Azure para consultar vários recursos do Application Insights e visualize os dados.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: magoedte
ms.openlocfilehash: 3f3de81197b05d4f025a3fd8638cffe4b07cecad
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61424660"
---
# <a name="unify-multiple-azure-monitor-application-insights-resources"></a>Uniformizar a vários recursos do Azure Monitor Application Insights 
Este artigo descreve como consultar e ver todos os Application Insights log dados da sua aplicação num único lugar, mesmo quando eles estão em diferentes subscrições do Azure, como um substituto para a descontinuação do conector do Application Insights. O número de recursos que podem ser incluídos numa única consulta recursos do Application Insights está limitado a 100.  

## <a name="recommended-approach-to-query-multiple-application-insights-resources"></a>Abordagem para consultar vários recursos do Application Insights recomendada 
Listagem vários recursos do Application Insights numa consulta pode ser complicado e difícil de manter. Em vez disso, pode aproveitar a função para separar a lógica de consulta a partir das aplicações de âmbito.  

Este exemplo demonstra como pode monitorizar vários recursos do Application Insights e visualizar a contagem de pedidos falhados por nome de aplicação. Antes de iniciar, execute esta consulta na área de trabalho que está ligada a recursos do Application Insights para obter a lista de aplicativos conectados: 

```
ApplicationInsights
| summarize by ApplicationName
```

Criar uma função com o operador union com a lista de aplicações, em seguida, guarde a consulta na sua área de trabalho como função com o alias *applicationsScoping*.  

```
union withsource=SourceApp 
app('Contoso-app1').requests,  
app('Contoso-app2').requests, 
app('Contoso-app3').requests, 
app('Contoso-app4').requests, 
app('Contoso-app5').requests 
| parse SourceApp with * "('" applicationName "')" *  
```

>[!NOTE]
>Pode modificar as aplicações listadas em qualquer altura no portal ao navegar para o Explorador de consultas na sua área de trabalho e selecionar a função de edição e, em seguida, salvar ou, usando o `SavedSearch` cmdlet do PowerShell. O `withsource= SourceApp` comando adiciona uma coluna para os resultados que designa o aplicativo que enviou o registo. 
>
>A consulta usa o esquema do Application Insights, apesar da consulta é executada na área de trabalho, uma vez que a função de applicationsScoping devolve a estrutura de dados do Application Insights. 
>
>O operador de análise é opcional neste exemplo, extrai o nome da aplicação da propriedade SourceApp. 

Agora está pronto para usar a função de applicationsScoping na consulta entre recursos:  

```
applicationsScoping 
| where timestamp > ago(12h)
| where success == 'False'
| parse SourceApp with * '(' applicationName ')' * 
| summarize count() by applicationName, bin(timestamp, 1h) 
| render timechart
```

O alias de função devolve a União dos pedidos de todos os aplicativos definidos. A consulta, em seguida, filtra para pedidos falhados e visualiza as tendências pelo aplicativo.

![Exemplo de resultados da consulta entre](media/unify-app-resource-data/app-insights-query-results.png)

## <a name="query-across-application-insights-resources-and-workspace-data"></a>Consultar em recursos do Application Insights e área de trabalho de dados 
Quando parar o conector e a necessidade de executar consultas através de um intervalo de tempo foi reduzido pela retenção de dados do Application Insights (90 dias), terá de efetuar [consultas entre recursos](../../azure-monitor/log-query/cross-workspace-query.md) na área de trabalho e no Application Insights recursos para um período intermédio. Isso é até que os seus dados de aplicativos acumulem pela retenção de dados do Application Insights novo mencionada acima. A consulta requer alguns manipulações, uma vez que os esquemas no Application Insights e a área de trabalho são diferentes. Consulte a tabela mais tarde nesta secção, realçando as diferenças de esquema. 

>[!NOTE]
>[Entre recursos consulta](../log-query/cross-workspace-query.md) no registo de alertas é suportado no novo [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). Por predefinição, o Azure Monitor utiliza o [herdados API de alerta do Log Analytics](../platform/api-alerts.md) para a criação de novo log de regras de alerta no portal do Azure, a menos que alternar do [API herdada de alertas de registo](../platform/alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api). Após a mudança, a nova API se tornará o padrão de novas regras de alerta no portal do Azure e permite-lhe criar regras de alertas do log de consulta de entre recursos. Pode criar [entre recursos consulta](../log-query/cross-workspace-query.md) regras de alerta de registo sem fazer o comutador, utilizando o [modelo ARM para scheduledQueryRules API](../platform/alerts-log.md#log-alert-with-cross-resource-query-using-azure-resource-template) –, mas esta regra de alerta é gerenciável no entanto [ scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) e não no portal do Azure.

Por exemplo, se o conector parado trabalhando em 2018-11-01, quando consultar os registos em todos os dados de aplicações e recursos do Application Insights na área de trabalho, seria possível construir a consulta semelhante ao seguinte exemplo:

```
applicationsScoping //this brings data from Application Insights resources 
| where timestamp between (datetime("2018-11-01") .. now()) 
| where success == 'False' 
| where duration > 1000 
| union ( 
    ApplicationInsights //this is Application Insights data in Log Analytics workspace 
    | where TimeGenerated < (datetime("2018-12-01") 
    | where RequestSuccess == 'False' 
    | where RequestDuration > 1000 
    | extend duration = RequestDuration //align to Application Insights schema 
    | extend timestamp = TimeGenerated //align to Application Insights schema 
    | extend name = RequestName //align to Application Insights schema 
    | extend resultCode = ResponseCode //align to Application Insights schema 
    | project-away RequestDuration , RequestName , ResponseCode , TimeGenerated 
) 
| project timestamp , duration , name , resultCode 
```

## <a name="application-insights-and-log-analytics-workspace-schema-differences"></a>Diferenças de esquema de área de trabalho do Application Insights e Log Analytics
A tabela seguinte mostra as diferenças de esquema entre o Log Analytics e Application Insights.  

| Propriedades da área de trabalho de análise de registo| Propriedades de recurso do Application Insights|
|------------|------------| 
| AnonUserId | user_id|
| ApplicationId | appId|
| ApplicationName | appName|
| ApplicationTypeVersion | application_Version |
| AvailabilityCount | itemCount |
| AvailabilityDuration | duração |
| AvailabilityMessage | message |
| AvailabilityRunLocation | localização |
| AvailabilityTestId | ID |
| AvailabilityTestName | nome |
| AvailabilityTimestamp | timestamp |
| Browser | client_browser |
| Localidade | client_city |
| ClientIP | client_IP |
| Computador | cloud_RoleInstance | 
| País | client_CountryOrRegion | 
| CustomEventCount | itemCount | 
| CustomEventDimensions | customDimensions |
| CustomEventName | nome | 
| DeviceModel | client_Model | 
| DeviceType | client_Type | 
| ExceptionCount | itemCount | 
| ExceptionHandledAt | handledAt |
| ExceptionMessage | message | 
| ExceptionType | tipo |
| OperationID | operation_id |
| OperationName | operation_Name | 
| SO | client_OS | 
| PageViewCount | itemCount |
| PageViewDuration | duração | 
| PageViewName | nome | 
| ParentOperationID | operation_Id | 
| RequestCount | itemCount | 
| RequestDuration | duração | 
| RequestID | ID | 
| RequestName | nome | 
| RequestSuccess | exito | 
| ResponseCode | resultCode | 
| Função | cloud_RoleName |
| RoleInstance | cloud_RoleInstance |
| SessionId | session_Id | 
| SourceSystem | operation_SyntheticSource |
| TelemetryTYpe | tipo |
| do IdP | _url |
| UserAccountId | user_AccountId |

## <a name="next-steps"></a>Passos Seguintes

Uso [pesquisa de registos](../../azure-monitor/log-query/log-query-overview.md) para ver informações detalhadas para as suas aplicações do Application Insights.
