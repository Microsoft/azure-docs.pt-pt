---
title: 'Quickstart: Criar uma rede virtual utilizando um modelo de Gestor de Recursos'
titleSuffix: Azure Virtual Network
description: Aprenda a usar um modelo de Gestor de Recursos para criar uma rede virtual Azure.
services: virtual-network
author: KumudD
ms.service: virtual-network
ms.topic: quickstart
ms.date: 06/23/2020
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: bc0ac1a6e882f4197828bf79c7989c16b2eb16f7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98217673"
---
# <a name="quickstart-create-a-virtual-network---resource-manager-template"></a>Quickstart: Criar uma rede virtual - Modelo de Gestor de Recursos

Neste arranque rápido, aprende-se a criar uma rede virtual com duas sub-redes utilizando o modelo Azure Resource Manager. Uma rede virtual é o bloco de construção fundamental para a sua rede privada em Azure. Permite que os recursos da Azure, como os VMs, se comuniquem de forma segura entre si e com a internet.


[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Também pode completar este quickstart utilizando o [portal Azure](quick-create-portal.md), [Azure PowerShell,](quick-create-powershell.md)ou [Azure CLI](quick-create-cli.md).

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="review-the-template"></a>Rever o modelo

O modelo usado neste quickstart é de [modelos Azure Quickstart](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vnet-two-subnets/azuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-vnet-two-subnets/azuredeploy.json" range="001-96" highlight="56-92":::

Os seguintes recursos Azure foram definidos no modelo:
- [**Microsoft.Network/virtualNetworks:**](/azure/templates/microsoft.network/virtualnetworks)criar uma rede virtual Azure.
-  [**Microsoft.Network/virtualNetworks/subnets**](/azure/templates/microsoft.network/virtualnetworks/subnets) - crie uma sub-rede.

## <a name="deploy-the-template"></a>Implementar o modelo

Implementar o modelo do Gestor de Recursos para Azure:

1. **Selecione Implementar para Azure** para iniciar súm na Azure e abrir o modelo. O modelo cria uma rede virtual com duas sub-redes.

   [![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-virtual-network-2vms-create%2Fazuredeploy.json)

2. No portal, na **Rede Virtual criar duas páginas de subnetas,** digite ou selecione os seguintes valores:
   - **Grupo de recursos**: Selecione **Criar novo,** digitar um nome para o grupo de recursos e selecionar **OK**.
   - **Nome da rede virtual**: Digite um nome para a nova rede virtual.
3. Selecione **Rever + criar** e, em seguida, selecione **Criar**.

## <a name="review-deployed-resources"></a>Revisão dos recursos implantados

Explore os recursos que foram criados com a rede virtual.

Para saber mais sobre a sintaxe JSON e propriedades para uma rede virtual num modelo, consulte [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks).

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não precisar dos recursos que criou com a rede virtual, elimine o grupo de recursos. Isto remove a rede virtual e todos os recursos relacionados.

Para eliminar o grupo de recursos, ligue para o `Remove-AzResourceGroup` cmdlet:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Passos seguintes
Neste arranque rápido, implementou uma rede virtual Azure com duas sub-redes. Para saber mais sobre as redes virtuais Azure, continue ao tutorial para redes virtuais.

> [!div class="nextstepaction"]
> [Filtre o tráfego de rede](tutorial-filter-network-traffic.md)