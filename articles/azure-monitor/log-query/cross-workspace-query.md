---
title: Consulta entre recursos com o Monitor Azure [ Monitor De Adc) Microsoft Docs
description: Este artigo descreve como pode consultar recursos de vários espaços de trabalho e app App Insights na sua subscrição.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/05/2019
ms.openlocfilehash: 4740034bd970f42833125fa43bfdf72f710ac147
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79249610"
---
# <a name="perform-cross-resource-log-queries-in-azure-monitor"></a>Realizar consultas de registo de recursos cruzados no Monitor Azure  

Anteriormente com o Monitor Azure, só era possível analisar dados de dentro do espaço de trabalho atual, e limitava a sua capacidade de consulta em vários espaços de trabalho definidos na sua subscrição.  Além disso, só foi possível pesquisar artigos de telemetria recolhidos a partir da sua aplicação baseada na Web com insights de aplicação diretamente em Application Insights ou no Visual Studio. Isto também o tornou um desafio para analisar de forma nativa os dados operacionais e de aplicação em conjunto.

Agora pode consultar não só vários espaços de trabalho do Log Analytics, mas também dados de uma aplicação específica de Application Insights no mesmo grupo de recursos, outro grupo de recursos ou outra subscrição. Isto fornece-lhe uma visão global dos seus dados. Só é possível realizar este tipo de consultas no [Log Analytics](portals.md).

## <a name="cross-resource-query-limits"></a>Limites de consulta de recursos cruzados 

