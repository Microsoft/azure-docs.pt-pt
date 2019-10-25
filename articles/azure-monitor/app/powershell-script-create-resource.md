---
title: Script do PowerShell para criar um recurso de Application Insights | Microsoft Docs
description: Automatize a criação de recursos de Application Insights.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 11/19/2016
ms.openlocfilehash: 11245d0f9d6e6b86a5d0249df65b33f851bee9d7
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72820683"
---
# <a name="powershell-script-to-create-an-application-insights-resource"></a>Script do PowerShell para criar um recurso do Application Insights

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Quando você deseja monitorar um novo aplicativo ou uma nova versão de um aplicativo – com o [aplicativo Azure insights](https://azure.microsoft.com/services/application-insights/), você configura um novo recurso no Microsoft Azure. Esse recurso é onde os dados de telemetria do seu aplicativo são analisados e exibidos. 

Você pode automatizar a criação de um novo recurso usando o PowerShell.

Por exemplo, se você estiver desenvolvendo um aplicativo de dispositivo móvel, é provável que, a qualquer momento, haverá várias versões publicadas do seu aplicativo em uso pelos seus clientes. Você não quer obter os resultados de telemetria de versões diferentes misturadas. Portanto, você obtém o processo de compilação para criar um novo recurso para cada compilação.

> [!NOTE]
> Se você quiser criar um conjunto de recursos todos ao mesmo tempo, considere [criar os recursos usando um modelo do Azure](powershell.md).
> 
> 

## <a name="script-to-create-an-application-insights-resource"></a>Script para criar um recurso de Application Insights
Consulte as especificações de cmdlet relevantes:

* [New-AzResource](https://msdn.microsoft.com/library/mt652510.aspx)
* [New-AzRoleAssignment](https://msdn.microsoft.com/library/mt678995.aspx)

*Script do PowerShell*  

```powershell


###########################################
# Set Values
###########################################

# If running manually, uncomment before the first 
# execution to login to the Azure Portal:

# Connect-AzAccount / Connect-AzAccount

# Set the name of the Application Insights Resource

$appInsightsName = "TestApp"

# Set the application name used for the value of the Tag "AppInsightsApp" 

$applicationTagName = "MyApp"

# Set the name of the Resource Group to use.  
# Default is the application name.
$resourceGroupName = "MyAppResourceGroup"

###################################################
# Create the Resource and Output the name and iKey
###################################################

# Select the azure subscription
Select-AzureSubscription -SubscriptionName "MySubscription"

# Create the App Insights Resource


$resource = New-AzResource `
  -ResourceName $appInsightsName `
  -ResourceGroupName $resourceGroupName `
  -Tag @{ applicationType = "web"; applicationName = $applicationTagName} `
  -ResourceType "Microsoft.Insights/components" `
  -Location "East US" `  # or North Europe, West Europe, South Central US
  -PropertyObject @{"Application_Type"="web"} `
  -Force

# Give owner access to the team

New-AzRoleAssignment `
  -SignInName "myteam@fabrikam.com" `
  -RoleDefinitionName Owner `
  -Scope $resource.ResourceId 


# Display iKey
Write-Host "App Insights Name = " $resource.Name
Write-Host "IKey = " $resource.Properties.InstrumentationKey

```

## <a name="what-to-do-with-the-ikey"></a>O que fazer com o iKey
Cada recurso é identificado por sua chave de instrumentação (iKey). O iKey é uma saída do script de criação de recurso. O script de compilação deve fornecer o iKey para o SDK Application Insights inserido em seu aplicativo.

Há duas maneiras de disponibilizar o iKey para o SDK:

* Em [ApplicationInsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md): 
  * `<instrumentationkey>`*ikey*`</instrumentationkey>`
* Ou no [código de inicialização](../../azure-monitor/app/api-custom-events-metrics.md): 
  * `Microsoft.ApplicationInsights.Extensibility.
    TelemetryConfiguration.Active.InstrumentationKey = "`*iKey*`";`

## <a name="see-also"></a>Ver também
* [Criar Application Insights e recursos de teste na Web a partir de modelos](powershell.md)
* [Configurar o monitoramento do diagnóstico do Azure com o PowerShell](powershell-azure-diagnostics.md) 
* [Definir alertas usando o PowerShell](powershell-alerts.md)

