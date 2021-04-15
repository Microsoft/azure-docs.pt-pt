---
title: Consulta através de recursos com a Azure Monitor | Microsoft Docs
description: Este artigo descreve como pode consultar recursos de vários espaços de trabalho e app App Insights na sua subscrição.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/11/2021
ms.openlocfilehash: 19cc85751fc5e4a165b646ac89d9d6b6e90c4408
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379558"
---
# <a name="perform-log-query-in-azure-monitor-that-span-across-workspaces-and-apps"></a>Realize consulta de log no Azure Monitor que se estende por espaços de trabalho e apps

Azure Monitor Logs suporta consulta em vários espaços de trabalho do Log Analytics e aplicações De Insights de Aplicação no mesmo grupo de recursos, outro grupo de recursos ou outra subscrição. Isto fornece-lhe uma visão de todo o sistema dos seus dados.

Existem dois métodos para consultar dados que são armazenados em múltiplos espaços de trabalho e apps:
1. Explicitamente especificando o espaço de trabalho e os detalhes da aplicação. Esta técnica é detalhada neste artigo.
2. Implicitamente usando [consultas de contexto de recursos.](./design-logs-deployment.md#access-mode) Quando consultar no contexto de um recurso específico, grupo de recursos ou uma subscrição, os dados relevantes serão recolhidos de todos os espaços de trabalho que contenham dados para estes recursos. Os dados do Application Insights que são armazenados em apps, não serão recolhidos.

> [!IMPORTANT]
> Se estiver a utilizar uma telemetria [de recursos de aplicações baseada no espaço de trabalho,](../app/create-workspace-resource.md) é armazenada num espaço de trabalho do Log Analytics com todos os outros dados de registo. Utilize a expressão espaço de trabalho para escrever uma consulta que inclua aplicação em vários espaços de trabalho. Para várias aplicações no mesmo espaço de trabalho, você não precisa de uma consulta de espaço de trabalho transversal.


## <a name="cross-resource-query-limits"></a>Limites de consulta de recursos cruzados 

* O número de recursos de Application Insights e espaços de trabalho log Analytics que pode incluir numa única consulta está limitado a 100.
* A consulta de recursos cruzados não é suportada no View Designer. Pode autorizar uma consulta no Log Analytics e fixá-la no painel Azure para [visualizar uma consulta de registo](../visualize/tutorial-logs-dashboards.md) ou incluir em livros de [trabalho.](../visualize/workbooks-overview.md)
* As consultas de recursos cruzados nos alertas de registo só são suportadas na atual [API agendada para a API](/rest/api/monitor/scheduledqueryrules). Se estiver a utilizar o legado Log Analytics Alerts API, terá de [mudar para a API atual.](../alerts/alerts-log-api-switch.md)


## <a name="querying-across-log-analytics-workspaces-and-from-application-insights"></a>Consulta através de espaços de trabalho log analytics e de Insights de Aplicação
Para consultar outro espaço de trabalho na sua consulta, use o identificador de [*espaço de trabalho*](../logs/workspace-expression.md) e, para uma aplicação da Application Insights, utilize o identificador de [*aplicações.*](./app-expression.md)  

### <a name="identifying-workspace-resources"></a>Identificar recursos do espaço de trabalho
Os exemplos que se seguem demonstram consultas através dos espaços de trabalho do Log Analytics para devolver as contagens sumárias de registos da tabela Update num espaço de trabalho chamado *contosoretail-it*. 

Identificar um espaço de trabalho pode ser realizado de várias formas:

* Nome de recurso - é um nome legível pelo homem do espaço de trabalho, por vezes referido como *nome de componente*. 

    >[!IMPORTANT]
    >Como os nomes de apps e espaço de trabalho não são únicos, este identificador pode ser ambíguo. Recomenda-se que a referência seja por nome qualificado, ID do espaço de trabalho ou ID de recursos Azure.

    `workspace("contosoretail-it").Update | count`

* Nome qualificado - é o "nome completo" do espaço de trabalho, composto pelo nome de assinatura, grupo de recursos e nome componente neste formato: *subscriptionName/resourceGroup/componentName*. 

    `workspace('contoso/contosoretail/contosoretail-it').Update | count`

    >[!NOTE]
    >Como os nomes de subscrição do Azure não são únicos, este identificador pode ser ambíguo.

* Workspace ID - Um ID de espaço de trabalho é o identificador único, imutável, atribuído a cada espaço de trabalho representado como um identificador globalmente único (GUID).

    `workspace("b459b4u5-912x-46d5-9cb1-p43069212nb4").Update | count`

* Azure Resource ID – a identidade única definida pelo Azure do espaço de trabalho. Utiliza o ID do recurso quando o nome do recurso é ambíguo.  Para espaços de trabalho, o formato é: */subscrições/subscriçãoId/grupos de recursos/resourceGroup/providers/microsoft. OperacionalInsights/workspaces/componentName*.  

    Por exemplo:
    ``` 
    workspace("/subscriptions/e427519-5645-8x4e-1v67-3b84b59a1985/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail-it").Update | count
    ```

### <a name="identifying-an-application"></a>Identificar uma aplicação
Os exemplos seguintes devolvem uma contagem resumida de pedidos feitos contra uma aplicação chamada *fabrikamapp* em Application Insights. 

A identificação de uma aplicação no Application Insights pode ser realizada com a expressão da *aplicação (Identifier).*  O argumento *do Identificador* especifica a aplicação utilizando uma das seguintes:

* Nome de recurso - é um nome legível humano da app, por vezes referido como o nome do *componente*.  

    `app("fabrikamapp")`

    >[!NOTE]
    >Identificar uma aplicação pelo nome pressupõe singularidade em todas as subscrições acessíveis. Se tiver várias aplicações com o nome especificado, a consulta falha devido à ambiguidade. Neste caso, deve usar um dos outros identificadores.

* Nome qualificado - é o "nome completo" da app, composta pelo nome de subscrição, grupo de recursos e nome componente neste formato: *nome de subscriçãoName/recursoGroup/componenteName*. 

    `app("AI-Prototype/Fabrikam/fabrikamapp").requests | count`

     >[!NOTE]
    >Como os nomes de subscrição do Azure não são únicos, este identificador pode ser ambíguo. 
    >

* ID - a aplicação GUID da aplicação.

    `app("b459b4f6-912x-46d5-9cb1-b43069212ab4").requests | count`

* Azure Resource ID - a identidade única definida pelo Azure da aplicação. Utiliza o ID do recurso quando o nome do recurso é ambíguo. O formato é: */subscrições/subscriçãoId/grupos de recursos/resourceGroup/providers/microsoft. OperacionalInsights/componentes/componenteName*.  

    Por exemplo:
    ```
    app("/subscriptions/b459b4f6-912x-46d5-9cb1-b43069212ab4/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp").requests | count
    ```

### <a name="performing-a-query-across-multiple-resources"></a>Realização de uma consulta através de múltiplos recursos
Pode consultar vários recursos de qualquer uma das suas instâncias de recursos, estes podem ser espaços de trabalho e aplicações combinadas.
    
Exemplo para consulta em dois espaços de trabalho:    

```
union Update, workspace("contosoretail-it").Update, workspace("b459b4u5-912x-46d5-9cb1-p43069212nb4").Update
| where TimeGenerated >= ago(1h)
| where UpdateState == "Needed"
| summarize dcount(Computer) by Classification
```

## <a name="using-cross-resource-query-for-multiple-resources"></a>Utilização de consulta de recursos cruzados para múltiplos recursos
Ao utilizar consultas de recursos cruzados para correlacionar dados de vários espaços de trabalho do Log Analytics e recursos de Insights de Aplicação, a consulta pode tornar-se complexa e difícil de manter. Deve aproveitar as funções nas consultas de registo do [Azure Monitor](./functions.md) para separar a lógica de consulta da deteção dos recursos de consulta, o que simplifica a estrutura de consulta. O exemplo a seguir demonstra como pode monitorizar vários recursos de Insights de Aplicação e visualizar a contagem de pedidos falhados pelo nome da aplicação. 

Crie uma consulta como a seguinte que faz referência ao âmbito dos recursos de Insights de Aplicação. O `withsource= SourceApp` comando adiciona uma coluna que designa o nome de aplicação que enviou o registo. [Guarde a consulta em função](./functions.md#create-a-function) das aplicações de _pseudónimoS_.

```Kusto
// crossResource function that scopes my Application Insights resources
union withsource= SourceApp
app('Contoso-app1').requests, 
app('Contoso-app2').requests,
app('Contoso-app3').requests,
app('Contoso-app4').requests,
app('Contoso-app5').requests
```



Pode agora [utilizar esta função](./functions.md#use-a-function) numa consulta de recursos cruzados como as seguintes. As aplicações de _alias funçãoScoping_ devolve a união da tabela de pedidos de todas as candidaturas definidas. A consulta então filtra para pedidos falhados e visualiza as tendências por aplicação. O operador _de parse_ é opcional neste exemplo. Extrai o nome da aplicação da propriedade _SourceApp._

```Kusto
applicationsScoping 
| where timestamp > ago(12h)
| where success == 'False'
| parse SourceApp with * '(' applicationName ')' * 
| summarize count() by applicationName, bin(timestamp, 1h) 
| render timechart
```

>[!NOTE]
> Este método não pode ser usado com alertas de registo porque a validação de acesso dos recursos da regra de alerta, incluindo espaços de trabalho e aplicações, é realizada no momento de criação de alerta. A adição de novos recursos à função após a criação de alerta não é suportada. Se preferir utilizar a função para a deteção de recursos em alertas de registo, tem de editar a regra de alerta no portal ou com um modelo de Gestor de Recursos para atualizar os recursos agrafado. Em alternativa, pode incluir a lista de recursos na consulta de alerta de registo.


![Timechart](media/cross-workspace-query/chart.png)

## <a name="next-steps"></a>Passos seguintes

- [Analise os dados de registo no Azure Monitor](./log-query-overview.md) para obter uma visão geral das consultas de registo e como os dados de registo do Azure Monitor são estruturados.