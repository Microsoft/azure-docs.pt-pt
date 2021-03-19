---
title: Quickstart - Crie uma balança de máquina virtual do Windows definida com um modelo Azure
description: Saiba como criar rapidamente um dimensionamento de máquinas virtuais Windows com um modelo do Azure Resource Manager que implementa uma aplicação de exemplo e configura regras de dimensionamento automático
author: ju-shim
ms.author: jushiman
ms.topic: quickstart
ms.service: virtual-machine-scale-sets
ms.subservice: windows
ms.date: 03/27/2020
ms.reviewer: mimckitt
ms.custom: mimckitt, subject-armqs
ms.openlocfilehash: 03863171cb05c0557611fb8cd3c0ade667e7d79e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "88650082"
---
# <a name="quickstart-create-a-windows-virtual-machine-scale-set-with-an-arm-template"></a>Quickstart: Criar uma balança de máquina virtual do Windows definida com um modelo ARM

Um conjunto de escala de máquina virtual permite-lhe implantar e gerir um conjunto de máquinas virtuais de escala automática. Pode dimensionar o número de VMs no conjunto de dimensionamento manualmente ou definir regras para dimensionar automaticamente com base na utilização de recursos como CPU, exigência de memória ou tráfego de rede. Em seguida, um balanceador de carga do Azure distribui o tráfego pelas instâncias de VM no conjunto de dimensionamento. Neste arranque rápido, você cria um conjunto de escala de máquina virtual e implementa uma aplicação de amostra com um modelo Azure Resource Manager (modelo ARM).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Os modelos ARM permitem-lhe implantar grupos de recursos relacionados. Num único modelo, pode criar o conjunto de dimensionamento de máquinas virtuais, instalar aplicações e configurar regras de dimensionamento automático. Com a utilização de parâmetros e variáveis, este modelo pode ser reutilizado para atualizar conjuntos de dimensionamento existentes ou criar conjuntos de dimensionamento adicionais. Pode implementar modelos através do portal Azure, Azure CLI, Azure PowerShell ou de gasodutos de integração contínua/entrega contínua (CI/CD).

Se o seu ambiente cumpre os pré-requisitos e se está familiarizado com a utilização de modelos ARM, selecione o botão **Implementar no Azure**. O modelo será aberto no portal do Azure.

