---
title: Amostras de modelo de gestor de recursos para monitor Azure
description: Implementar e configurar funcionalidades do Monitor Azure utilizando modelos de Gestor de Recursos
author: bwren
ms.author: bwren
services: azure-monitor
ms.topic: sample
ms.date: 05/18/2020
ms.subservice: ''
ms.openlocfilehash: 0791ccf10c76f2a1781bf373c674f606ca365fff
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100628382"
---
# <a name="resource-manager-template-samples-for-azure-monitor"></a>Amostras de modelo de gestor de recursos para monitor Azure

O Monitor Azure pode ser implantado e configurado à escala utilizando o [modelo do Gestor de Recursos Azure](../azure-resource-manager/templates/template-syntax.md). Os seguintes artigos fornecem modelos de amostra para diferentes funcionalidades do Monitor Azure. Estas amostras podem ser modificadas para os seus requisitos específicos e implementadas usando qualquer método padrão para a implementação de modelos de Gestor de Recursos. 

## <a name="deploying-the-sample-templates"></a>Implantação dos modelos de amostra
Os passos básicos para utilizar as amostras são:

1. Copie o modelo e guarde como um ficheiro JSON.
2. Modifique os parâmetros para o seu ambiente e guarde como um ficheiro JSON.
4. Implemente o modelo utilizando [qualquer método de implementação para modelos de Gestor de Recursos](../azure-resource-manager/templates/deploy-powershell.md). 

Por exemplo, utilize os seguintes comandos para implantar o ficheiro de modelo e parâmetros para um grupo de recursos utilizando o PowerShell ou o Azure CLI.


```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName my-subscription
New-AzResourceGroupDeployment -Name AzureMonitorDeployment -ResourceGroupName my-resource-group -TemplateFile azure-monitor-deploy.json -TemplateParameterFile azure-monitor-deploy.parameters.json
```

```azurecli
az login
az deployment group create \
    --name AzureMonitorDeployment \
    --resource-group ResourceGroupofTargetResource \
    --template-file azure-monitor-deploy.json \
    --parameters azure-monitor-deploy.parameters.json
```

## <a name="list-of-sample-templates"></a>Lista de modelos de amostra

- [Agentes](agents/resource-manager-agent.md) - Implementar e configurar o agente Log Analytics e a extensão de diagnóstico.
- Alertas
  - [Regras](alerts/resource-manager-alerts-log.md) de alerta de registo - Alertas de consultas de registo e registo de atividades Azure.
  - [Regras de alerta métrico](alerts/resource-manager-alerts-metric.md) - Alertas de métricas usando diferentes tipos de lógica.
- [Application Insights](app/resource-manager-app-resource.md)
- [Definições de diagnóstico](essentials/resource-manager-diagnostic-settings.md) - Crie definições de diagnóstico para encaminhar registos e métricas de diferentes tipos de recursos.
- [Consultas de registo](logs/resource-manager-log-queries.md) - Crie consultas de registos guardados num espaço de trabalho log analytics.
- [Log Analytics Workspace](logs/resource-manager-workspace.md) - Criar log analytics workspace e configurar a recolha de diferentes fontes de dados do agente Log Analytics.
- [Livros de trabalho](visualize/resource-manager-workbooks.md) - Criar livros de trabalho.
- [Monitor azul para contentores](containers/resource-manager-container-insights.md) - Aglomerados a bordo para Azure Monitor para contentores.
- [Monitor Azure para VMs](vm/resource-manager-vminsights.md) - Máquinas virtuais a bordo para Azure Monitor para VMs.



## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [os modelos do Gestor de Recursos](../azure-resource-manager/templates/overview.md)