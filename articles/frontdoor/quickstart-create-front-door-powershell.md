---
title: 'Quickstart: Configurar alta disponibilidade com Azure Front Door - Azure PowerShell'
description: Este quickstart irá mostrar-lhe como usar a Porta Frontal Azure para criar uma aplicação web global de alta disponibilidade e alto desempenho usando a Azure PowerShell.
services: front-door
documentationcenter: na
author: duongau
ms.author: duau
manager: KumudD
ms.date: 09/21/2020
ms.topic: quickstart
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom:
- mode-api
ms.openlocfilehash: cd439a5931340f56401e5f6ba7a4e09f35ab7c7d
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107539044"
---
# <a name="quickstart-create-a-front-door-for-a-highly-available-global-web-application-using-azure-powershell"></a>Quickstart: Criar uma porta frontal para uma aplicação web global altamente disponível usando Azure PowerShell

Começa com a Azure Front Door usando o Azure PowerShell para criar uma aplicação web global altamente disponível e de alto desempenho.

A Porta da Frente direciona o tráfego web para recursos específicos numa piscina de backend. Definiu o domínio frontend, adicionou recursos a uma piscina de backend e criou uma regra de encaminhamento. Este artigo utiliza uma configuração simples de um pool de backend com dois recursos de aplicações web e uma regra de encaminhamento único usando o caminho padrão correspondente "/*".

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Azure PowerShell instalado localmente ou Azure Cloud Shell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-resource-group"></a>Criar grupo de recursos

Em Azure, aloca recursos relacionados a um grupo de recursos. Pode utilizar um grupo de recursos existente ou criar um novo.

Criar um grupo de recursos com [o New-AzResourceGroup:](/powershell/module/az.resources/new-azresourcegroup)

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupFD -Location centralus
```

## <a name="create-two-instances-of-a-web-app"></a>Criar duas instâncias de uma aplicação web

Este quickstart requer duas instâncias de uma aplicação web que funciona em diferentes regiões do Azure. Ambas as instâncias de aplicação web são executadas no modo Ative/Ative, para que qualquer um possa tomar o tráfego. Esta configuração difere de uma configuração Ative/Stand-By, onde se atua como um failover.

Se ainda não tiver uma aplicação web, use o seguinte script para configurar duas aplicações web de exemplo.

```azurepowershell-interactive
# Create first web app in Central US region.
$webapp1 = New-AzWebApp `
-Name "WebAppContoso-$(Get-Random)" `
-Location centralus `
-ResourceGroupName myResourceGroupFD `
-AppServicePlan myAppServicePlanCentralUS

# Create second web app in South Central US region.
$webapp2 = New-AzWebApp `
-Name "WebAppContoso-$(Get-Random)" `
-Location southcentralus `
-ResourceGroupName myResourceGroupFD `
-AppServicePlan myAppServicePlanSouthCentralUS
```

## <a name="create-a-front-door"></a>Criar uma Porta de Entrada

Esta secção detalha como pode criar e configurar os seguintes componentes da Porta da Frente:
    
* Um objeto frontal contém o domínio padrão da porta frontal.
* Um pool de backend é um conjunto de backends equivalentes aos quais a carga da Porta Frontal equilibra o seu pedido de cliente.
* Uma regra de encaminhamento mapeia o seu anfitrião frontend e o padrão de caminho URL correspondente a uma piscina de backend específica.

### <a name="create-a-frontend-object"></a>Criar um objeto frontal

O objeto frontal configura o nome de anfitrião para a porta da frente. Por predefinição, o nome de anfitrião terá um sufixo de **.azurefd.net*.

```azurepowershell-interactive
# Create a unique name
$fdname = "contoso-frontend-$(Get-Random)"

