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
ms.openlocfilehash: 91790f372dce4322d316b42c4bfa7ad36625c91d
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57315576"
---
# <a name="powershell-script-to-create-an-application-insights-resource"></a>Script do PowerShell para criar um recurso do Application Insights

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Quando quiser monitorizar uma nova aplicação – ou uma nova versão de um aplicativo - com [do Azure Application Insights](https://azure.microsoft.com/services/application-insights/), configurou um novo recurso no Microsoft Azure. Este recurso é onde os dados de telemetria da sua aplicação são analisados e apresentados. 

Pode automatizar a criação de um novo recurso com o PowerShell.

Por exemplo, se estiver a desenvolver uma aplicação de dispositivo móvel, é provável que, em qualquer altura, haverá várias versões publicadas do seu aplicativo em uso pelos clientes. Não quer obter os resultados de telemetria a partir de diferentes versões misturadas. Portanto, obtém seu processo de criação para criar um novo recurso para cada compilação.

> [!NOTE]
> Se quiser criar um conjunto de recursos todos ao mesmo tempo, considere [criação dos recursos utilizando um modelo Azure](powershell.md).
> 
> 

## <a name="script-to-create-an-application-insights-resource"></a>Script para criar um recurso do Application Insights
Consulte as especificações de cmdlet relevantes:

* [New-AzResource](https://msdn.microsoft.com/library/mt652510.aspx)
* [New-AzRoleAssignment](https://msdn.microsoft.com/library/mt678995.aspx)

*Script do PowerShell*  

```PowerShell


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

## <a name="what-to-do-with-the-ikey"></a>O que fazer com a iKey
Cada recurso é identificado pela respetiva chave de instrumentação (iKey). A iKey é uma saída do script de criação de recursos. O script de compilação deve fornecer que a iKey para o Application Insights SDK incorporou na sua aplicação.

Existem duas formas de disponibilizar a iKey para o SDK:

* Na [applicationinsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md): 
  * `<instrumentationkey>`*ikey*`</instrumentationkey>`
* Ou no [código de inicialização](../../azure-monitor/app/api-custom-events-metrics.md): 
  * `Microsoft.ApplicationInsights.Extensibility.
    TelemetryConfiguration.Active.InstrumentationKey = "`*iKey*`";`

## <a name="see-also"></a>Consulte também
* [Criar o Application Insights e recursos de teste da web a partir de modelos](powershell.md)
* [Configurar a monitorização dos diagnósticos do Azure com o PowerShell](powershell-azure-diagnostics.md) 
* [Definir alertas com o PowerShell](powershell-alerts.md)

