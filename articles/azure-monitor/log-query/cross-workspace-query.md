---
title: Consulta todos os recursos com o Azure Monitor | Documentos da Microsoft
description: Este artigo descreve como pode consultar relativamente aos recursos a partir de várias áreas de trabalho e aplicações do App Insights na sua subscrição.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: magoedte
ms.openlocfilehash: d69ddcd44fd947f3d1dc61ac960e7b55258c163e
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56872017"
---
# <a name="perform-cross-resource-log-queries-in-azure-monitor"></a>Executar consultas de registo entre recursos no Azure Monitor  

Anteriormente com o Azure Monitor, conseguia apenas analisar os dados a partir de área de trabalho atual e limitada a capacidade de consulta em várias áreas de trabalho definidas na sua subscrição.  Além disso, pode pesquisar apenas os itens de telemetria recolhidos a partir da sua aplicação baseada na web com o Application Insights diretamente no Application Insights ou a partir do Visual Studio.  Isso também tornou um desafio para nativamente analisar operacional e os dados das aplicações em conjunto.   

Agora, pode consultar não apenas em várias áreas de trabalho do Log Analytics, mas também dados a partir de uma aplicação específica do Application Insights no mesmo grupo de recursos, outro grupo de recursos ou outra subscrição. Isso fornece uma vista de todo o sistema dos seus dados.  Só pode realizar estes tipos de consultas [do Log Analytics](portals.md).

## <a name="cross-resource-query-limits"></a>Limites de consulta entre recursos 