#Create the frontend object
$FrontendEndObject = New-AzFrontDoorFrontendEndpointObject `
-Name "frontendEndpoint1" `
-HostName $fdname".azurefd.net"
```

### <a name="create-the-backend-pool"></a>Criar o conjunto de back-end

O pool backend consiste nas duas aplicações web criadas no início deste quickstart. A sonda de saúde e as definições de equilíbrio de carga definidas neste passo utilizam valores predefinidos.

```azurepowershell-interactive
# Create backend objects that points to the hostname of the web apps
$backendObject1 = New-AzFrontDoorBackendObject `
-Address $webapp1.DefaultHostName
$backendObject2 = New-AzFrontDoorBackendObject `
-Address $webapp2.DefaultHostName

# Create a health probe object
$HealthProbeObject = New-AzFrontDoorHealthProbeSettingObject `
-Name "HealthProbeSetting"

# Create the load balancing setting object
$LoadBalancingSettingObject = New-AzFrontDoorLoadBalancingSettingObject `
-Name "Loadbalancingsetting" `
-SampleSize "4" `
-SuccessfulSamplesRequired "2" `
-AdditionalLatencyInMilliseconds "0"

# Create a backend pool using the backend objects, health probe, and load balancing settings
$BackendPoolObject = New-AzFrontDoorBackendPoolObject `
-Name "myBackendPool" `
-FrontDoorName $fdname `
-ResourceGroupName myResourceGroupFD `
-Backend $backendObject1,$backendObject2 `
-HealthProbeSettingsName "HealthProbeSetting" `
-LoadBalancingSettingsName "Loadbalancingsetting"
```

### <a name="create-a-routing-rule"></a>Criar uma regra de encaminhamento

A regra de encaminhamento mapeia a piscina de backend para o domínio frontend e define o valor padrão de correspondência do caminho para "/*".

```azurepowershell-interactive
# Create a routing rule mapping the frontend host to the backend pool
$RoutingRuleObject = New-AzFrontDoorRoutingRuleObject `
-Name LocationRule `
-FrontDoorName $fdname `
-ResourceGroupName myResourceGroupFD `
-FrontendEndpointName "frontendEndpoint1" `
-BackendPoolName "myBackendPool" `
-PatternToMatch "/*"
```
### <a name="create-the-front-door"></a>Criar a Porta da Frente

Agora que criou os objetos necessários, crie a Porta da Frente:

```azurepowershell-interactive
# Creates the Front Door
New-AzFrontDoor `
-Name $fdname `
-ResourceGroupName myResourceGroupFD `
-RoutingRule $RoutingRuleObject `
-BackendPool $BackendPoolObject `
-FrontendEndpoint $FrontendEndObject `
-LoadBalancingSetting $LoadBalancingSettingObject `
-HealthProbeSetting $HealthProbeObject
```

Uma vez que a colocação tenha sucesso, pode testá-la seguindo os passos na secção seguinte.

## <a name="test-the-front-door"></a>Teste a porta da frente

Executar os comandos seguintes para obter o nome de anfitrião para a Porta da Frente.

```azurepowershell-interactive
# Gets Front Door in resource group and output the hostname of the frontend domain.
$fd = Get-AzFrontDoor -ResourceGroupName myResourceGroupFD
$fd.FrontendEndpoints[0].Hostname
```

Abra um navegador web e introduza o nome anfitrião obtido a partir dos comandos. A Porta da Frente irá direcionar o seu pedido para um dos recursos de backend. 

:::image type="content" source="./media/quickstart-create-front-door-powershell/front-door-test-page.png" alt-text="Página de teste da porta da frente":::

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não precisar dos recursos que criou com a Porta da Frente, elimine o grupo de recursos. Quando elimina o grupo de recursos, também elimina a Porta frontal e todos os seus recursos relacionados. 

Para eliminar o grupo de recursos, ligue para o `Remove-AzResourceGroup` cmdlet:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupFD
```

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou um:
* Front Door
* Duas aplicações web

Para aprender a adicionar um domínio personalizado à sua Porta da Frente, continue para os tutoriais da Porta da Frente.

> [!div class="nextstepaction"]
> [Adicionar um domínio personalizado](front-door-custom-domain.md)
