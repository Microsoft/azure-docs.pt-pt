---
title: Definições de regras de deteção inteligente - Azure Application Insights
description: Automatizar a gestão e configuração das regras inteligentes de deteção de aplicações Azure Com modelos de gestor de recursos Azure
ms.topic: conceptual
author: harelbr
ms.author: harelbr
ms.date: 02/14/2021
ms.reviewer: mbullwin
ms.openlocfilehash: e3a7b71cd8975957754ba014ecc700484c27a6d7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101726126"
---
# <a name="manage-application-insights-smart-detection-rules-using-azure-resource-manager-templates"></a>Gerir regras de deteção inteligentes de deteção de aplicações usando modelos de Gestor de Recursos Azure

As regras de deteção inteligente em Application Insights podem ser geridas e configuradas usando [modelos do Gestor de Recursos Azure.](../../azure-resource-manager/templates/template-syntax.md)
Este método pode ser utilizado ao implementar novos recursos de Insights de Aplicação com automação do Gestor de Recursos Azure ou para modificar as definições dos recursos existentes.

## <a name="smart-detection-rule-configuration"></a>Configuração de regra de deteção inteligente

Pode configurar as seguintes definições para uma regra de deteção inteligente:
- Se a regra estiver ativada (o padrão é **verdadeiro**.)
- Se os e-mails forem enviados aos utilizadores associados às funções de [Monitoring Reader](../../role-based-access-control/built-in-roles.md#monitoring-reader) e [Monitoring Da](../../role-based-access-control/built-in-roles.md#monitoring-contributor) subscrição quando uma deteção é encontrada (o padrão é **verdadeiro**.)
- Quaisquer destinatários de e-mail adicionais que devam receber uma notificação quando uma deteção é encontrada.
    -  A configuração de e-mail não está disponível para regras de Deteção Inteligente marcadas como _pré-visualização_.

Para permitir configurar as definições de regras através do Azure Resource Manager, a configuração da regra de deteção inteligente está agora disponível como um recurso interno dentro do recurso Application Insights, denominado **ProactiveDetectionConfigs**.
Para uma flexibilidade máxima, cada regra de deteção inteligente pode ser configurada com configurações de notificação únicas.

## <a name="examples"></a>Exemplos

Abaixo estão alguns exemplos que mostram como configurar as definições de regras inteligentes de deteção usando modelos de Gestor de Recursos Azure.
Todas as amostras referem-se a um recurso application Insights chamado _"myApplication"_ e à "regra de deteção inteligente de longa duração da dependência", que é internamente denominada _"longdependencyduration"._
Certifique-se de substituir o nome do recurso Application Insights e de especificar o nome interno da regra de deteção inteligente relevante. Consulte a tabela abaixo para obter uma lista dos nomes correspondentes do Gestor de Recursos Azure para cada regra de deteção inteligente.

### <a name="disable-a-smart-detection-rule"></a>Desativar uma regra de deteção inteligente

```json
{
      "apiVersion": "2018-05-01-preview",
      "name": "myApplication",
      "type": "Microsoft.Insights/components",
      "location": "[resourceGroup().location]",
      "properties": {
        "Application_Type": "web"
      },
      "resources": [
        {
          "apiVersion": "2018-05-01-preview",
          "name": "longdependencyduration",
          "type": "ProactiveDetectionConfigs",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Insights/components', 'myApplication')]"
          ],
          "properties": {
            "name": "longdependencyduration",
            "sendEmailsToSubscriptionOwners": true,
            "customEmails": [],
            "enabled": false
          }
        }
      ]
    }
```

### <a name="disable-sending-email-notifications-for-a-smart-detection-rule"></a>Desativar o envio de notificações por e-mail para uma regra de deteção inteligente

```json
{
      "apiVersion": "2018-05-01-preview",
      "name": "myApplication",
      "type": "Microsoft.Insights/components",
      "location": "[resourceGroup().location]",
      "properties": {
        "Application_Type": "web"
      },
      "resources": [
        {
          "apiVersion": "2018-05-01-preview",
          "name": "longdependencyduration",
          "type": "ProactiveDetectionConfigs",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Insights/components', 'myApplication')]"
          ],
          "properties": {
            "name": "longdependencyduration",
            "sendEmailsToSubscriptionOwners": false,
            "customEmails": [],
            "enabled": true
          }
        }
      ]
    }
```

### <a name="add-additional-email-recipients-for-a-smart-detection-rule"></a>Adicione destinatários de e-mail adicionais para uma regra de deteção inteligente

```json
{
      "apiVersion": "2018-05-01-preview",
      "name": "myApplication",
      "type": "Microsoft.Insights/components",
      "location": "[resourceGroup().location]",
      "properties": {
        "Application_Type": "web"
      },
      "resources": [
        {
          "apiVersion": "2018-05-01-preview",
          "name": "longdependencyduration",
          "type": "ProactiveDetectionConfigs",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Insights/components', 'myApplication')]"
          ],
          "properties": {
            "name": "longdependencyduration",
            "sendEmailsToSubscriptionOwners": true,
            "customEmails": ["alice@contoso.com", "bob@contoso.com"],
            "enabled": true
          }
        }
      ]
    }

```


## <a name="smart-detection-rule-names"></a>Nomes de regras de deteção inteligente

Abaixo está uma tabela de nomes de regras de deteção inteligentes como eles aparecem no portal, juntamente com os seus nomes internos, que devem ser usados no modelo Azure Resource Manager.

> [!NOTE]
> As regras de deteção inteligentes marcadas como _pré-visualização_ não suportam notificações de e-mail. Portanto, só pode definir a _propriedade ativada_ para estas regras. 

| Nome da regra do portal Azure | Nome interno
|:---|:---|
| Tempo de carga de página lenta | tempo de carga de páginas lentas |
| Tempo de resposta lenta do servidor | tempo de resposta lenta |
| Duração da dependência longa | longdependencyduration |
| Degradação no tempo de resposta do servidor | degradaçãoinserverresponsetime |
| Degradação da duração da dependência | degradação da dependência |
| Degradação na relação de severidade do traço (pré-visualização) | extension_traceseveritydetector |
| Aumento anormal do volume de exceções (pré-visualização) | extension_exceptionchangeextension |
| Possível fuga de memória detetada (pré-visualização) | extension_memoryleakextension |
| Problema de segurança potencial detetado (pré-visualização) | extension_securityextensionspackage |
| Aumento anormal do volume diário de dados (pré-visualização) | extension_billingdatavolumedailyspikeextension |

### <a name="failure-anomalies-alert-rule"></a>Regra de alerta de anomalias de falha

Este modelo de Gestor de Recursos Azure demonstra configurar uma regra de alerta de anomalias de falha com uma gravidade de 2.

> [!NOTE]
> Anomalias de falha são um serviço global, por isso a localização da regra é criada na localização global.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "microsoft.alertsmanagement/smartdetectoralertrules",
            "apiVersion": "2019-03-01",
            "name": "Failure Anomalies - my-app",
            "location": "global", 
            "properties": {
                  "description": "Failure Anomalies notifies you of an unusual rise in the rate of failed HTTP requests or dependency calls.",
                  "state": "Enabled",
                  "severity": "2",
                  "frequency": "PT1M",
                  "detector": {
                  "id": "FailureAnomaliesDetector"
                  },
                  "scope": ["/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/MyResourceGroup/providers/microsoft.insights/components/my-app"],
                  "actionGroups": {
                        "groupIds": ["/subscriptions/00000000-1111-2222-3333-444444444444/resourcegroups/MyResourceGroup/providers/microsoft.insights/actiongroups/MyActionGroup"]
                  }
            }
        }
    ]
}
```

> [!NOTE]
> Este modelo de Gestor de Recursos Azure é exclusivo da regra de alerta de Anomalias de Falha e é diferente das outras regras clássicas de Deteção Inteligente descritas neste artigo. Se pretender gerir as Anomalias de Falha manualmente, isto é feito em Alertas monitores Azure, enquanto todas as outras regras de Deteção Inteligente são geridas no painel de Deteção Inteligente da UI.

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre a deteção automática:

- [Anomalias de falhas](./proactive-failure-diagnostics.md)
- [Fugas de memória](./proactive-potential-memory-leak.md)
- [Anomalias de desempenho](./proactive-performance-diagnostics.md)

