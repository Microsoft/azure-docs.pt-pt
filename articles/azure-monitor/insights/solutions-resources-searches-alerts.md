---
title: Pesquisas guardadas em soluções de gestão | Documentos da Microsoft
description: Soluções de gestão incluem, geralmente, pesquisas guardadas no Log Analytics para analisar dados recolhidos pela solução. Eles podem também definir alertas para notificar o usuário ou automaticamente tomar medidas em resposta a um problema crítico. Este artigo descreve como definir pesquisas guardadas num modelo do Resource Manager, para que possam ser incluídos em soluções de gestão do Log Analytics.
services: monitoring
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2019
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f8590c9ef89e68a823beefd7e74a894edd219359
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57779390"
---
# <a name="adding-log-analytics-saved-searches-and-alerts-to-management-solution-preview"></a>Adicionar o Log Analytics guardar pesquisas e alertas para solução de gestão (pré-visualização)

> [!IMPORTANT]
> Os detalhes para a criação de um alerta com um modelo do Resource Manager são de data agora que [alertas do Log Analytics tem sido expandidas para o Azure Monitor](../platform/alerts-extend.md). Para obter detalhes sobre como criar um alerta de registo com um modelo do Resource Manager, consulte [gerir alertas de registo com o modelo de recursos do Azure](../platform/alerts-log.md#managing-log-alerts-using-azure-resource-template).

> [!NOTE]
> Esta é a documentação preliminar para a criação de soluções de gestão que estão atualmente em pré-visualização. Qualquer esquema descrita abaixo está sujeitas a alterações.

[Soluções de gestão](solutions.md) normalmente, irá incluir [pesquisas guardadas](../../azure-monitor/log-query/log-query-overview.md) no Log Analytics para analisar dados recolhidos pela solução. Podem também definir [alertas](../../azure-monitor/platform/alerts-overview.md) para notificar o utilizador ou efetuar automaticamente a ação em resposta a um problema crítico. Este artigo descreve como definir o Log Analytics pesquisas guardadas e alertas numa [modelo de gestão de recursos](../../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md) para que possam ser incluídos na [soluções de gestão](solutions-creating.md).

> [!NOTE]
> Os exemplos neste artigo utilizam parâmetros e variáveis que são necessárias ou comuns para soluções de gestão e descrito em [estrutura e compilação de uma solução de gestão no Azure](solutions-creating.md)

## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que já está familiarizado com a [criar uma solução de gestão](solutions-creating.md) e a estrutura de um [modelo do Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md) e o arquivo da solução.


## <a name="log-analytics-workspace"></a>Área de trabalho do Log Analytics
Todos os recursos do Log Analytics estão contidos num [área de trabalho](../../azure-monitor/platform/manage-access.md). Conforme descrito em [área de trabalho do Log Analytics e a conta de automatização](solutions.md#log-analytics-workspace-and-automation-account), a área de trabalho não está incluída na solução de gestão, mas tem de existir antes da solução está instalada. Se não estiver disponível, em seguida, a instalação da solução falha.

O nome da área de trabalho é no nome de cada recurso do Log Analytics. Isso é feito na solução com o **área de trabalho** parâmetro como no seguinte exemplo de um recurso de SavedSearch.

    "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearchId'))]"

## <a name="log-analytics-api-version"></a>Versão de API de análise do registo
Todos os recursos do Log Analytics definidos num modelo do Resource Manager de ter uma propriedade **apiVersion** que define a versão da API deve usar o recurso.

A tabela seguinte lista a versão de API para o recurso utilizado neste exemplo.

| Tipo de recurso | Versão de API | Consulta |
|:---|:---|:---|
| savedSearches | 2017-03-15-pré-visualização | Event &#124; where EventLevelName == "Error"  |


## <a name="saved-searches"></a>Pesquisas Guardadas
Incluem [pesquisas guardadas](../../azure-monitor/log-query/log-query-overview.md) numa solução para permitir que os utilizadores para consultar os dados recolhidos pela sua solução. Pesquisas de guardado, são apresentados em **pesquisas guardadas** no portal do Azure. Uma pesquisa guardada também é necessária para cada alerta.

[A pesquisa guardada do log Analytics](../../azure-monitor/log-query/log-query-overview.md) recursos têm um tipo de `Microsoft.OperationalInsights/workspaces/savedSearches` e ter a seguinte estrutura. Isto inclui as variáveis e parâmetros comuns, para que pode copiar e cole este fragmento de código no seu ficheiro de solução e alterar os nomes de parâmetro.

    {
        "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearch').Name)]",
        "type": "Microsoft.OperationalInsights/workspaces/savedSearches",
        "apiVersion": "[variables('LogAnalyticsApiVersion')]",
        "dependsOn": [
        ],
        "tags": { },
        "properties": {
            "etag": "*",
            "query": "[variables('SavedSearch').Query]",
            "displayName": "[variables('SavedSearch').DisplayName]",
            "category": "[variables('SavedSearch').Category]"
        }
    }

Cada propriedade de uma procura guardada é descrita na tabela seguinte.

| Propriedade | Descrição |
|:--- |:--- |
| categoria | A categoria para a pesquisa guardada.  Qualquer pesquisas guardadas na mesma solução, muitas vezes, irão partilhar uma única categoria para que estes são agrupados em conjunto na consola do. |
| DisplayName | Nome a apresentar para a pesquisa guardada no portal. |
| consulta | Consulta seja executada. |

> [!NOTE]
> Poderá ter de utilizar os carateres de escape na consulta, se ele inclui carateres que poderão ser interpretados como JSON. Por exemplo, se a consulta foi **AzureActivity | OperationName:"Microsoft.Compute/virtualMachines/write"**, deve ser gravado no arquivo da solução como **AzureActivity | OperationName: /\"Microsoft.Compute/virtualMachines/write\"**.

## <a name="alerts"></a>Alertas
[Alertas de registo do Azure](../../azure-monitor/platform/alerts-unified-log.md) são criados pelas regras de alertas do Azure que executam consultas de registo especificado em intervalos regulares. Se os resultados da consulta corresponderem a critérios especificados, é criado um registo de alerta e uma ou mais ações são executadas usando [grupos de ação](../../azure-monitor/platform/action-groups.md).

> [!NOTE]
> Todos os alertas numa instância de cloud pública do Azure da área de trabalho do Log Analytics a partir de 14 de Maio de 2018, começaram a expandir para o Azure. Para obter mais informações, consulte [expandir alertas para o Azure](../../azure-monitor/platform/alerts-extend.md). Para os utilizadores que estendem os alertas para o Azure, as ações agora são controladas em grupos de ação do Azure. Quando uma área de trabalho e os seus alertas são expandidas para o Azure, pode obter ou adicionar ações utilizando o [grupo de ação - modelo do Azure Resource Manager](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).
Regras de alerta numa solução de gestão são formadas dos seguintes três recursos diferentes.

- **Pesquisa guardada.** Define a pesquisa de registos que é executada. Várias regras de alerta podem partilhar uma única pesquisa guardada.
- **Agenda.** Define a frequência com que a pesquisa de registos é executada. Cada regra de alerta tem apenas um agendamento.
- **Ação do alerta.** Cada regra de alerta é um recurso do grupo de ação ou ação recursos (herdado) com um tipo de **alerta** que define os detalhes do alerta, como os critérios para quando é criado um registo de alerta e a gravidade do alerta. [Grupo de ação](../../azure-monitor/platform/action-groups.md) recursos podem ter uma lista de ações configuradas a tomar quando o alerta é acionado - por exemplo, a chamada de voz, SMS, e-mail, webhook, a ferramenta ITSM, runbook de automatização, aplicação lógica, etc.

O recurso de ação (Legado), opcionalmente, vai definir uma resposta de correio e o runbook.
- **Ação do Webhook (Legado).** Se a regra de alerta chama um webhook, em seguida, ele requer um recurso de qualquer ação adicional com um tipo de **Webhook**.

Guardar pesquisa recursos estão descritos acima. Os outros recursos estão descritos abaixo.

### <a name="schedule-resource"></a>Recurso de agenda

Uma pesquisa guardada pode ter uma ou mais agendas com cada agenda que representa uma regra de alerta separada. O plano define a frequência com que a pesquisa é a execução e o intervalo de tempo durante o qual os dados são recuperados. Recursos de agendamento tem um tipo de `Microsoft.OperationalInsights/workspaces/savedSearches/schedules/` e ter a seguinte estrutura. Isto inclui as variáveis e parâmetros comuns, para que pode copiar e cole este fragmento de código no seu ficheiro de solução e alterar os nomes de parâmetro.

    {
        "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearch').Name, '/', variables('Schedule').Name)]",
        "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/",
        "apiVersion": "[variables('LogAnalyticsApiVersion')]",
        "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('SavedSearch').Name)]"
        ],
        "properties": {
            "etag": "*",
            "interval": "[variables('Schedule').Interval]",
            "queryTimeSpan": "[variables('Schedule').TimeSpan]",
            "enabled": "[variables('Schedule').Enabled]"
        }
    }
As propriedades de recursos de agenda são descritas na tabela seguinte.
| Nome do elemento | Necessário | Descrição |
|:--|:--|:--|
| enabled       | Sim | Especifica se o alerta é ativado quando é criado. |
| intervalo      | Sim | A frequência com que a consulta é executada em minutos. |
| queryTimeSpan | Sim | Período de tempo em minutos sobre o qual avaliar os resultados. |
O recurso de agenda deve depender a pesquisa guardada para que ele seja criado antes da agenda.
> [!NOTE]
> Nome da agenda tem de ser exclusivo numa determinada área de trabalho; duas agendas não podem ter o mesmo ID, mesmo que estejam associados a diferentes pesquisas guardadas. Também o nome para pesquisas guardadas tudo, cronogramas e criadas com a API de análise de registo de ações deve estar em minúsculas.

### <a name="actions"></a>Ações
Uma agenda pode ter várias ações. Uma ação pode definir um ou mais processos para efetuar como enviar um email ou iniciar um runbook ou ele pode definir um limiar que determina quando os resultados de uma pesquisa corresponderem a critérios. Algumas ações definir ambos, para que os processos são executados quando o limiar for cumprido.
Ações podem ser definidas com o recurso de [grupo de ação] ou recurso de ação.
> [!NOTE]
> A partir de 14 de Maio de 2018, começaram a todos os alertas numa instância de cloud pública do Azure da área de trabalho do Log Analytics estender automaticamente para o Azure. Para obter mais informações, consulte [expandir alertas para o Azure](../../azure-monitor/platform/alerts-extend.md). Para os utilizadores que estendem os alertas para o Azure, as ações agora são controladas em grupos de ação do Azure. Quando uma área de trabalho e os seus alertas são expandidas para o Azure, pode obter ou adicionar ações utilizando o [grupo de ação - modelo do Azure Resource Manager](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).
Existem dois tipos de recurso de ação especificada pelos **tipo** propriedade. Uma agenda exige um deles **alerta** ação, que define os detalhes da regra de alerta e quais ações são executadas quando é criado um alerta. Recursos de ação tem um tipo de `Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions`.

Ações de alerta tem a seguinte estrutura. Isto inclui as variáveis e parâmetros comuns, para que pode copiar e cole este fragmento de código no seu ficheiro de solução e alterar os nomes de parâmetro.

```json
{
    "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearch').Name, '/', variables('Schedule').Name, '/', variables('Alert').Name)]",
    "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions",
    "apiVersion": "[variables('LogAnalyticsApiVersion')]",
    "dependsOn": [
        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('SavedSearch').Name, '/schedules/', variables('Schedule').Name)]"
    ],
    "properties": {
        "etag": "*",
        "type": "Alert",
        "name": "[variables('Alert').Name]",
        "description": "[variables('Alert').Description]",
        "severity": "[variables('Alert').Severity]",
        "threshold": {
            "operator": "[variables('Alert').Threshold.Operator]",
            "value": "[variables('Alert').Threshold.Value]",
            "metricsTrigger": {
                "triggerCondition": "[variables('Alert').Threshold.Trigger.Condition]",
                "operator": "[variables('Alert').Trigger.Operator]",
                "value": "[variables('Alert').Trigger.Value]"
            },
        },
        "AzNsNotification": {
            "GroupIds": "[variables('MyAlert').AzNsNotification.GroupIds]",
            "CustomEmailSubject": "[variables('MyAlert').AzNsNotification.CustomEmailSubject]",
            "CustomWebhookPayload": "[variables('MyAlert').AzNsNotification.CustomWebhookPayload]"
        }
    }
}
```

As propriedades de recursos de ação do alerta são descritas nas tabelas seguintes.

| Nome do elemento | Necessário | Descrição |
|:--|:--|:--|
| Type | Sim | Tipo de ação.  Isto é **alerta** para ações de alerta. |
| Name | Sim | Nome a apresentar para o alerta.  Este é o nome que é apresentado na consola para a regra de alerta. |
| Descrição | Não | Descrição opcional do alerta. |
| Gravidade | Sim | Gravidade de registo de alerta de entre os valores seguintes:<br><br> **critical**<br>**warning**<br>**informational**


#### <a name="threshold"></a>Limiar
Esta secção é necessária. Define as propriedades para o limiar de alerta.

| Nome do elemento | Necessário | Descrição |
|:--|:--|:--|
| Operador | Sim | Operador de comparação entre os valores seguintes:<br><br>**gt = maior que<br>lt = menor que** |
| Value | Sim | O valor a comparar os resultados. |

##### <a name="metricstrigger"></a>MetricsTrigger
Esta secção é opcional. Incluí-lo para um alerta de medida da métrica.

> [!NOTE]
> Alertas de medida da métrica estão atualmente em pré-visualização pública.

| Nome do elemento | Necessário | Descrição |
|:--|:--|:--|
| TriggerCondition | Sim | Especifica se o limiar de número total de falhas ou falhas consecutivas de entre os valores seguintes:<br><br>**Total<br>consecutivos** |
| Operador | Sim | Operador de comparação entre os valores seguintes:<br><br>**gt = maior que<br>lt = menor que** |
| Value | Sim | Número de vezes que os critérios têm de ser cumpridos para acionar o alerta. |


#### <a name="throttling"></a>Limitação
Esta secção é opcional. Inclua nesta secção, se desejar suprimir alertas a partir da mesma regra por um determinado período de tempo após a criação de um alerta.

| Nome do elemento | Necessário | Descrição |
|:--|:--|:--|
| DurationInMinutes | Sim, se o elemento incluído de limitação | Número de minutos para suprimir alertas depois da mesma regra de alerta é criada. |

#### <a name="azure-action-group"></a>Grupo de ação do Azure
Todos os alertas no Azure, utilize o grupo de ação como o mecanismo predefinido para a manipulação de ações. Com o grupo de ação, pode especificar as suas ações de uma vez e, em seguida, associar o grupo de ação para múltiplos alertas - em todo o Azure. Sem a necessidade, repetidamente declarar as mesmas ações repetidamente. Os grupos de ação suportam várias ações - incluindo e-mail, SMS, chamada de voz, a ligação ITSM, Runbook de automatização, Webhook URI e muito mais.

Para o utilizador que tiver expandido o seus alertas no Azure - uma agenda já deve ter os detalhes do grupo de ação transmitidos juntamente com o limiar, para poder criar um alerta. Detalhes de email, URLs de Webhook, detalhes de automatização de Runbook e outras ações, tem de ser definido no lado primeiro antes, criando um alerta, um grupo de ação é possível criar [grupo de ação do Azure Monitor](../../azure-monitor/platform/action-groups.md) no Portal ou de utilização [grupo de ação - modelo do Resource](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).

| Nome do elemento | Necessário | Descrição |
|:--|:--|:--|
| AzNsNotification | Sim | O ID de recurso do grupo de ação do Azure a ser associado ao alerta para tomar as medidas necessárias quando critérios de alerta são cumpridos. |
| CustomEmailSubject | Não | Linha de requerente personalizado de correio enviado para todos os endereços especificados no grupo de ação associada. |
| CustomWebhookPayload | Não | Payload personalizado para ser enviado para todos os pontos finais de webhook definidos no grupo de ação associada. O formato depende do que o webhook está esperando e deve ser um JSON serializado válido. |

#### <a name="actions-for-oms-legacy"></a>Ações para o OMS (Legado)

Cada agenda tem um **alerta** ação. Isso define os detalhes do alerta e, opcionalmente, ações de notificação e remediação. Uma notificação envia um e-mail para um ou mais endereços. Uma remediação inicia um runbook na automatização do Azure para tentar corrigir o problema detetado.

> [!NOTE]
> A partir de 14 de Maio de 2018, começaram a todos os alertas numa instância de cloud pública do Azure da área de trabalho do Log Analytics estender automaticamente para o Azure. Para obter mais informações, consulte [expandir alertas para o Azure](../../azure-monitor/platform/alerts-extend.md). Para os utilizadores que estendem os alertas para o Azure, as ações agora são controladas em grupos de ação do Azure. Quando uma área de trabalho e os seus alertas são expandidas para o Azure, pode obter ou adicionar ações utilizando o [grupo de ação - modelo do Azure Resource Manager](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).

##### <a name="emailnotification"></a>EmailNotification
 Esta secção é opcional incluí-lo se pretender que o alerta para enviar correio para um ou mais destinatários.

| Nome do elemento | Necessário | Descrição |
|:--|:--|:--|
| Destinatários | Sim | Lista delimitada por vírgulas de endereços de e-mail para enviar a notificação quando é criado um alerta, tal como no exemplo a seguir.<br><br>**[ "recipient1@contoso.com", "recipient2@contoso.com" ]** |
| Requerente | Sim | Assunto da mensagem. |
| Anexo | Não | Anexos não são atualmente suportados. Se este elemento está incluído, não haverá **None**. |

##### <a name="remediation"></a>Remediação
Esta secção é opcional incluí-lo se pretender que um runbook para começar em resposta ao alerta. 

| Nome do elemento | Necessário | Descrição |
|:--|:--|:--|
| RunbookName | Sim | Nome do runbook para começar. |
| WebhookUri | Sim | URI do webhook do runbook. |
| Validade | Não | Data e hora que expira a remediação. |

##### <a name="webhook-actions"></a>Ações de Webhook

Ações de Webhook iniciar um processo chamando uma URL e, opcionalmente, fornecendo um payload de envio. Eles são semelhantes às ações de remediação, exceto que eles se destinam-se a webhooks que pode invocar processos que não seja runbooks de automatização do Azure. Eles fornecem também a opção adicional de fornecer um payload possível entregar o processo remoto.

Se o alerta chamar um webhook, então, esta terá de um recurso de ação com um tipo de **Webhook** além do **alerta** recursos de ação.

    {
      "name": "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearch').Name, '/', variables('Schedule').Name, '/', variables('Webhook').Name)]",
      "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions/",
      "apiVersion": "[variables('LogAnalyticsApiVersion')]",
      "dependsOn": [
        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('SavedSearch').Name, '/schedules/', variables('Schedule').Name)]"
      ],
      "properties": {
        "etag": "*",
        "type": "[variables('Alert').Webhook.Type]",
        "name": "[variables('Alert').Webhook.Name]",
        "webhookUri": "[variables('Alert').Webhook.webhookUri]",
        "customPayload": "[variables('Alert').Webhook.CustomPayLoad]"
      }
    }
