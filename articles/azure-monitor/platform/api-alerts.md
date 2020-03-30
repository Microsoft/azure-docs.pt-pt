---
title: Utilização de Log Analytics Alert REST API
description: O Log Analytics Alert REST API permite-lhe criar e gerir alertas no Log Analytics, que faz parte do Log Analytics.  Este artigo fornece detalhes da API e vários exemplos para a realização de diferentes operações.
ms.subservice: logs
ms.topic: conceptual
ms.date: 07/29/2018
ms.openlocfilehash: a85dad2ba638505233e5df769e55fa5bd7b8dafd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77665005"
---
# <a name="create-and-manage-alert-rules-in-log-analytics-with-rest-api"></a>Criar e gerir regras de alerta em Log Analytics com rest API 

O Log Analytics Alert REST API permite-lhe criar e gerir alertas em Log Analytics.  Este artigo fornece detalhes da API e vários exemplos para a realização de diferentes operações.

> [!IMPORTANT]
> Como [anunciado anteriormente](https://azure.microsoft.com/updates/switch-api-preference-log-alerts/), log analytics workspace(s) criado após 1 de junho de *2019* - será capaz de gerir regras de alerta usando **apenas** o Azure scheduledQueryRules [REST API,](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/)Modelo de [Mananger de Recursos Azure](../../azure-monitor/platform/alerts-log.md#managing-log-alerts-using-azure-resource-template) e [cmdlet PowerShell.](../../azure-monitor/platform/alerts-log.md#managing-log-alerts-using-powershell) Os clientes podem facilmente [mudar os seus meios preferidos de gestão de regras de alerta](../../azure-monitor/platform/alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api) para espaços de trabalho mais antigos para alavancar o Programado Azure MonitorQueryRules como padrão e obter muitos novos [benefícios](../../azure-monitor/platform/alerts-log-api-switch.md#benefits-of-switching-to-new-azure-api) como a capacidade de usar cmdlets nativos powerShell, o aumento do tempo de retrospetiva nas regras, a criação de regras em grupo de recursos separados ou subscrição e muito mais.

O Log Analytics Search REST API é RESTful e pode ser acedido através da API REST Manager de Recursos Azure. Neste documento, encontrará exemplos em que a API é acedida a partir de uma linha de comando PowerShell utilizando o [ARMClient](https://github.com/projectkudu/ARMClient), uma ferramenta de linha de comando de código aberto que simplifica a invocação da API do Gestor de Recursos Azure. O uso do ARMClient e powerShell é uma das muitas opções para aceder à API de Pesquisa de Log Analytics. Com estas ferramentas, pode utilizar a API RESTful Azure Resource Manager para fazer chamadas para espaços de trabalho de Log Analytics e executar comandos de pesquisa dentro delas. A API irá obter resultados de pesquisa no formato JSON, permitindo-lhe utilizar os resultados da pesquisa de várias maneiras programaticamente.

## <a name="prerequisites"></a>Pré-requisitos
Atualmente, os alertas só podem ser criados com uma pesquisa guardada no Log Analytics.  Pode consultar a [API](../../azure-monitor/log-query/log-query-overview.md) de Pesquisa de Registo sem mais informações.

## <a name="schedules"></a>Agendas
Uma pesquisa guardada pode ter um ou mais horários. O horário define a frequência com que a procura é executada e o intervalo de tempo sobre o qual os critérios são identificados.
Os horários têm as propriedades na tabela seguinte.

| Propriedade | Descrição |
|:--- |:--- |
| Intervalo |Quantas vezes a procura é executada. Medido em minutos. |
| ConsultaTimeSpan |O intervalo de tempo sobre o qual os critérios são avaliados. Deve ser igual ou maior que intervalo. Medido em minutos. |
| Versão |A versão API está a ser utilizada.  Atualmente, este deve ser sempre definido para 1. |

Por exemplo, considere uma consulta de evento com um intervalo de 15 minutos e um Timepan de 30 minutos. Neste caso, a consulta seria executada a cada 15 minutos, e um alerta seria desencadeado se os critérios continuassem a ser resolvidos ao longo de um período de 30 minutos.

### <a name="retrieving-schedules"></a>Horários de recuperação
Utilize o método Get para recuperar todos os horários para uma pesquisa guardada.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules?api-version=2015-03-20

Utilize o método Get com um ID de agenda para recuperar um horário específico para uma pesquisa guardada.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20

Segue-se uma resposta de amostra para um horário.

```json
{
    "value": [{
        "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/sampleRG/providers/Microsoft.OperationalInsights/workspaces/MyWorkspace/savedSearches/0f0f4853-17f8-4ed1-9a03-8e888b0d16ec/schedules/a17b53ef-bd70-4ca4-9ead-83b00f2024a8",
        "etag": "W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\"",
        "properties": {
            "Interval": 15,
            "QueryTimeSpan": 15,
            "Enabled": true,
        }
    }]
}
```

### <a name="creating-a-schedule"></a>Criar um horário
Utilize o método Put com um ID de horário único para criar um novo horário.  Dois horários não podem ter a mesma identificação mesmo que estejam associados a diferentes pesquisas guardadas.  Quando cria uma programação na consola Log Analytics, é criado um GUID para o ID de agenda.

> [!NOTE]
> O nome para todas as pesquisas, horários e ações criadas com a API log analytics deve estar em minúsculas.

    $scheduleJson = "{'properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Enabled':'true' } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson

### <a name="editing-a-schedule"></a>Editar um horário
Utilize o método Put com um ID de agenda existente para a mesma pesquisa guardada para modificar esse horário; por exemplo, abaixo do horário é desativado. O corpo do pedido deve incluir o *etage* do horário.

      $scheduleJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\""','properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Enabled':'false' } }"
      armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson


### <a name="deleting-schedules"></a>Horários de aparação
Utilize o método Eliminar com um ID de agenda para apagar um horário.

    armclient delete /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20


## <a name="actions"></a>Ações
Um horário pode ter múltiplas ações. Uma ação pode definir um ou mais processos para executar, tais como enviar um correio ou iniciar um livro de execução, ou pode definir um limiar que determina quando os resultados de uma pesquisa correspondem a alguns critérios.  Algumas ações definirão ambos para que os processos sejam realizados quando o limiar é cumprido.

Todas as ações têm as propriedades na tabela seguinte.  Diferentes tipos de alertas têm diferentes propriedades adicionais, que são descritas abaixo.

| Propriedade | Descrição |
|:--- |:--- |
| `Type` |Tipo de ação.  Atualmente, os valores possíveis são Alerta e Webhook. |
| `Name` |Mostrar o nome do alerta. |
| `Version` |A versão API está a ser utilizada.  Atualmente, este deve ser sempre definido para 1. |

### <a name="retrieving-actions"></a>Recuperação de ações

Use o método Get para recuperar todas as ações para um horário.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules/{Schedule ID}/actions?api-version=2015-03-20

Utilize o método Get com o ID de ação para recuperar uma ação específica para um horário.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/actions/{Action ID}?api-version=2015-03-20

### <a name="creating-or-editing-actions"></a>Criação ou edição de ações
Utilize o método Put com um ID de ação que seja exclusivo da programação para criar uma nova ação.  Quando cria uma ação na consola Log Analytics, um GUID é para o ID de ação.

> [!NOTE]
> O nome para todas as pesquisas, horários e ações criadas com a API log analytics deve estar em minúsculas.

Utilize o método Put com um ID de ação existente para a mesma pesquisa guardada para modificar esse horário.  O corpo do pedido deve incluir o etage do horário.

O formato de pedido para a criação de uma nova ação varia de acordo com o tipo de ação, pelo que estes exemplos são fornecidos nas secções abaixo.

### <a name="deleting-actions"></a>Apagando ações

Utilize o método Eliminar com o ID de ação para eliminar uma ação.

    armclient delete /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/Actions/{Action ID}?api-version=2015-03-20

### <a name="alert-actions"></a>Ações de alerta
Um horário deve ter uma e apenas uma ação de alerta.  As ações de alerta têm uma ou mais das secções na tabela seguinte.  Cada um é descrito em mais detalhes abaixo.

| Section | Descrição | Utilização |
|:--- |:--- |:--- |
| Limiar |Critérios para quando a ação é executada.| Necessário para cada alerta, antes ou depois de ser estendido a Azure. |
| Gravidade |Etiqueta usada para classificar o alerta quando desencadeada.| Necessário para cada alerta, antes ou depois de ser estendido a Azure. |
| Suprimir |Opção de parar as notificações de alerta. | Opcional para cada alerta, antes ou depois de serem estendidos ao Azure. |
| Grupos de Ação |IDs do Azure ActionGroup onde as ações necessárias são especificadas, tais como - E-mails, SMSs, Voice Calls, Webhooks, Automation Runbooks, ITSM Connectors, etc.| Necessários uma vez que os alertas são estendidos a Azure|
| Personalizar ações|Modificar a saída padrão para ações selecionadas do ActionGroup| Opcional para cada alerta, pode ser usado após os alertas serem estendidos ao Azure. |

### <a name="thresholds"></a>Limiares
Uma ação de alerta deve ter um e apenas um limiar.  Quando os resultados de uma pesquisa guardada correspondem ao limiar numa ação associada a essa pesquisa, então quaisquer outros processos nessa ação são executados.  Uma ação também pode conter apenas um limiar para que possa ser usada com ações de outros tipos que não contêm limiares.

Os limiares têm as propriedades na tabela seguinte.

| Propriedade | Descrição |
|:--- |:--- |
| `Operator` |Operador para a comparação do limiar. <br> gt = Maior do que <br> Lt = Menos do que |
| `Value` |Valor para o limiar. |

Por exemplo, considere uma consulta de evento com um intervalo de 15 minutos, um Timespan de 30 minutos, e um Limiar superior a 10. Neste caso, a consulta seria executada a cada 15 minutos, e um alerta seria desencadeado se devolvesse 10 eventos que foram criados ao longo de um período de 30 minutos.

Segue-se uma resposta da amostra para uma ação com apenas um limiar.  

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My threshold action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "Version": 1
    }

Utilize o método Put com um ID de ação único para criar uma nova ação de limiar para um horário.  

    $thresholdJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

Utilize o método Put com um ID de ação existente para modificar uma ação de limiar para um horário.  O corpo do pedido deve incluir o etagem da ação.

    $thresholdJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

#### <a name="severity"></a>Gravidade
O Log Analytics permite-lhe classificar os seus alertas em categorias, para permitir uma gestão e triagem mais fáceis. A gravidade do Alerta definida é: informativa, de aviso e crítica. Estes são mapeados para a escala de gravidade normalizada dos Alertas Azure como:

|Nível de gravidade da análise de log  |Nível de gravidade dos alertas azure  |
|---------|---------|
|`critical` |Sev 0|
|`warning` |Sev 1|
|`informational` | Sev2|

Segue-se uma resposta da amostra para uma ação com apenas um limiar e gravidade. 

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My threshold action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "Severity": "critical",
        "Version": 1    }

