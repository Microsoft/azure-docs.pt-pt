---
title: Definições de regras de deteção inteligente - Insights de aplicação Azure
description: Automatizar gestão e configuração de regras inteligentes de deteção inteligentes de Insights de Aplicação Azure com modelos de gestor de recursos Azure
ms.topic: conceptual
author: harelbr
ms.author: harelbr
ms.date: 06/26/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 7ca4df620739b2ab55b8ba986031cc48fe87f1fa
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80294907"
---
# <a name="manage-application-insights-smart-detection-rules-using-azure-resource-manager-templates"></a>Gerir as regras de deteção inteligente de Insights de Aplicação utilizando modelos de Gestor de Recursos Azure

As regras de deteção inteligente em Insights de Aplicação podem ser geridas e configuradas usando modelos de [Gestor de Recursos Azure](../../azure-resource-manager/templates/template-syntax.md).
Este método pode ser utilizado na implementação de novos recursos de Insights de Aplicação com automatização do Gestor de Recursos Azure, ou para modificar as configurações dos recursos existentes.

## <a name="smart-detection-rule-configuration"></a>Configuração de regra de deteção inteligente

Pode configurar as seguintes definições para uma regra de deteção inteligente:
- Se a regra estiver ativada (o predefinido é **verdadeiro**.)
- Se os e-mails forem enviados aos utilizadores associados às funções de [Monitoring Reader](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) e [Monitoring Contributor](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) da subscrição quando for detetada uma deteção (o predefinido é **verdadeiro**.)
- Quaisquer destinatários adicionais de e-mail que devam receber uma notificação quando uma deteção for encontrada.
    -  A configuração do e-mail não está disponível para regras de Deteção Inteligente marcadas como _pré-visualização_.

Para permitir configurar as definições de regra através do Gestor de Recursos Azure, a configuração da regra de deteção inteligente está agora disponível como um recurso interno dentro do recurso Application Insights, denominado **ProactiveDetectionConfigs**.
Para uma flexibilidade máxima, cada regra de deteção inteligente pode ser configurada com configurações únicas de notificação.

## <a name="examples"></a>Exemplos

Abaixo estão alguns exemplos que mostram como configurar as definições de regras de deteção inteligentes usando modelos do Gestor de Recursos Azure.
Todas as amostras referem-se a um recurso Application Insights denominado _"myApplication",_ e à "regra de deteção inteligente de longa duração da dependência", que é internamente denominada _"longa duração da dependência"._
Certifique-se de substituir o nome de recurso Application Insights e especificar o nome interno da regra de deteção inteligente relevante. Consulte a tabela abaixo para obter uma lista dos nomes correspondentes do Gestor de Recursos Azure internos para cada regra de deteção inteligente.

### <a name="disable-a-smart-detection-rule"></a>Desativar uma regra de deteção inteligente

```json
{
      "apiVersion": "2018-05-01-preview",
      "name": "myApplication",
      "type": "Microsoft.Insights/components",
      "location": "[resourceGroup().location]",
      "properties": {
        "ApplicationId": "myApplication"
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
        "ApplicationId": "myApplication"
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

### <a name="add-additional-email-recipients-for-a-smart-detection-rule"></a>Adicione destinatários adicionais de e-mail para uma regra de deteção inteligente

```json
{
      "apiVersion": "2018-05-01-preview",
      "name": "myApplication",
      "type": "Microsoft.Insights/components",
      "location": "[resourceGroup().location]",
      "properties": {
        "ApplicationId": "myApplication"
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
            "customEmails": ['alice@contoso.com', 'bob@contoso.com'],
            "enabled": true
          }
        }
      ]
    }

```


## <a name="smart-detection-rule-names"></a>Nomes de regras de deteção inteligente

Abaixo está uma tabela de nomes de regras de deteção inteligente como aparecem no portal, juntamente com os seus nomes internos, que devem ser usados no modelo do Gestor de Recursos Azure.

> [!NOTE]
> As regras de deteção inteligentes marcadas como _pré-visualização_ não suportam notificações de e-mail. Portanto, você só pode definir o imóvel _habilitado_ para estas regras. 

| Nome da regra do portal Azure | Nome interno
|:---|:---|
| Tempo de carga de página lenta | tempo de carregamento de página lenta |
| Tempo de resposta do servidor lento | tempo de resposta do servidor lento |
| Longa duração da dependência | longa dependência duração |
| Degradação no tempo de resposta do servidor | degradação tempo de resposta servidor |
| Degradação da duração da dependência | degradação da dependência duração |
| Degradação na relação de gravidade dos vestígios (pré-visualização) | extension_traceseveritydetector |
| Aumento anormal do volume de exceção (pré-visualização) | extension_exceptionchangeextension |
| Fuga de memória potencial detetada (pré-visualização) | extension_memoryleakextension |
| Potencial problema de segurança detetado (pré-visualização) | extension_securityextensionspackage |
| Aumento anormal no volume diário de dados (pré-visualização) | extension_billingdatavolumedailyspikeextension |

### <a name="failure-anomalies-alert-rule"></a>Falha Anomalias regra de alerta

Este modelo de Gestor de Recursos Azure demonstra configurar uma regra de alerta de anomalias de falha com uma gravidade de 2. Esta nova versão da regra de alerta de Anomalias falhas faz parte da nova plataforma de alerta Azure, e substitui a versão clássica que está a ser retirada como parte do processo clássico de [alertas de reforma.](https://azure.microsoft.com/updates/classic-alerting-monitoring-retirement/)

> [!NOTE]
> A nota de falha é um serviço global, pelo que a localização da regra é criada na localização global.

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
> Este modelo de Gestor de Recursos Azure é exclusivo da regra de alerta de Anomalias de Falha e é diferente das outras regras clássicas de Deteção Inteligente descritas neste artigo. Se pretender gerir as Anomalias de Falha manualmente, isto é feito em Alertas de Monitor Azure, enquanto todas as outras regras de Deteção Inteligente são geridas no painel de deteção inteligente da UI.

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre a deteção automática:

- [Anomalias de falhas](../../azure-monitor/app/proactive-failure-diagnostics.md)
- [Fugas de memória](../../azure-monitor/app/proactive-potential-memory-leak.md)
- [Anomalias de desempenho](../../azure-monitor/app/proactive-performance-diagnostics.md)
