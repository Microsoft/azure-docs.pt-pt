---
title: Pesquisas salvas em soluções de gerenciamento | Microsoft Docs
description: As soluções de gerenciamento normalmente incluem consultas de log salvas para analisar os dados coletados pela solução. Este artigo descreve como definir Log Analytics pesquisas salvas em um modelo do Resource Manager.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/29/2019
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 488130fbd2939fa4d98e379126ba3353a417fd72
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75401754"
---
# <a name="adding-log-analytics-saved-searches-and-alerts-to-management-solution-preview"></a>Adicionando Log Analytics pesquisas e alertas salvos à solução de gerenciamento (versão prévia)

> [!IMPORTANT]
> Conforme [anunciado anteriormente](https://azure.microsoft.com/updates/switch-api-preference-log-alerts/), os espaços de trabalho do log Analytics criados após *1º de junho de 2019* – poderão gerenciar regras de alerta usando **apenas** a [API REST](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/)do Azure scheduledQueryRules, o modelo de [Azure Resource Manager](../../azure-monitor/platform/alerts-log.md#managing-log-alerts-using-azure-resource-template) e o cmdlet do [PowerShell](../../azure-monitor/platform/alerts-log.md#managing-log-alerts-using-powershell). Os clientes podem facilmente [alternar seus meios preferenciais de gerenciamento de regras de alerta](../../azure-monitor/platform/alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api) para espaços de trabalho mais antigos para aproveitar Azure monitor scheduledQueryRules como padrão e obter muitos [novos benefícios](../../azure-monitor/platform/alerts-log-api-switch.md#benefits-of-switching-to-new-azure-api) como a capacidade de usar cmdlets nativos do PowerShell, um período de tempo de lookback maior em regras, a criação de regras em um grupo de recursos ou assinatura separado e muito mais.

> [!NOTE]
> Esta é uma documentação preliminar para criar soluções de gerenciamento que estão atualmente em versão prévia. Qualquer esquema descrito abaixo está sujeito a alterações.

As [soluções de gerenciamento](solutions.md) normalmente incluirão [pesquisas salvas](../../azure-monitor/log-query/log-query-overview.md) em log Analytics para analisar os dados coletados pela solução. Eles também podem definir [alertas](../../azure-monitor/platform/alerts-overview.md) para notificar o usuário ou executar automaticamente a ação em resposta a um problema crítico. Este artigo descreve como definir Log Analytics pesquisas e alertas salvos em um [modelo de gerenciamento de recursos](../../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md) para que eles possam ser incluídos em soluções de [Gerenciamento](solutions-creating.md).

> [!NOTE]
> Os exemplos neste artigo usam parâmetros e variáveis que são necessários ou comuns para soluções de gerenciamento e são descritos em [projetar e criar uma solução de gerenciamento no Azure](solutions-creating.md)

## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que você já esteja familiarizado com como [criar uma solução de gerenciamento](solutions-creating.md) e a estrutura de um [modelo do Resource Manager](../../azure-resource-manager/templates/template-syntax.md) e um arquivo de solução.


## <a name="log-analytics-workspace"></a>Área de trabalho do Log Analytics
Todos os recursos no Log Analytics estão contidos em um [espaço de trabalho](../../azure-monitor/platform/manage-access.md). Conforme descrito em [log Analytics espaço de trabalho e conta de automação](solutions.md#log-analytics-workspace-and-automation-account), o espaço de trabalho não é incluído na solução de gerenciamento, mas deve existir antes de a solução ser instalada. Se não estiver disponível, a instalação da solução falhará.

O nome do espaço de trabalho está no nome de cada recurso de Log Analytics. Isso é feito na solução com o parâmetro **Workspace** como no exemplo a seguir de um recurso SavedSearch.

    "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearchId'))]"

## <a name="log-analytics-api-version"></a>Versão da API Log Analytics
Todos os Log Analytics recursos definidos em um modelo do Resource Manager têm uma propriedade **apiVersion** que define a versão da API que o recurso deve usar.

A tabela a seguir lista a versão de API para o recurso usado neste exemplo.

| Tipo de recurso | Versão da API | Consulta |
|:---|:---|:---|
| savedSearches | 2017-03-15-visualização | Evento &#124; em que EventLevelName = = "Error"  |


## <a name="saved-searches"></a>Pesquisas Guardadas
Inclua [pesquisas salvas](../../azure-monitor/log-query/log-query-overview.md) em uma solução para permitir que os usuários consultem os dados coletados por sua solução. As pesquisas salvas aparecem em **pesquisas salvas** no portal do Azure. Uma pesquisa salva também é necessária para cada alerta.

[Log Analytics recursos de pesquisa salvos](../../azure-monitor/log-query/log-query-overview.md) têm um tipo de `Microsoft.OperationalInsights/workspaces/savedSearches` e têm a seguinte estrutura. Isso inclui variáveis e parâmetros comuns para que você possa copiar e colar esse trecho de código em seu arquivo de solução e alterar os nomes de parâmetro.

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

Cada propriedade de uma pesquisa salva é descrita na tabela a seguir.

| Propriedade | Descrição |
|:--- |:--- |
| categoria | A categoria da pesquisa salva.  As pesquisas salvas na mesma solução muitas vezes compartilharão uma única categoria para que sejam agrupadas no console. |
| displayName | Nome a ser exibido para a pesquisa salva no Portal. |
| consulta | Consulta a ser executada. |

> [!NOTE]
> Talvez seja necessário usar caracteres de escape na consulta se eles incluírem caracteres que poderiam ser interpretados como JSON. Por exemplo, se sua consulta foi **AzureActivity | OperationName: "Microsoft. Compute/virtualMachines/Write"** , ele deve ser gravado no arquivo da solução como **AzureActivity | OperationName:/\"Microsoft. Compute/virtualMachines/Write\"** .

## <a name="alerts"></a>Alertas
Os [alertas de log do Azure](../../azure-monitor/platform/alerts-unified-log.md) são criados pelas regras de alerta do Azure que executam consultas de log especificadas em intervalos regulares. Se os resultados da consulta corresponderem aos critérios especificados, um registro de alerta será criado e uma ou mais ações serão executadas usando [grupos de ação](../../azure-monitor/platform/action-groups.md).

Para os utilizadores que estendem os alertas para o Azure, as ações agora são controladas em grupos de ação do Azure. Quando um espaço de trabalho e seus alertas são estendidos para o Azure, você pode recuperar ou adicionar ações usando o [modelo grupo de ação-Azure Resource Manager](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).
As regras de alerta na solução de gerenciamento herdado são formadas pelos três recursos diferentes a seguir.

- **Pesquisa salva.** Define a pesquisa de log que é executada. Várias regras de alerta podem compartilhar uma única pesquisa salva.
- **Agendamento.** Define a frequência com que a pesquisa de log é executada. Cada regra de alerta tem apenas uma agenda.
- **Ação de alerta.** Cada regra de alerta tem um recurso de grupo de ação ou recurso de ação (Herdado) com um tipo de **alerta** que define os detalhes do alerta, como os critérios para quando um registro de alerta é criado e a severidade do alerta. O recurso de [grupo de ações](../../azure-monitor/platform/action-groups.md) pode ter uma lista de ações configuradas a serem tomadas quando o alerta é disparado, como chamada de voz, SMS, email, webhook, ferramenta de ITSM, runbook de automação, aplicativo lógico, etc.

Os recursos de pesquisa salvos são descritos acima. Os outros recursos são descritos abaixo.

### <a name="schedule-resource"></a>Agendar recurso

Uma pesquisa salva pode ter uma ou mais agendas com cada agenda que representa uma regra de alerta separada. A agenda define a frequência com que a pesquisa é executada e o intervalo de tempo durante o qual os dados são recuperados. Os recursos de agendamento têm um tipo de `Microsoft.OperationalInsights/workspaces/savedSearches/schedules/` e têm a seguinte estrutura. Isso inclui variáveis e parâmetros comuns para que você possa copiar e colar esse trecho de código em seu arquivo de solução e alterar os nomes de parâmetro.

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
As propriedades de recursos de agendamento são descritas na tabela a seguir.

| Nome do elemento | Obrigatório | Descrição |
|:--|:--|:--|
| enabled       | Sim | Especifica se o alerta está habilitado quando ele é criado. |
| intervalo      | Sim | Com que frequência a consulta é executada em minutos. |
| queryTimeSpan | Sim | Período de tempo em minutos em que os resultados são avaliados. |

O recurso de agendamento deve depender da pesquisa salva para que ele seja criado antes da agenda.
> [!NOTE]
> O nome da agenda deve ser exclusivo em um determinado espaço de trabalho; dois agendamentos não podem ter a mesma ID, mesmo se estiverem associados a pesquisas salvas diferentes. Também nome para todas as pesquisas salvas, agendas e ações criadas com a API Log Analytics devem estar em minúsculas.

### <a name="actions"></a>Ações
Uma agenda pode ter várias ações. Uma ação pode definir um ou mais processos para efetuar como enviar um email ou iniciar um runbook ou ele pode definir um limiar que determina quando os resultados de uma pesquisa corresponderem a critérios. Algumas ações definir ambos, para que os processos são executados quando o limiar for cumprido.
As ações podem ser definidas usando o recurso [grupo de ações] ou recurso de ação.

Há dois tipos de recurso de ação especificados pela propriedade **Type** . Uma agenda requer uma ação de **alerta** , que define os detalhes da regra de alerta e quais ações são tomadas quando um alerta é criado. Os recursos de ação têm um tipo de `Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions`.

As ações de alerta têm a seguinte estrutura. Isso inclui variáveis e parâmetros comuns para que você possa copiar e colar esse trecho de código em seu arquivo de solução e alterar os nomes de parâmetro.

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

As propriedades dos recursos de ação de alerta são descritas nas tabelas a seguir.

| Nome do elemento | Obrigatório | Descrição |
|:--|:--|:--|
| `type` | Sim | Tipo de ação.  Este é um **alerta** para ações de alerta. |
| `name` | Sim | Nome a apresentar para o alerta.  Esse é o nome que é exibido no console para a regra de alerta. |
| `description` | Não | Descrição opcional do alerta. |
| `severity` | Sim | Severidade do registro de alerta com os seguintes valores:<br><br> **drasticamente**<br>**alerta**<br>**informational**

#### <a name="threshold"></a>Limiar
Esta seção é necessária. Ele define as propriedades para o limite de alerta.

| Nome do elemento | Obrigatório | Descrição |
|:--|:--|:--|
| `Operator` | Sim | Operador para a comparação dos seguintes valores:<br><br>**gt = maior que<br>lt = menor que** |
| `Value` | Sim | O valor para comparar os resultados. |

##### <a name="metricstrigger"></a>MetricsTrigger
Esta secção é opcional. Inclua-o para um alerta de medição de métrica.

| Nome do elemento | Obrigatório | Descrição |
|:--|:--|:--|
| `TriggerCondition` | Sim | Especifica se o limite é para o número total de violações ou violações consecutivas dos seguintes valores:<br><br>**Total de<br>consecutivas** |
| `Operator` | Sim | Operador para a comparação dos seguintes valores:<br><br>**gt = maior que<br>lt = menor que** |
| `Value` | Sim | Número de vezes que os critérios devem ser atendidos para disparar o alerta. |


#### <a name="throttling"></a>Limitação
Esta secção é opcional. Inclua esta seção se desejar suprimir alertas da mesma regra por algum tempo após a criação de um alerta.

| Nome do elemento | Obrigatório | Descrição |
|:--|:--|:--|
| DurationInMinutes | Sim se o elemento de limitação estiver incluído | Número de minutos para suprimir alertas após a criação de um da mesma regra de alerta. |

#### <a name="azure-action-group"></a>Grupo de ações do Azure
Todos os alertas no Azure, utilize o grupo de ação como o mecanismo predefinido para a manipulação de ações. Com o grupo de ação, pode especificar as suas ações de uma vez e, em seguida, associar o grupo de ação para múltiplos alertas - em todo o Azure. Sem a necessidade, repetidamente declarar as mesmas ações repetidamente. Os grupos de ação suportam várias ações - incluindo e-mail, SMS, chamada de voz, a ligação ITSM, Runbook de automatização, Webhook URI e muito mais.

Para o utilizador que tiver expandido o seus alertas no Azure - uma agenda já deve ter os detalhes do grupo de ação transmitidos juntamente com o limiar, para poder criar um alerta. Detalhes de email, URLs de webhook, detalhes de automação do runbook e outras ações, precisam ser definidos no primeiro grupo de ações antes de criar um alerta; é possível criar um [grupo de ações de Azure monitor](../../azure-monitor/platform/action-groups.md) no portal ou usar o [modelo de recurso grupo de ações](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).

| Nome do elemento | Obrigatório | Descrição |
|:--|:--|:--|
| AzNsNotification | Sim | A ID de recurso do grupo de ações do Azure a ser associado ao alerta para tomar as ações necessárias quando os critérios de alerta forem atendidos. |
| CustomEmailSubject | Não | Linha de assunto personalizada do email enviada para todos os endereços especificados no grupo de ações associado. |
| CustomWebhookPayload | Não | Conteúdo personalizado a ser enviado a todos os pontos de extremidade de webhook definidos no grupo de ações associado. O formato depende do que o webhook está esperando e deve ser um JSON serializado válido. |

## <a name="sample"></a>Exemplo

A seguir, um exemplo de uma solução que inclui os seguintes recursos:

- Pesquisa guardada
- Agenda
- Grupo de ação

O exemplo usa variáveis de [parâmetros de solução padrão]( solutions-solution-file.md#parameters) que normalmente seriam usadas em uma solução em vez de codificar valores nas definições de recurso.

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

O arquivo de parâmetro a seguir fornece valores de exemplos para essa solução.

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

## <a name="next-steps"></a>Passos seguintes
* [Adicione exibições](solutions-resources-views.md) à sua solução de gerenciamento.
* [Adicione runbooks de automação e outros recursos](solutions-resources-automation.md) à sua solução de gerenciamento.