Utilize o método Put com um ID de ação único para criar uma nova ação para um horário com gravidade.  

    $thresholdWithSevJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdWithSevJson

Utilize o método Put com um ID de ação existente para modificar uma ação de gravidade para um horário.  O corpo do pedido deve incluir o etagem da ação.

    $thresholdWithSevJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdWithSevJson

#### <a name="suppress"></a>Suprimir
Os alertas de consulta baseados em Log Analytics dispararão sempre que o limiar for cumprido ou ultrapassado. Com base na lógica implícita na consulta, isto pode resultar em alerta ser disparado por uma série de intervalos e, portanto, notificações também são enviadas constantemente. Para evitar tal cenário, um utilizador pode definir a opção Suprimir instruindo o Log Analytics a aguardar um tempo estipulado antes de a notificação ser disparada pela segunda vez para a regra de alerta. Assim, se a repressão estiver definida durante 30 minutos; em seguida, o alerta disparará pela primeira vez e enviará notificações configuradas. Mas depois aguarde 30 minutos, antes que a notificação para a regra de alerta seja novamente usada. Durante o período provisório, a regra de alerta continuará a ser executada - apenas a notificação é suprimida pelo Log Analytics durante o tempo determinado, independentemente do número de vezes que a regra de alerta disparada neste período.