[![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-vmss-windows-webapp-dsc-autoscale%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="review-the-template"></a>Rever o modelo

O modelo utilizado neste início rápido pertence aos [modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/201-vmss-windows-webapp-dsc-autoscale/).

:::code language="json" source="~/quickstart-templates/201-vmss-windows-webapp-dsc-autoscale/azuredeploy.json":::

Estes recursos são definidos nestes modelos:

- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft.Network/loadBalancers**](/azure/templates/microsoft.network/loadbalancers)
- [**Microsoft.Compute/virtualMachineScaleSets**](/azure/templates/microsoft.compute/virtualmachinescalesets)
- [**Microsoft.Insights/AutoscaleSettings**](/azure/templates/microsoft.insights/autoscalesettings)

### <a name="define-a-scale-set"></a>Definir um conjunto de dimensionamento

Para criar um dimensionamento com um modelo, tem de definir os recursos adequados. As partes principais do tipo de recurso de conjunto de dimensionamento de máquinas virtuais são:

| Propriedade                     | Descrição da propriedade                                  | Valor de modelo de exemplo                    |
|------------------------------|----------------------------------------------------------|-------------------------------------------|
| tipo                         | Tipo de recurso do Azure a criar                            | Microsoft.Compute/virtualMachineScaleSets |
| name                         | O nome do conjunto de dimensionamento                                       | myScaleSet                                |
| localização                     | A localização para criar o conjunto de dimensionamento                     | E.U.A. Leste                                   |
| sku.name                     | O tamanho da VM para cada instância do conjunto de dimensionamento                  | Standard_A1                               |
| sku.capacity                 | O número de instâncias de VM a criar inicialmente           | 2                                         |
| upgradePolicy.mode           | Modo de atualização de instâncias de VM quando ocorrem alterações              | Automático                                 |
| imageReference               | A plataforma ou imagem personalizada a utilizar para as instâncias de VM | Microsoft Windows Server 2016 Datacenter  |
| osProfile.computerNamePrefix | O prefixo de nome para cada instância de VM                     | myvmss                                    |
| osProfile.adminUsername      | O nome de utilizador para cada instância de VM                        | azureuser                                 |
| osProfile.adminPassword      | A palavra-passe para cada instância de VM                        | P@ssw0rd!                                 |

Para personalizar um modelo de conjunto de escala, pode alterar o tamanho do VM ou a capacidade inicial. Outra opção é usar uma plataforma diferente ou uma imagem personalizada.

### <a name="add-a-sample-application"></a>Adicionar uma aplicação de exemplo

Para testar o conjunto de dimensionamento, instale uma aplicação Web básica. Quando implementa um conjunto de dimensionamento, as extensões de VM podem fornecer tarefas de automatização e configuração pós-implementação, tais como a instalação de uma aplicação. Os scripts podem ser transferidos a partir do armazenamento do Azure ou do GitHub, ou fornecidos para o portal do Azure no runtime da extensão. Para aplicar uma extensão ao conjunto de dimensionamento, adicione a secção *extensionProfile* ao exemplo de recurso anterior. Normalmente, o perfil de extensão define as seguintes propriedades:

- Tipo de extensão
- Publicador da extensão
- Versão da extensão
- Localização dos scripts de configuração ou instalação
- Comandos para executar nas instâncias de VM

O modelo utiliza a extensão DSC powerShell para instalar uma aplicação MVC ASP.NET que funciona no IIS.

Um script de instalação é transferido a partir do GitHub, tal como definido em *url*. Em seguida, a extensão executa *InstallIIS* a partir do script *IISInstall.ps1*, tal como definido em *função* e *Script*. A aplicação ASP.NET propriamente dita é fornecida como um pacote do Web Deploy, que também é transferido a partir do GitHub, tal como definido em *WebDeployPackagePath*:

## <a name="deploy-the-template"></a>Implementar o modelo

Pode implantar o modelo selecionando o botão **Implementar para Azure.** Este botão abre o portal do Azure, carrega o modelo completo e pede-lhe alguns parâmetros, tais como um nome de conjunto de dimensionamento, uma contagem de instâncias e as credenciais de administrador.

[![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-vmss-windows-webapp-dsc-autoscale%2Fazuredeploy.json)

Também pode implementar um modelo de Gestor de Recursos utilizando a Azure PowerShell:

```azurepowershell-interactive
# Create a resource group
New-AzResourceGroup -Name myResourceGroup -Location EastUS

# Deploy template into resource group
New-AzResourceGroupDeployment `
    -ResourceGroupName myResourceGroup `
    -TemplateURI https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-windows-webapp-dsc-autoscale/azuredeploy.json

# Update the scale set and apply the extension
Update-AzVmss `
    -ResourceGroupName myResourceGroup `
    -VmScaleSetName myVMSS `
    -VirtualMachineScaleSet $vmssConfig
```

Responda aos pedidos para fornecer um nome do conjunto de dimensionamento e as credenciais de administrador para as instâncias de VM. Pode demorar 10 a 15 minutos para que a escala definida seja criada e aplique a extensão para configurar a aplicação.

## <a name="validate-the-deployment"></a>Validar a implementação

Para ver o conjunto de dimensionamento em ação, aceda à aplicação Web de exemplo num browser. Obtenha o endereço IP público do seu balanceador de carga com [o Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) da seguinte forma:

```azurepowershell-interactive
Get-AzPublicIpAddress -ResourceGroupName myResourceGroup | Select IpAddress
```

Introduza o endereço IP público do balançador de carga num navegador web no formato *http: \/ /publicIpAddress/MyApp*. O balanceador de carga distribui o tráfego para uma das suas instâncias de VM, conforme mostra o exemplo seguinte:

![Site do IIS em execução](./media/virtual-machine-scale-sets-create-powershell/running-iis-site.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não é necessário, pode utilizar o [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos, conjunto de escala. O parâmetro `-Force` confirma que pretende eliminar os recursos sem uma linha de comandos adicional para fazê-lo. O parâmetro `-AsJob` devolve o controlo à linha de comandos, sem aguardar a conclusão da operação.

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup" -Force -AsJob
```

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou um conjunto de escala do Windows com um modelo ARM e utilizou a extensão DSC powerShell para instalar uma aplicação básica ASP.NET nas instâncias VM. Para obter mais informações, avance para o tutorial para saber como criar e gerir conjuntos de dimensionamento de máquinas virtuais do Azure.

> [!div class="nextstepaction"]
> [Criar e gerir conjuntos de dimensionamento de máquinas virtuais do Azure](tutorial-create-and-manage-powershell.md)
