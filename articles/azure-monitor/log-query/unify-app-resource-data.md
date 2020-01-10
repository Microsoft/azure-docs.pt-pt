---
title: Unificar vários recursos de Application Insights de Azure Monitor | Microsoft Docs
description: Este artigo fornece detalhes sobre como usar uma função nos logs de Azure Monitor para consultar vários recursos de Application Insights e visualizar esses dados.
author: bwren
ms.author: bwren
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/19/2019
ms.openlocfilehash: 07dd4c96ba51b1ac1e0cb2807c9e26df87a6daa7
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75364973"
---
# <a name="unify-multiple-azure-monitor-application-insights-resources"></a>Unificar vários recursos de Application Insights Azure Monitor 
Este artigo descreve como consultar e exibir todos os seus Application Insights dados de log em um único local, mesmo quando eles estão em assinaturas diferentes do Azure, como uma substituição para a reprovação da Conector do Application Insights. O número de recursos de Application Insights que você pode incluir em uma única consulta é limitado a 100.

## <a name="recommended-approach-to-query-multiple-application-insights-resources"></a>Abordagem recomendada para consultar vários recursos de Application Insights 
A listagem de vários recursos Application Insights em uma consulta pode ser complicada e difícil de manter. Em vez disso, você pode aproveitar a função para separar a lógica de consulta do escopo de aplicativos.  

Este exemplo demonstra como você pode monitorar vários recursos do Application Insights e visualizar a contagem de solicitações com falha por nome do aplicativo. Antes de começar, execute esta consulta no espaço de trabalho que está conectado a Application Insights recursos para obter a lista de aplicativos conectados: 

```
ApplicationInsights
| summarize by ApplicationName
```

Crie uma função usando o operador Union com a lista de aplicativos e salve a consulta em seu espaço de trabalho como função com o alias *applicationsScoping*. 

Você pode modificar os aplicativos listados a qualquer momento no portal navegando até o Gerenciador de consultas no seu espaço de trabalho e selecionando a função para editar e salvar ou usar o cmdlet `SavedSearch` PowerShell. 

>[!NOTE]
>Esse método não pode ser usado com alertas de log porque a validação de acesso dos recursos de regra de alerta, incluindo espaços de trabalho e aplicativos, é executada no momento da criação do alerta. Não há suporte para a adição de novos recursos à função após a criação do alerta. Se preferir usar a função para o escopo de recursos em alertas de log, você precisará editar a regra de alerta no portal ou com um modelo do Resource Manager para atualizar os recursos com escopo. Como alternativa, você pode incluir a lista de recursos na consulta de alerta de log.

O comando `withsource= SourceApp` adiciona uma coluna aos resultados que designa o aplicativo que enviou o log. O operador Parse é opcional neste exemplo e usa para extrair o nome do aplicativo da propriedade SourceApp. 

```
union withsource=SourceApp 
app('Contoso-app1').requests,  
app('Contoso-app2').requests, 
app('Contoso-app3').requests, 
app('Contoso-app4').requests, 
app('Contoso-app5').requests 
| parse SourceApp with * "('" applicationName "')" *  
```

Agora você está pronto para usar a função applicationsScoping na consulta entre recursos:  

```
applicationsScoping 
| where timestamp > ago(12h)
| where success == 'False'
| parse SourceApp with * '(' applicationName ')' * 
| summarize count() by applicationName, bin(timestamp, 1h) 
| render timechart
```

A consulta usa Application Insights esquema, embora a consulta seja executada no espaço de trabalho, já que a função applicationsScoping retorna a estrutura de dados de Application Insights. O alias da função retorna a União das solicitações de todos os aplicativos definidos. Em seguida, a consulta filtra as solicitações com falha e visualiza as tendências por aplicativo.

![Exemplo de resultados de consulta cruzada](media/unify-app-resource-data/app-insights-query-results.png)