Suprimir a propriedade da regra de alerta Log Analytics é especificada usando o valor *de estrangulamento* e o período de supressão usando o valor *DurationInMinutes.*

Segue-se uma resposta da amostra para uma ação com apenas um limiar, gravidade e suprimir propriedade

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My threshold action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "Throttling": {
          "DurationInMinutes": 30
        },
        "Severity": "critical",
        "Version": 1    }

Utilize o método Put com um ID de ação único para criar uma nova ação para um horário com gravidade.  

    $AlertSuppressJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Throttling': { 'DurationInMinutes': 30 },'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myalert?api-version=2015-03-20 $AlertSuppressJson

Utilize o método Put com um ID de ação existente para modificar uma ação de gravidade para um horário.  O corpo do pedido deve incluir o etagem da ação.

    $AlertSuppressJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Throttling': { 'DurationInMinutes': 30 },'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myalert?api-version=2015-03-20 $AlertSuppressJson

#### <a name="action-groups"></a>Grupos de Ação
Todos os alertas em Azure, use o Action Group como o mecanismo padrão para o manuseamento de ações. Com o Action Group, pode especificar as suas ações uma vez e, em seguida, associar o grupo de ação a múltiplos alertas - através do Azure. Sem a necessidade, de declarar repetidamente as mesmas ações repetidamente. Os Action Groups apoiam múltiplas ações - incluindo e-mail, SMS, Voice Call, ITSM Connection, Automation Runbook, Webhook URI e muito mais. 