As propriedades de recursos de ação do Webhook são descritas nas tabelas seguintes.
| Nome do elemento | Necessário | Descrição |
|:--|:--|:--|
| tipo | Sim | Tipo de ação. Isto é **Webhook** para ações de webhook. |
| nome | Sim | Nome a apresentar para a ação. Não é apresentado na consola do. |
| webhookUri | Sim | URI do webhook. |
| customPayload | Não | Payload personalizado para ser enviado para o webhook. O formato depende o que o webhook está esperando. |

## <a name="sample"></a>Sample

Segue-se um exemplo de uma solução que inclui os seguintes recursos:

- Pesquisa guardada
- Agenda
- Grupo de ações

O exemplo usa [parâmetros de solução padrão]( solutions-solution-file.md#parameters) variáveis que normalmente seriam usadas numa solução em vez de embutir valores nas definições de recursos.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0",
    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
                "Description": "Name of Log Analytics workspace"
            }
        },
        "workspaceregionId": {
            "type": "string",
            "metadata": {
                "Description": "Region of Log Analytics workspace"
            }
        },
        "actiongroup": {
            "type": "string",
            "metadata": {
                "Description": "List of action groups for alert actions separated by semicolon"
            }
        }
    },
    "variables": {
        "SolutionName": "MySolution",
        "SolutionVersion": "1.0",
        "SolutionPublisher": "Contoso",
        "ProductName": "SampleSolution",
        "LogAnalyticsApiVersion-Search": "2017-03-15-preview",
        "LogAnalyticsApiVersion-Solution": "2015-11-01-preview",
        "MySearch": {
            "displayName": "Error records by hour",
            "query": "MyRecord_CL | summarize AggregatedValue = avg(Rating_d) by Instance_s, bin(TimeGenerated, 60m)",
            "category": "Samples",
            "name": "Samples-Count of data"
        },
        "MyAlert": {
            "Name": "[toLower(concat('myalert-',uniqueString(resourceGroup().id, deployment().name)))]",
            "DisplayName": "My alert rule",
            "Description": "Sample alert. Fires when 3 error records found over hour interval.",
            "Severity": "critical",
            "ThresholdOperator": "gt",
            "ThresholdValue": 3,
            "Schedule": {
                "Name": "[toLower(concat('myschedule-',uniqueString(resourceGroup().id, deployment().name)))]",
                "Interval": 15,
                "TimeSpan": 60
            },
            "MetricsTrigger": {
                "TriggerCondition": "Consecutive",
                "Operator": "gt",
                "Value": 3
            },
            "ThrottleMinutes": 60,
            "AzNsNotification": {
                "GroupIds": [
                    "[parameters('actiongroup')]"
                ],
                "CustomEmailSubject": "Sample alert"
            }
        }
    },
    "resources": [
        {
            "name": "[concat(variables('SolutionName'), '[' ,parameters('workspacename'), ']')]",
            "location": "[parameters('workspaceRegionId')]",
            "tags": { },
            "type": "Microsoft.OperationsManagement/solutions",
            "apiVersion": "[variables('LogAnalyticsApiVersion-Solution')]",
            "dependsOn": [
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches', parameters('workspacename'), variables('MySearch').Name)]",
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name)]",
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name, variables('MyAlert').Name)]"
            ],
            "properties": {
                "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspacename'))]",
                "referencedResources": [
                ],
                "containedResources": [
                    "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches', parameters('workspacename'), variables('MySearch').Name)]",
                    "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name)]",
                    "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name, variables('MyAlert').Name)]"
                ]
            },
            "plan": {
                "name": "[concat(variables('SolutionName'), '[' ,parameters('workspaceName'), ']')]",
                "Version": "[variables('SolutionVersion')]",
                "product": "[variables('ProductName')]",
                "publisher": "[variables('SolutionPublisher')]",
                "promotionCode": ""
            }
        },
        {
            "name": "[concat(parameters('workspaceName'), '/', variables('MySearch').Name)]",
            "type": "Microsoft.OperationalInsights/workspaces/savedSearches",
            "apiVersion": "[variables('LogAnalyticsApiVersion-Search')]",
            "dependsOn": [ ],
            "tags": { },
            "properties": {
                "etag": "*",
                "query": "[variables('MySearch').query]",
                "displayName": "[variables('MySearch').displayName]",
                "category": "[variables('MySearch').category]"
            }
        },
        {
            "name": "[concat(parameters('workspaceName'), '/', variables('MySearch').Name, '/', variables('MyAlert').Schedule.Name)]",
            "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/",
            "apiVersion": "[variables('LogAnalyticsApiVersion-Search')]",
            "dependsOn": [
                "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('MySearch').Name)]"
            ],
            "properties": {
                "etag": "*",
                "interval": "[variables('MyAlert').Schedule.Interval]",
                "queryTimeSpan": "[variables('MyAlert').Schedule.TimeSpan]",
                "enabled": true
            }
        },
        {
            "name": "[concat(parameters('workspaceName'), '/', variables('MySearch').Name, '/', variables('MyAlert').Schedule.Name, '/', variables('MyAlert').Name)]",
            "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions",
            "apiVersion": "[variables('LogAnalyticsApiVersion-Search')]",
            "dependsOn": [
                "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('MySearch').Name, '/schedules/', variables('MyAlert').Schedule.Name)]"
            ],
            "properties": {
                "etag": "*",
                "Type": "Alert",
                "Name": "[variables('MyAlert').DisplayName]",
                "Description": "[variables('MyAlert').Description]",
                "Severity": "[variables('MyAlert').Severity]",
                "Threshold": {
                    "Operator": "[variables('MyAlert').ThresholdOperator]",
                    "Value": "[variables('MyAlert').ThresholdValue]",
                    "MetricsTrigger": {
                        "TriggerCondition": "[variables('MyAlert').MetricsTrigger.TriggerCondition]",
                        "Operator": "[variables('MyAlert').MetricsTrigger.Operator]",
                        "Value": "[variables('MyAlert').MetricsTrigger.Value]"
                    }
                },
                "Throttling": {
                    "DurationInMinutes": "[variables('MyAlert').ThrottleMinutes]"
                },
                "AzNsNotification": {
                    "GroupIds": "[variables('MyAlert').AzNsNotification.GroupIds]",
                    "CustomEmailSubject": "[variables('MyAlert').AzNsNotification.CustomEmailSubject]"
                }
            }
        }
    ]
}
```

O ficheiro de parâmetros seguintes fornece valores de exemplos para esta solução.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspacename": {
            "value": "myWorkspace"
        },
        "accountName": {
            "value": "myAccount"
        },
        "workspaceregionId": {
            "value": "East US"
        },
        "regionId": {
            "value": "East US 2"
        },
        "pricingTier": {
            "value": "Free"
        },
        "actiongroup": {
            "value": "/subscriptions/3b540246-808d-4331-99aa-917b808a9166/resourcegroups/myTestGroup/providers/microsoft.insights/actiongroups/sample"
        }
    }
}
```

## <a name="next-steps"></a>Passos Seguintes
* [Adicionar vistas](solutions-resources-views.md) à sua solução de gestão.
* [Adicionar runbooks de automatização e outros recursos](solutions-resources-automation.md) à sua solução de gestão.
