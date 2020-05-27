---
title: Amostras de modelo de gestor de recursos para monitor Azure
description: Implementar e configurar funcionalidades do Monitor Azure utilizando modelos de Gestor de Recursos
author: bwren
ms.author: bwren
services: azure-monitor
ms.topic: sample
ms.date: 05/18/2020
ms.subservice: ''
ms.openlocfilehash: 2c9287e6f4bda429309dd0041215b271678d03d3
ms.sourcegitcommit: 95269d1eae0f95d42d9de410f86e8e7b4fbbb049
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/26/2020
ms.locfileid: "83860703"
---
# <a name="resource-manager-template-samples-for-azure-monitor"></a>Amostras de modelo de gestor de recursos para monitor Azure

O Monitor Azure pode ser implantado e configurado à escala utilizando o modelo do Gestor de [Recursos Azure](../../azure-resource-manager/templates/template-syntax.md). Os seguintes artigos fornecem modelos de amostra para diferentes funcionalidades do Monitor Azure. Estas amostras podem ser modificadas para os seus requisitos específicos e implementadas utilizando qualquer método padrão para implementar modelos do Gestor de Recursos. 

## <a name="deploying-the-sample-templates"></a>Implantação dos modelos de amostra
Os passos básicos para a utilização das amostras são:

1. Copie o modelo e guarde como um ficheiro JSON.
2. Modifique os parâmetros para o seu ambiente e guarde como ficheiro JSON.
4. Implemente o modelo utilizando [qualquer método de implementação para os modelos do Gestor](../../azure-resource-manager/templates/deploy-powershell.md)de Recursos . 

Por exemplo, utilize os seguintes comandos para implantar o modelo e o ficheiro de parâmetros na sua subscrição utilizando o PowerShell ou o Azure CLI.


```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName my-subscription
New-AzResourceGroupDeployment -Name AzureMonitorDeployment -ResourceGroupName my-resource-group -TemplateFile azure-monitor-deploy.json -TemplateParameterFile azure-monitor-deploy.parameters.json
```

```azurecli
az login
az group deployment create \
    --name AlertDeployment \
    --resource-group ResourceGroupofTargetResource \
    --template-file azure-monitor-deploy.json \
    --parameters azure-monitor-deploy.parameters.json
```

## <a name="list-of-sample-templates"></a>Lista de modelos de amostra

- [Agentes](resource-manager-agent.md) - Implementar e configurar o agente Log Analytics e a extensão de diagnóstico.
- Alertas
  - [Regras](resource-manager-alerts-log.md) de alerta de log - Alertas de consultas de registo e registo de atividade do Azure.
  - [Regras](resource-manager-alerts-metric.md) de alerta métrico - Alertas de métricas usando diferentes tipos de lógica.
- Insights de Aplicação - Em breve.
- [Definições de diagnóstico](resource-manager-diagnostic-settings.md) - Criar configurações de diagnóstico para encaminhar registos e métricas de diferentes tipos de recursos.
- [Consultas](resource-manager-log-queries.md) de log - Crie consultas de registo guardadas num espaço de trabalho de Log Analytics.
- [Log Analytics Workspace](resource-manager-workspace.md) - Crie o espaço de trabalho do Log Analytics e configure a recolha de diferentes fontes de dados do agente Log Analytics.
- [Livros](resource-manager-workbooks.md) - Criar livros.
- [Monitor Azure para contentores](resource-manager-container-insights.md) - Aglomerados a bordo para o Monitor Azure para contentores.
- [Monitor Azure para VMs](resource-manager-vminsights.md) - Máquinas virtuais a bordo para O Monitor Azure para VMs.



## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [os modelos de Gestor de Recursos](../../azure-resource-manager/templates/overview.md)
