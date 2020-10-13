---
title: Automatizar insights de aplicação Azure com PowerShell Microsoft Docs
description: Automatizar a criação e gestão de recursos, alertas e testes de disponibilidade no PowerShell utilizando um modelo de Gestor de Recursos Azure.
ms.topic: conceptual
ms.date: 05/02/2020
ms.openlocfilehash: b5f3ba12fe8a730ce45e64b896ccc9c32b17b30c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91760908"
---
#  <a name="manage-application-insights-resources-using-powershell"></a>Gerir recursos de Insights de Aplicação utilizando o PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Este artigo mostra-lhe como automatizar a criação e atualização dos recursos de Insights de [Aplicação](./app-insights-overview.md) automaticamente utilizando a Azure Resource Management. Pode, por exemplo, fazê-lo como parte de um processo de construção. Juntamente com o recurso básico Application Insights, pode criar [testes web de disponibilidade,](./monitor-web-app-availability.md)configurar [alertas,](../platform/alerts-log.md)definir o [esquema de preços](pricing.md)e criar outros recursos Azure.

A chave para a criação destes recursos são os modelos JSON para [O Gestor de Recursos Azure.](../../azure-resource-manager/management/manage-resources-powershell.md) O procedimento básico é: descarregar as definições JSON dos recursos existentes; parametrizar certos valores, tais como nomes; e, em seguida, executar o modelo sempre que quiser criar um novo recurso. Você pode embalar vários recursos juntos, para criá-los todos de uma só vez - por exemplo, um monitor de aplicações com testes de disponibilidade, alertas e armazenamento para exportação contínua. Há algumas subtilezas em algumas das parametrizações, que vamos explicar aqui.

## <a name="one-time-setup"></a>Configuração única
Se ainda não utilizou o PowerShell com a sua subscrição Azure:

Instale o módulo Azure PowerShell na máquina onde pretende executar os scripts:

