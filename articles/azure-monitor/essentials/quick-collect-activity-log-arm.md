---
title: Envie o registo de atividade do Azure para log analytics espaço de trabalho usando o modelo de Gestor de Recursos Azure
description: Utilize modelos ARM para criar um espaço de trabalho log analytics e uma definição de diagnóstico para enviar o registo de atividade para registos do Monitor Azure.
ms.topic: quickstart
ms.custom: subject-armqs, devx-track-azurecli
author: bwren
ms.author: bwren
ms.date: 06/25/2020
ms.openlocfilehash: 56810cffcb9665810c452276be34e6924fd992b2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102033287"
---
# <a name="quickstart-send-azure-activity-log-to-log-analytics-workspace-using-an-arm-template"></a>Quickstart: Enviar log de atividade azure para log analytics espaço de trabalho usando um modelo ARM

O Registo de atividades é um registo de plataformas no Azure que proporciona informações sobre eventos ao nível da subscrição. Tal inclui informações como quando um recurso é modificado ou quando uma máquina virtual é iniciada. Pode visualizar o registo de Atividade no portal Azure ou recuperar entradas com PowerShell e CLI. Este quickstart mostra como usar os modelos do Gestor de Recursos Azure (modelos ARM) para criar um espaço de trabalho do Log Analytics e uma definição de diagnóstico para enviar o registo de atividade para registos do Monitor Azure, onde pode analisá-lo usando [consultas de registo](../logs/log-query-overview.md) e ativar [outras funcionalidades,](../alerts/alerts-log-query.md) tais como alertas de registo e livros de [trabalho.](../visualize/workbooks-overview.md)

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
- Para executar os comandos a partir do seu computador local, instale o Azure CLI ou os módulos Azure PowerShell. Para obter mais informações, consulte [instalar o Azure CLI](/cli/azure/install-azure-cli) e [instalar a Azure PowerShell](/powershell/azure/install-az-ps).

## <a name="create-a-log-analytics-workspace"></a>Criar uma área de trabalho do Log Analytics

### <a name="review-the-template"></a>Rever o modelo

