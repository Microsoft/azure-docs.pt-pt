---
title: Automatizar o Azure Application Insights com o PowerShell | Documentos da Microsoft
description: Automatize a criar testes de recursos, alerta e a disponibilidade no PowerShell com um modelo Azure Resource Manager.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 9f73b87f-be63-4847-88c8-368543acad8b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/02/2017
ms.author: mbullwin
ms.openlocfilehash: ea4bc61dec59308b2c2311e8300e44aae78fc041
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57313519"
---
#  <a name="create-application-insights-resources-using-powershell"></a>Criar recursos do Application Insights com o PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Este artigo mostra-lhe como automatizar a criação e atualização das [Application Insights](../../azure-monitor/app/app-insights-overview.md) recursos automaticamente, com a gestão de recursos do Azure. Pode, por exemplo, tal como parte de um processo de compilação. Juntamente com o recurso do Application Insights básico, pode criar [testes web de disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md), configure a [alertas](../../azure-monitor/app/alerts.md), defina o [preços esquema](pricing.md)e criar outros recursos do Azure .

A chave para criar esses recursos é modelos JSON para [do Azure Resource Manager](../../azure-resource-manager/manage-resources-powershell.md). Em resumo, o procedimento é: transferir as definições de JSON dos recursos existentes; parametrizar determinados valores, tais como nomes; e, em seguida, execute o modelo, sempre que pretender criar um novo recurso. Pode empacotar vários recursos em conjunto, para criá-los todos em um ir - por exemplo, um monitor de aplicação com testes de disponibilidade, alertas e armazenamento para a exportação contínua. Existem algumas sutilezas a algumas das parameterizations, o que vamos explicar aqui.

## <a name="one-time-setup"></a>Configuração de uso individual
Se ainda não utilizou o PowerShell com a sua subscrição do Azure antes de:

Instale o módulo Azure Powershell na máquina em que pretende executar os scripts:

