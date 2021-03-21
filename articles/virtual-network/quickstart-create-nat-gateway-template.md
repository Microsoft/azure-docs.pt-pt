---
title: Criar um gateway NAT - Modelo de gestor de recursos
titleSuffix: Azure Virtual Network NAT
description: Este quickstart mostra como criar um gateway NAT utilizando o modelo Azure Resource Manager (modelo ARM).
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a NAT gateway by using an Azure Resource Manager template so that I can provide outbound connectivity for my virtual machines.
ms.service: virtual-network
ms.subservice: nat
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/27/2020
ms.author: allensu
ms.custom: subject-armqs, devx-track-azurecli
ms.openlocfilehash: 68e08b0f029e6297beee85135b4af1e4575d5470
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97703794"
---
# <a name="quickstart-create-a-nat-gateway---arm-template"></a>Quickstart: Criar um portal NAT - modelo ARM

Inicie-se com o VIRTUAL Network NAT utilizando um modelo de Gestor de Recursos Azure (modelo ARM). Este modelo implementa uma rede virtual, um recurso de gateway NAT e uma máquina virtual Ubuntu. A máquina virtual Ubuntu é implantada numa sub-rede que está associada ao recurso de gateway NAT.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se o seu ambiente cumpre os pré-requisitos e se está familiarizado com a utilização de modelos ARM, selecione o botão **Implementar no Azure**. O modelo será aberto no portal do Azure.

[![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-nat-gateway-1-vm%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="review-the-template"></a>Rever o modelo

O modelo utilizado neste início rápido pertence aos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-nat-gateway-1-vm).

Este modelo está configurado para criar um:

* Rede virtual
* Recurso do NAT gateway
* Máquina virtual Ubuntu

O Ubuntu VM é implantado numa sub-rede que está associada ao recurso de gateway NAT.

:::code language="json" source="~/quickstart-templates/101-nat-gateway-1-vm/azuredeploy.json":::

Nove recursos Azure são definidos no modelo:

* **[Microsoft.Network/networkSecurityGroups](/azure/templates/microsoft.network/networksecuritygroups)**: Cria um grupo de segurança de rede.
* **[Microsoft.Network/networkSecurityGroups/securityRules](/azure/templates/microsoft.network/networksecuritygroups/securityrules)**: Cria uma regra de segurança.
* **[Microsoft.Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)**: Cria um endereço IP público.
* **[Microsoft.Network/publicIPPrefixes](/azure/templates/microsoft.network/publicipprefixes)**: Cria um prefixo IP público.
* **[Microsoft.Compute/virtualMachines](/azure/templates/Microsoft.Compute/virtualMachines)**: Cria uma máquina virtual.
* **[Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)**: Cria uma rede virtual.
* **[Microsoft.Network/natGateways](/azure/templates/microsoft.network/natgateways)**: Cria um recurso de gateway NAT.
* **[Microsoft.Network/virtualNetworks/subnets](/azure/templates/microsoft.network/virtualnetworks/subnets)**: Cria uma sub-rede de rede virtual.
* **[Microsoft.Network/networkinterfaces](/azure/templates/microsoft.network/networkinterfaces)**: Cria uma interface de rede.

## <a name="deploy-the-template"></a>Implementar o modelo

**CLI do Azure**

```azurecli-interactive
read -p "Enter the location (i.e. westcentralus): " location
resourceGroupName="myResourceGroupNAT"
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-nat-gateway-1-vm/azuredeploy.json"

az group create \
--name $resourceGroupName \
--location $location

az deployment group create \
--resource-group $resourceGroupName \
--template-uri  $templateUri
```

**Azure PowerShell**

```azurepowershell-interactive
$location = Read-Host -Prompt "Enter the location (i.e. westcentralus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-nat-gateway-1-vm/azuredeploy.json"

$resourceGroupName = "myResourceGroupNAT"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri
```

**Portal do Azure**

[![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-nat-gateway-1-vm%2Fazuredeploy.json)

## <a name="review-deployed-resources"></a>Revisão dos recursos implantados

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Selecione **grupos** de recursos do painel esquerdo.

1. Selecione o grupo de recursos que criou na secção anterior. O nome de grupo de recursos predefinidos é **myResourceGroupNAT**

1. Verifique se foram criados os seguintes recursos no grupo de recursos:

    ![Grupo de recursos NAT de rede virtual](./media/quick-create-template/nat-gateway-template-rg.png)

## <a name="clean-up-resources"></a>Limpar os recursos

**CLI do Azure**

Quando já não é necessário, pode utilizar o comando de eliminação do [grupo az](/cli/azure/group#az-group-delete) para remover o grupo de recursos e todos os recursos contidos no seu interior.

```azurecli-interactive
  az group delete \
    --name myResourceGroupNAT
```

**Azure PowerShell**

Quando já não é necessário, pode utilizar o comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos e todos os recursos contidos no seu interior.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupNAT
```

**Portal do Azure**

Quando já não for necessário, elimine o grupo de recursos, o gateway NAT e todos os recursos relacionados. Selecione o grupo de recursos **myResourceGroupNAT** que contém o gateway NAT e, em seguida, selecione **Delete**.

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou um:

* Recurso do NAT gateway
* Rede virtual
* Máquina virtual Ubuntu

A máquina virtual é implantada numa sub-rede de rede virtual associada ao gateway NAT.

Para saber mais sobre o Virtual Network NAT e o Azure Resource Manager, continue para os artigos abaixo.

* Leia uma [visão geral da Rede Virtual NAT](nat-overview.md)
* Leia sobre o [recurso NAT Gateway](nat-gateway-resource.md)
* Saiba mais sobre o [Azure Resource Manager](../azure-resource-manager/management/overview.md)
