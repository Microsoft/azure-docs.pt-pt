---
title: Configurações de regra de detecção inteligente-insights Aplicativo Azure
description: Automatizar o gerenciamento e a configuração de regras de detecção inteligente do Aplicativo Azure insights com modelos de Azure Resource Manager
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: harelbr
ms.author: harelbr
ms.date: 06/26/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 74014a91684749eec7c4086a88785f2024694677
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74929083"
---
# <a name="manage-application-insights-smart-detection-rules-using-azure-resource-manager-templates"></a>Gerenciar Application Insights regras de detecção inteligente usando modelos de Azure Resource Manager

Regras de detecção inteligente no Application Insights podem ser gerenciadas e configuradas usando [modelos de Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md).
Esse método pode ser usado ao implantar novos recursos de Application Insights com Azure Resource Manager automação ou para modificar as configurações de recursos existentes.

## <a name="smart-detection-rule-configuration"></a>Configuração de regra de detecção inteligente

Pode configurar as seguintes definições para uma regra de deteção inteligente:
- Se a regra estiver habilitada (o padrão é **true**).
- Se os emails devem ser enviados aos usuários associados ao [leitor de monitoramento](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) da assinatura e às funções de [colaborador de monitoramento](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) quando uma detecção é encontrada (o padrão é **true**).
- Todos os destinatários de email adicionais que devem receber uma notificação quando uma detecção é encontrada.
    -  A configuração de email não está disponível para as regras de detecção inteligente marcadas como _Visualização_.

Para permitir a configuração das configurações de regra via Azure Resource Manager, a configuração da regra de detecção inteligente agora está disponível como um recurso interno dentro do recurso Application Insights, chamado **ProactiveDetectionConfigs**.
Para obter a flexibilidade máxima, cada regra de detecção inteligente pode ser configurada com configurações de notificação exclusivas.

## 

## <a name="examples"></a>Exemplos

Abaixo estão alguns exemplos que mostram como definir as configurações de regras de detecção inteligente usando modelos de Azure Resource Manager.
Todos os exemplos referem-se a um recurso de Application Insights chamado _"MyApplication"_ e à "regra de detecção inteligente de duração de dependência longa", que é internamente denominada _"longdependencyduration"_ .
Certifique-se de substituir o nome do recurso de Application Insights e para especificar o nome interno da regra de detecção inteligente relevante. Verifique a tabela abaixo para obter uma lista dos nomes de Azure Resource Manager internos correspondentes para cada regra de detecção inteligente.

### <a name="disable-a-smart-detection-rule"></a>Desabilitar uma regra de detecção inteligente

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

### <a name="disable-sending-email-notifications-for-a-smart-detection-rule"></a>Desabilitar o envio de notificações por email para uma regra de detecção inteligente

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

### <a name="add-additional-email-recipients-for-a-smart-detection-rule"></a>Adicionar destinatários de email adicionais para uma regra de detecção inteligente

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

### <a name="failure-anomalies-v2-non-classic-alert-rule"></a>Regra de alerta de anomalias de falha v2 (não clássico)

Este modelo de Azure Resource Manager demonstra como configurar uma regra de alerta de anomalias de falha V2 com uma severidade de 2. Essa nova versão da regra de alerta de anomalias de falha faz parte da nova plataforma de alerta do Azure e substitui a versão clássica que está sendo desativada como parte do [processo de aposentadoria de alertas clássicos](https://azure.microsoft.com/updates/classic-alerting-monitoring-retirement/).

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
> Este modelo de Azure Resource Manager é exclusivo para a regra de alerta de anomalias V2 e é diferente das outras regras de detecção inteligente clássicas descritas neste artigo.   

## <a name="smart-detection-rule-names"></a>Nomes de regra de detecção inteligente

Abaixo está uma tabela de nomes de regras de detecção inteligente conforme eles aparecem no portal, juntamente com seus nomes internos, que devem ser usados no modelo de Azure Resource Manager.

> [!NOTE]
> As regras de detecção inteligente marcadas como _Visualização_ não dão suporte a notificações por email. Portanto, você só pode definir a propriedade _Enabled_ para essas regras. 

| Nome da regra de portal do Azure | Nome interno
|:---|:---|
| Tempo de carregamento de página lento | slowpageloadtime |
| Tempo de resposta do servidor lento | slowserverresponsetime |
| Duração de dependência longa | longdependencyduration |
| Degradação no tempo de resposta do servidor | degradationinserverresponsetime |
| Degradação na duração da dependência | degradationindependencyduration |
| Degradação na taxa de gravidade de rastreamento (versão prévia) | extension_traceseveritydetector |
| Aumento anormal no volume de exceção (versão prévia) | extension_exceptionchangeextension |
| Possível vazamento de memória detectado (versão prévia) | extension_memoryleakextension |
| Possível problema de segurança detectado (versão prévia) | extension_securityextensionspackage |
| Aumento anormal no volume de dados diário (versão prévia) | extension_billingdatavolumedailyspikeextension |

## <a name="next-steps"></a>Próximos Passos

Saiba mais sobre a detecção automática de:

- [Anomalias de falha](../../azure-monitor/app/proactive-failure-diagnostics.md)
- [Vazamentos de memória](../../azure-monitor/app/proactive-potential-memory-leak.md)
- [Anomalias de desempenho](../../azure-monitor/app/proactive-performance-diagnostics.md)