1. Instale o Instalador da Plataforma Web do [Microsoft (v5 ou superior)](https://www.microsoft.com/web/downloads/platform.aspx).
2. Utilize-o para instalar o Microsoft Azure PowerShell.

Além de utilizar modelos de Gestor de Recursos, existe um rico conjunto de [cmdlets PowerShell](/powershell/module/az.applicationinsights)de Insights de Aplicação, que facilitam a configuração dos recursos de Insights de Aplicação programaticamente. As capacidades ativadas pelos cmdlets incluem:

* Criar e eliminar recursos de Insights de Aplicação
* Obtenha listas de recursos de Insights de Aplicação e suas propriedades
* Criar e gerir a Exportação Contínua
* Criar e gerir Chaves de Aplicação
* Definir o Daily Cap
* Definir o Plano de Preços

## <a name="create-application-insights-resources-using-a-powershell-cmdlet"></a>Criar recursos de Insights de Aplicação utilizando um cmdlet PowerShell

Eis como criar um novo recurso application insights no centro de dados Azure East US usando o [cmdlet New-AzApplicationInsights:](/powershell/module/az.applicationinsights/new-azapplicationinsights)

```PS
New-AzApplicationInsights -ResourceGroupName <resource group> -Name <resource name> -location eastus
```


## <a name="create-application-insights-resources-using-a-resource-manager-template"></a>Criar recursos de Insights de Aplicação usando um modelo de Gestor de Recursos

Aqui está como criar um novo recurso Application Insights usando um modelo de Gestor de Recursos.

### <a name="create-the-azure-resource-manager-template"></a>Crie o modelo de Gestor de Recursos Azure

Crie um novo ficheiro .json - vamos chamá-lo `template1.json` neste exemplo. Copie este conteúdo nele:

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
                "defaultValue": 0,
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
                    "ApplicationId": "[parameters('appName')]",
                    "retentionInDays": "[parameters('retentionInDays')]"
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

### <a name="use-the-resource-manager-template-to-create-a-new-application-insights-resource"></a>Utilize o modelo de Gestor de Recursos para criar um novo recurso Application Insights

1. Em PowerShell, inscreva-se no Azure usando `$Connect-AzAccount`
2. Desa parte para uma subscrição com `Set-AzContext "<subscription ID>"`
2. Executar uma nova implementação para criar um novo recurso Application Insights:
   
    ```PS
        New-AzResourceGroupDeployment -ResourceGroupName Fabrikam `
               -TemplateFile .\template1.json `
               -appName myNewApp

    ``` 
   
   * `-ResourceGroupName` é o grupo onde se quer criar os novos recursos.
   * `-TemplateFile` deve ocorrer antes dos parâmetros personalizados.
   * `-appName` O nome do recurso para criar.

Pode adicionar outros parâmetros - encontrará as suas descrições na secção de parâmetros do modelo.

## <a name="get-the-instrumentation-key"></a>Obtenha a chave de instrumentação

Depois de criar um recurso de aplicação, vai querer a chave de instrumentação: 

1. `$Connect-AzAccount`
2. `Set-AzContext "<subscription ID>"`
3. `$resource = Get-AzResource -Name "<resource name>" -ResourceType "Microsoft.Insights/components"`
4. `$details = Get-AzResource -ResourceId $resource.ResourceId`
5. `$details.Properties.InstrumentationKey`

Para ver uma lista de muitas outras propriedades do seu recurso Application Insights, utilize:

```PS
Get-AzApplicationInsights -ResourceGroupName Fabrikam -Name FabrikamProd | Format-List
```

Propriedades adicionais estão disponíveis através dos cmdlets:
* `Set-AzApplicationInsightsDailyCap`
* `Set-AzApplicationInsightsPricingPlan`
* `Get-AzApplicationInsightsApiKey`
* `Get-AzApplicationInsightsContinuousExport`

Consulte a [documentação detalhada](/powershell/module/az.applicationinsights) para os parâmetros destes cmdlets.  

## <a name="set-the-data-retention"></a>Definir a retenção de dados

Abaixo estão três métodos para definir programáticamente a retenção de dados num recurso Application Insights.

### <a name="setting-data-retention-using-a-powershell-commands"></a>Definição da retenção de dados utilizando comandos PowerShell

Aqui está um conjunto simples de comandos PowerShell para definir a retenção de dados para o seu recurso Application Insights:

```PS
$Resource = Get-AzResource -ResourceType Microsoft.Insights/components -ResourceGroupName MyResourceGroupName -ResourceName MyResourceName
$Resource.Properties.RetentionInDays = 365
$Resource | Set-AzResource -Force
```

### <a name="setting-data-retention-using-rest"></a>Definir a retenção de dados usando o REST

Para obter a retenção de dados atual para o seu recurso Application Insights, pode utilizar a ferramenta OSS [ARMClient](https://github.com/projectkudu/ARMClient).  (Saiba mais sobre o ARMClient a partir de artigos de [David Ebbo](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) e [Daniel Bowbyes](https://blog.bowbyes.co.nz/2016/11/02/using-armclient-to-directly-access-azure-arm-rest-apis-and-list-arm-policy-details/).)  Aqui está um exemplo `ARMClient` usando, para obter a retenção atual:

```PS
armclient GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName?api-version=2018-05-01-preview
```

Para definir a retenção, o comando é um PUT semelhante:

```PS
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName?api-version=2018-05-01-preview "{location: 'eastus', properties: {'retentionInDays': 365}}"
```

Para definir a retenção de dados para 365 dias usando o modelo acima, corra:

```PS
New-AzResourceGroupDeployment -ResourceGroupName "<resource group>" `
       -TemplateFile .\template1.json `
       -retentionInDays 365 `
       -appName myApp
```

### <a name="setting-data-retention-using-a-powershell-script"></a>Definição da retenção de dados usando um script PowerShell

O seguinte script também pode ser usado para alterar a retenção. Copie este script para guardar como `Set-ApplicationInsightsRetention.ps1` .

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

Este script pode então ser usado como:

```PS
Set-ApplicationInsightsRetention `
        [-SubscriptionId] <String> `
        [-ResourceGroupName] <String> `
        [-Name] <String> `
        [-RetentionInDays <Int>]
```

## <a name="set-the-daily-cap"></a>Definir a tampa diária

Para obter as propriedades da tampa diária, utilize o [cmdlet Set-AzApplicationInsightsPricingPlan:](/powershell/module/az.applicationinsights/set-azapplicationinsightspricingplan) 

```PS
Set-AzApplicationInsightsDailyCap -ResourceGroupName <resource group> -Name <resource name> | Format-List
```

Para definir as propriedades da tampa diária, utilize o mesmo cmdlet. Por exemplo, para fixar a tampa para 300 GB/dia,

```PS
Set-AzApplicationInsightsDailyCap -ResourceGroupName <resource group> -Name <resource name> -DailyCapGB 300
```

Também pode utilizar [o ARMClient](https://github.com/projectkudu/ARMClient) para obter e definir parâmetros de tampa diária.  Para obter os valores atuais, use:

```PS
armclient GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName/CurrentBillingFeatures?api-version=2018-05-01-preview
```

## <a name="set-the-daily-cap-reset-time"></a>Desajuste do tempo de reset da tampa diária

Para definir o tempo de reset da tampa diária, pode utilizar [o ARMClient](https://github.com/projectkudu/ARMClient). Aqui está um exemplo `ARMClient` usando, para definir o tempo de reset para uma nova hora (neste exemplo 12:00 UTC):

```PS
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName/CurrentBillingFeatures?api-version=2018-05-01-preview "{'CurrentBillingFeatures':['Basic'],'DataVolumeCap':{'ResetTime':12}}"
```

<a id="price"></a>
## <a name="set-the-pricing-plan"></a>Definir o plano de preços 

Para obter o plano de preços atual, utilize o [cmdlet Set-AzApplicationInsightsPricingPlan:](/powershell/module/az.applicationinsights/set-azapplicationinsightspricingplan)

```PS
Set-AzApplicationInsightsPricingPlan -ResourceGroupName <resource group> -Name <resource name> | Format-List
```

Para definir o plano de preços, utilize o mesmo cmdlet com o `-PricingPlan` especificado:  

```PS
Set-AzApplicationInsightsPricingPlan -ResourceGroupName <resource group> -Name <resource name> -PricingPlan Basic
```

Também pode definir o plano de preços num recurso de Insights de Aplicação existente utilizando o modelo de Gestor de Recursos acima, omitindo o recurso "microsoft.insights/componentes" e o `dependsOn` nó do recurso de faturação. Por exemplo, para defini-lo para o plano Per GB (anteriormente chamado de plano básico), executar:

```PS
        New-AzResourceGroupDeployment -ResourceGroupName "<resource group>" `
               -TemplateFile .\template1.json `
               -priceCode 1 `
               -appName myApp
```

O `priceCode` é definido como:

|preçoDesco|plano|
|---|---|
|1|Per GB (anteriormente nomeado o plano básico)|
|2|Per Nó (anteriormente nomeio o plano Enterprise)|

Finalmente, pode utilizar [o ARMClient](https://github.com/projectkudu/ARMClient) para obter e definir planos de preços e parâmetros de tampa diária.  Para obter os valores atuais, use:

```PS
armclient GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName/CurrentBillingFeatures?api-version=2018-05-01-preview
```

E pode definir todos estes parâmetros usando:

```PS
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName/CurrentBillingFeatures?api-version=2018-05-01-preview
"{'CurrentBillingFeatures':['Basic'],'DataVolumeCap':{'Cap':200,'ResetTime':12,'StopSendNotificationWhenHitCap':true,'WarningThreshold':90,'StopSendNotificationWhenHitThreshold':true}}"
```

Isto irá definir o limite diário para 200 GB/dia, configurar o tempo de reset da tampa diária para 12:00 UTC, enviar e-mails tanto quando a tampa é atingida e o nível de aviso é cumprido, e definir o limiar de aviso para 90% da tampa.  

## <a name="add-a-metric-alert"></a>Adicione um alerta métrico

Para automatizar a criação de alertas métricos, consulte o [artigo do modelo de alertas métricos](../platform/alerts-metric-create-templates.md#template-for-a-simple-static-threshold-metric-alert)


## <a name="add-an-availability-test"></a>Adicionar um teste de disponibilidade

Para automatizar os testes de disponibilidade, consulte o [artigo do modelo de alertas métricos](../platform/alerts-metric-create-templates.md#template-for-an-availability-test-along-with-a-metric-alert).

## <a name="add-more-resources"></a>Adicionar mais recursos

Para automatizar a criação de qualquer outro recurso de qualquer tipo, crie um exemplo manualmente e, em seguida, copie e parametize o seu código a partir do [Azure Resource Manager](https://resources.azure.com/). 

1. Abrir [gestor de recursos Azure](https://resources.azure.com/). Navegue até `subscriptions/resourceGroups/<your resource group>/providers/Microsoft.Insights/components` ao seu recurso de aplicação. 
   
    ![Navegação no Explorador de Recursos Azure](./media/powershell/01.png)
   
    Os componentes são os recursos *básicos* de Insights de Aplicação para exibir aplicações. Existem recursos separados para as regras de alerta associadas e testes web de disponibilidade.
2. Copie o JSON do componente para o local apropriado em `template1.json` .
3. Eliminar estas propriedades:
   
   * `id`
   * `InstrumentationKey`
   * `CreationDate`
   * `TenantId`
4. Abra as `webtests` secções e `alertrules` copie o JSON para itens individuais no seu modelo. (Não copie dos `webtests` ou `alertrules` nó: entre nos itens por baixo deles.)
   
    Cada teste web tem uma regra de alerta associada, por isso tem de copiar os dois.
   
    Também pode incluir alertas em métricas. [Nomes métricos](powershell-alerts.md#metric-names).
5. Insira esta linha em cada recurso:
   
    `"apiVersion": "2015-05-01",`

### <a name="parameterize-the-template"></a>Parametize o modelo
Agora tem que substituir os nomes específicos por parâmetros. Para [parametrizar um modelo,](../../azure-resource-manager/templates/template-syntax.md)escreva expressões utilizando um [conjunto de funções de ajudante](../../azure-resource-manager/templates/template-functions.md). 

Não se pode parametrizar apenas uma parte de uma corda, por isso use `concat()` para construir cordas.

Aqui estão exemplos das substituições que vai querer fazer. Há várias ocorrências de cada substituição. Pode precisar de outros no seu modelo. Estes exemplos usam os parâmetros e variáveis que definimos no topo do modelo.

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
Azure deve criar os recursos em ordem estrita. Para se certificar de que uma configuração termina antes do início do próximo, adicione linhas de dependência:

* No recurso de teste de disponibilidade:
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/components', parameters('appName'))]"],`
* No recurso de alerta para um teste de disponibilidade:
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/webtests', variables('testName'))]"],`



## <a name="next-steps"></a>Passos seguintes
Outros artigos de automação:

* [Crie um recurso Application Insights](./create-new-resource.md#creating-a-resource-automatically) - método rápido sem usar um modelo.
* [Configurar alertas](powershell-alerts.md)
* [Criar testes Web](https://azure.microsoft.com/blog/creating-a-web-test-alert-programmatically-with-application-insights/)
* [Enviar o Diagnóstico do Azure ao Application Insights](powershell-azure-diagnostics.md)
* [Criar anotações de libertação](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1)

