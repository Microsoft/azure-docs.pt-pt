---
title: Script do PowerShell para criar um recurso do Application Insights | Documentos da Microsoft
description: Automatize a criação de recursos do Application Insights.
services: application-insights
documentationcenter: windows
author: mrbullwinkle
manager: carmonm
ms.assetid: f0082c9b-43ad-4576-a417-4ea8e0daf3d9
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 11/19/2016
ms.author: mbullwin
ms.openlocfilehash: 6cb1bc8608aa106d56382264184c1c4eb017538a
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/27/2018
ms.locfileid: "53790067"
---
# <a name="powershell-script-to-create-an-application-insights-resource"></a>Script do PowerShell para criar um recurso do Application Insights


Quando quiser monitorizar uma nova aplicação – ou uma nova versão de um aplicativo - com [do Azure Application Insights](https://azure.microsoft.com/services/application-insights/), configurou um novo recurso no Microsoft Azure. Este recurso é onde os dados de telemetria da sua aplicação são analisados e apresentados. 

Pode automatizar a criação de um novo recurso com o PowerShell.

Por exemplo, se estiver a desenvolver uma aplicação de dispositivo móvel, é provável que, em qualquer altura, haverá várias versões publicadas do seu aplicativo em uso pelos clientes. Não quer obter os resultados de telemetria a partir de diferentes versões misturadas. Portanto, obtém seu processo de criação para criar um novo recurso para cada compilação.

> [!NOTE]
> Se quiser criar um conjunto de recursos todos ao mesmo tempo, considere [criação dos recursos utilizando um modelo Azure](app-insights-powershell.md).
> 
> 

## <a name="script-to-create-an-application-insights-resource"></a>Script para criar um recurso do Application Insights
Consulte as especificações de cmdlet relevantes:

* [New-AzureRmResource](https://msdn.microsoft.com/library/mt652510.aspx)
* [New-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt678995.aspx)

*Script do PowerShell*  

```PowerShell


###########################################
# Set Values
###########################################

# If running manually, uncomment before the first 
# execution to login to the Azure Portal:

# Connect-AzureRmAccount / Connect-AzureRmAccount

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


$resource = New-AzureRmResource `
  -ResourceName $appInsightsName `
  -ResourceGroupName $resourceGroupName `
  -Tag @{ applicationType = "web"; applicationName = $applicationTagName} `
  -ResourceType "Microsoft.Insights/components" `
  -Location "East US" `  # or North Europe, West Europe, South Central US
  -PropertyObject @{"Application_Type"="web"} `
  -Force

# Give owner access to the team

New-AzureRmRoleAssignment `
  -SignInName "myteam@fabrikam.com" `
  -RoleDefinitionName Owner `
  -Scope $resource.ResourceId 


# Display iKey
Write-Host "App Insights Name = " $resource.Name
Write-Host "IKey = " $resource.Properties.InstrumentationKey

```

## <a name="what-to-do-with-the-ikey"></a>O que fazer com a iKey
Cada recurso é identificado pela respetiva chave de instrumentação (iKey). A iKey é uma saída do script de criação de recursos. O script de compilação deve fornecer que a iKey para o Application Insights SDK incorporou na sua aplicação.

Existem duas formas de disponibilizar a iKey para o SDK:

* Na [applicationinsights. config](../azure-monitor/app/configuration-with-applicationinsights-config.md): 
  * `<instrumentationkey>`*ikey*`</instrumentationkey>`
* Ou no [código de inicialização](../azure-monitor/app/api-custom-events-metrics.md): 
  * `Microsoft.ApplicationInsights.Extensibility.
    TelemetryConfiguration.Active.InstrumentationKey = "`*iKey*`";`

## <a name="see-also"></a>Consulte também
* [Criar o Application Insights e recursos de teste da web a partir de modelos](app-insights-powershell.md)
* [Configurar a monitorização dos diagnósticos do Azure com o PowerShell](app-insights-powershell-azure-diagnostics.md) 
* [Definir alertas com o PowerShell](app-insights-powershell-alerts.md)

