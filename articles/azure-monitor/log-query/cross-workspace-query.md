---
title: Consultar entre recursos com Azure Monitor | Microsoft Docs
description: Este artigo descreve como você pode consultar recursos de vários espaços de trabalho e aplicativo do Application insights em sua assinatura.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/05/2019
ms.openlocfilehash: 0eaaf1157bf49068958bc07d17a23fc31dd99de0
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75365500"
---
# <a name="perform-cross-resource-log-queries-in-azure-monitor"></a>Executar consultas de log entre recursos no Azure Monitor  

Anteriormente, com Azure Monitor, você podia apenas analisar dados de dentro do espaço de trabalho atual e ele limitou sua capacidade de consultar em vários espaços de trabalho definidos em sua assinatura.  Além disso, você só podia pesquisar itens de telemetria coletados de seu aplicativo baseado na Web com Application Insights diretamente no Application Insights ou no Visual Studio. Isso também tornou um desafio para analisar nativamente os dados operacionais e de aplicativos juntos.

Agora você pode consultar não apenas em vários espaços de trabalho do Log Analytics, mas também dados de um aplicativo Application Insights específico no mesmo grupo de recursos, outro grupo de recursos ou outra assinatura. Isso fornece uma exibição de seus dados em todo o sistema. Você só pode executar esses tipos de consultas no [log Analytics](portals.md).

## <a name="cross-resource-query-limits"></a>Limites de consulta entre recursos 

