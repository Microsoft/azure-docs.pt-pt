---
title: Usando Log Analytics API REST de alerta
description: A API REST do alerta de Log Analytics permite criar e gerenciar alertas no Log Analytics, que faz parte do Log Analytics.  Este artigo fornece detalhes da API e vários exemplos para executar operações diferentes.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/29/2018
ms.openlocfilehash: 9cc9c9db1438196190df38082f18d650eff38249
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932686"
---
# <a name="create-and-manage-alert-rules-in-log-analytics-with-rest-api"></a>Criar e gerenciar regras de alerta no Log Analytics com a API REST
A API REST do alerta de Log Analytics permite criar e gerenciar alertas no Log Analytics.  Este artigo fornece detalhes da API e vários exemplos para executar operações diferentes.

> [!IMPORTANT]
> Conforme [anunciado anteriormente](https://azure.microsoft.com/updates/switch-api-preference-log-alerts/), os espaços de trabalho do log Analytics criados após *1º de junho de 2019* – poderão gerenciar regras de alerta usando **apenas** a [API REST](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/)do Azure scheduledQueryRules, o [modelo Manager de recursos do Azure](../../azure-monitor/platform/alerts-log.md#managing-log-alerts-using-azure-resource-template) e [ Cmdlet do PowerShell](../../azure-monitor/platform/alerts-log.md#managing-log-alerts-using-powershell). Os clientes podem facilmente [alternar seus meios preferenciais de gerenciamento de regras de alerta](../../azure-monitor/platform/alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api) para espaços de trabalho mais antigos para aproveitar Azure monitor scheduledQueryRules como padrão e obter muitos [novos benefícios](../../azure-monitor/platform/alerts-log-api-switch.md#benefits-of-switching-to-new-azure-api) como a capacidade de usar cmdlets nativos do PowerShell, aumentou período de tempo de lookback em regras, criação de regras em um grupo de recursos ou assinatura separado e muito mais.

A API REST de pesquisa Log Analytics é RESTful e pode ser acessada por meio da API REST do Azure Resource Manager. Neste documento, você encontrará exemplos em que a API é acessada de uma linha de comando do PowerShell usando o [ARMClient](https://github.com/projectkudu/ARMClient), uma ferramenta de linha de comando de software livre que simplifica a invocação da API Azure Resource Manager. O uso do ARMClient e do PowerShell é uma das muitas opções para acessar a API de pesquisa Log Analytics. Com essas ferramentas, você pode utilizar a API de Azure Resource Manager RESTful para fazer chamadas para Log Analytics espaços de trabalho e executar comandos de pesquisa dentro deles. A API produzirá resultados de pesquisa para você no formato JSON, permitindo que você use os resultados da pesquisa de várias maneiras diferentes programaticamente.

## <a name="prerequisites"></a>Pré-requisitos
Atualmente, os alertas só podem ser criados com uma pesquisa salva no Log Analytics.  Você pode consultar a [API REST da pesquisa de logs](../../azure-monitor/log-query/log-query-overview.md) para obter mais informações.

## <a name="schedules"></a>Agendas
Uma pesquisa salva pode ter um ou mais agendamentos. A agenda define a frequência com que a pesquisa é executada e o intervalo de tempo durante o qual os critérios são identificados.
As agendas têm as propriedades na tabela a seguir.

| Propriedade | Descrição |
|:--- |:--- |
| Intervalo |Com que frequência a pesquisa é executada. Medido em minutos. |
| queryTimeSpan |O intervalo de tempo durante o qual os critérios são avaliados. Deve ser igual ou maior que intervalo. Medido em minutos. |
| Versão |A versão da API que está sendo usada.  Atualmente, isso deve sempre ser definido como 1. |

Por exemplo, considere uma consulta de evento com um intervalo de 15 minutos e um período de 30 minutos. Nesse caso, a consulta seria executada a cada 15 minutos e um alerta será disparado se os critérios continuarem a ser resolvidos para verdadeiro em um período de 30 minutos.

### <a name="retrieving-schedules"></a>Recuperando agendas
Use o método Get para recuperar todos os agendamentos de uma pesquisa salva.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules?api-version=2015-03-20

Use o método Get com uma ID de agendamento para recuperar uma agenda específica para uma pesquisa salva.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20

Veja a seguir uma resposta de exemplo para uma agenda.

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

### <a name="creating-a-schedule"></a>Criando uma agenda
Use o método Put com uma ID de agendamento exclusiva para criar uma nova agenda.  Dois agendamentos não podem ter a mesma ID, mesmo se estiverem associados a pesquisas salvas diferentes.  Quando você cria uma agenda no console do Log Analytics, um GUID é criado para a ID da agenda.

> [!NOTE]
> O nome de todas as pesquisas, agendas e ações salvas criadas com a API Log Analytics deve estar em letras minúsculas.

    $scheduleJson = "{'properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Enabled':'true' } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson

### <a name="editing-a-schedule"></a>Editando uma agenda
Use o método Put com uma ID de agenda existente para a mesma pesquisa salva para modificar essa agenda; no exemplo abaixo, a agenda está desabilitada. O corpo da solicitação deve incluir a *ETag* da agenda.

      $scheduleJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\""','properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Enabled':'false' } }"
      armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson


### <a name="deleting-schedules"></a>Excluindo agendas
Use o método Delete com uma ID de agendamento para excluir uma agenda.

    armclient delete /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20


## <a name="actions"></a>Ações
Uma agenda pode ter várias ações. Uma ação pode definir um ou mais processos a serem executados, como enviar um email ou iniciar um runbook, ou pode definir um limite que determina quando os resultados de uma pesquisa correspondem a alguns critérios.  Algumas ações definirão ambos para que os processos sejam executados quando o limite for atingido.

Todas as ações têm as propriedades na tabela a seguir.  Tipos diferentes de alertas têm diferentes propriedades adicionais, que são descritas abaixo.

| Propriedade | Descrição |
|:--- |:--- |
| `Type` |Tipo da ação.  Atualmente, os valores possíveis são alerta e webhook. |
| `Name` |Nome de exibição do alerta. |
| `Version` |A versão da API que está sendo usada.  Atualmente, isso deve sempre ser definido como 1. |

### <a name="retrieving-actions"></a>Recuperando ações

Use o método Get para recuperar todas as ações de um agendamento.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules/{Schedule ID}/actions?api-version=2015-03-20

Use o método Get com a ID de ação para recuperar uma ação específica para um agendamento.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/actions/{Action ID}?api-version=2015-03-20

### <a name="creating-or-editing-actions"></a>Criando ou editando ações
Use o método Put com uma ID de ação que seja exclusiva para a agenda para criar uma nova ação.  Quando você cria uma ação no console do Log Analytics, um GUID é para a ID de ação.

> [!NOTE]
> O nome de todas as pesquisas, agendas e ações salvas criadas com a API Log Analytics deve estar em letras minúsculas.

Use o método Put com uma ID de ação existente para a mesma pesquisa salva para modificar essa agenda.  O corpo da solicitação deve incluir a ETag da agenda.

O formato da solicitação para criar uma nova ação varia de acordo com o tipo de ação, portanto, esses exemplos são fornecidos nas seções a seguir.

### <a name="deleting-actions"></a>Excluindo ações

Use o método Delete com a ID de ação para excluir uma ação.

    armclient delete /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/Actions/{Action ID}?api-version=2015-03-20

### <a name="alert-actions"></a>Ações de alerta
Uma agenda deve ter apenas uma ação de alerta.  As ações de alerta têm uma ou mais das seções na tabela a seguir.  Cada um é descrito em mais detalhes abaixo.

| Section | Descrição | Utilização |
|:--- |:--- |:--- |
| Os |Critérios para quando a ação é executada.| Necessário para cada alerta, antes ou depois que eles são estendidos para o Azure. |
| Gravidade |Rótulo usado para classificar o alerta quando disparado.| Necessário para cada alerta, antes ou depois que eles são estendidos para o Azure. |
| Eliminação |Opção para parar as notificações do alerta. | Opcional para cada alerta, antes ou depois que eles são estendidos para o Azure. |
| Grupos de Ações |IDs do Azure Actionproperty em que as ações necessárias são especificadas, como-emails, SMSs, chamadas de voz, WebHooks, Runbooks de automação, conectores de ITSM, etc.| Necessário depois que os alertas são estendidos para o Azure|
| Personalizar ações|Modificar a saída padrão para selecionar ações do The Action| Opcional para cada alerta, pode ser usado depois que os alertas são estendidos para o Azure. |

### <a name="thresholds"></a>Limites
Uma ação de alerta deve ter apenas um limite.  Quando os resultados de uma pesquisa salva correspondem ao limite em uma ação associada a essa pesquisa, todos os outros processos nessa ação são executados.  Uma ação também pode conter apenas um limite para que possa ser usada com ações de outros tipos que não contêm limites.

Os limites têm as propriedades na tabela a seguir.

| Propriedade | Descrição |
|:--- |:--- |
| `Operator` |Operador para a comparação de limites. <br> gt = maior que <br> lt = menor que |
| `Value` |Valor para o limite. |

Por exemplo, considere uma consulta de evento com um intervalo de 15 minutos, um TimeSpan de 30 minutos e um limite maior que 10. Nesse caso, a consulta seria executada a cada 15 minutos e um alerta seria disparado se ele retornasse 10 eventos que foram criados em um período de 30 minutos.

Veja a seguir uma resposta de exemplo para uma ação com apenas um limite.  

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

Use o método Put com uma ID de ação exclusiva para criar uma nova ação de limite para um agendamento.  

    $thresholdJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

Use o método Put com uma ID de ação existente para modificar uma ação de limite para um agendamento.  O corpo da solicitação deve incluir a ETag da ação.

    $thresholdJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

#### <a name="severity"></a>Gravidade
Log Analytics permite que você classifique seus alertas em categorias, para permitir o gerenciamento e a triagem mais fáceis. A severidade do alerta definida é: informativo, aviso e crítico. Eles são mapeados para a escala de gravidade normalizada dos alertas do Azure como:

|Nível de severidade de Log Analytics  |Nível de severidade dos alertas do Azure  |
|---------|---------|
|`critical` |Sev 0|
|`warning` |Sev 1|
|`informational` | Sev 2|

Veja a seguir uma resposta de exemplo para uma ação com apenas um limite e uma severidade. 

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

Use o método Put com uma ID de ação exclusiva para criar uma nova ação para uma agenda com severidade.  

    $thresholdWithSevJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdWithSevJson

Use o método Put com uma ID de ação existente para modificar uma ação de severidade para uma agenda.  O corpo da solicitação deve incluir a ETag da ação.

    $thresholdWithSevJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdWithSevJson

#### <a name="suppress"></a>Eliminação
Os alertas de consulta baseados em Log Analytics serão acionados sempre que o limite for atingido ou excedido. Com base na lógica implícita na consulta, isso pode resultar em um alerta sendo acionado para uma série de intervalos e, portanto, as notificações também são enviadas constantemente. Para evitar esse cenário, um usuário pode definir a opção de supressão, instruindo Log Analytics a esperar por um período de tempo estipulado antes que a notificação seja disparada pela segunda vez para a regra de alerta. Portanto, se suprimir estiver definido por 30 minutos; em seguida, o alerta será acionado pela primeira vez e enviará notificações configuradas. Mas, em seguida, aguarde 30 minutos, antes de a notificação para a regra de alerta ser usada novamente. No período intermediário, a regra de alerta continuará a ser executada somente quando a regra de alerta for suprimida por Log Analytics durante o tempo especificado, independentemente de quantas vezes ela foi disparada nesse período.

A propriedade suprimir da regra de alerta Log Analytics é especificada usando o valor de *limitação* e o período de supressão usando o valor *DurationInMinutes* .

Veja a seguir uma resposta de exemplo para uma ação com apenas um limite, severidade e propriedade de supressão

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

Use o método Put com uma ID de ação exclusiva para criar uma nova ação para uma agenda com severidade.  

    $AlertSuppressJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Throttling': { 'DurationInMinutes': 30 },'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myalert?api-version=2015-03-20 $AlertSuppressJson

Use o método Put com uma ID de ação existente para modificar uma ação de severidade para uma agenda.  O corpo da solicitação deve incluir a ETag da ação.

    $AlertSuppressJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Throttling': { 'DurationInMinutes': 30 },'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myalert?api-version=2015-03-20 $AlertSuppressJson

#### <a name="action-groups"></a>Grupos de Ações
Todos os alertas no Azure, use o grupo de ações como o mecanismo padrão para manipular ações. Com o grupo de ações, você pode especificar suas ações uma vez e associar o grupo de ações a vários alertas – no Azure. Sem a necessidade, para declarar repetidamente as mesmas ações várias vezes. Os grupos de ação dão suporte a várias ações, incluindo email, SMS, chamada de voz, conexão de ITSM, runbook de automação, URI de webhook e muito mais. 

Para usuários que estenderam seus alertas para o Azure – uma agenda agora deve ter detalhes do grupo de ações aprovados junto com o limite para poder criar um alerta. Detalhes de email, URLs de webhook, detalhes de automação do runbook e outras ações, precisam ser definidos no primeiro grupo de ações antes de criar um alerta; é possível criar um [grupo de ações de Azure monitor](../../azure-monitor/platform/action-groups.md) no portal ou usar a [API do grupo de ações](https://docs.microsoft.com/rest/api/monitor/actiongroups).

Para adicionar Associação de grupo de ações a um alerta, especifique a ID de Azure Resource Manager exclusiva do grupo de ação na definição de alerta. Uma ilustração de exemplo é fornecida abaixo:

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

Use o método Put com uma ID de ação exclusiva para associar o grupo de ações já existente a um agendamento.  Veja a seguir uma ilustração de exemplo de uso.

    $AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

Use o método Put com uma ID de ação existente para modificar um grupo de ação associado a um agendamento.  O corpo da solicitação deve incluir a ETag da ação.

    $AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

#### <a name="customize-actions"></a>Personalizar ações
Por padrão, as ações seguem o modelo padrão e o formato para notificações. Mas o usuário pode personalizar algumas ações, mesmo se elas forem controladas por grupos de ação. Atualmente, a personalização é possível para o assunto do email e o conteúdo do webhook.

##### <a name="customize-e-mail-subject-for-action-group"></a>Personalizar o assunto do email para o grupo de ações
Por padrão, o assunto do email para alertas é: `<AlertName>` de notificação de alerta para `<WorkspaceName>`. Mas isso pode ser personalizado, para que você possa usar palavras ou marcas específicas – para permitir que você empregue facilmente regras de filtro em sua caixa de entrada. Os detalhes do cabeçalho Personalizar email precisam ser enviados junto com os detalhes do conjunto de ações, como no exemplo abaixo.

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

Use o método Put com uma ID de ação exclusiva para associar o grupo de ações já existente à personalização de um agendamento.  Veja a seguir uma ilustração de exemplo de uso.

    $AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'], 'CustomEmailSubject': 'Azure Alert fired'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

Use o método Put com uma ID de ação existente para modificar um grupo de ação associado a um agendamento.  O corpo da solicitação deve incluir a ETag da ação.

    $AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']}, 'CustomEmailSubject': 'Azure Alert fired' }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

##### <a name="customize-webhook-payload-for-action-group"></a>Personalizar o conteúdo do webhook para o grupo de ações
Por padrão, o webhook enviado por meio do grupo de ações para o log Analytics tem uma estrutura fixa. Mas um pode personalizar a carga JSON usando variáveis específicas com suporte, para atender aos requisitos do ponto de extremidade do webhook. Para obter mais informações, consulte [ação de webhook para regras de alerta de log](../../azure-monitor/platform/alerts-log-webhook.md). 

Os detalhes de webhook personalizados precisam ser enviados junto com os detalhes do grupo de ações e serão aplicados a todos os URI do webhook especificados dentro do grupos de ações; como no exemplo abaixo.

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

Use o método Put com uma ID de ação exclusiva para associar o grupo de ações já existente à personalização de um agendamento.  Veja a seguir uma ilustração de exemplo de uso.

    $AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'], 'CustomEmailSubject': 'Azure Alert fired','CustomWebhookPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

Use o método Put com uma ID de ação existente para modificar um grupo de ação associado a um agendamento.  O corpo da solicitação deve incluir a ETag da ação.

    $AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']}, 'CustomEmailSubject': 'Azure Alert fired','CustomWebhookPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}' }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson


## <a name="next-steps"></a>Passos seguintes

* Use a [API REST para executar pesquisas de log](../../azure-monitor/log-query/log-query-overview.md) em log Analytics.
* Saiba mais sobre os [alertas de log no Azure monitor](../../azure-monitor/platform/alerts-unified-log.md)
* Como [criar, editar ou gerenciar regras de alerta de log no Azure monitor](../../azure-monitor/platform/alerts-log.md)

