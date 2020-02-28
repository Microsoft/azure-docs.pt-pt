---
title: Usando a API de REST de alerta do Log Analytics
description: O Log Analytics Alert REST API permite-lhe criar e gerir alertas no Log Analytics, que faz parte do Log Analytics.  Este artigo fornece detalhes da API e vários exemplos para realizar operações diferentes.
ms.subservice: logs
ms.topic: conceptual
ms.date: 07/29/2018
ms.openlocfilehash: a85dad2ba638505233e5df769e55fa5bd7b8dafd
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77665005"
---
# <a name="create-and-manage-alert-rules-in-log-analytics-with-rest-api"></a>Criar e gerir regras de alerta no Log Analytics com a REST API 

A API de REST alerta do Log Analytics permite-lhe criar e gerir alertas no Log Analytics.  Este artigo fornece detalhes da API e vários exemplos para realizar operações diferentes.

> [!IMPORTANT]
> Como [anunciado anteriormente](https://azure.microsoft.com/updates/switch-api-preference-log-alerts/), log analytics workspace(s) criado após 1 de junho de *2019* - será capaz de gerir regras de alerta usando **apenas** o Azure scheduledQueryRules [REST API,](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/)Modelo de [Mananger de Recursos Azure](../../azure-monitor/platform/alerts-log.md#managing-log-alerts-using-azure-resource-template) e [cmdlet PowerShell.](../../azure-monitor/platform/alerts-log.md#managing-log-alerts-using-powershell) Os clientes podem facilmente [mudar os seus meios preferidos de gestão de regras de alerta](../../azure-monitor/platform/alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api) para espaços de trabalho mais antigos para alavancar o Programado Azure MonitorQueryRules como padrão e obter muitos novos [benefícios](../../azure-monitor/platform/alerts-log-api-switch.md#benefits-of-switching-to-new-azure-api) como a capacidade de usar cmdlets nativos powerShell, o aumento do tempo de retrospetiva nas regras, a criação de regras em grupo de recursos separados ou subscrição e muito mais.

A API de REST de pesquisa do Log Analytics é RESTful e pode ser acedido através da API de REST do Azure Resource Manager. Neste documento, encontrará exemplos em que a API é acedida a partir de uma linha de comando PowerShell utilizando o [ARMClient](https://github.com/projectkudu/ARMClient), uma ferramenta de linha de comando de código aberto que simplifica a invocação da API do Gestor de Recursos Azure. O uso de ARMClient e o PowerShell é uma das muitas opções para acessar a API de pesquisa do Log Analytics. Com essas ferramentas, pode utilizar o Gestor de recursos do API RESTful do Azure para fazer chamadas para áreas de trabalho do Log Analytics e executar comandos de pesquisa dentro dos mesmos. A API irá enviar os resultados da pesquisa para si no formato JSON, permitindo que use os resultados da pesquisa de muitas formas diferentes através de programação.

## <a name="prerequisites"></a>Pré-requisitos
Atualmente, os alertas só podem ser criados com uma pesquisa guardada do Log Analytics.  Pode consultar a [API](../../azure-monitor/log-query/log-query-overview.md) de Pesquisa de Registo sem mais informações.

## <a name="schedules"></a>Agendas
Uma pesquisa guardada pode ter uma ou mais agendas. O plano define a frequência com que a pesquisa é a execução e o intervalo de tempo durante o qual os critérios é identificado.
Agendas têm as propriedades na tabela seguinte.

| Propriedade | Descrição |
|:--- |:--- |
| Intervalo |A frequência com que a pesquisa é executada. Medido em minutos. |
| QueryTimeSpan |O intervalo de tempo durante o qual os critérios é avaliada. Tem de ser igual ou maior do que o intervalo. Medido em minutos. |
| Versão |A versão de API que está a ser utilizada.  Atualmente, isso deve ser sempre definido como 1. |

Por exemplo, considere uma consulta de eventos com um intervalo de 15 minutos e um intervalo de tempo de 30 minutos. Neste caso, a consulta deve ser executada a cada 15 minutos, e seria acionado um alerta se os critérios de continuação resolver para a ativação pós-falha verdadeira um intervalo de 30 minutos.

### <a name="retrieving-schedules"></a>A obter agendas
Utilize o método Get para obter todas as agendas de uma procura guardada.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules?api-version=2015-03-20

Utilize o método Get com um ID de agenda para obter uma determinada agenda para uma procura guardada.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20

Segue-se uma resposta de exemplo para uma agenda.

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

### <a name="creating-a-schedule"></a>Criar uma agenda
Utilize o método Put com um ID de agendamento exclusivas para criar uma nova agenda.  Dois horários não podem ter a mesma identificação mesmo que estejam associados a diferentes pesquisas guardadas.  Quando cria uma agenda na consola do Log Analytics, um GUID é criado para o ID de agenda.

> [!NOTE]
> O nome para pesquisas guardadas tudo, cronogramas e ações criadas com a API de análise de registo tem de ser em minúsculas.

    $scheduleJson = "{'properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Enabled':'true' } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson

### <a name="editing-a-schedule"></a>Editar uma agenda
Utilize o método Put com um ID de agenda existente para a mesma pesquisa guardada para modificar esse horário; por exemplo, abaixo do horário é desativado. O corpo do pedido deve incluir o *etage* do horário.

      $scheduleJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\""','properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Enabled':'false' } }"
      armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson


### <a name="deleting-schedules"></a>A eliminar agendas
Utilize o método Delete com um ID de agenda para eliminar uma agenda.

    armclient delete /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20


## <a name="actions"></a>Ações
Uma agenda pode ter várias ações. Uma ação pode definir um ou mais processos para efetuar como enviar um email ou iniciar um runbook ou ele pode definir um limiar que determina quando os resultados de uma pesquisa corresponderem a critérios.  Algumas ações definir ambos, para que os processos são executados quando o limiar for cumprido.

Todas as ações têm as propriedades na tabela seguinte.  Diferentes tipos de alertas têm diferentes propriedades adicionais, que são descritas abaixo.

| Propriedade | Descrição |
|:--- |:--- |
| `Type` |Tipo de ação.  Atualmente, os valores possíveis são alerta e Webhook. |
| `Name` |Nome a apresentar para o alerta. |
| `Version` |A versão de API que está a ser utilizada.  Atualmente, isso deve ser sempre definido como 1. |

### <a name="retrieving-actions"></a>Obter ações

Utilize o método Get para obter todas as ações para uma agenda.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules/{Schedule ID}/actions?api-version=2015-03-20

Utilize o método Get com o ID de ação para obter uma ação específica para uma agenda.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/actions/{Action ID}?api-version=2015-03-20

### <a name="creating-or-editing-actions"></a>Criar ou editar ações
Utilize o método Put com um ID de ação que é exclusivo para a agenda para criar uma nova ação.  Quando cria uma ação na consola do Log Analytics, é um GUID para o ID de ação.

> [!NOTE]
> O nome para pesquisas guardadas tudo, cronogramas e ações criadas com a API de análise de registo tem de ser em minúsculas.

Utilize o método Put com um ID de ação existente para a mesma pesquisa guardada para modificar essa agenda.  O corpo do pedido tem de incluir a etag da agenda.

O formato do pedido para criar uma nova ação varia consoante o tipo de ação, pelo que esses exemplos são fornecidos nas secções abaixo.

### <a name="deleting-actions"></a>A eliminar ações

Utilize o método Delete com o ID de ação para eliminar uma ação.

    armclient delete /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/Actions/{Action ID}?api-version=2015-03-20

### <a name="alert-actions"></a>Ações de alerta
Uma agenda deve ter apenas uma ação do alerta.  Ações de alerta tem uma ou mais das seções na tabela seguinte.  Cada um é descrito mais detalhadamente abaixo.

| Section | Descrição | Utilização |
|:--- |:--- |:--- |
| Limiar |Critérios para quando a ação é executada.| É necessário para cada alerta, antes ou depois de eles são estendidos para o Azure. |
| Gravidade |Etiqueta utilizada para classificar o alerta quando acionado.| É necessário para cada alerta, antes ou depois de eles são estendidos para o Azure. |
| Suprimir |Opção para parar as notificações de alerta. | Opcional para cada alerta, antes ou depois de eles são estendidos para o Azure. |
| Grupos de Ação |IDs de ActionGroup do Azure em que são especificadas as ações necessárias, como - emails, SMSs, chamadas de voz, Webhooks, Runbooks de automatização, conectores de ITSM, etc.| Necessário depois de alertas são expandidos para o Azure|
| Personalizar Ações|Modificar a saída padrão para selecionadas ações de ActionGroup| Opcional para cada alerta, pode ser utilizado depois de alertas são expandidos para o Azure. |

### <a name="thresholds"></a>Limiares
Ação do alerta deve ter apenas um limiar.  Quando os resultados de uma procura guardada correspondem o limiar numa ação associada que a pesquisa, em seguida, todos os outros processos nessa ação são executados.  Uma ação também pode conter apenas um limiar para que possa ser utilizado com as ações de outros tipos que não contenham limiares.

Limiares de tem as propriedades na tabela seguinte.

| Propriedade | Descrição |
|:--- |:--- |
| `Operator` |Operador de comparação de limiares. <br> gt = maior que <br> lt = menor que |
| `Value` |Valor para o limiar. |

Por exemplo, considere uma consulta de eventos com um intervalo de 15 minutos, um intervalo de tempo de 30 minutos e um limite superior a 10. Neste caso, a consulta deve ser executada a cada 15 minutos, e seria acionado um alerta se ele retornasse 10 eventos que foram criados ao longo de um período de 30 minutos.

Segue-se uma resposta de exemplo para uma ação com apenas um limiar.  

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

Utilize o método Put com um ID exclusivo de ação para criar uma nova ação de limiar para uma agenda.  

    $thresholdJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

Utilize o método Put com um ID de ação existente para modificar uma ação de limiar para uma agenda.  O corpo do pedido tem de incluir a etag da ação.

    $thresholdJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

#### <a name="severity"></a>Gravidade
O log Analytics permite-lhe classificar os alertas em categorias, para permitir a gestão mais fácil e de triagem. É a gravidade do alerta definida: informativo, aviso e crítico. Estes são mapeados para a escala de gravidade normalizado de alertas do Azure, como:

|Nível de gravidade do log Analytics  |Nível de gravidade de alertas do Azure  |
|---------|---------|
|`critical` |Gravidade 0|
|`warning` |Gravidade 1|
|`informational` | Gravidade 2|

Segue-se uma resposta de exemplo para uma ação com apenas um limiar e a gravidade. 

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

Utilize o método Put com um ID exclusivo de ação para criar uma nova ação para uma agenda com gravidade.  

    $thresholdWithSevJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdWithSevJson

Utilize o método Put com um ID de ação existente para modificar uma ação de gravidade para uma agenda.  O corpo do pedido tem de incluir a etag da ação.

    $thresholdWithSevJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdWithSevJson

#### <a name="suppress"></a>Suprimir
O log Analytics com base em consulta alertas serão acionados sempre que o limite é atingido ou excedido. Com base na lógica implícita na consulta, isso pode resultar em obter acionada para uma série de intervalos de alerta e, por conseguinte, as notificações também a ser enviadas constantemente. Para impedir que tal cenário, um utilizador pode definir a opção Suppress instruindo o Log Analytics para aguardar um período estipulado de tempo antes de notificação é disparada na segunda vez para a regra de alerta. Então, se suprimir está definido para 30 minutos; em seguida, alerta será acionado pela primeira vez e enviar notificações configuradas. Mas, em seguida, aguarde 30 minutos, antes de notificação para a regra de alerta é novamente utilizada. No período provisório, regra de alerta continuará a ser executado – apenas notificação suprimida pelo Log Analytics para o período de tempo especificado, independentemente do número de vezes que a regra de alerta acionada neste período.

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

Utilize o método Put com um ID exclusivo de ação para criar uma nova ação para uma agenda com gravidade.  

    $AlertSuppressJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Throttling': { 'DurationInMinutes': 30 },'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myalert?api-version=2015-03-20 $AlertSuppressJson

Utilize o método Put com um ID de ação existente para modificar uma ação de gravidade para uma agenda.  O corpo do pedido tem de incluir a etag da ação.

    $AlertSuppressJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Throttling': { 'DurationInMinutes': 30 },'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myalert?api-version=2015-03-20 $AlertSuppressJson

#### <a name="action-groups"></a>Grupos de Ação
Todos os alertas no Azure, utilize o grupo de ação como o mecanismo predefinido para a manipulação de ações. Com o grupo de ação, pode especificar as suas ações de uma vez e, em seguida, associar o grupo de ação para múltiplos alertas - em todo o Azure. Sem a necessidade, repetidamente declarar as mesmas ações repetidamente. Os grupos de ação suportam várias ações - incluindo e-mail, SMS, chamada de voz, a ligação ITSM, Runbook de automatização, Webhook URI e muito mais. 

Para os utilizadores que tenham alargado os seus alertas ao Azure - um calendário deve agora ter detalhes do Action Group aprovados juntamente com o limiar, para poderem criar um alerta. Os detalhes do e-mail, URLs Webhook, detalhes da Automatização do Livro de Executa e outras Ações, precisam de ser definidos ao lado de um Grupo de Ação primeiro antes de criar um alerta; pode criar [o Grupo de Ação a partir do Monitor Azure](../../azure-monitor/platform/action-groups.md) no Portal ou utilizar a [API do Grupo de Ação](https://docs.microsoft.com/rest/api/monitor/actiongroups).

Para Adicionar associação de grupo de ação para um alerta, especifique o ID de Gestor de recursos do Azure exclusivo do grupo de ação na definição de alerta. Uma ilustração de exemplo é fornecida abaixo:

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

Utilize o método Put com um ID exclusivo da ação para associar o grupo de ação já existente para uma agenda.  Segue-se uma ilustração de exemplo de utilização.

    $AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']} } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

Utilize o método Put com um ID de ação existente para modificar um grupo de ação associado para uma agenda.  O corpo do pedido tem de incluir a etag da ação.

    $AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', 'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': { 'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'] } } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

#### <a name="customize-actions"></a>Personalizar Ações
Por ações predefinidas, siga o modelo padrão e o formato para as notificações. No entanto, o utilizador pode personalizar algumas ações, mesmo que eles são controlados por grupos de ação. Atualmente, a personalização é possível para o assunto do E-Mail e Webhook Payload.

##### <a name="customize-e-mail-subject-for-action-group"></a>Personalizar o assunto do E-Mail para o grupo de ação
Por predefinição, o endereço de e-mail para alertas é: Notificação de alerta `<AlertName>` para `<WorkspaceName>`. Mas isso pode ser personalizado, para que possa palavras específicas ou etiquetas - para que possa facilmente empregar as regras de filtro na pasta a receber. Os detalhes de cabeçalho do e-mail de personalizar tem de enviar, juntamente com detalhes de ActionGroup, tal como no exemplo abaixo.

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

Utilize o método Put com um ID exclusivo de ação para associar o grupo de ação já existente com a personalização para uma agenda.  Segue-se uma ilustração de exemplo de utilização.

    $AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'], 'CustomEmailSubject': 'Azure Alert fired'} } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

Utilize o método Put com um ID de ação existente para modificar um grupo de ação associado para uma agenda.  O corpo do pedido tem de incluir a etag da ação.

    $AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', 'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']}, 'CustomEmailSubject': 'Azure Alert fired' } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

##### <a name="customize-webhook-payload-for-action-group"></a>Personalizar o Payload do Webook para o grupo de ação
Por predefinição, o webhook enviado por meio do grupo de ação para o log analytics tem uma estrutura fixa. No entanto, um pode personalizar o payload JSON, utilizando variáveis específicas suportadas, para atender aos requisitos do ponto final do webhook. Para mais informações, consulte a [ação do Webhook para obter regras](../../azure-monitor/platform/alerts-log-webhook.md)de alerta de registo . 

Os detalhes do webhook de personalizar precisam de enviar, juntamente com detalhes de ActionGroup e serão aplicadas a todos os Webhook URI especificado no interior do grupo de ação; tal como no exemplo abaixo.

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

Utilize o método Put com um ID exclusivo de ação para associar o grupo de ação já existente com a personalização para uma agenda.  Segue-se uma ilustração de exemplo de utilização.

    $AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'], 'CustomEmailSubject': 'Azure Alert fired','CustomWebhookPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}'} } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

Utilize o método Put com um ID de ação existente para modificar um grupo de ação associado para uma agenda.  O corpo do pedido tem de incluir a etag da ação.

    $AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', 'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']}, 'CustomEmailSubject': 'Azure Alert fired','CustomWebhookPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}' } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson


## <a name="next-steps"></a>Passos seguintes

* Utilize a [API REST para efetuar pesquisas](../../azure-monitor/log-query/log-query-overview.md) de log no Log Analytics.
* Saiba mais sobre alertas de [log no monitor Azure](../../azure-monitor/platform/alerts-unified-log.md)
* Como [criar, editar ou gerir regras](../../azure-monitor/platform/alerts-log.md) de alerta de registo no monitor Azure

