---
title: Automatizar informações de Aplicativo Azure com o PowerShell | Microsoft Docs
description: Automatize a criação e o gerenciamento de recursos, alertas e testes de disponibilidade no PowerShell usando um modelo de Azure Resource Manager.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 10/17/2019
ms.openlocfilehash: 938511069500c551eb526b6c7238546b85d59dce
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72818943"
---
#  <a name="manage-application-insights-resources-using-powershell"></a>Gerenciar Application Insights recursos usando o PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Este artigo mostra como automatizar a criação e a atualização de [Application insights](../../azure-monitor/app/app-insights-overview.md) recursos automaticamente usando o gerenciamento de recursos do Azure. Você pode, por exemplo, fazer isso como parte de um processo de compilação. Juntamente com o recurso de Application Insights básica, você pode criar [testes da Web de disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md), configurar [alertas](../../azure-monitor/app/alerts.md), definir o [esquema de preços](pricing.md)e criar outros recursos do Azure.

A chave para criar esses recursos são os modelos JSON para [Azure Resource Manager](../../azure-resource-manager/manage-resources-powershell.md). O procedimento básico é: baixar as definições de JSON dos recursos existentes; parametrizar determinados valores, como nomes; em seguida, execute o modelo sempre que desejar criar um novo recurso. Você pode empacotar vários recursos juntos, para criá-los em um só lugar, por exemplo, um monitor de aplicativo com testes de disponibilidade, alertas e armazenamento para exportação contínua. Há algumas sutilezas para algumas das parametrizações, que explicaremos aqui.

## <a name="one-time-setup"></a>Configuração única
Se você não usou o PowerShell com sua assinatura do Azure antes de:

Instale o módulo do Azure PowerShell no computador em que você deseja executar os scripts:

