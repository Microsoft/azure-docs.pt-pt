---
title: Início rápido - criar um perfil do Gestor de tráfego para elevada disponibilidade de aplicações com o Azure PowerShell
description: Este artigo de início rápido descreve como criar um perfil do Gestor de tráfego para criar uma aplicação web de elevada disponibilidade.
services: traffic-manager
author: KumudD
Customer intent: As an IT admin, I want to direct user traffic to ensure high availability of web applications.
ms.service: traffic-manager
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/04/2019
ms.author: kumud
ms.openlocfilehash: 6ffecf973632911113608b7478d2af2aef036257
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57344625"
---
# <a name="quickstart-create-a-traffic-manager-profile-for-a-highly-available-web-application-using-azure-powershell"></a>Início rápido: Criar um perfil do Gestor de tráfego para uma aplicação web de elevada disponibilidade com o Azure PowerShell

Este início rápido descreve como criar um perfil do Gestor de tráfego que fornece elevada disponibilidade para a sua aplicação web.

Neste início rápido, irá criar duas instâncias de um aplicativo web. Cada uma delas está em execução numa região diferente do Azure. Irá criar um perfil do Gestor de tráfego com base na [prioridade de ponto final](traffic-manager-routing-methods.md#priority). O perfil direciona o tráfego de utilizador para o site primário que executa a aplicação web. O Gestor de tráfego monitoriza continuamente a aplicação web. Se o site primário não estiver disponível, ele fornece ativação pós-falha automática para o site de cópia de segurança.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) agora.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Se optar por instalar e utilizar o PowerShell localmente, este artigo requer a versão 5.4.1 ou posterior do módulo Azure PowerShell. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-Az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.

## <a name="create-a-resource-group"></a>Criar um Grupo de Recursos
Criar um grupo de recursos utilizando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).

```azurepowershell-interactive


# Variables
$Location1="WestUS"

# Create a Resource Group
New-AzResourceGroup -Name MyResourceGroup -Location $Location1
```

## <a name="create-a-traffic-manager-profile"></a>Criar um perfil do Gestor de Tráfego

Criar um perfil do Gestor de tráfego utilizando [New-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/new-aztrafficmanagerprofile) que direciona o tráfego de utilizador com base na prioridade de ponto final.

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

## <a name="create-web-apps"></a>Criar aplicações Web

Neste início rápido, terá duas instâncias de uma aplicação web implementada em duas regiões do Azure diferentes (*E.U.A. oeste* e *E.U.A. Leste*). Cada um irá servir como pontos finais de principal e de ativação pós-falha para o Gestor de tráfego.

### <a name="create-web-app-service-plans"></a>Criar planos de serviço de aplicações Web
Criar aplicação Web através de planos do serviço [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) para as duas instâncias da aplicação web que implantará em duas regiões do Azure diferentes.

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
### <a name="create-a-web-app-in-the-app-service-plan"></a>Criar uma aplicação Web no plano do serviço de aplicações
Criar duas instâncias da aplicação web com [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) no serviço de aplicações planos na *E.U.A. oeste* e *E.U.A. Leste* regiões do Azure.

```azurepowershell-interactive
$App1ResourceId=(New-AzWebApp -Name $App1Name -ResourceGroupName MyResourceGroup -Location $Location1 -AppServicePlan "$App1Name-Plan").Id
$App2ResourceId=(New-AzWebApp -Name $App2Name -ResourceGroupName MyResourceGroup -Location $Location2 -AppServicePlan "$App2Name-Plan").Id

```

## <a name="add-traffic-manager-endpoints"></a>Adicionar pontos finais do Gestor de Tráfego
Adicione as duas aplicações Web como pontos finais do Gestor de tráfego utilizando [New-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/new-aztrafficmanagerendpoint) para o Gestor de tráfego do perfil da seguinte forma:
- Adicionar a aplicação Web, localizado na *E.U.A. oeste* região do Azure como o ponto final primário para encaminhar todo o tráfego de utilizador. 
- Adicionar a aplicação Web, localizado na *E.U.A. Leste* região do Azure como o ponto final de ativação pós-falha. Quando o ponto final primário não estiver disponível, o tráfego encaminha automaticamente para o ponto final de ativação pós-falha.

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

Nesta secção, irá verificar o nome de domínio do perfil do Traffic Manager. Também irá configurar o ponto final principal fique indisponível. Por fim, obtém a que a aplicação web está ainda disponível. É porque o Gestor de tráfego envia o tráfego para o ponto final de ativação pós-falha.

### <a name="determine-the-dns-name"></a>Determinar o nome DNS

Determinar o nome DNS da utilização de perfil do Gestor de tráfego [Get-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/get-aztrafficmanagerprofile).

```azurepowershell-interactive
Get-AzTrafficManagerProfile -Name $mytrafficmanagerprofile `
-ResourceGroupName MyResourceGroup
```

Copiar o **RelativeDnsName** valor. É o nome DNS do seu perfil do Gestor de tráfego *http://&lt*relativednsname *>. trafficmanager.net*. 

### <a name="view-traffic-manager-in-action"></a>Ver o Gestor de Tráfego em ação
1. Num browser, introduza o nome DNS do seu perfil do Gestor de tráfego (*http://&lt*relativednsname *>. trafficmanager.net*) para ver o Web site predefinido da sua aplicação Web.

    > [!NOTE]
    > Neste cenário de início rápido, todos os pedidos de rota para o ponto final primário. Ele é definido como **prioridade 1**.
2. Para ver a ativação pós-falha do Gestor de tráfego em ação, desative a seu site primário a utilizar [Disable-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/disable-aztrafficmanagerendpoint).

   ```azurepowershell-interactive
    Disable-AzTrafficManagerEndpoint -Name $App1Name-$Location1 `
    -Type AzureEndpoints `
    -ProfileName $mytrafficmanagerprofile `
    -ResourceGroupName MyResourceGroup `
    -Force
   ```
3. Copie o nome DNS do seu perfil do Gestor de tráfego (*http://&lt*relativednsname *>. trafficmanager.net*) para ver o site numa nova sessão de browser web.
4. Certifique-se de que a aplicação web está ainda disponível.

## <a name="clean-up-resources"></a>Limpar recursos

Quando tiver terminado, elimine a grupos de recursos, aplicativos web e todos os recursos relacionados usando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, criou um perfil do Gestor de tráfego que fornece elevada disponibilidade para a sua aplicação web. Para saber mais sobre o encaminhamento de tráfego, avance para os tutoriais do Gestor de tráfego.

> [!div class="nextstepaction"]
> [Traffic Manager tutorials](tutorial-traffic-manager-improve-website-response.md) (Tutoriais do Gestor de Tráfego)