O modelo a seguir cria um espaço de trabalho vazio do Log Analytics. Guarde este modelo como *CreateWorkspace.js.*

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "type": "string",
      "metadata": {
        "description": "Name of the workspace."
      }
    },
    "sku": {
      "type": "string",
      "defaultValue": "pergb2018",
      "allowedValues": [
        "pergb2018",
        "Free",
        "Standalone",
        "PerNode",
        "Standard",
        "Premium"
      ],
      "metadata": {
        "description": "Pricing tier: PerGB2018 or legacy tiers (Free, Standalone, PerNode, Standard or Premium) which are not available to all customers."
      }
    },
    "location": {
      "type": "string",
      "allowedValues": [
        "australiacentral",
        "australiaeast",
        "australiasoutheast",
        "brazilsouth",
        "canadacentral",
        "centralindia",
        "centralus",
        "eastasia",
        "eastus",
        "eastus2",
        "francecentral",
        "japaneast",
        "koreacentral",
        "northcentralus",
        "northeurope",
        "southafricanorth",
        "southcentralus",
        "southeastasia",
        "switzerlandnorth",
        "switzerlandwest",
        "uksouth",
        "ukwest",
        "westcentralus",
        "westeurope",
        "westus",
        "westus2"
      ],
      "metadata": {
        "description": "Specifies the location for the workspace."
      }
    },
    "retentionInDays": {
      "type": "int",
      "defaultValue": 120,
      "metadata": {
        "description": "Number of days to retain data."
      }
    },
    "resourcePermissions": {
      "type": "bool",
      "defaultValue": true,
      "metadata": {
        "description": "true to use resource or workspace permissions. false to require workspace permissions."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.OperationalInsights/workspaces",
      "apiVersion": "2020-08-01",
      "name": "[parameters('workspaceName')]",
      "location": "[parameters('location')]",
      "properties": {
        "sku": {
          "name": "[parameters('sku')]"
        },
        "retentionInDays": "[parameters('retentionInDays')]",
        "features": {
          "searchVersion": 1,
          "legacy": 0,
          "enableLogAccessUsingOnlyResourcePermissions": "[parameters('resourcePermissions')]"
        }
      }
    }
  ]
}
```

Este modelo define um recurso:

- [Microsoft.OperationalInsights/workspaces](/azure/templates/microsoft.operationalinsights/workspaces)

### <a name="deploy-the-template"></a>Implementar o modelo

Implemente o modelo utilizando qualquer método padrão para [a implementação de um modelo ARM,](../../azure-resource-manager/templates/deploy-portal.md) como os seguintes exemplos utilizando CLI e PowerShell. Substitua os valores da amostra **do Grupo de Recursos, do** espaço de **trabalhoName** e **da localização** por valores adequados para o seu ambiente. O nome do espaço de trabalho deve ser único entre todas as subscrições do Azure.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az login
az deployment group create \
    --name CreateWorkspace \
    --resource-group my-resource-group \
    --template-file CreateWorkspace.json \
    --parameters workspaceName='my-workspace-01' location='eastus'

```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName my-subscription
New-AzResourceGroupDeployment -Name AzureMonitorDeployment -ResourceGroupName my-resource-group -TemplateFile CreateWorkspace.json -workspaceName my-workspace-01 -location eastus
```

---

### <a name="validate-the-deployment"></a>Validar a implementação

Verifique se o espaço de trabalho foi criado utilizando um dos seguintes comandos. Substitua os valores da amostra **do Grupo de Recursos** e do espaço de **trabalhoName** pelos valores acima utilizados.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az monitor log-analytics workspace show --resource-group my-workspace-01 --workspace-name my-resource-group
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Get-AzOperationalInsightsWorkspace -Name my-workspace-01 -ResourceGroupName my-resource-group
```

---

## <a name="create-diagnostic-setting"></a>Criar definição de diagnóstico

### <a name="review-the-template"></a>Rever o modelo

