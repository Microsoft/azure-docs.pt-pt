---
title: 'Quickstart: Criar um equilibrador de carga interno usando um modelo'
description: Este quickstart mostra como criar um equilibrador de carga Azure interno utilizando um modelo de Gestor de Recursos Azure (modelo ARM).
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: quickstart
ms.custom: subject-armqs, devx-track-azurecli
ms.author: allensu
ms.date: 09/14/2020
ms.openlocfilehash: 749b233b827c27d2c998cfd6be66cf79cf48089d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94831655"
---
# <a name="quickstart-create-an-internal-load-balancer-to-load-balance-vms-by-using-an-arm-template"></a>Quickstart: Criar um equilibrador de carga interno para carregar VMs de equilíbrio utilizando um modelo ARM

Este quickstart descreve como usar um modelo de Gestor de Recursos Azure (modelo ARM) para criar um equilibrador de carga Azure interno.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se o seu ambiente cumpre os pré-requisitos e se está familiarizado com a utilização de modelos ARM, selecione o botão **Implementar no Azure**. O modelo será aberto no portal do Azure.

[![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-2-vms-internal-load-balancer%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="review-the-template"></a>Rever o modelo

O modelo utilizado neste arranque rápido é dos [modelos Azure Quickstart](https://azure.microsoft.com/resources/templates/201-2-vms-internal-load-balancer).

:::code language="json" source="~/quickstart-templates/201-2-vms-internal-load-balancer/azuredeploy.json":::

Vários recursos Azure foram definidos no modelo:

- [**Microsoft.Storage/storageAstas:**](/azure/templates/microsoft.storage/storageaccounts)Contas de armazenamento de máquinas virtuais para diagnósticos de arranque.
- [**Microsoft.Compute/availabilitySets**](/azure/templates/microsoft.compute/availabilitySets): Disponibilidade definida para máquinas virtuais.
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualNetworks): Rede virtual para equilibrador de carga e máquinas virtuais.
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkInterfaces): Interfaces de rede para máquinas virtuais.
- [**Microsoft.Network/loadBalancers**](/azure/templates/microsoft.network/loadBalancers): Balanceador de carga interno.
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualMachines): Máquinas virtuais.

Para encontrar mais modelos relacionados com o Azure Load Balancer, consulte [os modelos Azure Quickstart](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Implementar o modelo

**CLI do Azure**

```azurecli-interactive
read -p "Enter the location (i.e. westcentralus): " location
resourceGroupName="myResourceGroupLB"
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-internal-load-balancer/azuredeploy.json"

az group create \
--name $resourceGroupName \
--location $location

az deployment group create \
--resource-group $resourceGroupName \
--template-uri  $templateUri
```

## <a name="review-deployed-resources"></a>Revisão dos recursos implantados

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Selecione **grupos** de recursos do painel esquerdo.

1. Selecione o grupo de recursos que criou na secção anterior. O nome de grupo de recursos predefinidos é **myResourceGroupLB**

1. Verifique se foram criados os seguintes recursos no grupo de recursos:

:::image type="content" source="media/quickstart-load-balancer-standard-internal-template/verify-creation.png" alt-text="Portal Azure do utilizador para verificar a criação de recursos." border="true":::

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não é necessário, pode utilizar o comando de eliminação do [grupo az](/cli/azure/group#az-group-delete) para remover o grupo de recursos e todos os recursos contidos no seu interior.

```azurecli-interactive
  az group delete \
    --name myResourceGroupLB
```

## <a name="next-steps"></a>Passos seguintes

Para um tutorial passo a passo que o guia através do processo de criação de um modelo, consulte:

> [!div class="nextstepaction"]
> [Tutorial: Crie e implemente o seu primeiro modelo ARM](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