* O número de recursos do Application Insights que podem ser incluídos numa única consulta está limitado a 100.
* Entre recursos consulta não é suportada no estruturador de vistas. Pode criar uma consulta do Log Analytics e afixá-la ao dashboard do Azure e [visualizar uma pesquisa de registos](../../azure-monitor/learn/tutorial-logs-dashboards.md#visualize-a-log-search). 
* Consulta de entre recursos nos alertas de registo é suportada no novo [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). Por predefinição, o Azure Monitor utiliza o [herdados API de alerta do Log Analytics](../platform/api-alerts.md) para a criação de novo log de regras de alerta no portal do Azure, a menos que alternar do [API herdada de alertas de registo](../platform/alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api). Após a mudança, a nova API se tornará o padrão de novas regras de alerta no portal do Azure e permite-lhe criar regras de alertas do log de consulta de entre recursos. Log de consulta entre recursos pode criar regras de alerta sem fazendo a mudança, utilizando o [modelo ARM para scheduledQueryRules API](../platform/alerts-log.md#log-alert-with-cross-resource-query-using-azure-resource-template) –, mas esta regra de alerta é gerenciável entanto [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) e não no portal do Azure.


## <a name="querying-across-log-analytics-workspaces-and-from-application-insights"></a>Consultar em áreas de trabalho do Log Analytics e o Application insights
Para fazer referência a outra área de trabalho na sua consulta, utilize o [ *área de trabalho* ](https://docs.microsoft.com/azure/log-analytics/query-language/workspace-expression) identificador e para uma aplicação do Application Insights, utilize o [ *aplicação* ](https://docs.microsoft.com/azure/log-analytics/query-language/app-expression)identificador.  

### <a name="identifying-workspace-resources"></a>Identificar os recursos de área de trabalho
Os exemplos seguintes demonstram consultas em áreas de trabalho do Log Analytics a devolver resumidas contagens dos registos da tabela de atualização numa área de trabalho com o nome *contosoretail-it*. 

Identificar uma área de trabalho pode ser feita de várias formas:

* Nome do recurso - é um nome legível por humanos da área de trabalho, por vezes denominado *nome do componente*. 

    `workspace("contosoretail-it").Update | count`
 
    >[!NOTE]
    >Identificar uma área de trabalho por nome de parte do princípio de exclusividade em todas as subscrições acessíveis. Se tiver várias aplicações com o nome especificado, a consulta falha devido a ambiguidade. Neste caso, tem de utilizar um dos outros identificadores.

* O nome qualificado - é o "nome completo" da área de trabalho, composto pelo nome da subscrição, o grupo de recursos e o nome do componente no seguinte formato: *subscriptionName/resourceGroup/componentName*. 

    `workspace('contoso/contosoretail/contosoretail-it').Update | count `

    >[!NOTE]
    >Uma vez que os nomes de subscrição do Azure não são exclusivos, este identificador pode ser ambígua. 
    >

* ID da área de trabalho – um ID de área de trabalho é o identificador exclusivo e imutável, atribuído a cada área de trabalho representada como um identificador exclusivo global (GUID).

    `workspace("b459b4u5-912x-46d5-9cb1-p43069212nb4").Update | count`

* ID do Azure de recurso – a identidade exclusiva definido pelo Azure da área de trabalho. Utilize o ID de recurso quando o nome do recurso é ambíguo.  Para áreas de trabalho, o formato é: */subscriptions/subscriptionId/resourcegroups/resourceGroup/providers/microsoft. Áreas de trabalho/OperationalInsights/componentName*.  

    Por exemplo:
    ``` 
    workspace("/subscriptions/e427519-5645-8x4e-1v67-3b84b59a1985/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail-it").Update | count
    ```

### <a name="identifying-an-application"></a>Identificar uma aplicação
Os exemplos seguintes podem devolver uma contagem resumida de pedidos efetuados em relação a uma aplicação com o nome *fabrikamapp* no Application Insights. 

Identificar uma aplicação no Application Insights pode ser feito com o *app(Identifier)* expressão.  O *identificador* argumento especifica a aplicação com um dos seguintes:

* Nome do recurso - é um nome legível por humano da aplicação, por vezes referido como o *nome do componente*.  

    `app("fabrikamapp")`

* O nome qualificado - é o "nome completo" da aplicação, composto pelo nome da subscrição, o grupo de recursos e o nome do componente no seguinte formato: *subscriptionName/resourceGroup/componentName*. 

    `app("AI-Prototype/Fabrikam/fabrikamapp").requests | count`

     >[!NOTE]
    >Uma vez que os nomes de subscrição do Azure não são exclusivos, este identificador pode ser ambígua. 
    >

* ID - o GUID da aplicação da aplicação.

    `app("b459b4f6-912x-46d5-9cb1-b43069212ab4").requests | count`

* ID do Azure de recurso - identidade exclusiva definido pelo Azure da aplicação. Utilize o ID de recurso quando o nome do recurso é ambíguo. O formato é: */subscriptions/subscriptionId/resourcegroups/resourceGroup/providers/microsoft. Componentes/OperationalInsights/componentName*.  

    Por exemplo:
    ```
    app("/subscriptions/b459b4f6-912x-46d5-9cb1-b43069212ab4/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp").requests | count
    ```

### <a name="performing-a-query-across-multiple-resources"></a>A realização de uma consulta em vários recursos
Pode consultar vários recursos a partir de qualquer uma das suas instâncias de recursos, estes podem ser aplicações e áreas de trabalho combinadas.
    
Exemplo de consulta em duas áreas de trabalho:    

```
union Update, workspace("contosoretail-it").Update, workspace("b459b4u5-912x-46d5-9cb1-p43069212nb4").Update
| where TimeGenerated >= ago(1h)
| where UpdateState == "Needed"
| summarize dcount(Computer) by Classification
```

## <a name="using-cross-resource-query-for-multiple-resources"></a>Usando a consulta de entre recursos de vários recursos
Ao usar consultas entre recursos para correlacionar dados a partir de várias áreas de trabalho do Log Analytics e recursos do Application Insights, a consulta pode tornar-se complexa e difícil de manter. Deve aproveitar [consultas de registo de funções no Azure Monitor](functions.md) para separar a lógica de consulta de âmbito dos recursos de consulta, que simplifica a estrutura de consulta. O exemplo seguinte demonstra como pode monitorizar vários recursos do Application Insights e visualizar a contagem de pedidos falhados por nome de aplicação. 

Crie uma consulta semelhante ao seguinte que referencia o âmbito de recursos do Application Insights. O `withsource= SourceApp` comando adiciona uma coluna que designa o nome da aplicação que enviou o registo. [Guardar consulta como função](functions.md#create-a-function) com o alias _applicationsScoping_.

```Kusto
// crossResource function that scopes my Application Insights resources
union withsource= SourceApp
app('Contoso-app1').requests, 
app('Contoso-app2').requests,
app('Contoso-app3').requests,
app('Contoso-app4').requests,
app('Contoso-app5').requests
```



Agora, pode [utilizar esta função](../../azure-monitor/log-query/functions.md#use-a-function) numa consulta entre recursos como o seguinte. O alias de função _applicationsScoping_ devolve a União da tabela de pedidos de todos os aplicativos definidos. A consulta, em seguida, filtra para pedidos falhados e visualiza as tendências pelo aplicativo. O _analisar_ operador é opcional neste exemplo. Extrai o nome da aplicação do _SourceApp_ propriedade.

```Kusto
applicationsScoping 
| where timestamp > ago(12h)
| where success == 'False'
| parse SourceApp with * '(' applicationName ')' * 
| summarize count() by applicationName, bin(timestamp, 1h) 
| render timechart
```
![Pego](media/cross-workspace-query/chart.png)

## <a name="next-steps"></a>Passos Seguintes

- Revisão [analisar dados de registo no Azure Monitor](log-query-overview.md) para uma descrição geral de consultas de registo e como os dados de registo do Azure Monitor são estruturados.
- Revisão [consultas de registo do Azure Monitor](query-language.md) para ver todos os recursos para consultas de registo do Azure Monitor.