* O número de recursos de Application Insights e espaços de trabalho de Log Analytics que você pode incluir em uma única consulta é limitado a 100.
* Não há suporte para a consulta entre recursos no designer de exibição. Você pode criar uma consulta em Log Analytics e fixá-la no painel do Azure para [Visualizar uma consulta de log](../learn/tutorial-logs-dashboards.md). 
* Há suporte para a consulta entre recursos em alertas de log na nova [API do scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). Por padrão, Azure Monitor usa a [API de alerta log Analytics herdada](../platform/api-alerts.md) para criar novas regras de alerta de log de portal do Azure, a menos que você mude da [API de alertas de log herdado](../platform/alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api). Após a opção, a nova API torna-se o padrão para novas regras de alerta no portal do Azure e permite criar regras de alertas de log de consulta entre recursos. Você pode criar regras de alerta de log de consulta de recurso cruzado sem fazer a alternância usando o [modelo de Azure Resource Manager para a API scheduledQueryRules](../platform/alerts-log.md#log-alert-with-cross-resource-query-using-azure-resource-template) – mas essa regra de alerta é gerenciável, embora a [API scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) e não de portal do Azure.


## <a name="querying-across-log-analytics-workspaces-and-from-application-insights"></a>Consultando em Log Analytics espaços de trabalho e de Application Insights
Para fazer referência a outro espaço de trabalho em sua consulta, use o identificador do [*espaço de trabalho*](https://docs.microsoft.com/azure/log-analytics/query-language/workspace-expression) e, para um aplicativo de Application insights, use o identificador do [*aplicativo*](https://docs.microsoft.com/azure/log-analytics/query-language/app-expression) .  

### <a name="identifying-workspace-resources"></a>Identificando recursos do espaço de trabalho
Os exemplos a seguir demonstram consultas em Log Analytics espaços de trabalho para retornar contagens resumidas de logs da tabela de atualização em um espaço de trabalho chamado *contosoretail-it*. 

A identificação de um espaço de trabalho pode ser realizada de várias maneiras:

* Nome do recurso – é um nome legível do espaço de trabalho, às vezes chamado de *nome de componente*. 

    `workspace("contosoretail-it").Update | count`

* Nome qualificado – é o "nome completo" do espaço de trabalho, composto pelo nome da assinatura, pelo grupo de recursos e pelo nome do componente neste formato: *subscriptionname/resourcegroup/ComponentName*. 

    `workspace('contoso/contosoretail/contosoretail-it').Update | count`

    >[!NOTE]
    >Como os nomes de assinatura do Azure não são exclusivos, esse identificador pode ser ambíguo. 
    >

* ID do espaço de trabalho-uma ID de espaço de trabalho é o identificador exclusivo e imutável atribuído a cada espaço de trabalho representado como um GUID (identificador global exclusivo).

    `workspace("b459b4u5-912x-46d5-9cb1-p43069212nb4").Update | count`

* ID de recurso do Azure – a identidade exclusiva definida pelo Azure do espaço de trabalho. Você usa a ID do recurso quando o nome do recurso é ambíguo.  Para espaços de trabalho, o formato é: */subscriptions/SubscriptionId/resourcegroups/resourceGroup/Providers/Microsoft. OperationalInsights/Workspaces/ComponentName*.  

    Por exemplo:
    ``` 
    workspace("/subscriptions/e427519-5645-8x4e-1v67-3b84b59a1985/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail-it").Update | count
    ```

### <a name="identifying-an-application"></a>Identificando um aplicativo
Os exemplos a seguir retornam uma contagem resumida de solicitações feitas em um aplicativo chamado *fabrikamapp* em Application insights. 

A identificação de um aplicativo no Application Insights pode ser realizada com a expressão do *aplicativo (identificador)* .  O argumento *Identifier* especifica o aplicativo usando um dos seguintes:

* Nome do recurso – é um nome legível por humanos do aplicativo, às vezes chamado de *nome do componente*.  

    `app("fabrikamapp")`

    >[!NOTE]
    >A identificação de um aplicativo por nome pressupõe exclusividade em todas as assinaturas acessíveis. Se você tiver vários aplicativos com o nome especificado, a consulta falhará devido à ambiguidade. Nesse caso, você deve usar um dos outros identificadores.

* Nome qualificado – é o "nome completo" do aplicativo, composto pelo nome da assinatura, pelo grupo de recursos e pelo nome do componente neste formato: *subscriptionname/resourcegroup/ComponentName*. 

    `app("AI-Prototype/Fabrikam/fabrikamapp").requests | count`

     >[!NOTE]
    >Como os nomes de assinatura do Azure não são exclusivos, esse identificador pode ser ambíguo. 
    >

* ID-o GUID do aplicativo.

    `app("b459b4f6-912x-46d5-9cb1-b43069212ab4").requests | count`

* ID de recurso do Azure-a identidade exclusiva definida pelo Azure do aplicativo. Você usa a ID do recurso quando o nome do recurso é ambíguo. O formato é: */subscriptions/SubscriptionId/resourcegroups/resourceGroup/Providers/Microsoft. OperationalInsights/Components/ComponentName*.  

    Por exemplo:
    ```
    app("/subscriptions/b459b4f6-912x-46d5-9cb1-b43069212ab4/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp").requests | count
    ```

### <a name="performing-a-query-across-multiple-resources"></a>Executando uma consulta em vários recursos
Você pode consultar vários recursos de qualquer uma de suas instâncias de recurso, que podem ser aplicativos e espaços de trabalho combinados.
    
Exemplo de consulta em dois espaços de trabalho:    

```
union Update, workspace("contosoretail-it").Update, workspace("b459b4u5-912x-46d5-9cb1-p43069212nb4").Update
| where TimeGenerated >= ago(1h)
| where UpdateState == "Needed"
| summarize dcount(Computer) by Classification
```

## <a name="using-cross-resource-query-for-multiple-resources"></a>Usando a consulta entre recursos para vários recursos
Ao usar consultas entre recursos para correlacionar dados de vários espaços de trabalho do Log Analytics e Application Insights recursos, a consulta pode se tornar complexa e difícil de manter. Você deve aproveitar as [funções em Azure monitor consultas de log](functions.md) para separar a lógica de consulta do escopo dos recursos de consulta, o que simplifica a estrutura de consulta. O exemplo a seguir demonstra como você pode monitorar vários Application Insights recursos e visualizar a contagem de solicitações com falha por nome do aplicativo. 

Crie uma consulta como a seguinte que faça referência ao escopo dos recursos de Application Insights. O comando `withsource= SourceApp` adiciona uma coluna que designa o nome do aplicativo que enviou o log. [Salve a consulta como função](functions.md#create-a-function) com o alias _applicationsScoping_.

```Kusto
// crossResource function that scopes my Application Insights resources
union withsource= SourceApp
app('Contoso-app1').requests, 
app('Contoso-app2').requests,
app('Contoso-app3').requests,
app('Contoso-app4').requests,
app('Contoso-app5').requests
```



Agora você pode [usar essa função](../../azure-monitor/log-query/functions.md#use-a-function) em uma consulta entre recursos, como a seguinte. O alias de função _applicationsScoping_ retorna a União da tabela de solicitações de todos os aplicativos definidos. Em seguida, a consulta filtra as solicitações com falha e visualiza as tendências por aplicativo. O operador _Parse_ é opcional neste exemplo. Ele extrai o nome do aplicativo da propriedade _SourceApp_ .

```Kusto
applicationsScoping 
| where timestamp > ago(12h)
| where success == 'False'
| parse SourceApp with * '(' applicationName ')' * 
| summarize count() by applicationName, bin(timestamp, 1h) 
| render timechart
```

>[!NOTE]
>Esse método não pode ser usado com alertas de log porque a validação de acesso dos recursos de regra de alerta, incluindo espaços de trabalho e aplicativos, é executada no momento da criação do alerta. Não há suporte para a adição de novos recursos à função após a criação do alerta. Se preferir usar a função para o escopo de recursos em alertas de log, você precisará editar a regra de alerta no portal ou com um modelo do Resource Manager para atualizar os recursos com escopo. Como alternativa, você pode incluir a lista de recursos na consulta de alerta de log.


![Timechart](media/cross-workspace-query/chart.png)

## <a name="next-steps"></a>Passos seguintes

- Examine [analisar dados de log em Azure monitor](log-query-overview.md) para obter uma visão geral das consultas de log e como Azure monitor dados de log são estruturados.
- Examine [Azure monitor consultas de log](query-language.md) para exibir todos os recursos para Azure monitor consultas de log.
