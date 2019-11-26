---
title: Quickstart:Create a profile for high availability of applications - Azure PowerShell - Azure Traffic Manager
description: This quickstart article describes how to create a Traffic Manager profile to build a highly available web application.
services: traffic-manager
author: asudbring
mnager: twooley
Customer intent: As an IT admin, I want to direct user traffic to ensure high availability of web applications.
ms.service: traffic-manager
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/04/2019
ms.author: allensu
ms.openlocfilehash: 01749c2bd9091449e11e4dd30e88d2fe7d0df78b
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74483759"
---
# <a name="quickstart-create-a-traffic-manager-profile-for-a-highly-available-web-application-using-azure-powershell"></a>Quickstart: Create a Traffic Manager profile for a highly available web application using Azure PowerShell

This quickstart describes how to create a Traffic Manager profile that delivers high availability for your web application.

In this quickstart, you'll create two instances of a web application. Each of them is running in a different Azure region. You'll create a Traffic Manager profile based on [endpoint priority](traffic-manager-routing-methods.md#priority-traffic-routing-method). The profile directs user traffic to the primary site running the web application. Traffic Manager continuously monitors the web application. If the primary site is unavailable, it provides automatic failover to the backup site.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) agora.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar o PowerShell localmente, este artigo requer a versão 5.4.1 ou posterior do módulo Azure PowerShell. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-Az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.

## <a name="create-a-resource-group"></a>Criar um Grupo de Recursos
Create a resource group using [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).

```azurepowershell-interactive


# Variables
$Location1="WestUS"

# Create a Resource Group
New-AzResourceGroup -Name MyResourceGroup -Location $Location1
```

## <a name="create-a-traffic-manager-profile"></a>Criar um perfil do Gestor de Tráfego

Create a Traffic Manager profile using [New-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/new-aztrafficmanagerprofile) that directs user traffic based on endpoint priority.

```azurepowershell-interactive

# Generates a random value
$Random=(New-Guid).ToString().Substring(0,8)
$mytrafficmanagerprofile="mytrafficmanagerprofile$Random"

New-AzTrafficManagerProfile `
-Name $mytrafficmanagerprofile `
-ResourceGroupName MyResourceGroup `
-TrafficRoutingMethod Priority `
-MonitorPath '/' `
-MonitorProtocol "HTTP" `
-RelativeDnsName $mytrafficmanagerprofile `
-Ttl 30 `
-MonitorPort 80
```

## <a name="create-web-apps"></a>Create Web Apps

For this quickstart, you'll need two instances of a web application deployed in two different Azure regions (*West US* and *East US*). Each will serve as primary and failover endpoints for Traffic Manager.

### <a name="create-web-app-service-plans"></a>Create Web App Service plans
Create Web App service plans using [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) for the two instances of the web application that you will deploy in two different Azure regions.

```azurepowershell-interactive

# Variables
$App1Name="AppServiceTM1$Random"
$App2Name="AppServiceTM2$Random"
$Location1="WestUS"
$Location2="EastUS"

# Create an App service plan
New-AzAppservicePlan -Name "$App1Name-Plan" -ResourceGroupName MyResourceGroup -Location $Location1 -Tier Standard
New-AzAppservicePlan -Name "$App2Name-Plan" -ResourceGroupName MyResourceGroup -Location $Location2 -Tier Standard

```
### <a name="create-a-web-app-in-the-app-service-plan"></a>Create a Web App in the App Service Plan
Create two instances the web application using [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) in the App Service plans in the *West US* and *East US* Azure regions.

```azurepowershell-interactive
$App1ResourceId=(New-AzWebApp -Name $App1Name -ResourceGroupName MyResourceGroup -Location $Location1 -AppServicePlan "$App1Name-Plan").Id
$App2ResourceId=(New-AzWebApp -Name $App2Name -ResourceGroupName MyResourceGroup -Location $Location2 -AppServicePlan "$App2Name-Plan").Id

```

## <a name="add-traffic-manager-endpoints"></a>Adicionar pontos finais do Gestor de Tráfego
Add the two Web Apps as Traffic Manager endpoints using [New-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/new-aztrafficmanagerendpoint) to the Traffic Manager profile as follows:
- Add the Web App located in the *West US* Azure region as the primary endpoint to route all the user traffic. 
- Add the Web App located in the *East US* Azure region as the failover endpoint. When the primary endpoint is unavailable, traffic automatically routes to the failover endpoint.

```azurepowershell-interactive
New-AzTrafficManagerEndpoint -Name "$App1Name-$Location1" `
-ResourceGroupName MyResourceGroup `
-ProfileName "$mytrafficmanagerprofile" `
-Type AzureEndpoints `
-TargetResourceId $App1ResourceId `
-EndpointStatus "Enabled"

New-AzTrafficManagerEndpoint -Name "$App2Name-$Location2" `
-ResourceGroupName MyResourceGroup `
-ProfileName "$mytrafficmanagerprofile" `
-Type AzureEndpoints `
-TargetResourceId $App2ResourceId `
-EndpointStatus "Enabled"
```

## <a name="test-traffic-manager-profile"></a>Testar o perfil do Gestor de Tráfego

In this section, you'll check the domain name of your Traffic Manager profile. You'll also configure the primary endpoint to be unavailable. Finally, you get to see that the web app is still available. It's because Traffic Manager sends the traffic to the failover endpoint.

### <a name="determine-the-dns-name"></a>Determinar o nome DNS

Determine the DNS name of the Traffic Manager profile using [Get-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/get-aztrafficmanagerprofile).

```azurepowershell-interactive
Get-AzTrafficManagerProfile -Name $mytrafficmanagerprofile `
-ResourceGroupName MyResourceGroup
```

Copy the **RelativeDnsName** value. The DNS name of your Traffic Manager profile is *http://<* relativednsname *>.trafficmanager.net*. 

### <a name="view-traffic-manager-in-action"></a>Ver o Gestor de Tráfego em ação
1. In a web browser, enter the DNS name of your Traffic Manager profile (*http://<* relativednsname *>.trafficmanager.net*) to view your Web App's default website.

    > [!NOTE]
    > In this quickstart scenario, all requests route to the primary endpoint. It is set to **Priority 1**.
2. To view Traffic Manager failover in action, disable your primary site using [Disable-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/disable-aztrafficmanagerendpoint).

   ```azurepowershell-interactive
    Disable-AzTrafficManagerEndpoint -Name $App1Name-$Location1 `
    -Type AzureEndpoints `
    -ProfileName $mytrafficmanagerprofile `
    -ResourceGroupName MyResourceGroup `
    -Force
   ```
3. Copy the DNS name of your Traffic Manager profile (*http://<* relativednsname *>.trafficmanager.net*) to view the website in a new web browser session.
4. Verify that the web app is still available.

## <a name="clean-up-resources"></a>Limpar recursos

When you're done, delete the resource groups, web applications, and all related resources using [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

In this quickstart, you created a Traffic Manager profile that provides high availability for your web application. To learn more about routing traffic, continue to the Traffic Manager tutorials.

> [!div class="nextstepaction"]
> [Traffic Manager tutorials](tutorial-traffic-manager-improve-website-response.md) (Tutoriais do Gestor de Tráfego)
