---
title: Crie um gestor de tráfego utilizando o modelo de gestor de recursos Azure (modelo ARM)
description: Aprenda a criar um perfil de Gestor de Tráfego Azure utilizando o modelo de Gestor de Recursos Azure (modelo ARM).
services: traffic-manager
author: duongau
ms.service: traffic-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: duau
ms.date: 08/24/2020
ms.openlocfilehash: bc7cc3220f228a4c8f15370b7b0fcde56db8186d
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/01/2020
ms.locfileid: "89237299"
---
# <a name="quickstart-create-a-traffic-manager-profile-using-an-arm-template"></a>Quickstart: Criar um perfil de Gestor de Tráfego usando um modelo ARM

Este quickstart descreve como usar um modelo de Gestor de Recursos Azure (Modelo ARM) para criar um perfil de Gestor de Tráfego com pontos finais externos utilizando o método de encaminhamento de desempenho.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se o seu ambiente cumpre os pré-requisitos e se está familiarizado com a utilização de modelos ARM, selecione o botão **Implementar no Azure**. O modelo será aberto no portal do Azure.

[![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-traffic-manager-external-endpoint%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="review-the-template"></a>Rever o modelo

O modelo utilizado neste início rápido pertence aos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-traffic-manager-external-endpoint).

:::code language="json" source="~/quickstart-templates/101-traffic-manager-external-endpoint/azuredeploy.json":::

No modelo, está definido um recurso do Azure:

* [**Microsoft.Network/trafficManagerProfiles**](/azure/templates/microsoft.network/trafficmanagerprofiles)

Para encontrar mais modelos relacionados com O Gestor de Tráfego Azure, consulte [Azure Quickstart Templates](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Implementar o modelo

1. Selecione **Experimentá-lo** a partir do bloco de código que se segue para abrir a Azure Cloud Shell e, em seguida, siga as instruções para iniciar súm no Azure. 

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-traffic-manager-external-endpoint/azuredeploy.json"

    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

    Read-Host -Prompt "Press [ENTER] to continue ..."
    ```

    Espere até ver o pedido da consola.

1. Selecione **Copiar** do bloco de código anterior para copiar o script PowerShell.

1. Clique com o botão direito da placa de consola da concha e, em seguida, **selecione Pasta**.

1. Insira os valores.

    A implementação do modelo cria um perfil com dois pontos finais externos. **O Endpoint1** utiliza um ponto final-alvo de *w<span>ww.microsoft.com</span>* com a localização no **Norte da Europa**. **O Ponto Final2** utiliza um ponto final de destino d *<span>ocs.microsoft.com</span>* com a localização em **South Central US**. 

    O nome do grupo de recursos é o nome do projeto com **rg** anexado.

    > [!NOTE]
    > **o nome uniqueDNS tem** de ser um nome globalmente único para que o modelo se implemente com sucesso. Se a implantação falhar, comece de novo com o Passo 1.

    Leva alguns minutos para implementar o modelo. Quando concluída, a saída é semelhante a:

    :::image type="content" source="./media/quickstart-create-traffic-manager-profile/traffic-manager-arm-powershell-output.png" alt-text="Modelo de implementação do Gestor de Recursos do Gestor de Tráfego Azure":::

Azure PowerShell é usado para implementar o modelo. Além do Azure PowerShell, também pode utilizar o portal Azure CLI e a REST API. Para aprender outros métodos de implementação, consulte [os modelos de implementação](../azure-resource-manager/templates/deploy-portal.md).

## <a name="validate-the-deployment"></a>Validar a implementação

1. Determine o nome DNS do perfil do Gestor de Tráfego utilizando [o Get-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/get-aztrafficmanagerprofile).

    ```azurepowershell-interactive
    Get-AzTrafficManagerProfile -Name ExternalEndpointExample -ResourceGroupName $resourceGroupName | Select RelativeDnsName
    ```

    Copie o valor **relativo do Nome Denas.** O nome DNS do seu perfil de Gestor de Tráfego é relativo nome *<* *>.trafficmanager.net*. 

1. A partir de um powershell local executar o seguinte comando substituindo a variável **{relativeDNSname}** por *<* nome relativo *>.trafficmanager.net*.

    ```powershell
    Resolve-DnsName -Name {relativeDNSname} | Select-Object NameHost | Select -First 1
    ```
    Você deve obter um NameHost de *w<span>ww.microsoft.com</span>* ou *d<span>ocs.microsoft.com</span>* dependendo da região mais próxima de você.

1. Para verificar se consegue resolver o outro ponto final, desative o ponto final para o alvo que obteve no último passo. Substitua o **{endpointName}** por **um ponto final1** ou **ponto final2** para desativar o alvo para *w<span>ww.microsoft.com</span>* ou *d<span>ocs.microsoft</span>.com* respectivamente.

    ```azurepowershell-interactive
    Disable-AzTrafficManagerEndpoint -Name {endpointName} -Type ExternalEndpoints -ProfileName ExternalEndpointExample -ResourceGroupName $resourceGroupName -Force
    ```
1. Executar o comando do Passo 2 novamente em um PowerShell local. Desta vez, deves arranjar o outro NameHost para o outro ponto final. 

## <a name="clean-up-resources"></a>Limpar os recursos

Quando terminar, elimine os grupos de recursos, aplicações web e todos os recursos relacionados usando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou um:
* Perfil do Gestor de Tráfego

Para saber mais sobre o tráfego de encaminhamento, continue para os tutoriais do Traffic Manager.

> [!div class="nextstepaction"]
> [Traffic Manager tutorials](tutorial-traffic-manager-improve-website-response.md) (Tutoriais do Gestor de Tráfego)