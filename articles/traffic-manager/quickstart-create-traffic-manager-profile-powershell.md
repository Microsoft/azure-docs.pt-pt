---
title: Quickstart:Criar um perfil para a elevada disponibilidade de aplicações - Azure PowerShell - Gestor de Tráfego Azure
description: Este artigo de arranque rápido descreve como criar um perfil de Gestor de Tráfego para construir uma aplicação web altamente disponível.
services: traffic-manager
author: rohinkoul
mnager: twooley
Customer intent: As an IT admin, I want to direct user traffic to ensure high availability of web applications.
ms.service: traffic-manager
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/04/2019
ms.author: rohink
ms.openlocfilehash: 0ab7392b4fa6e248d51392706fedaed156344a99
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/01/2020
ms.locfileid: "76934807"
---
# <a name="quickstart-create-a-traffic-manager-profile-for-a-highly-available-web-application-using-azure-powershell"></a>Quickstart: Criar um perfil de Gestor de Tráfego para uma aplicação web altamente disponível usando o Azure PowerShell

Este quickstart descreve como criar um perfil de Gestor de Tráfego que proporciona alta disponibilidade para a sua aplicação web.

Neste arranque rápido, irá criar duas instâncias de uma aplicação web. Cada um deles está a funcionar numa região de Azure diferente. Criará um perfil de Gestor de Tráfego baseado na [prioridade do ponto final.](traffic-manager-routing-methods.md#priority-traffic-routing-method) O perfil direciona o tráfego do utilizador para o site principal que executa a aplicação web. O Gestor de Tráfego monitoriza continuamente a aplicação web. Se o site principal não estiver disponível, fornece falha automática ao site de backup.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) agora.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar o PowerShell localmente, este artigo requer a versão 5.4.1 ou posterior do módulo Azure PowerShell. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-Az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.

## <a name="create-a-resource-group"></a>Criar um Grupo de Recursos
Crie um grupo de recursos utilizando o [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).

```azurepowershell-interactive


# Variables
$Location1="WestUS"

# Create a Resource Group
New-AzResourceGroup -Name MyResourceGroup -Location $Location1
```

## <a name="create-a-traffic-manager-profile"></a>Criar um perfil do Gestor de Tráfego

Crie um perfil de Gestor de Tráfego utilizando [o New-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/new-aztrafficmanagerprofile) que direciona o tráfego do utilizador com base na prioridade do ponto final.

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

## <a name="create-web-apps"></a>Criar aplicações web

Para este arranque rápido, você precisará de dois casos de uma aplicação web implantada em duas regiões azure diferentes (*Oeste dos EUA* e Leste dos *EUA).* Cada um servirá como pontos finais primários e falhados para o Gestor de Tráfego.

### <a name="create-web-app-service-plans"></a>Criar planos de serviço de aplicações web
Crie planos de serviço sinuoso utilizando [o New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) para as duas instâncias da aplicação web que irá implementar em duas regiões azure diferentes.

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
### <a name="create-a-web-app-in-the-app-service-plan"></a>Criar uma Aplicação Web no Plano de Serviço de Aplicações
Crie duas instâncias a aplicação web usando [new-AzWebApp](/powershell/module/az.websites/new-azwebapp) nos planos do Serviço de Aplicações nas regiões *Oeste dos EUA* e East *US* Azure.

```azurepowershell-interactive
$App1ResourceId=(New-AzWebApp -Name $App1Name -ResourceGroupName MyResourceGroup -Location $Location1 -AppServicePlan "$App1Name-Plan").Id
$App2ResourceId=(New-AzWebApp -Name $App2Name -ResourceGroupName MyResourceGroup -Location $Location2 -AppServicePlan "$App2Name-Plan").Id

```

## <a name="add-traffic-manager-endpoints"></a>Adicionar pontos finais do Gestor de Tráfego
Adicione as duas Aplicações Web como pontos finais do Gestor de Tráfego utilizando [o New-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/new-aztrafficmanagerendpoint) para o perfil do Gestor de Tráfego da seguinte forma:
- Adicione a Aplicação Web localizada na região *west us* azure como o principal ponto final para direcionar todo o tráfego de utilizadores. 
- Adicione a Aplicação Web localizada na região *east us* azure como o ponto final de failover. Quando o ponto final principal não está disponível, o tráfego automaticamente se encaminha para o ponto final de falha.

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

Nesta secção, você verificará o nome de domínio do seu perfil de Gestor de Tráfego. Também configurará o ponto final principal para não estar disponível. Finalmente, pode ver que a aplicação da web ainda está disponível. É porque o Gestor de Tráfego envia o tráfego para o ponto final do fracasso.

### <a name="determine-the-dns-name"></a>Determinar o nome DNS

Determine o nome DNS do perfil do Gestor de Tráfego utilizando [o Get-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/get-aztrafficmanagerprofile).

```azurepowershell-interactive
Get-AzTrafficManagerProfile -Name $mytrafficmanagerprofile `
-ResourceGroupName MyResourceGroup
```

Copie o valor **relativodnsnome.** O nome DNS do seu perfil de Traffic Manager é *http://<* relativednsname *>.trafficmanager.net*. 

### <a name="view-traffic-manager-in-action"></a>Ver o Gestor de Tráfego em ação
1. Num navegador web, introduza o nome DNS do seu perfil de Traffic Manager *(http://<* relativednsname *>.trafficmanager.net*) para ver o website predefinido da sua Web App.

    > [!NOTE]
    > Neste cenário de arranque rápido, todos os pedidos de rota para o ponto final primário. Está definido para a **Prioridade 1**.
2. Para ver o failover do Traffic Manager em ação, desative o seu site principal utilizando o [Desactivador-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/disable-aztrafficmanagerendpoint).

   ```azurepowershell-interactive
    Disable-AzTrafficManagerEndpoint -Name $App1Name-$Location1 `
    -Type AzureEndpoints `
    -ProfileName $mytrafficmanagerprofile `
    -ResourceGroupName MyResourceGroup `
    -Force
   ```
3. Copie o nome DNS do seu perfil de Traffic Manager *(http://<* relativednsname *>.trafficmanager.net*) para ver o website numa nova sessão de navegador web.
4. Verifique se a aplicação da web ainda está disponível.

## <a name="clean-up-resources"></a>Limpar recursos

Quando terminar, elimine os grupos de recursos, aplicações web e todos os recursos relacionados utilizando [o Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou um perfil de Gestor de Tráfego que proporciona uma elevada disponibilidade para a sua aplicação web. Para saber mais sobre o tráfego de encaminhamento, continue para os tutoriais do Gestor de Tráfego.

> [!div class="nextstepaction"]
> [Traffic Manager tutorials](tutorial-traffic-manager-improve-website-response.md) (Tutoriais do Gestor de Tráfego)