O modelo a seguir cria uma definição de diagnóstico que envia o registo de Atividade para um espaço de trabalho Log Analytics. Guarde este modelo como *CreateDiagnosticSetting.js.*

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "settingName": {
        "type": "String"
    },
    "workspaceId": {
        "type": "String"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Insights/diagnosticSettings",
      "apiVersion": "2017-05-01-preview",
      "name": "[parameters('settingName')]",
      "dependsOn": [],
      "properties": {
        "workspaceId": "[parameters('workspaceId')]",
        "logs": [
          {
          "category": "Administrative",
          "enabled": true
          },
          {
          "category": "Alert",
          "enabled": true
          },
          {
          "category": "Autoscale",
          "enabled": true
          },
          {
          "category": "Policy",
          "enabled": true
          },
          {
          "category": "Recommendation",
          "enabled": true
          },
          {
          "category": "ResourceHealth",
          "enabled": true
          },
          {
          "category": "Security",
          "enabled": true
          },
          {
          "category": "ServiceHealth",
          "enabled": true
          }
        ]
      }
    }
  ]
}
```

Este modelo define um recurso:

- [Microsoft.Insights/diagnosticSettings](/azure/templates/microsoft.insights/diagnosticsettings)

### <a name="deploy-the-template"></a>Implementar o modelo

Implemente o modelo utilizando qualquer método padrão para [a implementação de um modelo ARM,](../../azure-resource-manager/templates/deploy-portal.md) como os seguintes exemplos utilizando CLI e PowerShell. Substitua os valores da amostra **do Grupo de Recursos, do** espaço de **trabalhoName** e **da localização** por valores adequados para o seu ambiente. O nome do espaço de trabalho deve ser único entre todas as subscrições do Azure.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az deployment sub create --name CreateDiagnosticSetting --location eastus --template-file CreateDiagnosticSetting.json --parameters settingName='Send Activity log to workspace' workspaceId='/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace-01'

```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
New-AzSubscriptionDeployment -Name CreateDiagnosticSetting -location eastus -TemplateFile CreateDiagnosticSetting.json -settingName "Send Activity log to workspace" -workspaceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace-01"
```
---

### <a name="validate-the-deployment"></a>Validar a implementação

Verifique se a definição de diagnóstico foi criada utilizando um dos seguintes comandos. Substitua os valores da amostra para a subscrição e o nome de definição pelos valores acima utilizados.

> [!NOTE]
> Atualmente, não é possível obter definições de diagnóstico de nível de subscrição utilizando o PowerShell.

```azurecli
az monitor diagnostic-settings show --resource '/subscriptions/00000000-0000-0000-0000-000000000000' --name 'Send Activity log to workspace'
```

## <a name="generate-log-data"></a>Gerar dados de registo

Apenas novas entradas de registo de atividade serão enviadas para o espaço de trabalho Log Analytics, por isso execute algumas ações na sua subscrição que serão registadas, como iniciar ou parar uma máquina virtual ou criar ou modificar outro recurso. Pode ter de esperar alguns minutos para que a definição de diagnóstico seja criada e que os dados sejam inicialmente escritos no espaço de trabalho. Após este atraso, todos os eventos escritos no registo de atividade serão enviados para o espaço de trabalho dentro de alguns segundos.

## <a name="retrieve-data-with-a-log-query"></a>Recuperar dados com uma consulta de registo

Utilize o portal Azure para utilizar o Log Analytics para obter dados do espaço de trabalho. No portal Azure, procure e, em seguida, selecione **Monitor**.

![Portal do Azure](media/quick-collect-activity-log/azure-portal-monitor.png)

Selecione **Registos** no menu **Azure Monitor.** Feche a página **de consultas Exemplo.** Se o âmbito não estiver definido para o espaço de trabalho que criou, clique em **Selecionar o âmbito** e localizá-lo.

![Log Analytics](media/quick-collect-activity-log/log-analytics-scope.png)

Na janela de consulta, `AzureActivity` escreva e clique em **Executar**. Esta é uma consulta simples que devolve todos os registos na tabela *AzureActivity,* que contém todos os registos enviados a partir do registo de Atividade.

![Consulta simples](media/quick-collect-activity-log/query-01.png)

Expandir um dos registos para ver as suas propriedades detalhadas.

![Expandir propriedades](media/quick-collect-activity-log/expand-properties.png)

Experimente uma consulta mais complexa, como `AzureActivity | summarize count() by CategoryValue` a que dá uma contagem de eventos resumidos por categoria.

![Consulta complexa](media/quick-collect-activity-log/query-02.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Se planeia continuar a trabalhar com os rápidos e tutoriais subsequentes, talvez queira deixar estes recursos no lugar. Quando já não for necessário, elimine o grupo de recursos, que elimina a regra de alerta e os recursos conexos. Para eliminar o grupo de recursos utilizando a Azure CLI ou a Azure PowerShell

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az group delete --name my-resource-group
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Remove-AzResourceGroup -Name my-resource-group
```

---

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, configuraste o registo de Atividade para ser enviado para um espaço de trabalho do Log Analytics. Pode agora configurar outros dados a serem recolhidos no espaço de trabalho onde pode analisá-los em conjunto usando [consultas de registo](../logs/log-query-overview.md) no Azure Monitor e funcionalidades de alavancagem, tais como [alertas](../alerts/alerts-log-query.md) de registo e livros de [trabalho.](../visualize/workbooks-overview.md) Em seguida, deverá recolher [registos](../essentials/resource-logs.md) de recursos dos seus recursos Azure que complementam os dados no registo de Atividade fornecendo informações sobre as operações que foram realizadas dentro de cada recurso.

> [!div class="nextstepaction"]
> [Recolher e analisar registos de recursos com o Azure Monitor](../essentials/tutorial-resource-logs.md)