1. Instale [Microsoft Web Platform Installer (v5 ou superior)](https://www.microsoft.com/web/downloads/platform.aspx).
2. Usá-lo para instalar o Microsoft Azure Powershell.

## <a name="create-an-azure-resource-manager-template"></a>Criar um modelo Azure Resource Manager
Criar um novo ficheiro. JSON - vamos chamá-lo `template1.json` neste exemplo. Copie este conteúdo para o mesmo:

```JSON
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "appName": {
                "type": "string",
                "metadata": {
                    "description": "Enter the application name."
                }
            },
            "appType": {
                "type": "string",
                "defaultValue": "web",
                "allowedValues": [
                    "web",
                    "java",
                    "other"
                ],
                "metadata": {
                    "description": "Enter the application type."
                }
            },
            "appLocation": {
                "type": "string",
                "defaultValue": "East US",
                "allowedValues": [
                    "South Central US",
                    "West Europe",
                    "East US",
                    "North Europe"
                ],
                "metadata": {
                    "description": "Enter the application location."
                }
            },
            "priceCode": {
                "type": "int",
                "defaultValue": 1,
                "allowedValues": [
                    1,
                    2
                ],
                "metadata": {
                    "description": "1 = Basic, 2 = Enterprise"
                }
            },
            "dailyQuota": {
                "type": "int",
                "defaultValue": 100,
                "minValue": 1,
                "metadata": {
                    "description": "Enter daily quota in GB."
                }
            },
            "dailyQuotaResetTime": {
                "type": "int",
                "defaultValue": 24,
                "metadata": {
                    "description": "Enter daily quota reset hour in UTC (0 to 23). Values outside the range will get a random reset hour."
                }
            },
            "warningThreshold": {
                "type": "int",
                "defaultValue": 90,
                "minValue": 1,
                "maxValue": 100,
                "metadata": {
                    "description": "Enter the % value of daily quota after which warning mail to be sent. "
                }
            }
        },
        "variables": {
            "priceArray": [
                "Basic",
                "Application Insights Enterprise"
            ],
            "pricePlan": "[take(variables('priceArray'),parameters('priceCode'))]",
            "billingplan": "[concat(parameters('appName'),'/', variables('pricePlan')[0])]"
        },
        "resources": [
            {
                "type": "microsoft.insights/components",
                "kind": "[parameters('appType')]",
                "name": "[parameters('appName')]",
                "apiVersion": "2014-04-01",
                "location": "[parameters('appLocation')]",
                "tags": {},
                "properties": {
                    "ApplicationId": "[parameters('appName')]"
                },
                "dependsOn": []
            },
            {
                "name": "[variables('billingplan')]",
                "type": "microsoft.insights/components/CurrentBillingFeatures",
                "location": "[parameters('appLocation')]",
                "apiVersion": "2015-05-01",
                "dependsOn": [
                    "[resourceId('microsoft.insights/components', parameters('appName'))]"
                ],
                "properties": {
                    "CurrentBillingFeatures": "[variables('pricePlan')]",
                    "DataVolumeCap": {
                        "Cap": "[parameters('dailyQuota')]",
                        "WarningThreshold": "[parameters('warningThreshold')]",
                        "ResetTime": "[parameters('dailyQuotaResetTime')]"
                    }
                }
            }
        ]
    }
```



## <a name="create-application-insights-resources"></a>Criar recursos do Application Insights
1. No PowerShell, inicie sessão no Azure:
   
    `Connect-AzAccount`
2. Execute um comando como este:
   
    ```PS
   
        New-AzResourceGroupDeployment -ResourceGroupName Fabrikam `
               -TemplateFile .\template1.json `
               -appName myNewApp

    ``` 
   
   * `-ResourceGroupName` é o grupo de onde pretende criar novos recursos.
   * `-TemplateFile` tem de ocorrer antes dos parâmetros personalizados.
   * `-appName` O nome do recurso a criar.

Pode adicionar outros parâmetros, encontrará as suas descrições na secção de parâmetros do modelo.

## <a name="to-get-the-instrumentation-key"></a>Para obter a chave de instrumentação
Depois de criar um recurso de aplicação, poderá ser útil a chave de instrumentação: 

```PS
    $resource = Find-AzResource -ResourceNameEquals "<YOUR APP NAME>" -ResourceType "Microsoft.Insights/components"
    $details = Get-AzResource -ResourceId $resource.ResourceId
    $ikey = $details.Properties.InstrumentationKey
```


<a id="price"></a>
## <a name="set-the-price-plan"></a>Definir o plano de preços

Pode definir o [plano de preços](pricing.md).

Para criar um recurso de aplicação com o plano de preços do Enterprise, usando o modelo acima:

```PS
        New-AzResourceGroupDeployment -ResourceGroupName Fabrikam `
               -TemplateFile .\template1.json `
               -priceCode 2 `
               -appName myNewApp
```

|priceCode|plano|
|---|---|
|1|Básica|
|2|Enterprise|

* Se apenas quiser utilizar o plano de preços básico predefinido, pode omitir o recurso de CurrentBillingFeatures do modelo.
* Se quiser alterar o plano de preços depois do recurso do componente tiver sido criado, pode utilizar um modelo que omite o recurso de "microsoft.insights/components". Além disso, omita o `dependsOn` nó do recurso de faturação. 

Para verificar o plano de preços atualizadas, observe a **utilização e a página de custos estimados** painel no browser. **Atualize a vista de browser** para se certificar de que vê o estado mais recente.



## <a name="add-a-metric-alert"></a>Adicionar um alerta de métrica

Para configurar um alerta de métrica ao mesmo tempo, como o recurso de aplicação, mesclar código como este para o ficheiro de modelo:

```JSON
{
    parameters: { ... // existing parameters ...
            "responseTime": {
              "type": "int",
              "defaultValue": 3,
              "minValue": 1,
              "metadata": {
                "description": "Enter response time threshold in seconds."
              }
    },
    variables: { ... // existing variables ...
      // Alert names must be unique within resource group.
      "responseAlertName": "[concat('ResponseTime-', toLower(parameters('appName')))]"
    }, 
    resources: { ... // existing resources ...
     {
      //
      // Metric alert on response time
      //
      "name": "[variables('responseAlertName')]",
      "type": "Microsoft.Insights/alertrules",
      "apiVersion": "2014-04-01",
      "location": "[parameters('appLocation')]",
      // Ensure this resource is created after the app resource:
      "dependsOn": [
        "[resourceId('Microsoft.Insights/components', parameters('appName'))]"
      ],
      "tags": {
        "[concat('hidden-link:', resourceId('Microsoft.Insights/components', parameters('appName')))]": "Resource"
      },
      "properties": {
        "name": "[variables('responseAlertName')]",
        "description": "response time alert",
        "isEnabled": true,
        "condition": {
          "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.ThresholdRuleCondition, Microsoft.WindowsAzure.Management.Mon.Client",
          "odata.type": "Microsoft.Azure.Management.Insights.Models.ThresholdRuleCondition",
          "dataSource": {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleMetricDataSource, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleMetricDataSource",
            "resourceUri": "[resourceId('microsoft.insights/components', parameters('appName'))]",
            "metricName": "request.duration"
          },
          "threshold": "[parameters('responseTime')]", //seconds
          "windowSize": "PT15M" // Take action if changed state for 15 minutes
        },
        "actions": [
          {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleEmailAction, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
            "sendToServiceOwners": true,
            "customEmails": []
          }
        ]
      }
    }
}
```

Quando invoca o modelo, opcionalmente, pode adicionar esse parâmetro:

    `-responseTime 2`

É claro, é possível parametrizar outros campos. 

Para obter os nomes de tipo e os detalhes de configuração de outras regras de alerta, criar manualmente uma regra e, em seguida, Inspecione-na [do Azure Resource Manager](https://resources.azure.com/). 


## <a name="add-an-availability-test"></a>Adicionar um teste de disponibilidade

Este exemplo destina-se um teste de ping (testar uma única página).  

**Existem duas partes** num teste de disponibilidade: o próprio teste e o alerta que notifica-o de falhas.

Intercale o código seguinte para o ficheiro de modelo que cria a aplicação.

```JSON
{
    parameters: { ... // existing parameters here ...
      "pingURL": { "type": "string" },
      "pingText": { "type": "string" , defaultValue: ""}
    },
    variables: { ... // existing variables here ...
      "pingTestName":"[concat('PingTest-', toLower(parameters('appName')))]",
      "pingAlertRuleName": "[concat('PingAlert-', toLower(parameters('appName')), '-', subscription().subscriptionId)]"
    },
    resources: { ... // existing resources here ...
    { //
      // Availability test: part 1 configures the test
      //
      "name": "[variables('pingTestName')]",
      "type": "Microsoft.Insights/webtests",
      "apiVersion": "2014-04-01",
      "location": "[parameters('appLocation')]",
      // Ensure this is created after the app resource:
      "dependsOn": [
        "[resourceId('Microsoft.Insights/components', parameters('appName'))]"
      ],
      "tags": {
        "[concat('hidden-link:', resourceId('Microsoft.Insights/components', parameters('appName')))]": "Resource"
      },
      "properties": {
        "Name": "[variables('pingTestName')]",
        "Description": "Basic ping test",
        "Enabled": true,
        "Frequency": 900, // 15 minutes
        "Timeout": 120, // 2 minutes
        "Kind": "ping", // single URL test
        "RetryEnabled": true,
        "Locations": [
          {
            "Id": "us-va-ash-azr"
          },
          {
            "Id": "emea-nl-ams-azr"
          },
          {
            "Id": "apac-jp-kaw-edge"
          }
        ],
        "Configuration": {
          "WebTest": "[concat('<WebTest   Name=\"', variables('pingTestName'), '\"   Enabled=\"True\"         CssProjectStructure=\"\"    CssIteration=\"\"  Timeout=\"120\"  WorkItemIds=\"\"         xmlns=\"http://microsoft.com/schemas/VisualStudio/TeamTest/2010\"         Description=\"\"  CredentialUserName=\"\"  CredentialPassword=\"\"         PreAuthenticate=\"True\"  Proxy=\"default\"  StopOnError=\"False\"         RecordedResultFile=\"\"  ResultsLocale=\"\">  <Items>  <Request Method=\"GET\"    Version=\"1.1\"  Url=\"', parameters('Url'),   '\" ThinkTime=\"0\"  Timeout=\"300\" ParseDependentRequests=\"True\"         FollowRedirects=\"True\" RecordResult=\"True\" Cache=\"False\"         ResponseTimeGoal=\"0\"  Encoding=\"utf-8\"  ExpectedHttpStatusCode=\"200\"         ExpectedResponseUrl=\"\" ReportingName=\"\" IgnoreHttpStatusCode=\"False\" />        </Items>  <ValidationRules> <ValidationRule  Classname=\"Microsoft.VisualStudio.TestTools.WebTesting.Rules.ValidationRuleFindText, Microsoft.VisualStudio.QualityTools.WebTestFramework, Version=10.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a\" DisplayName=\"Find Text\"         Description=\"Verifies the existence of the specified text in the response.\"         Level=\"High\"  ExecutionOrder=\"BeforeDependents\">  <RuleParameters>        <RuleParameter Name=\"FindText\" Value=\"',   parameters('pingText'), '\" />  <RuleParameter Name=\"IgnoreCase\" Value=\"False\" />  <RuleParameter Name=\"UseRegularExpression\" Value=\"False\" />  <RuleParameter Name=\"PassIfTextFound\" Value=\"True\" />  </RuleParameters> </ValidationRule>  </ValidationRules>  </WebTest>')]"
        },
        "SyntheticMonitorId": "[variables('pingTestName')]"
      }
    },

    {
      //
      // Availability test: part 2, the alert rule
      //
      "name": "[variables('pingAlertRuleName')]",
      "type": "Microsoft.Insights/alertrules",
      "apiVersion": "2014-04-01",
      "location": "[parameters('appLocation')]", 
      "dependsOn": [
        "[resourceId('Microsoft.Insights/webtests', variables('pingTestName'))]"
      ],
      "tags": {
        "[concat('hidden-link:', resourceId('Microsoft.Insights/components', parameters('appName')))]": "Resource",
        "[concat('hidden-link:', resourceId('Microsoft.Insights/webtests', variables('pingTestName')))]": "Resource"
      },
      "properties": {
        "name": "[variables('pingAlertRuleName')]",
        "description": "alert for web test",
        "isEnabled": true,
        "condition": {
          "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.LocationThresholdRuleCondition, Microsoft.WindowsAzure.Management.Mon.Client",
          "odata.type": "Microsoft.Azure.Management.Insights.Models.LocationThresholdRuleCondition",
          "dataSource": {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleMetricDataSource, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleMetricDataSource",
            "resourceUri": "[resourceId('microsoft.insights/webtests', variables('pingTestName'))]",
            "metricName": "GSMT_AvRaW"
          },
          "windowSize": "PT15M", // Take action if changed state for 15 minutes
          "failedLocationCount": 2
        },
        "actions": [
          {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleEmailAction, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
            "sendToServiceOwners": true,
            "customEmails": []
          }
        ]
      }
    }
}
```

Para detetar os códigos para outras localizações de teste, ou para automatizar a criação de testes da web mais complexos, criar um exemplo manualmente e, em seguida, parametrizar o código a partir [do Azure Resource Manager](https://resources.azure.com/).

## <a name="add-more-resources"></a>Adicionar mais recursos

Para automatizar a criação de qualquer outro recurso de qualquer tipo, criar um exemplo manualmente e, em seguida, copiar e parametrizar o seu código a partir [do Azure Resource Manager](https://resources.azure.com/). 

1. Open [o Azure Resource Manager](https://resources.azure.com/). Percorra para baixo `subscriptions/resourceGroups/<your resource group>/providers/Microsoft.Insights/components`, para o recurso do aplicativo. 
   
    ![Navegação no Explorador de recursos do Azure](./media/powershell/01.png)
   
    *Componentes* são os recursos do Application Insights básicos para a exibição de aplicativos. Existem recursos separados para as regras de alerta associadas e testes web de disponibilidade.
2. Copie o JSON do componente para o local apropriado na `template1.json`.
3. Elimine estas propriedades:
   
   * `id`
   * `InstrumentationKey`
   * `CreationDate`
   * `TenantId`
4. Abra as secções de testes Web e alertrules e copie o JSON para itens individuais no seu modelo. (Não copia a partir de nós de testes Web ou alertrules: vá para os itens abaixo deles.)
   
    Cada teste web tem uma regra de alerta associada, para que tenha de copiar os dois.
   
    Também pode incluir alertas em métricas. [Nomes de métrica](powershell-alerts.md#metric-names).
5. Insira esta linha em cada recurso:
   
    `"apiVersion": "2015-05-01",`

### <a name="parameterize-the-template"></a>Parametrizar o modelo
Agora, tem de substituir os nomes específicos com parâmetros. Para [parametrizar um modelo](../../azure-resource-manager/resource-group-authoring-templates.md), que escreve expressões utilizando um [conjunto de funções auxiliares](../../azure-resource-manager/resource-group-template-functions.md). 

Não é possível parametrizar apenas parte de uma cadeia, por isso, utilize `concat()` para criar cadeias de caracteres.

Seguem-se exemplos de substituições que vai querer fazer. Existem várias ocorrências de cada substituição. Poderá ter outras pessoas no seu modelo. Estes exemplos utilizam os parâmetros e variáveis que definimos na parte superior do modelo.

| find | Substituir por |
| --- | --- |
| `"hidden-link:/subscriptions/.../../components/MyAppName"` |`"[concat('hidden-link:',`<br/>` resourceId('microsoft.insights/components',` <br/> ` parameters('appName')))]"` |
| `"/subscriptions/.../../alertrules/myAlertName-myAppName-subsId",` |`"[resourceId('Microsoft.Insights/alertrules', variables('alertRuleName'))]",` |
| `"/subscriptions/.../../webtests/myTestName-myAppName",` |`"[resourceId('Microsoft.Insights/webtests', parameters('webTestName'))]",` |
| `"myWebTest-myAppName"` |`"[variables(testName)]"'` |
| `"myTestName-myAppName-subsId"` |`"[variables('alertRuleName')]"` |
| `"myAppName"` |`"[parameters('appName')]"` |
| `"myappname"` (minúsculas) |`"[toLower(parameters('appName'))]"` |
| `"<WebTest Name=\"myWebTest\" ...`<br/>` Url=\"http://fabrikam.com/home\" ...>"` |`[concat('<WebTest Name=\"',` <br/> `parameters('webTestName'),` <br/> `'\" ... Url=\"', parameters('Url'),` <br/> `'\"...>')]"`<br/>Eliminar Guid e ID. |

### <a name="set-dependencies-between-the-resources"></a>Conjunto de dependências entre os recursos
Azure deve configurar os recursos na ordem estrita. Para certificar-se de que um programa de configuração é concluída antes do início da próxima, adicione as linhas de dependência:

* No recurso de teste de disponibilidade:
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/components', parameters('appName'))]"],`
* No recurso de alerta para um teste de disponibilidade:
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/webtests', variables('testName'))]"],`



## <a name="next-steps"></a>Passos Seguintes
Outros artigos de automatização:

* [Criar um recurso do Application Insights](powershell-script-create-resource.md) -método rápido de criação sem utilizar um modelo.
* [Configurar alertas](powershell-alerts.md)
* [Criar testes web](https://azure.microsoft.com/blog/creating-a-web-test-alert-programmatically-with-application-insights/)
* [Send Azure Diagnostics to Application Insights (Enviar o Diagnóstico do Azure para o Application Insights)](powershell-azure-diagnostics.md)
* [Implementar no Azure a partir do GitHub](https://blogs.msdn.com/b/webdev/archive/2015/09/16/deploy-to-azure-from-github-with-application-insights.aspx)
* [Criar notas de versão](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1)