Para os utilizadores que tenham alargado os seus alertas ao Azure - um calendário deve agora ter detalhes do Action Group aprovados juntamente com o limiar, para poderem criar um alerta. Os detalhes do e-mail, URLs Webhook, detalhes da Automatização do Livro de Executa e outras Ações, precisam de ser definidos ao lado de um Grupo de Ação primeiro antes de criar um alerta; pode criar [o Grupo de Ação a partir do Monitor Azure](../../azure-monitor/platform/action-groups.md) no Portal ou utilizar a [API do Grupo de Ação](https://docs.microsoft.com/rest/api/monitor/actiongroups).

Para adicionar associação de grupo de ação a um alerta, especifique o ID exclusivo do Gestor de Recursos Azure do grupo de ação na definição de alerta. Uma ilustração de amostra é fornecida abaixo:

     "etag": "W/\"datetime'2017-12-13T10%3A52%3A21.1697364Z'\"",
      "properties": {
        "Type": "Alert",
        "Name": "test-alert",
        "Description": "I need to put a description here",
        "Threshold": {
          "Operator": "gt",
          "Value": 12
        },
        "AzNsNotification": {
          "GroupIds": [
            "/subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup"
          ]
        },
        "Severity": "critical",
        "Version": 1
      },

Utilize o método Put com um ID de ação único para associar o Grupo de Ação já existente para um horário.  Segue-se uma amostra de ilustração de utilização.

    $AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']} } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

Utilize o método Put com um ID de ação existente para modificar um Grupo de Ação associado a um horário.  O corpo do pedido deve incluir o etagem da ação.

    $AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', 'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': { 'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'] } } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

#### <a name="customize-actions"></a>Personalizar ações
Por ações predefinidas, siga o modelo padrão e o formato para notificações. Mas o utilizador pode personalizar algumas ações, mesmo que sejam controladas por Grupos de Ação. Atualmente, a personalização é possível para o Email Subject e Webhook Payload.

##### <a name="customize-e-mail-subject-for-action-group"></a>Personalize o e-mail subject for Action Group
Por predefinição, o sujeito de `<AlertName>` e-mail para alertas é: Notificação de alerta para `<WorkspaceName>`. Mas isto pode ser personalizado, para que possa especificar palavras ou tags - para permitir que utilize facilmente regras de filtro na sua Caixa de Entrada. Os detalhes personalizados do cabeçalho de e-mail precisam de ser enviados juntamente com os detalhes do ActionGroup, como na amostra abaixo.

     "etag": "W/\"datetime'2017-12-13T10%3A52%3A21.1697364Z'\"",
      "properties": {
        "Type": "Alert",
        "Name": "test-alert",
        "Description": "I need to put a description here",
        "Threshold": {
          "Operator": "gt",
          "Value": 12
        },
        "AzNsNotification": {
          "GroupIds": [
            "/subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup"
          ],
          "CustomEmailSubject": "Azure Alert fired"
        },
        "Severity": "critical",
        "Version": 1
      },

Utilize o método Put com um ID de ação único para associar o Grupo de Ação já existente com a personalização para um horário.  Segue-se uma amostra de ilustração de utilização.

    $AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'], 'CustomEmailSubject': 'Azure Alert fired'} } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

Utilize o método Put com um ID de ação existente para modificar um Grupo de Ação associado a um horário.  O corpo do pedido deve incluir o etagem da ação.

    $AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', 'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']}, 'CustomEmailSubject': 'Azure Alert fired' } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

##### <a name="customize-webhook-payload-for-action-group"></a>Personalize a carga útil do Webhook para o Grupo de Ação
Por predefinição, o webhook enviado via Action Group para análise de registos tem uma estrutura fixa. Mas pode-se personalizar a carga útil JSON utilizando variáveis específicas suportadas, para satisfazer os requisitos do ponto final do webhook. Para mais informações, consulte a [ação do Webhook para obter regras](../../azure-monitor/platform/alerts-log-webhook.md)de alerta de registo . 

Os detalhes personalizados do webhook precisam de ser enviados juntamente com os detalhes do ActionGroup e serão aplicados a todos os Webhook URI especificados dentro do grupo de ação; como na amostra abaixo.

     "etag": "W/\"datetime'2017-12-13T10%3A52%3A21.1697364Z'\"",
      "properties": {
        "Type": "Alert",
        "Name": "test-alert",
        "Description": "I need to put a description here",
        "Threshold": {
          "Operator": "gt",
          "Value": 12
        },
        "AzNsNotification": {
          "GroupIds": [
            "/subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup"
          ],
          "CustomWebhookPayload": "{\"field1\":\"value1\",\"field2\":\"value2\"}",
          "CustomEmailSubject": "Azure Alert fired"
        },
        "Severity": "critical",
        "Version": 1
      },

Utilize o método Put com um ID de ação único para associar o Grupo de Ação já existente com a personalização para um horário.  Segue-se uma amostra de ilustração de utilização.

    $AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'], 'CustomEmailSubject': 'Azure Alert fired','CustomWebhookPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}'} } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

Utilize o método Put com um ID de ação existente para modificar um Grupo de Ação associado a um horário.  O corpo do pedido deve incluir o etagem da ação.

    $AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', 'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']}, 'CustomEmailSubject': 'Azure Alert fired','CustomWebhookPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}' } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson


## <a name="next-steps"></a>Passos seguintes

* Utilize a [API REST para efetuar pesquisas](../../azure-monitor/log-query/log-query-overview.md) de log no Log Analytics.
* Saiba mais sobre alertas de [log no monitor Azure](../../azure-monitor/platform/alerts-unified-log.md)
* Como [criar, editar ou gerir regras](../../azure-monitor/platform/alerts-log.md) de alerta de registo no monitor Azure