* O número de recursos de Application Insights e espaços de trabalho de Log Analytics que pode incluir numa única consulta está limitado a 100.
* A consulta de recursos cruzados não é suportada no View Designer. Pode escrever uma consulta no Log Analytics e fixá-la no painel azure para [visualizar uma consulta](../learn/tutorial-logs-dashboards.md)de log . 
* A consulta de recursos cruzados em alertas de registo é suportada na nova [API agendadaQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). Por padrão, o Azure Monitor utiliza o [legado Log Analytics Alert API](../platform/api-alerts.md) para criar novas regras de alerta de log a partir do portal Azure, a menos que troque do legado Log [Alerts API](../platform/alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api). Após a troca, a nova API torna-se o padrão para novas regras de alerta no portal Azure e permite criar regras de alerta de consulta de recursos cruzados. Pode criar regras de alerta de consulta de cross-resource sem fazer a troca utilizando o modelo do Gestor de [Recursos Azure para a API agendada](../platform/alerts-log.md#log-alert-with-cross-resource-query-using-azure-resource-template) – mas esta regra de alerta é controlável apesar da [API agendada](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) e não do portal Azure.


## <a name="querying-across-log-analytics-workspaces-and-from-application-insights"></a>Consulta em espaços de trabalho log Analytics e a partir de Insights de Aplicação
Para fazer referência a outro espaço de trabalho na sua consulta, utilize o identificador espaço de [*trabalho*](https://docs.microsoft.com/azure/log-analytics/query-language/workspace-expression) e, para uma aplicação da Application Insights, utilize o identificador da [*aplicação.*](https://docs.microsoft.com/azure/log-analytics/query-language/app-expression)  

### <a name="identifying-workspace-resources"></a>Identificação de recursos espaciais de trabalho
Os exemplos seguintes demonstram consultas em espaços de trabalho da Log Analytics para devolver contagens resumidas de registos da tabela Update num espaço de trabalho chamado *contosoretail-it*. 

Identificar um espaço de trabalho pode ser realizado de várias formas:

* Nome de recurso - é um nome legível pelo homem do espaço de trabalho, por vezes referido como *nome do componente*. 

    `workspace("contosoretail-it").Update | count`

* Nome qualificado - é o "nome completo" do espaço de trabalho, composto pelo nome de subscrição, grupo de recursos e nome do componente neste formato: *subscriçãoNome/recursoGroup/componentName*. 

    `workspace('contoso/contosoretail/contosoretail-it').Update | count`

    >[!NOTE]
    >Como os nomes de subscrição do Azure não são únicos, este identificador pode ser ambíguo. 
    >

* ID do espaço de trabalho - Um ID espaço de trabalho é o identificador único, imutável e identificador atribuído a cada espaço de trabalho representado como um identificador globalmente único (GUID).

    `workspace("b459b4u5-912x-46d5-9cb1-p43069212nb4").Update | count`

* Azure Resource ID – a identidade única definida pelo Azure do espaço de trabalho. Usa o ID de recurso quando o nome do recurso é ambíguo.  Para espaços de trabalho, o formato é: */subscrições/subscriçõesId/resourcegroups/resourceGroup/providers/microsoft. OperationalInsights/espaços de trabalho/componentName*.  

    Por exemplo:
    ``` 
    workspace("/subscriptions/e427519-5645-8x4e-1v67-3b84b59a1985/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail-it").Update | count
    ```

### <a name="identifying-an-application"></a>Identificação de uma aplicação
Os exemplos seguintes devolvem uma contagem resumida de pedidos feitos contra uma aplicação chamada *fabrikamapp* em Application Insights. 

A identificação de uma aplicação em Insights de Aplicação pode ser realizada com a expressão *da aplicação (Identificador).*  O argumento *do identificador* especifica a aplicação utilizando um dos seguintes:

* Nome de recurso - é um nome legível humano da app, por vezes referido como o nome do *componente*.  

    `app("fabrikamapp")`

    >[!NOTE]
    >Identificar uma aplicação por nome pressupõe uma singularidade em todas as subscrições acessíveis. Se tiver várias aplicações com o nome especificado, a consulta falha devido à ambiguidade. Neste caso, deve usar um dos outros identificadores.

* Nome qualificado - é o "nome completo" da app, composto pelo nome de subscrição, grupo de recursos e nome do componente neste formato: *subscriçãoNome/recursoGroup/componentName*. 

    `app("AI-Prototype/Fabrikam/fabrikamapp").requests | count`

     >[!NOTE]
    >Como os nomes de subscrição do Azure não são únicos, este identificador pode ser ambíguo. 
    >

* ID - a aplicação GUID da aplicação.

    `app("b459b4f6-912x-46d5-9cb1-b43069212ab4").requests | count`

* Azure Resource ID - a identidade única definida pelo Azure da app. Usa o ID de recurso quando o nome do recurso é ambíguo. O formato é: */subscrições/subscriçõesId/resourcegroups/resourceGroup/providers/microsoft. OperationalInsights/componentes/componentName*.  

    Por exemplo:
    ```
    app("/subscriptions/b459b4f6-912x-46d5-9cb1-b43069212ab4/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp").requests | count
    ```

### <a name="performing-a-query-across-multiple-resources"></a>Realizar uma consulta em vários recursos
Você pode consultar vários recursos de qualquer um dos seus casos de recursos, estes podem ser espaços de trabalho e aplicações combinados.
    
Exemplo para consulta em dois espaços de trabalho:    

```
union Update, workspace("contosoretail-it").Update, workspace("b459b4u5-912x-46d5-9cb1-p43069212nb4").Update
| where TimeGenerated >= ago(1h)
| where UpdateState == "Needed"
| summarize dcount(Computer) by Classification
```

## <a name="using-cross-resource-query-for-multiple-resources"></a>Usando consulta de recursos cruzados para vários recursos
Ao utilizar consultas de recursos cruzados para correlacionar dados de vários espaços de trabalho do Log Analytics e recursos de Aplicação Insights, a consulta pode tornar-se complexa e difícil de manter. Deve aproveitar funções em consultas de [registo do Monitor Azure](functions.md) para separar a lógica de consulta da deteção dos recursos de consulta, o que simplifica a estrutura de consulta. O exemplo que se segue demonstra como pode monitorizar vários recursos do Application Insights e visualizar a contagem de pedidos falhados pelo nome da aplicação. 

Criar uma consulta como a seguinte que referencia o âmbito dos recursos da Application Insights. O `withsource= SourceApp` comando adiciona uma coluna que designa o nome da aplicação que enviou o registo. [Guarde a consulta em função](functions.md#create-a-function) com as aplicações do _pseudónimoSS ._

```Kusto
// crossResource function that scopes my Application Insights resources
union withsource= SourceApp
app('Contoso-app1').requests, 
app('Contoso-app2').requests,
app('Contoso-app3').requests,
app('Contoso-app4').requests,
app('Contoso-app5').requests
```



Agora pode [utilizar esta função](../../azure-monitor/log-query/functions.md#use-a-function) numa consulta de recurso cruzado como a seguinte. A função pseudónimo _aplicaçõesScoping_ devolve a união do quadro de pedidos de todas as aplicações definidas. A consulta filtra então pedidos falhados e visualiza as tendências por aplicação. Neste exemplo, o operador _de parse_ é opcional. Extrai o nome da aplicação da propriedade _SourceApp._

```Kusto
applicationsScoping 
| where timestamp > ago(12h)
| where success == 'False'
| parse SourceApp with * '(' applicationName ')' * 
| summarize count() by applicationName, bin(timestamp, 1h) 
| render timechart
```

>[!NOTE]
>Este método não pode ser utilizado com alertas de registo porque a validação de acesso dos recursos de regra de alerta, incluindo espaços de trabalho e aplicações, é realizada em tempo de criação de alerta. Adicionar novos recursos à função após a criação de alerta não é suportado. Se preferir utilizar a função de deteção de recursos em alertas de registo, tem de editar a regra de alerta no portal ou com um modelo de Gestor de Recursos para atualizar os recursos com âmbito de aplicação. Em alternativa, pode incluir a lista de recursos na consulta de alerta de registo.


![Gráfico de tempo](media/cross-workspace-query/chart.png)

## <a name="next-steps"></a>Passos seguintes

- Reveja os dados de [log do Monitor de Análise no Monitor Azure](log-query-overview.md) para uma visão geral das consultas de registo e como os dados de registo do Monitor Do Azure são estruturados.
- Reveja as consultas de registo do [Monitor Azure](query-language.md) para visualizar todos os recursos para consultas de registo do Monitor Azure.
