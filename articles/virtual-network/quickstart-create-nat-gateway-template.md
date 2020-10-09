---
title: Criar um gateway NAT - Modelo de gestor de recursos
titleSuffix: Azure Virtual Network NAT
description: Este quickstart mostra como criar uma porta de entrada NAT utilizando o modelo Azure Resource Manager.
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
ms.date: 03/09/2020
ms.author: allensu
ms.custom: subject-armqs
ms.openlocfilehash: fc4804070e0fa4ca6e9e54dcf6e04aafcc17f91a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88053903"
---
# <a name="create-a-nat-gateway---resource-manager-template"></a>Criar um gateway NAT - Modelo de Gestor de Recursos

Inicie-se com o VIRTUAL Network NAT utilizando um modelo de gestor de recursos Azure.  Este modelo implementa uma rede virtual, um recurso de gateway NAT e uma máquina virtual Ubuntu. A máquina virtual Ubuntu é implantada numa sub-rede que está associada ao recurso de gateway NAT.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-a-nat-gateway-and-supporting-resources"></a>Criar uma porta de entrada NAT e recursos de apoio

Este modelo está configurado para criar um 

* Rede virtual 
* Recurso do NAT gateway
* Máquina virtual Ubuntu

O Ubuntu VM é implantado numa sub-rede que está associada ao recurso de gateway NAT.

### <a name="review-the-template"></a>Rever o modelo

O modelo usado neste quickstart é de [modelos Azure Quickstart](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-nat-gateway-1-vm/azuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-nat-gateway-1-vm/azuredeploy.json" range="1-335" highlight="256-282":::

Nove recursos Azure são definidos no modelo:

**Microsoft.Network**

* **[Microsoft.Network/natGateways](https://docs.microsoft.com/azure/templates/microsoft.network/natgateways)**: Cria um recurso de gateway NAT.

* **[Microsoft.Network/networkSecurityGroups](https://docs.microsoft.com/azure/templates/microsoft.network/networksecuritygroups)**: Cria um grupo de segurança de rede.

    * **[Microsoft.Network/networkSecurityGroups/securityRules](https://docs.microsoft.com/azure/templates/microsoft.network/networksecuritygroups/securityrules)**: Cria uma regra de segurança.

* **[Microsoft.Network/publicIPAddresses](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses)**: Cria um endereço IP público.

* **[Microsoft.Network/publicIPPrefixes](https://docs.microsoft.com/azure/templates/microsoft.network/publicipprefixes)**: Cria um prefixo IP público.

* **[Microsoft.Network/virtualNetworks](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks)**: Cria uma rede virtual.

    * **[Microsoft.Network/virtualNetworks/subnets](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks/subnets)**: Cria uma sub-rede de rede virtual.

* **[Microsoft.Network/networkinterfaces](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces)**: Cria uma interface de rede.

**Microsoft.Compute**

* **[Microsoft.Compute/virtualMachines](https://docs.microsoft.com/azure/templates/Microsoft.Compute/virtualMachines)**: Cria uma máquina virtual.

### <a name="deploy-the-template"></a>Implementar o modelo

**CLI do Azure**

```azurecli-interactive
read -p "Enter the location (i.e. westcentralus): " location
resourceGroupName="myResourceGroupNAT"
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-nat-gateway-1-vm/azuredeploy.json" 

az group create \
--name $resourceGroupName \
--location $location

az group deployment create \
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

2. Selecione **grupos** de recursos do painel esquerdo.

3. Selecione o grupo de recursos que criou na secção anterior. O nome de grupo de recursos predefinidos é **myResourceGroupNAT**

4. Verifique se foram criados os seguintes recursos no grupo de recursos:

    ![Grupo de recursos NAT de rede virtual](./media/quick-create-template/nat-gateway-template-rg.png)

## <a name="clean-up-resources"></a>Limpar os recursos

**CLI do Azure**

Quando já não é necessário, pode utilizar o comando de eliminação do [grupo az](/cli/azure/group#az-group-delete) para remover o grupo de recursos e todos os recursos contidos no seu interior.

```azurecli-interactive 
  az group delete \
    --name myResourceGroupNAT
```

**Azure PowerShell**

Quando já não é necessário, pode utilizar o comando [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=latest) para remover o grupo de recursos e todos os recursos contidos no seu interior.

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