## <a name="query-across-application-insights-resources-and-workspace-data"></a>Consultar em Application Insights recursos e dados de espaço de trabalho 
Quando você interrompe o conector e precisa executar consultas em um intervalo de tempo que foi cortado por Application Insights retenção de dados (90 dias), você precisa executar [consultas entre recursos](../../azure-monitor/log-query/cross-workspace-query.md) no espaço de trabalho e Application insights recursos para um período intermediário. Isso ocorre até que os dados de seus aplicativos se acumulem de acordo com a nova retenção de dados Application Insights mencionada acima. A consulta requer algumas manipulações, pois os esquemas em Application Insights e o espaço de trabalho são diferentes. Consulte a tabela mais adiante nesta seção realçando as diferenças de esquema. 

>[!NOTE]
>Há suporte para a [consulta entre recursos](../log-query/cross-workspace-query.md) em alertas de log na nova [API do scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). Por padrão, Azure Monitor usa a [API de alerta log Analytics herdada](../platform/api-alerts.md) para criar novas regras de alerta de log de portal do Azure, a menos que você mude da [API de alertas de log herdado](../platform/alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api). Após a opção, a nova API torna-se o padrão para novas regras de alerta no portal do Azure e permite criar regras de alertas de log de consulta entre recursos. Você pode criar regras de alerta do log de [consultas entre recursos](../log-query/cross-workspace-query.md) sem fazer a alternância usando o [modelo ARM para a API scheduledQueryRules](../platform/alerts-log.md#log-alert-with-cross-resource-query-using-azure-resource-template) – mas essa regra de alerta é gerenciável, embora a [api scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) e não de portal do Azure.

Por exemplo, se o conector parou de funcionar em 2018-11-01, quando você consulta logs entre Application Insights recursos e dados de aplicativos no espaço de trabalho, sua consulta seria construída como o exemplo a seguir:

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

## <a name="application-insights-and-log-analytics-workspace-schema-differences"></a>Diferenças de esquema de espaço de trabalho Application Insights e Log Analytics
A tabela a seguir mostra as diferenças de esquema entre Log Analytics e Application Insights.  

| Propriedades do espaço de trabalho Log Analytics| Propriedades do recurso Application Insights|
|------------|------------| 
| AnonUserId | user_id|
| ApplicationId | appId|
| ApplicationName | appName|
| ApplicationTypeVersion | application_Version |
| AvailabilityCount | itemCount |
| AvailabilityDuration | duration |
| AvailabilityMessage | message |
| AvailabilityRunLocation | localização |
| AvailabilityTestId | ID |
| AvailabilityTestName | nome |
| AvailabilityTimestamp | carimbo de data/hora |
| Browser | client_browser |
| Cidade | client_city |
| ClientIP | client_IP |
| Computador | cloud_RoleInstance | 
| País | client_CountryOrRegion | 
| CustomEventCount | itemCount | 
| CustomEventDimensions | customDimensions |
| CustomEventName | nome | 
| DeviceModel | client_Model | 
| deviceType | client_Type | 
| ExceptionCount | itemCount | 
| ExceptionHandledAt | handledAt |
| ExceptionMessage | message | 
| ExceptionType | tipo |
| OperationID | operation_id |
| OperationName | operation_Name | 
| SO | client_OS | 
| PageViewCount | itemCount |
| PageViewDuration | duration | 
| PageViewName | nome | 
| ParentOperationID | operation_Id | 
| RequestCount | itemCount | 
| RequestDuration | duration | 
| RequestID | ID | 
| RequestName | nome | 
| RequestSuccess | exito | 
| ResponseCode | resultCode | 
| Função | cloud_RoleName |
| RoleInstance | cloud_RoleInstance |
| SessionId | session_Id | 
| SourceSystem | operation_SyntheticSource |
| Telemetria | tipo |
| URL | url |
| UserAccountId | user_AccountId |

## <a name="next-steps"></a>Passos seguintes

Uso [pesquisa de registos](../../azure-monitor/log-query/log-query-overview.md) para ver informações detalhadas para as suas aplicações do Application Insights.
