---
title: 'Início rápido: criar um perfil para alta disponibilidade de aplicativos-Azure PowerShell-Gerenciador de tráfego do Azure'
description: Este artigo de início rápido descreve como criar um perfil do Gerenciador de tráfego para criar um aplicativo Web altamente disponível.
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
# <a name="quickstart-create-a-traffic-manager-profile-for-a-highly-available-web-application-using-azure-powershell"></a>Início rápido: criar um perfil do Gerenciador de tráfego para um aplicativo Web altamente disponível usando Azure PowerShell

Este guia de início rápido descreve como criar um perfil do Gerenciador de tráfego que fornece alta disponibilidade para seu aplicativo Web.

Neste guia de início rápido, você criará duas instâncias de um aplicativo Web. Cada um deles está sendo executado em uma região diferente do Azure. Você criará um perfil do Gerenciador de tráfego com base na [prioridade do ponto de extremidade](traffic-manager-routing-methods.md#priority-traffic-routing-method). O perfil direciona o tráfego do usuário para o site primário que está executando o aplicativo Web. O Gerenciador de tráfego monitora continuamente o aplicativo Web. Se o site primário não estiver disponível, ele fornecerá o failover automático para o site de backup.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) agora.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar o PowerShell localmente, este artigo requer a versão 5.4.1 ou posterior do módulo Azure PowerShell. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-Az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.

## <a name="create-a-resource-group"></a>Criar um Grupo de Recursos
Crie um grupo de recursos usando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).

```azurepowershell-interactive


# Variables
$Location1="WestUS"

# Create a Resource Group
New-AzResourceGroup -Name MyResourceGroup -Location $Location1
```

## <a name="create-a-traffic-manager-profile"></a>Criar um perfil do Gestor de Tráfego

Crie um perfil do Gerenciador de tráfego usando [New-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/new-aztrafficmanagerprofile) que direciona o tráfego do usuário com base na prioridade do ponto de extremidade.

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

## <a name="create-web-apps"></a>Criar aplicativos Web

Para este guia de início rápido, você precisará de duas instâncias de um aplicativo Web implantado em duas regiões diferentes do Azure (*oeste dos EUA* e *leste dos EUA*). Cada um servirá como pontos de extremidade primários e de failover para o Gerenciador de tráfego.

### <a name="create-web-app-service-plans"></a>Criar planos do serviço de aplicativo Web
Crie planos de serviço de aplicativo Web usando [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) para as duas instâncias do aplicativo Web que serão implantadas em duas regiões diferentes do Azure.

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
### <a name="create-a-web-app-in-the-app-service-plan"></a>Criar um aplicativo Web no plano do serviço de aplicativo
Crie duas instâncias do aplicativo Web usando [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) nos planos do serviço de aplicativo nas regiões *oeste dos EUA* e *leste dos EUA* do Azure.

```azurepowershell-interactive
$App1ResourceId=(New-AzWebApp -Name $App1Name -ResourceGroupName MyResourceGroup -Location $Location1 -AppServicePlan "$App1Name-Plan").Id
$App2ResourceId=(New-AzWebApp -Name $App2Name -ResourceGroupName MyResourceGroup -Location $Location2 -AppServicePlan "$App2Name-Plan").Id

```

## <a name="add-traffic-manager-endpoints"></a>Adicionar pontos finais do Gestor de Tráfego
Adicione os dois aplicativos Web como pontos de extremidade do Gerenciador de tráfego usando [New-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/new-aztrafficmanagerendpoint) ao perfil do Gerenciador de tráfego da seguinte maneira:
- Adicione o aplicativo Web localizado na região *oeste dos EUA* do Azure como o ponto de extremidade primário para rotear todo o tráfego do usuário. 
- Adicione o aplicativo Web localizado na região *leste dos EUA* do Azure como o ponto de extremidade de failover. Quando o ponto de extremidade primário não está disponível, o tráfego roteia automaticamente para o ponto de extremidade de failover.

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

Nesta seção, você verificará o nome de domínio do seu perfil do Gerenciador de tráfego. Você também configurará o ponto de extremidade primário como indisponível. Por fim, você verá que o aplicativo Web ainda está disponível. É porque o Traffic Manager envia o tráfego para o ponto de extremidade de failover.

### <a name="determine-the-dns-name"></a>Determinar o nome DNS

Determine o nome DNS do perfil do Gerenciador de tráfego usando [Get-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/get-aztrafficmanagerprofile).

```azurepowershell-interactive
Get-AzTrafficManagerProfile -Name $mytrafficmanagerprofile `
-ResourceGroupName MyResourceGroup
```

Copie o valor **RelativeDnsName** . O nome DNS do seu perfil do Gerenciador de tráfego é *http://<* relativednsname *>. trafficmanager. net*. 

### <a name="view-traffic-manager-in-action"></a>Ver o Gestor de Tráfego em ação
1. Em um navegador da Web, insira o nome DNS do seu perfil do Gerenciador de tráfego (*http://<* relativednsname *>. trafficmanager. net*) para exibir o site padrão do seu aplicativo Web.

    > [!NOTE]
    > Neste cenário de início rápido, todas as solicitações são roteadas para o ponto de extremidade primário. Ele é definido como **prioridade 1**.
2. Para exibir o failover do Gerenciador de tráfego em ação, desabilite o site primário usando [Disable-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/disable-aztrafficmanagerendpoint).

   ```azurepowershell-interactive
    Disable-AzTrafficManagerEndpoint -Name $App1Name-$Location1 `
    -Type AzureEndpoints `
    -ProfileName $mytrafficmanagerprofile `
    -ResourceGroupName MyResourceGroup `
    -Force
   ```
3. Copie o nome DNS do seu perfil do Gerenciador de tráfego (*http://<* relativednsname *>. trafficmanager. net*) para exibir o site em uma nova sessão do navegador da Web.
4. Verifique se o aplicativo Web ainda está disponível.

## <a name="clean-up-resources"></a>Limpar recursos

Quando terminar, exclua os grupos de recursos, aplicativos Web e todos os recursos relacionados usando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você criou um perfil do Gerenciador de tráfego que fornece alta disponibilidade para seu aplicativo Web. Para saber mais sobre o tráfego de roteamento, continue para os tutoriais do Gerenciador de tráfego.

> [!div class="nextstepaction"]
> [Traffic Manager tutorials](tutorial-traffic-manager-improve-website-response.md) (Tutoriais do Gestor de Tráfego)
