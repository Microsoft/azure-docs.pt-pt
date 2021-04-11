---
title: 'Quickstart: Criar um perfil para alta disponibilidade de aplicações - Azure PowerShell - Azure Traffic Manager'
description: Este artigo de arranque rápido descreve como criar um perfil de Gestor de Tráfego para construir uma aplicação web altamente disponível.
services: traffic-manager
author: duongau
mnager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/01/2020
ms.author: duau
ms.openlocfilehash: ed3f6c96f6c02d5dad686947ee7f61e8852b559f
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106063820"
---
# <a name="quickstart-create-a-traffic-manager-profile-for-a-highly-available-web-application-using-azure-powershell"></a>Quickstart: Criar um perfil de Gestor de Tráfego para uma aplicação web altamente disponível usando a Azure PowerShell

Este quickstart descreve como criar um perfil de Gestor de Tráfego que oferece alta disponibilidade para a sua aplicação web.

Neste arranque rápido, criará duas instâncias de uma aplicação web. Cada um deles está a correr numa região diferente do Azure. Você vai criar um perfil de Gestor de Tráfego baseado na [prioridade do ponto final](traffic-manager-routing-methods.md#priority-traffic-routing-method). O perfil direciona o tráfego do utilizador para o site primário que executa a aplicação web. O Gestor de Tráfego monitoriza continuamente a aplicação web. Se o site principal não estiver disponível, fornece falha automática no site de reserva.

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) agora.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar o PowerShell localmente, este artigo requer a versão 5.4.1 ou posterior do módulo Azure PowerShell. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-Az-ps). Se estiver a executar o PowerShell localmente, também precisa de correr `Connect-AzAccount` para criar uma ligação com o Azure.

## <a name="create-a-resource-group"></a>Criar um Grupo de Recursos
Criar um grupo de recursos utilizando [o New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).

```azurepowershell-interactive

# Variables
$Location1="WestUS"

# Create a Resource Group
New-AzResourceGroup -Name MyResourceGroup -Location $Location1
```

## <a name="create-a-traffic-manager-profile"></a>Criar um perfil do Gestor de Tráfego

Crie um perfil de Gestor de Tráfego utilizando [o New-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/new-aztrafficmanagerprofile) que direcione o tráfego do utilizador com base na prioridade do ponto final.

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

## <a name="create-web-apps"></a>Criar aplicativos web

Para este arranque rápido, você precisará de duas instâncias de uma aplicação web implantada em duas regiões diferentes de Azure *(Eua Ocidental* e *Leste dos EUA).* Cada um servirá como ponto final primário e de saída para o Gestor de Tráfego.

### <a name="create-web-app-service-plans"></a>Criar planos de Serviço de Aplicações Web
Crie planos de serviço de aplicações web utilizando o [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) para as duas instâncias da aplicação web que irá implementar em duas regiões diferentes do Azure.

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
### <a name="create-a-web-app-in-the-app-service-plan"></a>Criar uma aplicação web no Plano de Serviço de Aplicações
Crie duas instâncias a aplicação web utilizando o [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) nos planos do Serviço de Aplicações nas regiões Azure *dos EUA* e *Leste dos EUA.*

```azurepowershell-interactive
$App1ResourceId=(New-AzWebApp -Name $App1Name -ResourceGroupName MyResourceGroup -Location $Location1 -AppServicePlan "$App1Name-Plan").Id
$App2ResourceId=(New-AzWebApp -Name $App2Name -ResourceGroupName MyResourceGroup -Location $Location2 -AppServicePlan "$App2Name-Plan").Id

```

## <a name="add-traffic-manager-endpoints"></a>Adicionar pontos finais do Gestor de Tráfego
Adicione as duas Aplicações Web como pontos finais do Gestor de Tráfego utilizando [o New-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/new-aztrafficmanagerendpoint) ao perfil de Gestor de Tráfego da seguinte forma:
- Adicione a Web App localizada na região *do West US* Azure como o principal ponto final para encaminhar todo o tráfego do utilizador. 
- Adicione a Web App localizada na região *de East US* Azure como o ponto final de failover. Quando o ponto final principal não está disponível, o tráfego liga-se automaticamente para o ponto final de saída.

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

Nesta secção, você verificará o nome de domínio do seu perfil de Gestor de Tráfego. Também configurará o ponto final principal para não estar disponível. Finalmente, pode ver que a aplicação web ainda está disponível. É porque o Gerente de Tráfego envia o tráfego para o ponto final de falha.

### <a name="determine-the-dns-name"></a>Determinar o nome DNS

Determine o nome DNS do perfil do Gestor de Tráfego utilizando [o Get-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/get-aztrafficmanagerprofile).

```azurepowershell-interactive
Get-AzTrafficManagerProfile -Name $mytrafficmanagerprofile `
-ResourceGroupName MyResourceGroup
```

Copie o valor **relativo do Nome Denas.** O nome DNS do seu perfil de Gestor de Tráfego é *http://<* nome relativo *>.trafficmanager.net*. 

### <a name="view-traffic-manager-in-action"></a>Ver o Gestor de Tráfego em ação
1. Num navegador web, insira o nome DNS do seu perfil de Gestor de Tráfego (*http://<* nome relativo *>.trafficmanager.net*) para ver o website padrão da sua Web App.

    > [!NOTE]
    > Neste cenário de arranque rápido, todos os pedidos de rota para o ponto final principal. Está definido para **a Prioridade 1**.
2. Para ver o Gestor de Tráfego falhar em ação, desative o seu site principal utilizando [Disable-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/disable-aztrafficmanagerendpoint).

   ```azurepowershell-interactive
    Disable-AzTrafficManagerEndpoint -Name $App1Name-$Location1 `
    -Type AzureEndpoints `
    -ProfileName $mytrafficmanagerprofile `
    -ResourceGroupName MyResourceGroup `
    -Force
   ```
3. Copie o nome DNS do seu perfil de Gestor de Tráfego (*http://<* nome relativo *>.trafficmanager.net*) para ver o site numa nova sessão de navegador web.
4. Verifique se a aplicação web ainda está disponível.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando terminar, elimine os grupos de recursos, aplicações web e todos os recursos relacionados usando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, criou um perfil de Gestor de Tráfego que proporciona uma elevada disponibilidade para a sua aplicação web. Para saber mais sobre o tráfego de encaminhamento, continue para os tutoriais do Traffic Manager.

> [!div class="nextstepaction"]
> [Traffic Manager tutorials](tutorial-traffic-manager-improve-website-response.md) (Tutoriais do Gestor de Tráfego)
