---
title: Usando a API de REST de alerta do Log Analytics
description: A API de REST alerta do Log Analytics permite-lhe criar e gerir alertas no Log Analytics que faz parte do Log Analytics.  Este artigo fornece detalhes da API e vários exemplos para realizar operações diferentes.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 628ad256-7181-4a0d-9e68-4ed60c0f3f04
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/10/2018
ms.author: bwren
ms.openlocfilehash: 31d9e2170461b9c4023bfe6b3e01fb1d7dda7fee
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57895894"
---
# <a name="create-and-manage-alert-rules-in-log-analytics-with-rest-api"></a>Criar e gerir regras de alerta no Log Analytics com a REST API
A API de REST alerta do Log Analytics permite-lhe criar e gerir alertas no Log Analytics.  Este artigo fornece detalhes da API e vários exemplos para realizar operações diferentes.

A API de REST de pesquisa do Log Analytics é RESTful e pode ser acedido através da API de REST do Azure Resource Manager. Neste documento, encontrará exemplos em que a API é acessada a partir de uma linha de comandos do PowerShell através de [ARMClient](https://github.com/projectkudu/ARMClient), uma ferramenta de linha de comandos de código-fonte aberto que simplifica a invocar a API do Azure Resource Manager. O uso de ARMClient e o PowerShell é uma das muitas opções para acessar a API de pesquisa do Log Analytics. Com essas ferramentas, pode utilizar o Gestor de recursos do API RESTful do Azure para fazer chamadas para áreas de trabalho do Log Analytics e executar comandos de pesquisa dentro dos mesmos. A API irá enviar os resultados da pesquisa para si no formato JSON, permitindo que use os resultados da pesquisa de muitas formas diferentes através de programação.

## <a name="prerequisites"></a>Pré-requisitos
Atualmente, os alertas só podem ser criados com uma pesquisa guardada do Log Analytics.  Pode consultar o [API do REST de pesquisa de registo](../../azure-monitor/log-query/log-query-overview.md) para obter mais informações.

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
Utilize o método Put com um ID de agendamento exclusivas para criar uma nova agenda.  Tenha em atenção que duas agendas não podem ter o mesmo ID, mesmo que estejam associados a diferentes pesquisas guardadas.  Quando cria uma agenda na consola do Log Analytics, um GUID é criado para o ID de agenda.

> [!NOTE]
> O nome para pesquisas guardadas tudo, cronogramas e ações criadas com a API de análise de registo tem de ser em minúsculas.

    $scheduleJson = "{'properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Active':'true' } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson

### <a name="editing-a-schedule"></a>Editar uma agenda
Utilize o método Put com um ID de agenda existente para a mesma pesquisa guardada para modificar essa agenda; no exemplo a seguir, a agenda está desativada. O corpo do pedido tem de incluir o *etag* da agenda.

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
| Tipo |Tipo de ação.  Atualmente, os valores possíveis são alerta e Webhook. |
| Nome |Nome a apresentar para o alerta. |
| Versão |A versão de API que está a ser utilizada.  Atualmente, isso deve ser sempre definido como 1. |

### <a name="retrieving-actions"></a>Obter ações

> [!NOTE]
> A partir de 14 de Maio de 2018, todos os alertas numa instância de cloud pública do Azure da área de trabalho do Log Analytics serão automaticamente expandidos para o Azure. Um utilizador voluntariamente pode iniciar a expansão de alertas para o Azure antes de 14 de Maio de 2018. Para obter mais informações, consulte [expandir alertas no Azure a partir do Log Analytics](../../azure-monitor/platform/alerts-extend.md). Para os utilizadores que estendem os alertas para o Azure, as ações agora são controladas em grupos de ação do Azure. Quando uma área de trabalho e os seus alertas são expandidas para o Azure, pode obter ou adicionar ações utilizando o [API do grupo de ação](https://docs.microsoft.com/rest/api/monitor/actiongroups).

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

> [!NOTE]
> A partir de 14 de Maio de 2018, todos os alertas numa instância de cloud pública do Azure da área de trabalho do Log Analytics serão automaticamente expandidos para o Azure. Um utilizador voluntariamente pode iniciar a expansão de alertas para o Azure antes de 14 de Maio de 2018. Para obter mais informações, consulte [expandir alertas no Azure a partir do Log Analytics](../../azure-monitor/platform/alerts-extend.md). Para os utilizadores que estendem os alertas para o Azure, as ações agora são controladas em grupos de ação do Azure. Quando uma área de trabalho e os seus alertas são expandidas para o Azure, pode obter ou adicionar ações utilizando o [API do grupo de ação](https://docs.microsoft.com/rest/api/monitor/actiongroups).

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
| EmailNotification |Envie um e-mail para vários destinatários. | Não obrigatório, se os alertas são expandidos para o Azure|
| Remediação |Inicie um runbook na automatização do Azure para tentar corrigir o problema identificado. |Não obrigatório, se os alertas são expandidos para o Azure|
| Ações de Webhook | Enviar dados de alertas, para o serviço desejado como JSON |Não obrigatório, se os alertas são expandidos para o Azure|

> [!NOTE]
> A partir de 14 de Maio de 2018, todos os alertas numa instância de cloud pública do Azure da área de trabalho do Log Analytics serão automaticamente expandidos para o Azure. Um utilizador voluntariamente pode iniciar a expansão de alertas para o Azure antes de 14 de Maio de 2018. Para obter mais informações, consulte [expandir alertas no Azure a partir do Log Analytics](../../azure-monitor/platform/alerts-extend.md).

#### <a name="thresholds"></a>Limiares
Ação do alerta deve ter apenas um limiar.  Quando os resultados de uma procura guardada correspondem o limiar numa ação associada que a pesquisa, em seguida, todos os outros processos nessa ação são executados.  Uma ação também pode conter apenas um limiar para que possa ser utilizado com as ações de outros tipos que não contenham limiares.

Limiares de tem as propriedades na tabela seguinte.

| Propriedade | Descrição |
|:--- |:--- |
| Operador |Operador de comparação de limiares. <br> gt = maior que <br> lt = menor que |
| Valor |Valor para o limiar. |

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

    $thresholdJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

Utilize o método Put com um ID de ação existente para modificar uma ação de limiar para uma agenda.  O corpo do pedido tem de incluir a etag da ação.

    $thresholdJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

#### <a name="severity"></a>Gravidade
O log Analytics permite-lhe classificar os alertas em categorias, para permitir a gestão mais fácil e de triagem. É a gravidade do alerta definida: informativo, aviso e crítico. Estes são mapeados para a escala de gravidade normalizado de alertas do Azure, como:

|Nível de gravidade do log Analytics  |Nível de gravidade de alertas do Azure  |
|---------|---------|
|crítico |Gravidade 0|
|aviso |Gravidade 1|
|Informativa | Gravidade 2|

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

    $thresholdWithSevJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdWithSevJson

Utilize o método Put com um ID de ação existente para modificar uma ação de gravidade para uma agenda.  O corpo do pedido tem de incluir a etag da ação.

    $thresholdWithSevJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdWithSevJson

#### <a name="suppress"></a>Suprimir
O log Analytics com base em consulta alertas serão acionados sempre que o limite é atingido ou excedido. Com base na lógica implícita na consulta, isso pode resultar em obter acionada para uma série de intervalos de alerta e, por conseguinte, as notificações também a ser enviadas constantemente. Para impedir que tal cenário, um utilizador pode definir a opção Suppress instruindo o Log Analytics para aguardar um período estipulado de tempo antes de notificação é disparada na segunda vez para a regra de alerta. Então, se suprimir está definido para 30 minutos; em seguida, alerta será acionado pela primeira vez e enviar notificações configuradas. Mas, em seguida, aguarde 30 minutos, antes de notificação para a regra de alerta é novamente utilizada. No período provisório, regra de alerta continuará a ser executado – apenas notificação suprimida pelo Log Analytics para o período de tempo especificado, independentemente do número de vezes que a regra de alerta acionada neste período.

Suprimir a propriedade do Log Analytics a regra de alerta é especificada com o *limitação* valor e a utilização do período de supressão *DurationInMinutes* valor.

É uma resposta de exemplo para uma ação com apenas um limite, gravidade, a seguir e suprimir a propriedade

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

    $AlertSuppressJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Throttling': { 'DurationInMinutes': 30 },'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myalert?api-version=2015-03-20 $AlertSuppressJson

Utilize o método Put com um ID de ação existente para modificar uma ação de gravidade para uma agenda.  O corpo do pedido tem de incluir a etag da ação.

    $AlertSuppressJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Throttling': { 'DurationInMinutes': 30 },'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myalert?api-version=2015-03-20 $AlertSuppressJson

#### <a name="action-groups"></a>Grupos de Ação
Todos os alertas no Azure, utilize o grupo de ação como o mecanismo predefinido para a manipulação de ações. Com o grupo de ação, pode especificar as suas ações de uma vez e, em seguida, associar o grupo de ação para múltiplos alertas - em todo o Azure. Sem a necessidade, repetidamente declarar as mesmas ações repetidamente. Os grupos de ação suportam várias ações - incluindo e-mail, SMS, chamada de voz, a ligação ITSM, Runbook de automatização, Webhook URI e muito mais. 

Para os utilizadores que têm expandidos seus alertas para o Azure - uma agenda já deve ter os detalhes do grupo de ação transmitidos juntamente com o limiar, para poder criar um alerta. Detalhes de email, URLs de Webhook, detalhes de automatização de Runbook e outras ações, tem de ser definido no lado primeiro antes, criando um alerta, um grupo de ação é possível criar [grupo de ação do Azure Monitor](../../azure-monitor/platform/action-groups.md) no Portal ou de utilização [API de grupo de ação](https://docs.microsoft.com/rest/api/monitor/actiongroups).

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

    $AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

Utilize o método Put com um ID de ação existente para modificar um grupo de ação associado para uma agenda.  O corpo do pedido tem de incluir a etag da ação.

    $AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

#### <a name="customize-actions"></a>Personalizar Ações
Por ações predefinidas, siga o modelo padrão e o formato para as notificações. No entanto, o utilizador pode personalizar algumas ações, mesmo que eles são controlados por grupos de ação. Atualmente, a personalização é possível para o assunto do E-Mail e Webhook Payload.

##### <a name="customize-e-mail-subject-for-action-group"></a>Personalizar o assunto do E-Mail para o grupo de ação
Por predefinição, é o assunto do e-mail para alertas: A notificação de alerta <AlertName> para <WorkspaceName>. Mas isso pode ser personalizado, para que possa palavras específicas ou etiquetas - para que possa facilmente empregar as regras de filtro na pasta a receber. Os detalhes de cabeçalho do e-mail de personalizar tem de enviar, juntamente com detalhes de ActionGroup, tal como no exemplo abaixo.

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

    $AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'], 'CustomEmailSubject': 'Azure Alert fired'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

Utilize o método Put com um ID de ação existente para modificar um grupo de ação associado para uma agenda.  O corpo do pedido tem de incluir a etag da ação.

    $AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']}, 'CustomEmailSubject': 'Azure Alert fired' }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

##### <a name="customize-webhook-payload-for-action-group"></a>Personalizar o Payload do Webook para o grupo de ação
Por predefinição, o webhook enviado por meio do grupo de ação para o log analytics tem uma estrutura fixa. No entanto, um pode personalizar o payload JSON, utilizando variáveis específicas suportadas, para atender aos requisitos do ponto final do webhook. Para obter mais informações, consulte [ação do Webhook para regras de alerta de registo](../../azure-monitor/platform/alerts-log-webhook.md). 

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

    $AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'], 'CustomEmailSubject': 'Azure Alert fired','CustomWebhookPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

Utilize o método Put com um ID de ação existente para modificar um grupo de ação associado para uma agenda.  O corpo do pedido tem de incluir a etag da ação.

    $AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']}, 'CustomEmailSubject': 'Azure Alert fired','CustomWebhookPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}' }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

#### <a name="email-notification"></a>Notificação por E-mail
Notificações por e-mail enviam um e-mail para um ou mais destinatários.  Eles incluem as propriedades na tabela seguinte.

> [!NOTE]
> A partir de 14 de Maio de 2018, todos os alertas numa instância de cloud pública do Azure da área de trabalho do Log Analytics serão automaticamente expandidos para o Azure. Um utilizador voluntariamente pode iniciar a expansão de alertas para o Azure antes de 14 de Maio de 2018. Para obter mais informações, consulte [expandir alertas no Azure a partir do Log Analytics](../../azure-monitor/platform/alerts-extend.md). Para os utilizadores que estendem os alertas para o Azure, as ações como notificação por email agora são controladas em grupos de ação do Azure. Quando uma área de trabalho e os seus alertas são expandidas para o Azure, pode obter ou adicionar ações utilizando o [API do grupo de ação](https://docs.microsoft.com/rest/api/monitor/actiongroups).
   

| Propriedade | Descrição |
|:--- |:--- |
| Destinatários |Lista de endereços de correio. |
| Requerente |O assunto da mensagem. |
| Anexo |Anexos não são atualmente suportados, para que isso sempre terá um valor de "None". |

Segue-se uma resposta de exemplo para uma ação de notificação de e-mail com um limiar.  

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My email action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "EmailNotification": {
            "Recipients": [
                "recipient1@contoso.com",
                "recipient2@contoso.com"
            ],
            "Subject": "This is the subject",
            "Attachment": "None"
        },
        "Version": 1
    }

Utilize o método Put com um ID exclusivo de ação para criar uma nova ação de e-mail para uma agenda.  O exemplo seguinte cria uma notificação por e-mail com um limiar para que o email é enviado quando os resultados da pesquisa guardada excederem o limiar.

    $emailJson = "{'properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myemailaction?api-version=2015-03-20 $emailJson

Utilize o método Put com um ID de ação existente para modificar uma ação de e-mail para uma agenda.  O corpo do pedido tem de incluir a etag da ação.

    $emailJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myemailaction?api-version=2015-03-20 $emailJson

#### <a name="remediation-actions"></a>Ações de remediação
Remediações iniciar um runbook na automatização do Azure, que tenta corrigir o problema identificado por esse alerta.  Tem de criar um webhook para o runbook utilizado uma ação de remediação e, em seguida, especifique o URI na propriedade WebhookUri.  Ao criar esta ação através do portal do Azure, um novo webhook é criado automaticamente para o runbook.

> [!NOTE]
> A partir de 14 de Maio de 2018, todos os alertas numa instância de cloud pública do Azure da área de trabalho do Log Analytics serão automaticamente expandidos para o Azure. Um utilizador voluntariamente pode iniciar a expansão de alertas para o Azure antes de 14 de Maio de 2018. Para obter mais informações, consulte [expandir alertas no Azure a partir do Log Analytics](../../azure-monitor/platform/alerts-extend.md). Para os utilizadores que estendem os alertas para o Azure, as ações como a remediação com runbook agora são controladas em grupos de ação do Azure. Quando uma área de trabalho e os seus alertas são expandidas para o Azure, pode obter ou adicionar ações utilizando o [API do grupo de ação](https://docs.microsoft.com/rest/api/monitor/actiongroups).

Remediações incluem as propriedades na tabela seguinte.

| Propriedade | Descrição |
|:--- |:--- |
| RunbookName |Nome do runbook. Isto deve corresponder ao runbook publicado na conta de automatização configurado na solução de automatização na sua área de trabalho do Log Analytics. |
| WebhookUri |URI do webhook. |
| Validade |A data de expiração e hora do webhook.  Se o webhook não tiver uma vencimento, em seguida, isso pode ser qualquer data futura válida. |

Segue-se uma resposta de exemplo para uma ação de remediação com um limiar.

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My remediation action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "Remediation": {
            "RunbookName": "My-Runbook",
            "WebhookUri": "https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d",
            "Expiry": "2018-02-25T18:27:20"
            },
        "Version": 1
    }

Utilize o método Put com um ID exclusivo de ação para criar uma nova ação de remediação para uma agenda.  O exemplo seguinte cria uma remediação com um limiar para que o runbook é iniciado quando os resultados da pesquisa guardada excederem o limiar.

    $remediateJson = "{'properties': { 'Type':'Alert', 'Name': 'My Remediation Action', 'Version':'1', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'Remediation': {'RunbookName': 'My-Runbook', 'WebhookUri':'https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d', 'Expiry':'2018-02-25T18:27:20Z'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myremediationaction?api-version=2015-03-20 $remediateJson

Utilize o método Put com um ID de ação existente para modificar uma ação de remediação para uma agenda.  O corpo do pedido tem de incluir a etag da ação.

    $remediateJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Type':'Alert', 'Name': 'My Remediation Action', 'Version':'1', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'Remediation': {'RunbookName': 'My-Runbook', 'WebhookUri':'https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d', 'Expiry':'2018-02-25T18:27:20Z'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myremediationaction?api-version=2015-03-20 $remediateJson

#### <a name="example"></a>Exemplo
Segue-se um exemplo completo para criar um novo alerta de e-mail.  Esta ação cria uma nova agenda, juntamente com uma ação que contém um limiar e e-mail.

    $subscriptionId = "3d56705e-5b26-5bcc-9368-dbc8d2fafbfc"
    $resourceGroup  = "MyResourceGroup"    
    $workspaceName    = "MyWorkspace"
    $searchId       = "MySearch"
    $scheduleId     = "MySchedule"
    $thresholdId    = "MyThreshold"
    $actionId       = "MyEmailAction"
    
    $scheduleJson = "{'properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Active':'true' }"
    armclient put /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.OperationalInsights/workspaces/$workspaceName/savedSearches/$searchId/schedules/$scheduleId/?api-version=2015-03-20 $scheduleJson
    
    $emailJson = "{'properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Severity':'Warning', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.OperationalInsights/workspaces/$workspaceName/savedSearches/$searchId/schedules/$scheduleId/actions/$actionId/?api-version=2015-03-20 $emailJson

#### <a name="webhook-actions"></a>Ações de Webhook
Ações de Webhook iniciar um processo chamando uma URL e, opcionalmente, fornecendo um payload de envio.  Eles são semelhantes às ações de remediação, exceto que eles se destinam-se a webhooks que pode invocar processos que não seja runbooks de automatização do Azure.  Eles fornecem também a opção adicional de fornecer um payload possível entregar o processo remoto.

> [!NOTE]
> A partir de 14 de Maio de 2018, todos os alertas numa instância de cloud pública do Azure da área de trabalho do Log Analytics serão automaticamente expandidos para o Azure. Um utilizador voluntariamente pode iniciar a expansão de alertas para o Azure antes de 14 de Maio de 2018. Para obter mais informações, consulte [expandir alertas no Azure a partir do Log Analytics](../../azure-monitor/platform/alerts-extend.md). Para os utilizadores que estendem os alertas para o Azure, as ações como Webhook agora são controladas em grupos de ação do Azure. Quando uma área de trabalho e os seus alertas são expandidas para o Azure, pode obter ou adicionar ações utilizando o [API do grupo de ação](https://docs.microsoft.com/rest/api/monitor/actiongroups).


Ações de Webhook não têm um limite, mas em vez disso, devem ser adicionadas a uma agenda que tenha a ação do alerta com um limiar.  

Segue-se uma resposta de exemplo para a ação do alerta associada com um limiar e de ação do webhook.

    {
        "__metadata": {},
        "value": [
            {
                "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/bwren/savedSearches/2d1b30fb-7f48-4de5-9614-79ee244b52de/schedules/b80f5621-7217-4007-b32d-165d14377093/Actions/72884702-acf9-4653-bb67-f42436b342b4",
                "etag": "W/\"datetime'2016-02-26T20%3A25%3A00.6862124Z'\"",
                "properties": {
                    "Type": "Webhook",
                    "Name": "My Webhook Action",
                    "WebhookUri": "https://oaaswebhookdf.cloudapp.net/webhooks?token=VfkYTIlpk%2fc%2bJBP",
                    "CustomPayload": "{\"fielld1\":\"value1\",\"field2\":\"value2\"}",
                    "Version": 1
                }
            },
            {
                "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/bwren/savedSearches/2d1b30fb-7f48-4de5-9614-79ee244b52de/schedules/b80f5621-7217-4007-b32d-165d14377093/Actions/90a27cf8-71b7-4df2-b04f-54ed01f1e4b6",
                "etag": "W/\"datetime'2016-02-26T20%3A25%3A00.565204Z'\"",
                "properties": {
                    "Type": "Alert",
                    "Name": "Threshold for my webhook action",
                    "Threshold": {
                        "Operator": "gt",
                        "Value": 10
                    },
                    "Version": 1
                }
            }
        ]
    }

##### <a name="create-or-edit-a-webhook-action"></a>Criar ou editar uma ação do webhook
Utilize o método Put com um ID exclusivo de ação para criar uma nova ação de webhook para uma agenda.  O exemplo seguinte cria uma ação de Webhook e ação do alerta com um limiar para que o webhook será acionado quando os resultados da pesquisa guardada excederem o limiar.

    $thresholdAction = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdAction

    $webhookAction = "{'properties': {'Type': 'Webhook', 'Name': 'My Webhook", 'WebhookUri': 'https://oaaswebhookdf.cloudapp.net/webhooks?token=VrkYTKlhk%2fc%2bKBP', 'CustomPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}', 'Version': 1 }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mywebhookaction?api-version=2015-03-20 $webhookAction

Utilize o método Put com um ID de ação existente para modificar uma ação do webhook para uma agenda.  O corpo do pedido tem de incluir a etag da ação.

    $webhookAction = "{'etag': 'W/\"datetime'2016-02-26T20%3A25%3A00.6862124Z'\"','properties': {'Type': 'Webhook', 'Name': 'My Webhook", 'WebhookUri': 'https://oaaswebhookdf.cloudapp.net/webhooks?token=VrkYTKlhk%2fc%2bKBP', 'CustomPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}', 'Version': 1 }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mywebhookaction?api-version=2015-03-20 $webhookAction


## <a name="next-steps"></a>Passos Seguintes
* Utilize o [API REST para realizar pesquisas de registos](../../azure-monitor/log-query/log-query-overview.md) no Log Analytics.
* Saiba mais sobre [alertas de registo nos alertas do azure](../../azure-monitor/platform/alerts-unified-log.md)