1. Instale o [Microsoft Web Platform Installer (V5 ou superior)](https://www.microsoft.com/web/downloads/platform.aspx).
2. Use-o para instalar Microsoft Azure PowerShell.

Além de usar modelos do Resource Manager, há um conjunto avançado de [cmdlets Application insights PowerShell](https://docs.microsoft.com/powershell/module/az.applicationinsights), que facilitam a configuração de Application insights recursos de forma programática. Os recursos habilitados pelos cmdlets incluem:

* Criar e excluir Application Insights recursos
* Obter listas de recursos de Application Insights e suas propriedades
* Criar e gerenciar a exportação contínua
* Criar e gerenciar chaves de aplicativo
* Definir o limite diário
* Definir o plano de preços

## <a name="create-application-insights-resources-using-a-powershell-cmdlet"></a>Criar Application Insights recursos usando um cmdlet do PowerShell

Veja como criar um novo recurso de Application Insights no datacenter do leste dos EUA do Azure usando o cmdlet [New-AzApplicationInsights](https://docs.microsoft.com/powershell/module/az.applicationinsights/New-AzApplicationInsights) :

```PS
New-AzApplicationInsights -ResourceGroupName <resource group> -Name <resource name> -location eastus
```


## <a name="create-application-insights-resources-using-a-resource-manager-template"></a>Criar Application Insights recursos usando um modelo do Resource Manager

Veja como criar um novo recurso de Application Insights usando um modelo do Resource Manager.

### <a name="create-the-azure-resource-manager-template"></a>Criar o modelo de Azure Resource Manager

Criar um novo arquivo. JSON-vamos chamá-lo de `template1.json` neste exemplo. Copie este conteúdo para ele:

```JSON
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "appName": {
                "type": "string",
                "metadata": {
                    "description": "Enter the name of your Application Insights resource."
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
                    "description": "Enter the type of the monitored application."
                }
            },
            "appLocation": {
                "type": "string",
                "defaultValue": "eastus",
                "metadata": {
                    "description": "Enter the location of your Application Insights resource."
                }
            },
            "retentionInDays": {
                "type": "int",
                "defaultValue": 90,
                "allowedValues": [
                    30,
                    60,
                    90,
                    120,
                    180,
                    270,
                    365,
                    550,
                    730
                ],
                "metadata": {
                    "description": "Data retention in days"
                }
            },
            "ImmediatePurgeDataOn30Days": {
                "type": "bool",
                "defaultValue": false,
                "metadata": {
                    "description": "If set to true when changing retention to 30 days, older data will be immediately deleted. Use this with extreme caution. This only applies when retention is being set to 30 days."
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
                    "description": "Pricing plan: 1 = Per GB (or legacy Basic plan), 2 = Per Node (legacy Enterprise plan)"
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
                    "retentionInDays": "[parameters('retentionInDays')]",
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

### <a name="use-the-resource-manager-template-to-create-a-new-application-insights-resource"></a>Usar o modelo do Resource Manager para criar um novo recurso de Application Insights

1. No PowerShell, entre no Azure usando `$Connect-AzAccount`
2. Defina seu contexto para uma assinatura com `Set-AzContext "<subscription ID>"`
2. Execute uma nova implantação para criar um novo recurso de Application Insights:
   
    ```PS
        New-AzResourceGroupDeployment -ResourceGroupName Fabrikam `
               -TemplateFile .\template1.json `
               -appName myNewApp

    ``` 
   
   * `-ResourceGroupName` é o grupo no qual você deseja criar os novos recursos.
   * `-TemplateFile` deve ocorrer antes dos parâmetros personalizados.
   * `-appName` o nome do recurso a ser criado.

Você pode adicionar outros parâmetros – você encontrará suas descrições na seção de parâmetros do modelo.

## <a name="get-the-instrumentation-key"></a>Obter a chave de instrumentação

Depois de criar um recurso de aplicativo, você desejará a chave de instrumentação: 

1. `$Connect-AzAccount`
2. `Set-AzContext "<subscription ID>"`
3. `$resource = Get-AzResource -Name "<resource name>" -ResourceType "Microsoft.Insights/components"`
4. `$details = Get-AzResource -ResourceId $resource.ResourceId`
5. `$details.Properties.InstrumentationKey`

Para ver uma lista de muitas outras propriedades do recurso Application Insights, use:

```PS
Get-AzApplicationInsights -ResourceGroupName Fabrikam -Name FabrikamProd | Format-List
```

As propriedades adicionais estão disponíveis por meio dos cmdlets:
* `Set-AzApplicationInsightsDailyCap`
* `Set-AzApplicationInsightsPricingPlan`
* `Get-AzApplicationInsightsApiKey`
* `Get-AzApplicationInsightsContinuousExport`

Consulte a [documentação detalhada](https://docs.microsoft.com/powershell/module/az.applicationinsights) para os parâmetros para esses cmdlets.  

## <a name="set-the-data-retention"></a>Definir a retenção de dados 

Para obter a retenção de dados atual para seu recurso de Application Insights, você pode usar a ferramenta OSS [ARMClient](https://github.com/projectkudu/ARMClient).  (Saiba mais sobre o ARMClient de artigos de [David Ebbo](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) e [Daniel Bowbyes](https://blog.bowbyes.co.nz/2016/11/02/using-armclient-to-directly-access-azure-arm-rest-apis-and-list-arm-policy-details/).)  Aqui está um exemplo usando `ARMClient` para obter a retenção atual:

```PS
armclient GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName?api-version=2018-05-01-preview
```

Para definir a retenção, o comando é um PUT semelhante:

```PS
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName?api-version=2018-05-01-preview "{location: 'eastus', properties: {'retentionInDays': 365}}"
```

Para definir a retenção de dados para 365 dias usando o modelo acima, execute:

```PS
        New-AzResourceGroupDeployment -ResourceGroupName "<resource group>" `
               -TemplateFile .\template1.json `
               -retentionInDays 365 `
               -appName myApp
```

O script a seguir também pode ser usado para alterar a retenção. Copie este script para salvar como `Set-ApplicationInsightsRetention.ps1`.

```PS
Param(
    [Parameter(Mandatory = $True)]
    [string]$SubscriptionId,

    [Parameter(Mandatory = $True)]
    [string]$ResourceGroupName,

    [Parameter(Mandatory = $True)]
    [string]$Name,

    [Parameter(Mandatory = $True)]
    [string]$RetentionInDays
)
$ErrorActionPreference = 'Stop'
if (-not (Get-Module Az.Accounts)) {
    Import-Module Az.Accounts
}
$azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
if (-not $azProfile.Accounts.Count) {
    Write-Error "Ensure you have logged in before calling this function."    
}
$currentAzureContext = Get-AzContext
$profileClient = New-Object Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient($azProfile)
$token = $profileClient.AcquireAccessToken($currentAzureContext.Tenant.TenantId)
$UserToken = $token.AccessToken
$RequestUri = "https://management.azure.com/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.Insights/components/$($Name)?api-version=2015-05-01"
$Headers = @{
    "Authorization"         = "Bearer $UserToken"
    "x-ms-client-tenant-id" = $currentAzureContext.Tenant.TenantId
}
## Get Component object via ARM
$GetResponse = Invoke-RestMethod -Method "GET" -Uri $RequestUri -Headers $Headers 

## Update RetentionInDays property
if($($GetResponse.properties | Get-Member "RetentionInDays"))
{
    $GetResponse.properties.RetentionInDays = $RetentionInDays
}
else
{
    $GetResponse.properties | Add-Member -Type NoteProperty -Name "RetentionInDays" -Value $RetentionInDays
}
## Upsert Component object via ARM
$PutResponse = Invoke-RestMethod -Method "PUT" -Uri "$($RequestUri)" -Headers $Headers -Body $($GetResponse | ConvertTo-Json) -ContentType "application/json"
$PutResponse
```

Esse script pode ser usado como:

```PS
Set-ApplicationInsightsRetention `
        [-SubscriptionId] <String> `
        [-ResourceGroupName] <String> `
        [-Name] <String> `
        [-RetentionInDays <Int>]
```

## <a name="set-the-daily-cap"></a>Definir o limite diário

Para obter as propriedades de limite diário, use o cmdlet [set-AzApplicationInsightsPricingPlan](https://docs.microsoft.com/powershell/module/az.applicationinsights/Set-AzApplicationInsightsPricingPlan) : 

```PS
Set-AzApplicationInsightsDailyCap -ResourceGroupName <resource group> -Name <resource name> | Format-List
```

Para definir as propriedades de limite diário, use o mesmo cmdlet. Por exemplo, para definir o limite de 300 GB/dia, 

```PS
Set-AzApplicationInsightsDailyCap -ResourceGroupName <resource group> -Name <resource name> -DailyCapGB 300
```

<a id="price"></a>
## <a name="set-the-pricing-plan"></a>Definir o plano de preços 

Para obter o plano de preços atual, use o cmdlet [set-AzApplicationInsightsPricingPlan](https://docs.microsoft.com/powershell/module/az.applicationinsights/Set-AzApplicationInsightsPricingPlan) : 

```PS
Set-AzApplicationInsightsPricingPlan -ResourceGroupName <resource group> -Name <resource name> | Format-List
```

Para definir o plano de preços, use o mesmo cmdlet com o `-PricingPlan` especificado:  

```PS
Set-AzApplicationInsightsPricingPlan -ResourceGroupName <resource group> -Name <resource name> -PricingPlan Basic
```

Você também pode definir o plano de preços em um recurso de Application Insights existente usando o modelo do Resource Manager acima, omitindo o recurso "Microsoft. insights/Components" e o nó `dependsOn` do recurso de cobrança. Por exemplo, para defini-lo para o plano por GB (anteriormente chamado de plano básico), execute:

```PS
        New-AzResourceGroupDeployment -ResourceGroupName "<resource group>" `
               -TemplateFile .\template1.json `
               -priceCode 1 `
               -appName myApp
```

|priceCode|intenção|
|---|---|
|1|Por GB (anteriormente chamado de plano básico)|
|2|Por nó (anteriormente chamado de plano Enterprise)|

## <a name="add-a-metric-alert"></a>Adicionar um alerta de métrica

Para configurar um alerta de métrica ao mesmo tempo que o recurso de aplicativo, mescle o código como este no arquivo de modelo:

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

Ao invocar o modelo, você pode opcionalmente adicionar esse parâmetro:

    `-responseTime 2`

É claro que você pode parametrizar outros campos. 

Para descobrir os nomes de tipo e os detalhes de configuração de outras regras de alerta, crie uma regra manualmente e, em seguida, inspecione-a em [Azure Resource Manager](https://resources.azure.com/). 


## <a name="add-an-availability-test"></a>Adicionar um teste de disponibilidade

Este exemplo é para um teste de ping (para testar uma única página).  

**Há duas partes** em um teste de disponibilidade: o teste em si e o alerta que notifica você sobre falhas.

Mescle o código a seguir no arquivo de modelo que cria o aplicativo.

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

Para descobrir os códigos de outros locais de teste ou para automatizar a criação de testes da Web mais complexos, crie um exemplo manualmente e, em seguida, parametriza o código de [Azure Resource Manager](https://resources.azure.com/).

## <a name="add-more-resources"></a>Adicionar mais recursos

Para automatizar a criação de qualquer outro recurso de qualquer tipo, crie um exemplo manualmente e, em seguida, copie e parametrizando seu código de [Azure Resource Manager](https://resources.azure.com/). 

1. Abra [Azure Resource Manager](https://resources.azure.com/). Navegue até `subscriptions/resourceGroups/<your resource group>/providers/Microsoft.Insights/components`, para o recurso de aplicativo. 
   
    ![Navegação no Azure Resource Explorer](./media/powershell/01.png)
   
    Os *componentes* são os recursos básicos de Application insights para a exibição de aplicativos. Há recursos separados para as regras de alerta associadas e testes da Web de disponibilidade.
2. Copie o JSON do componente no local apropriado em `template1.json`.
3. Exclua essas propriedades:
   
   * `id`
   * `InstrumentationKey`
   * `CreationDate`
   * `TenantId`
4. Abra as seções `webtests` e `alertrules` e copie o JSON para itens individuais em seu modelo. (Não copie dos nós `webtests` ou `alertrules`: Vá para os itens sob eles.)
   
    Cada teste na Web tem uma regra de alerta associada, portanto, você precisa copiar ambos.
   
    Você também pode incluir alertas sobre métricas. [Nomes de métrica](powershell-alerts.md#metric-names).
5. Inserir esta linha em cada recurso:
   
    `"apiVersion": "2015-05-01",`

### <a name="parameterize-the-template"></a>Parametrizar o modelo
Agora você precisa substituir os nomes específicos por parâmetros. Para [Parametrizar um modelo](../../azure-resource-manager/resource-group-authoring-templates.md), você escreve expressões usando um [conjunto de funções auxiliares](../../azure-resource-manager/resource-group-template-functions.md). 

Não é possível parametrizar apenas parte de uma cadeia de caracteres, portanto, use `concat()` para criar cadeias.

Aqui estão exemplos das substituições que você desejará fazer. Há várias ocorrências de cada substituição. Talvez você precise de outras pessoas em seu modelo. Esses exemplos usam os parâmetros e variáveis que definimos na parte superior do modelo.

| find | substituir por |
| --- | --- |
| `"hidden-link:/subscriptions/.../../components/MyAppName"` |`"[concat('hidden-link:',`<br/>`resourceId('microsoft.insights/components',` <br/> `parameters('appName')))]"` |
| `"/subscriptions/.../../alertrules/myAlertName-myAppName-subsId",` |`"[resourceId('Microsoft.Insights/alertrules', variables('alertRuleName'))]",` |
| `"/subscriptions/.../../webtests/myTestName-myAppName",` |`"[resourceId('Microsoft.Insights/webtests', parameters('webTestName'))]",` |
| `"myWebTest-myAppName"` |`"[variables(testName)]"'` |
| `"myTestName-myAppName-subsId"` |`"[variables('alertRuleName')]"` |
| `"myAppName"` |`"[parameters('appName')]"` |
| `"myappname"` (minúscula) |`"[toLower(parameters('appName'))]"` |
| `"<WebTest Name=\"myWebTest\" ...`<br/>`Url=\"http://fabrikam.com/home\" ...>"` |`[concat('<WebTest Name=\"',` <br/> `parameters('webTestName'),` <br/> `'\" ... Url=\"', parameters('Url'),` <br/> `'\"...>')]"`|

### <a name="set-dependencies-between-the-resources"></a>Definir dependências entre os recursos
O Azure deve configurar os recursos em ordem estrita. Para garantir que uma instalação seja concluída antes do próximo início, adicione linhas de dependência:

* No recurso de teste de disponibilidade:
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/components', parameters('appName'))]"],`
* No recurso de alerta para um teste de disponibilidade:
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/webtests', variables('testName'))]"],`



## <a name="next-steps"></a>Passos seguintes
Outros artigos de automação:

* [Crie um recurso Application insights](powershell-script-create-resource.md) – método rápido sem usar um modelo.
* [Configurar alertas](powershell-alerts.md)
* [Criar testes da Web](https://azure.microsoft.com/blog/creating-a-web-test-alert-programmatically-with-application-insights/)
* [Send Azure Diagnostics to Application Insights (Enviar o Diagnóstico do Azure para o Application Insights)](powershell-azure-diagnostics.md)
* [Implantar no Azure por meio do GitHub](https://blogs.msdn.com/b/webdev/archive/2015/09/16/deploy-to-azure-from-github-with-application-insights.aspx)
* [Criar anotações de versão](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1)