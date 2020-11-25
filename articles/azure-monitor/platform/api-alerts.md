---
title: Utilização de alerta de log analytics REST API
description: A API de Alerta de Log Analytics permite-lhe criar e gerir alertas no Log Analytics, que faz parte do Log Analytics.  Este artigo fornece detalhes da API e vários exemplos para a realização de diferentes operações.
ms.subservice: logs
ms.topic: conceptual
ms.date: 07/29/2018
ms.openlocfilehash: dce340db90c1528c46c1be0bc172751a04feaf31
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96006409"
---
# <a name="create-and-manage-alert-rules-in-log-analytics-with-rest-api"></a>Criar e gerir regras de alerta em Log Analytics com REST API 

> [!IMPORTANT]
> Conforme [anunciado,](https://azure.microsoft.com/updates/switch-api-preference-log-alerts/)log analytics workspace(s) criado após 1 de *junho de 2019* gere regras de alerta usando a atual [API de Regras De Castas.](/rest/api/monitor/scheduledqueryrules/) Os clientes são encorajados [a mudar para a API atual](./alerts-log-api-switch.md) em espaços de trabalho mais antigos para aproveitar os [benefícios](./alerts-log-api-switch.md#benefits)agendados do Azure MonitorQueryRules . Este artigo descreve a gestão das regras de alerta usando a API legado.

A API de Alerta de Log Analytics permite-lhe criar e gerir alertas em Log Analytics.  Este artigo fornece detalhes da API e vários exemplos para a realização de diferentes operações.

O Log Analytics Search REST API é RESTful e pode ser acedido através da API do Gestor de Recursos Azure. Neste documento, encontrará exemplos onde a API é acedida a partir de uma linha de comando PowerShell utilizando  [o ARMClient](https://github.com/projectkudu/ARMClient), uma ferramenta de linha de comando de código aberto que simplifica a invocação da API do Gestor de Recursos Azure. A utilização de ARMClient e PowerShell é uma das muitas opções para aceder à API de Pesquisa de Ambulasção de Log Analytics. Com estas ferramentas, pode utilizar a API restful Azure Resource Manager para fazer chamadas para log analytics e executar comandos de pesquisa dentro deles. A API irá obter resultados de pesquisa para si no formato JSON, permitindo-lhe utilizar os resultados da pesquisa de várias maneiras programáticamente.

## <a name="prerequisites"></a>Pré-requisitos
Atualmente, os alertas só podem ser criados com uma pesquisa guardada no Log Analytics.  Pode consultar a [API do Log Search REST](../log-query/log-query-overview.md) para obter mais informações.

## <a name="schedules"></a>Agendas
Uma pesquisa guardada pode ter um ou mais horários. O calendário define a frequência com que a pesquisa é executada e o intervalo de tempo sobre o qual os critérios são identificados.
Os horários têm as propriedades na tabela seguinte.

| Propriedade | Descrição |
|:--- |:--- |
| Intervalo |Quantas vezes a procura é executada. Medido em minutos. |
| ConsultaTimespan |O intervalo de tempo sobre o qual os critérios são avaliados. Deve ser igual ou maior que intervalo. Medido em minutos. |
| Versão |A versão API está a ser usada.  Atualmente, este deve ser sempre definido para 1. |

Por exemplo, considere uma consulta de evento com um intervalo de 15 minutos e um Timespan de 30 minutos. Neste caso, a consulta seria executada a cada 15 minutos, e um alerta seria desencadeado se os critérios continuassem a ser verdadeiros ao longo de um período de 30 minutos.

### <a name="retrieving-schedules"></a>Horários de recuperação
Utilize o método Get para recuperar todos os horários para uma pesquisa guardada.

```powershell
armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules?api-version=2015-03-20
```

Utilize o método Get com um ID de horário para recuperar um determinado horário para uma pesquisa guardada.

```powershell
armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20
```

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
Utilize o método Put com um ID de horário único para criar um novo horário.  Dois horários não podem ter o mesmo ID mesmo que estejam associados a diferentes pesquisas guardadas.  Quando cria um horário na consola Log Analytics, é criado um GUID para o ID de programação.

> [!NOTE]
> O nome de todas as pesquisas, horários e ações guardados criados com a API do Log Analytics deve ser minúsculo.

```powershell
$scheduleJson = "{'properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Enabled':'true' } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson
```

### <a name="editing-a-schedule"></a>Edição de um horário
Utilize o método 'Colocar' com um ID de horário existente para a mesma pesquisa guardada para modificar esse horário; em exemplo abaixo do horário é desativado. O corpo do pedido deve incluir o *etag* da programação.

```powershell
$scheduleJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\""','properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Enabled':'false' } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson
```

### <a name="deleting-schedules"></a>Excluir horários
Utilize o método Eliminar com um ID de programação para eliminar um horário.

```powershell
armclient delete /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20
```

## <a name="actions"></a>Ações
Um horário pode ter várias ações. Uma ação pode definir um ou mais processos para executar, como o envio de um correio ou o início de um livro de execução, ou pode definir um limiar que determina quando os resultados de uma pesquisa correspondem a alguns critérios.  Algumas ações definirão ambas para que os processos sejam realizados quando o limiar for atingido.

Todas as ações têm as propriedades na tabela seguinte.  Diferentes tipos de alertas têm diferentes propriedades adicionais, que são descritas abaixo.

| Propriedade | Descrição |
|:--- |:--- |
| `Type` |Tipo de ação.  Atualmente os valores possíveis são Alert e Webhook. |
| `Name` |Mostrar o nome do alerta. |
| `Version` |A versão API está a ser usada.  Atualmente, este deve ser sempre definido para 1. |

### <a name="retrieving-actions"></a>Ações de recuperação

Utilize o método Get para recuperar todas as ações para um horário.

```powershell
armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules/{Schedule ID}/actions?api-version=2015-03-20
```

Utilize o método Get com o ID de ação para recuperar uma determinada ação para um horário.

```powershell
armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/actions/{Action ID}?api-version=2015-03-20
```

### <a name="creating-or-editing-actions"></a>Criar ou editar ações
Utilize o método Put com um ID de ação único na programação para criar uma nova ação.  Quando cria uma ação na consola Log Analytics, um GUID é para o ID de ação.

> [!NOTE]
> O nome de todas as pesquisas, horários e ações guardados criados com a API do Log Analytics deve ser minúsculo.

Utilize o método Put com um ID de ação existente para a mesma pesquisa guardada para modificar esse horário.  O corpo do pedido deve incluir o etag da programação.

O formato de pedido para a criação de uma nova ação varia consoando por tipo de ação, pelo que estes exemplos são fornecidos nas secções abaixo.

### <a name="deleting-actions"></a>Exclusão de ações

Utilize o método Eliminar com o ID de ação para eliminar uma ação.

```powershell
armclient delete /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/Actions/{Action ID}?api-version=2015-03-20
```

### <a name="alert-actions"></a>Ações de Alerta
A Agenda deve ter uma e única ação de alerta.  As ações de alerta têm uma ou mais secções na tabela seguinte.  Cada um é descrito em mais detalhes abaixo.

| Section | Descrição | Utilização |
|:--- |:--- |:--- |
| Limiar |Critérios para quando a ação é executada.| Necessário para cada alerta, antes ou depois de estendidos ao Azure. |
| Gravidade |Etiqueta usada para classificar o alerta quando acionado.| Necessário para cada alerta, antes ou depois de estendidos ao Azure. |
| Suprimir |Opção para impedir notificações de alerta. | Opcional para cada alerta, antes ou depois de serem estendidos ao Azure. |
| Grupos de Ação |IDs do Azure ActionGroup onde as ações necessárias são especificadas, como - E-Mails, SMSs, Chamadas de Voz, Webhooks, Cartões de Automação, Conectores ITSM, etc.| Uma vez que os alertas são estendidos ao Azure|
| Personalizar ações|Modificar a saída padrão para ações selecionadas do ActionGroup| Opcional para cada alerta, pode ser usado após alertas estendidos ao Azure. |

### <a name="thresholds"></a>Limiares
Uma ação de alerta deve ter um e apenas um limiar.  Quando os resultados de uma pesquisa guardada correspondem ao limiar de uma ação associada a essa pesquisa, então quaisquer outros processos nessa ação são executados.  Uma ação também pode conter apenas um limiar para que possa ser usada com ações de outros tipos que não contenham limiares.

Os limiares têm as propriedades na tabela seguinte.

| Propriedade | Descrição |
|:--- |:--- |
| `Operator` |Operador para a comparação do limiar. <br> gt = Maior do que <br> lt = Menos que |
| `Value` |Valor para o limiar. |

Por exemplo, considere uma consulta de evento com um intervalo de 15 minutos, um Timespan de 30 minutos e um Limiar superior a 10. Neste caso, a consulta seria executada a cada 15 minutos, e um alerta seria desencadeado se devolvesse 10 eventos que foram criados ao longo de um período de 30 minutos.

Segue-se uma resposta de amostra para uma ação com apenas um limiar.  

```json
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
```

Utilize o método Put com um ID de ação único para criar uma nova ação limiar para um horário.  

```powershell
$thresholdJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson
```

Utilize o método Put com um ID de ação existente para modificar uma ação limiar para um horário.  O corpo do pedido deve incluir o ponto de etag da ação.

```powershell
$thresholdJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson
```

#### <a name="severity"></a>Gravidade
O Log Analytics permite-lhe classificar os seus alertas em categorias, para permitir uma gestão e triagem mais fáceis. A gravidade do alerta definida é: informativo, aviso e crítico. Estes são mapeados para a escala de severidade normalizada dos Alertas Azure como:

|Nível de Severidade de Análise de Registo  |Azure alerta o nível de severidade  |
|---------|---------|
|`critical` |Sev 0|
|`warning` |Sev 1|
|`informational` | Sev2|

Segue-se uma resposta de amostra para uma ação com apenas um limiar e gravidade. 

```json
"etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
"properties": {
   "Type": "Alert",
   "Name": "My threshold action",
   "Threshold": {
      "Operator": "gt",
      "Value": 10
   },
   "Severity": "critical",
   "Version": 1
}
```

Utilize o método Put com um ID de ação único para criar uma nova ação para um horário com severidade.  

```powershell
$thresholdWithSevJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdWithSevJson
```

Utilize o método Put com um ID de ação existente para modificar uma ação de gravidade para um horário.  O corpo do pedido deve incluir o ponto de etag da ação.

```powershell
$thresholdWithSevJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdWithSevJson
```

#### <a name="suppress"></a>Suprimir
Os alertas de consulta baseados em Log Analytics dispararão sempre que o limiar for atingido ou excedido. Com base na lógica implícita na consulta, isto pode resultar em alerta para uma série de intervalos e, portanto, notificações também são enviadas constantemente. Para evitar tal cenário, um utilizador pode definir a opção Supressa instruindo o Log Analytics a aguardar um período de tempo estipulado antes de a notificação ser disparada pela segunda vez para a regra de alerta. Assim, se a supressão for definida durante 30 minutos; em seguida, o alerta disparará pela primeira vez e enviará notificações configuradas. Mas, em seguida, aguarde por 30 minutos, antes que a notificação para a regra de alerta seja novamente usada. Durante o período provisório, a regra de alerta continuará a ser executada - apenas a notificação é suprimida pela Log Analytics por tempo especificado, independentemente do número de vezes que a regra de alerta disparada neste período.

Suprir a regra de alerta log Analytics é especificada usando o valor *de Throttling* e o período de supressão usando o valor *DurationInMinutes.*

Segue-se uma resposta de amostra para uma ação com apenas um limiar, gravidade e suprimir propriedade

```json
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
   "Version": 1
}
```

Utilize o método Put com um ID de ação único para criar uma nova ação para um horário com severidade.  

```powershell
$AlertSuppressJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Throttling': { 'DurationInMinutes': 30 },'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myalert?api-version=2015-03-20 $AlertSuppressJson
```

Utilize o método Put com um ID de ação existente para modificar uma ação de gravidade para um horário.  O corpo do pedido deve incluir o ponto de etag da ação.

```powershell
$AlertSuppressJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Throttling': { 'DurationInMinutes': 30 },'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myalert?api-version=2015-03-20 $AlertSuppressJson
```

#### <a name="action-groups"></a>Grupos de Ação
Todos os alertas em Azure, utilize o Action Group como o mecanismo padrão para lidar com as ações. Com o Action Group, pode especificar as suas ações uma vez e, em seguida, associar o grupo de ação a vários alertas - através do Azure. Sem a necessidade de declarar repetidamente as mesmas ações vezes sem conta. Os Grupos de Ação suportam múltiplas ações - incluindo e-mail, SMS, Voice Call, ITSM Connection, Automation Runbook, Webhook URI e muito mais. 

Para os utilizadores que tenham estendido os seus alertas para o Azure - um calendário deve agora ter os detalhes do Action Group passados juntamente com o limiar, para poderem criar um alerta. Os detalhes do e-mail, URLs Webhook, detalhes da Automação Runbook e outras Ações, precisam de ser definidos em primeiro lugar um Grupo de Ação antes de criar um alerta; pode-se criar [o Action Group a partir do Azure Monitor](./action-groups.md) no Portal ou utilizar a [API do Grupo de Ação](/rest/api/monitor/actiongroups).

Para adicionar um alerta à associação de grupo de ação, especifique o ID único do Gestor de Recursos Azure do grupo de ação na definição de alerta. Abaixo é fornecida uma ilustração da amostra:

```json
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
}
```

Utilize o método Put com um ID de ação único para associar o Grupo de Ação já existente para um horário.  Segue-se uma amostra de ilustração de utilização.

```powershell
$AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']} } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson
```

Utilize o método Put com um ID de ação existente para modificar um Grupo de Ação associado a um horário.  O corpo do pedido deve incluir o ponto de etag da ação.

```powershell
$AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', 'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': { 'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'] } } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson
```

#### <a name="customize-actions"></a>Personalizar ações
Por padrão de ações, siga o modelo padrão e o formato para notificações. Mas o utilizador pode personalizar algumas ações, mesmo que sejam controladas por Grupos de Ação. Atualmente, a personalização é possível para o Email Subject e para Webhook Payload.

##### <a name="customize-e-mail-subject-for-action-group"></a>Personalizar e-mail subject para grupo de ação
Por predefinição, o e-mail para alertas é: Notificação de alerta `<AlertName>` para `<WorkspaceName>` . Mas isto pode ser personalizado, para que possa especificar palavras ou tags - para permitir que você use facilmente regras de filtro na sua Caixa de Entrada. Os detalhes do cabeçalho de e-mail personalizado precisam enviar juntamente com os detalhes do ActionGroup, como na amostra abaixo.

```json
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
}
```

Utilize o método Put com um ID de ação único para associar o Grupo de Ação já existente à personalização de um horário.  Segue-se uma amostra de ilustração de utilização.

```powershell
$AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'], 'CustomEmailSubject': 'Azure Alert fired'} } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson
```

Utilize o método Put com um ID de ação existente para modificar um Grupo de Ação associado a um horário.  O corpo do pedido deve incluir o ponto de etag da ação.

```powershell
$AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', 'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']}, 'CustomEmailSubject': 'Azure Alert fired' } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson
```

##### <a name="customize-webhook-payload-for-action-group"></a>Personalizar a carga útil do Webhook para o Grupo de Ação
Por predefinição, o webhook enviado via Action Group para análise de registos tem uma estrutura fixa. Mas pode-se personalizar a carga útil do JSON utilizando variáveis específicas suportadas, para satisfazer os requisitos do ponto final webhook. Para obter mais informações, consulte [a ação webhook para obter regras de alerta de registo](./alerts-log-webhook.md). 

Os detalhes personalizados do webhook precisam enviar juntamente com os detalhes do ActionGroup e serão aplicados a todos os Webhook URI especificados dentro do grupo de ação; como na amostra abaixo.

```json
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
```

Utilize o método Put com um ID de ação único para associar o Grupo de Ação já existente à personalização de um horário.  Segue-se uma amostra de ilustração de utilização.

```powershell
$AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'], 'CustomEmailSubject': 'Azure Alert fired','CustomWebhookPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}'} } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson
```

Utilize o método Put com um ID de ação existente para modificar um Grupo de Ação associado a um horário.  O corpo do pedido deve incluir o ponto de etag da ação.

```powershell
$AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', 'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']}, 'CustomEmailSubject': 'Azure Alert fired','CustomWebhookPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}' } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson
```

## <a name="next-steps"></a>Passos seguintes

* Utilize a [API REST para realizar pesquisas de registo](../log-query/log-query-overview.md) no Log Analytics.
* Saiba mais sobre [alertas de registo no monitor Azure](./alerts-unified-log.md)
* Como [criar, editar ou gerir regras de alerta de registo no monitor Azure](./alerts-log.md)

