---
title: Expandir alertas do Log Analytics para o Azure Government Cloud
description: Este artigo descreve as ferramentas e a API através do qual pode expandir alertas do Log Analytics para alertas do Azure.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/04/2018
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: 9d734f74c4e12b369e46c15dcb9d01a8185dddd6
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60431177"
---
# <a name="extend-alerts-from-log-analytics-into-azure-alerts"></a>Expandir alertas do Log Analytics para alertas do Azure
A funcionalidade de alertas no portal do OMS está a ser substituída através de alertas do Azure na cloud do Azure Government. Como parte desta transição, os alertas que configurou originalmente no Log Analytics irão ser expandidas para o Azure. Se não quiser esperar que elas sejam movidas automaticamente para o Azure, pode iniciar o processo:

- Manualmente a partir do portal do Operations Management Suite. 
- Por meio de programação, utilizando a API AlertsVersion.  

> [!NOTE]
> Microsoft irá ampliar automaticamente os alertas criados em instâncias portais do Azure Government OMS do Log Analytics para alertas do Azure a partir de 1 de Março de 2019, de forma sistemática. Se tiver quaisquer problemas ao criar [grupos de ação](../../azure-monitor/platform/action-groups.md), utilize [estes passos de remediação](alerts-extend-tool.md#troubleshooting) para obter os grupos de ação criados automaticamente. Pode utilizar estes passos até 15 de Março de 2019 no portal do Azure Government OMS.

## <a name="option-1-initiate-from-the-operations-management-suite-portal"></a>Opção 1: Iniciar a partir do portal do Operations Management Suite
Os passos seguintes descrevem como ampliar alertas para a área de trabalho a partir do portal do Operations Management Suite para a cloud do Azure Government.  

1. No portal do Azure, selecione **Todos os serviços**. Na lista de recursos, escreva **Log Analytics**. À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione **Log Analytics**.
2. No painel de subscrições do Log Analytics, selecione uma área de trabalho e, em seguida, selecione o **Portal do OMS** mosaico.
![Painel de subscrição de captura de ecrã do Log Analytics, com o mosaico do Portal do OMS realçado](media/alerts-extend-tool/azure-portal-01.png) 
3. Depois, será redirecionado para o portal do Operations Management Suite, selecione o **definições** ícone.
![Portal de captura de ecrã do Operations Management Suite, com o ícone de definições realçado](media/alerts-extend-tool/oms-portal-settings-option.png) 
4. Partir do **configurações** página, selecione **alertas**.  
5. Selecione **ampliados para o Azure**.
![Captura de ecrã do Operations Management Suite definições de alerta página do portal, com expandir para Azure realçado](media/alerts-extend-tool/ExtendInto.png)
6. Um Assistente de três etapas é apresentado na **alertas** painel. Leia a visão geral e selecione **seguinte**.
![Captura de ecrã do passo 1 do Assistente](media/alerts-extend-tool/ExtendStep1.png)  
7. Na segunda etapa, é apresentado um resumo das alterações propostas, listagem apropriado [grupos de ação](../../azure-monitor/platform/action-groups.md) para os alertas. Se a ações semelhantes são vistas em mais do que um alerta, o assistente propõe associar um grupo de ação única todos eles.  A Convenção de nomenclatura é o seguinte: *WorkspaceName_AG_#Number*. Para continuar, selecione **seguinte**.
![Captura de ecrã do passo 2 do Assistente](media/alerts-extend-tool/ExtendStep2.png)  
8. No último passo do assistente, selecione **concluir**e confirmar quando lhe for pedido para iniciar o processo. Opcionalmente, pode fornecer um endereço de e-mail, para que será notificado quando o processo for concluído e todos os alertas foram movidos com êxito para alertas do Azure.
![Captura de ecrã do passo 3 do Assistente](media/alerts-extend-tool/ExtendStep3.png)

Quando o assistente esteja concluído, sobre o **definições de alerta** página, a opção para expandir os alertas para o Azure é removida. Em segundo plano, os alertas são movidos para o Azure e isto pode demorar algum tempo. Durante a operação, pode fazer alterações aos alertas do portal do Operations Management Suite. Pode ver o estado atual na faixa na parte superior do portal de. Se um endereço de e-mail que indicou anteriormente, receberá um e-mail quando o processo foi concluída com êxito.  


Alertas continuam a ser listados no portal do Operations Management Suite, mesmo depois são movidas com êxito para o Azure.
![Página de definições de alerta de portal captura de ecrã do Operations Management Suite](media/alerts-extend-tool/PostExtendList.png)


## <a name="option-2-use-the-alertsversion-api"></a>Opção 2: Utilizar a API AlertsVersion
Pode utilizar a API AlertsVersion do Log Analytics para expandir alertas do Log Analytics para alertas do Azure de qualquer cliente que pode chamar uma API REST. Pode aceder à API do PowerShell, utilizando [ARMClient](https://github.com/projectkudu/ARMClient), uma ferramenta de linha de comandos de código-fonte aberto. O utilizador pode apresentar os resultados em JSON.  

Para utilizar a API, primeiro crie um pedido GET. Esta é avaliada e devolve um resumo das alterações propostas, antes de tentar, na verdade, expandir para o Azure através de um pedido POST. Os resultados listam os alertas e uma lista de propostas de [grupos de ação](../../azure-monitor/platform/action-groups.md), no formato JSON. Se a ações semelhantes são vistas em mais do que um alerta, o serviço propõe associar todos eles com um grupo de ação única. A Convenção de nomenclatura é o seguinte: *WorkspaceName_AG_#Number*.

```
armclient GET  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Se a solicitação GET for bem-sucedida, código de estado HTTP 200 é retornado, juntamente com uma lista de alertas e proposto grupos de ação nos dados JSON. Segue-se uma resposta de exemplo:

```json
{
    "version": 1,
    "migrationSummary": {
        "alertsCount": 2,
        "actionGroupsCount": 2,
        "alerts": [
            {
                "alertName": "DemoAlert_1",
                "alertId": " /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/savedSearches/<savedSearchId>/schedules/<scheduleId>/actions/<actionId>",
                "actionGroupName": "<workspaceName>_AG_1"
            },
            {
                "alertName": "DemoAlert_2",
                "alertId": " /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/savedSearches/<savedSearchId>/schedules/<scheduleId>/actions/<actionId>",
                "actionGroupName": "<workspaceName>_AG_2"
            }
        ],
        "actionGroups": [
            {
                "actionGroupName": "<workspaceName>_AG_1",
                "actionGroupResourceId": "/subscriptions/<subscriptionid>/resourceGroups/<resourceGroupName>/providers/microsoft.insights/actionGroups/<workspaceName>_AG_1",
                "actions": {
                    "emailIds": [
                        "JohnDoe@mail.com"
                    ],
                    "webhookActions": [
                        {
                            "name": "Webhook_1",
                            "serviceUri": "https://test.com"
                        }
                    ],
                    "itsmAction": {}
                }
            },
            {
                "actionGroupName": "<workspaceName>_AG_1",
                "actionGroupResourceId": "/subscriptions/<subscriptionid>/resourceGroups/<resourceGroupName>/providers/microsoft.insights/actionGroups/<workspaceName>_AG_1",
                 "actions": {
                    "emailIds": [
                        "test1@mail.com",
                          "test2@mail.com"
                    ],
                    "webhookActions": [],
                    "itsmAction": {
                        "connectionId": "<Guid>",
                        "templateInfo":"{\"PayloadRevision\":0,\"WorkItemType\":\"Incident\",\"UseTemplate\":false,\"WorkItemData\":\"{\\\"contact_type\\\":\\\"email\\\",\\\"impact\\\":\\\"3\\\",\\\"urgency\\\":\\\"2\\\",\\\"category\\\":\\\"request\\\",\\\"subcategory\\\":\\\"password\\\"}\",\"CreateOneWIPerCI\":false}"
                    }
                }
            }
        ]
    }
}

```
Se a área de trabalho especificada não tem quaisquer regras de alerta definidas, os dados JSON retorna o seguinte:

```json
{
    "version": 1,
    "Message": "No Alerts found in the workspace for migration."
}
```

Se todas as regras de alerta na área de trabalho especificada já tem sido expandidas para o Azure, é a resposta ao pedido GET:

```json
{
    "version": 2
}
```

Para iniciar a migrar os alertas para o Azure, inicie uma resposta de POSTAGEM. A resposta de POSTAGEM confirma que a sua intenção, bem como aceitação, ter alertas expandidas a partir do Log Analytics para alertas do Azure. A atividade for agendada e os alertas são processados como indicado, com base nos resultados quando efetuou anteriormente a resposta GET. Opcionalmente, pode fornecer uma lista de endereços de e-mail para o qual o Log Analytics envia um relatório quando o processo de plano de fundo agendada dos alertas de migração é concluída com êxito. Pode utilizar o seguinte exemplo de pedido:

```
$emailJSON = “{‘Recipients’: [‘a@b.com’, ‘b@a.com’]}”
armclient POST  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview $emailJSON
```

> [!NOTE]
> O resultado da migração alertas para alertas do Azure podem variar de acordo com o resumo fornecido pela resposta GET. Quando agendada, alertas no Log Analytics estão temporariamente indisponíveis para modificação no portal do Operations Management Suite. No entanto, pode criar novos alertas. 

Se o pedido POST for bem-sucedida, ela retorna um Estado de HTTP 200 OK, juntamente com a seguinte resposta:

```json
{
    "version": 2
}
```

Esta resposta indica que os alertas foram estendidos com êxito em alertas do Azure. A propriedade de versão é apenas para verificar se os alertas foram estendidas para o Azure e não tem relação com o [API de pesquisa do Log Analytics](../../azure-monitor/platform/api-alerts.md). Quando os alertas são expandidos para o Azure com êxito, qualquer endereços de e-mail fornecido com a mensagem de pedido são enviadas de um relatório. Se todos os alertas na área de trabalho especificada já estão agendados para serem expandidos, a resposta ao seu pedido POST é que a tentativa de foi proibida (um código de 403 status). Para ver qualquer mensagem de erro ou compreender se o processo está bloqueado, pode submeter um pedido GET. Se existir uma mensagem de erro, ele será retornado, juntamente com as informações de resumidas.

```json
{
    "version": 1,
    "message": "OMS was unable to extend your alerts into Azure, Error: The subscription is not registered to use the namespace 'microsoft.insights'. OMS will schedule extending your alerts, once remediation steps illustrated in the troubleshooting guide are done.",
    "recipients": [
       "john.doe@email.com",
       "jane.doe@email.com"
     ],
    "migrationSummary": {
        "alertsCount": 2,
        "actionGroupsCount": 2,
        "alerts": [
            {
                "alertName": "DemoAlert_1",
                "alertId": " /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/savedSearches/<savedSearchId>/schedules/<scheduleId>/actions/<actionId>",
                "actionGroupName": "<workspaceName>_AG_1"
            },
            {
                "alertName": "DemoAlert_2",
                "alertId": " /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/savedSearches/<savedSearchId>/schedules/<scheduleId>/actions/<actionId>",
                "actionGroupName": "<workspaceName>_AG_2"
            }
        ],
        "actionGroups": [
            {
                "actionGroupName": "<workspaceName>_AG_1",
                "actionGroupResourceId": "/subscriptions/<subscriptionid>/resourceGroups/<resourceGroupName>/providers/microsoft.insights/actionGroups/<workspaceName>_AG_1",
                "actions": {
                    "emailIds": [
                        "JohnDoe@mail.com"
                    ],
                    "webhookActions": [
                        {
                            "name": "Webhook_1",
                            "serviceUri": "https://test.com"
                        }
                    ],
                    "itsmAction": {}
                }
            },
            {
                "actionGroupName": "<workspaceName>_AG_1",
                "actionGroupResourceId": "/subscriptions/<subscriptionid>/resourceGroups/<resourceGroupName>/providers/microsoft.insights/actionGroups/<workspaceName>_AG_1",
                 "actions": {
                    "emailIds": [
                        "test1@mail.com",
                          "test2@mail.com"
                    ],
                    "webhookActions": [],
                    "itsmAction": {
                        "connectionId": "<Guid>",
                        "templateInfo":"{\"PayloadRevision\":0,\"WorkItemType\":\"Incident\",\"UseTemplate\":false,\"WorkItemData\":\"{\\\"contact_type\\\":\\\"email\\\",\\\"impact\\\":\\\"3\\\",\\\"urgency\\\":\\\"2\\\",\\\"category\\\":\\\"request\\\",\\\"subcategory\\\":\\\"password\\\"}\",\"CreateOneWIPerCI\":false}"
                    }
                }
            }
        ]
    }
}              

```

## <a name="troubleshooting"></a>Resolução de problemas 
Durante o processo de expansão de alertas, problemas podem impedir que o sistema de criar as informações necessárias [grupos de ação](../../azure-monitor/platform/action-groups.md). Nesses casos, verá uma mensagem de erro num banner no **alerta** seção do portal do Operations Management Suite ou em GET chamar concluído para a API.

> [!IMPORTANT]
> Se com base na cloud do Azure Government pelos utilizadores no portal OMS não efetue os seguintes passos de remediação antes de 15 de Março de 2019, alertas serão executadas no Azure, mas não irão disparar a notificação ou qualquer ação. Para obter notificações de alertas, tem manualmente de editar as suas regras de alerta no Azure e adicionar [grupos de ação](../../azure-monitor/platform/action-groups.md)

Eis os passos de remediação para cada erro:
- **Erro: Bloqueio de âmbito está presente ao nível do grupo de recursos/subscrição para operações de escrita**:   ![Captura de ecrã do Operations Management Suite page do portal definições de alerta, com a mensagem de erro de bloqueio de âmbito realçado](media/alerts-extend-tool/ErrorScopeLock.png)

    Quando o bloqueio de âmbito é ativado, a funcionalidade restringe alterações ao novo a subscrição ou grupo de recursos que contém a área de trabalho do Log Analytics (Operations Management Suite). O sistema é não é possível expandir os alertas para o Azure e criar grupos de ação necessário.
    
    Para resolver, elimine o *só de leitura* bloqueio na sua subscrição ou grupo de recursos que contém a área de trabalho. Pode fazê-lo ao utilizar o portal do Azure, PowerShell, CLI do Azure ou a API. Para obter mais informações, consulte [utilização de recursos de bloqueio](../../azure-resource-manager/resource-group-lock-resources.md). 
    
    Ao resolver o erro, utilizando as etapas ilustradas no artigo, o Operations Management Suite estende os alertas para o Azure em execução agendada do dia seguinte. Não precisa de fazer mais nada ou iniciar qualquer coisa.

- **Erro: Política está presente ao nível do grupo de recursos/subscrição**:   ![Captura de ecrã do Operations Management Suite page do portal definições de alerta, com a mensagem de erro de política realçada](media/alerts-extend-tool/ErrorPolicy.png)

    Quando [do Azure Policy](../../governance/policy/overview.md) é aplicada, restringe qualquer novo recurso num subscrição ou grupo de recursos que contém a área de trabalho do Log Analytics (Operations Management Suite). O sistema é não é possível expandir os alertas para o Azure e criar grupos de ação necessário.
    
    Para resolver, edite a política que está a causar o *[RequestDisallowedByPolicy](../../azure-resource-manager/resource-manager-policy-requestdisallowedbypolicy-error.md)* erro, o que impede a criação de novos recursos na sua subscrição ou grupo de recursos que contém a área de trabalho. Pode fazê-lo ao utilizar o portal do Azure, PowerShell, CLI do Azure ou a API. É possível auditar as ações para localizar a política adequada, que está causando a falha. Para obter mais informações, consulte [visualizar registos de atividades para auditar as ações](../../azure-resource-manager/resource-group-audit.md). 
    
    Ao resolver o erro, utilizando as etapas ilustradas no artigo, o Operations Management Suite estende os alertas para o Azure em execução agendada do dia seguinte. Não precisa de fazer mais nada ou iniciar qualquer coisa.


## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre a nova [experiência de alertas do Azure](../../azure-monitor/platform/alerts-overview.md).
* Saiba mais sobre [alertas de registo nos alertas do Azure](alerts-unified-log.md).

