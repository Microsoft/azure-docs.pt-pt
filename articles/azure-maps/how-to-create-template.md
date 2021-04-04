---
title: Crie a sua conta Azure Maps utilizando um modelo ARM | Microsoft Azure Maps
description: Saiba como criar uma conta Azure Maps utilizando um modelo Azure Resource Manager (ARM).
author: philmea
ms.author: philmea
ms.date: 10/20/2020
ms.topic: how-to
ms.service: azure-maps
ms.openlocfilehash: c715c0639e962f76f669515c1d2c826c8cf6cc9e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92525186"
---
# <a name="create-your-azure-maps-account-using-an-arm-template"></a>Crie a sua conta Azure Maps usando um modelo ARM

Pode criar a sua conta Azure Maps utilizando um modelo Azure Resource Manager (ARM). Depois de ter uma conta, pode implementar as APIs no seu website ou aplicação móvel.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se o seu ambiente cumpre os pré-requisitos e se está familiarizado com a utilização de modelos ARM, selecione o botão **Implementar no Azure**. O modelo será aberto no portal do Azure.

[![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-maps-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

Para completar este artigo:

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="review-the-template"></a>Rever o modelo

O modelo utilizado neste início rápido pertence aos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-maps-create/).

:::code language="json" source="~/quickstart-templates/101-maps-create/azuredeploy.json":::

O recurso de conta Azure Maps é definido neste modelo:

* [**Microsoft.Maps/accounts**](/azure/templates/microsoft.maps/accounts): criar uma conta Azure Maps.

## <a name="deploy-the-template"></a>Implementar o modelo

1. Selecione a imagem seguinte para iniciar sessão no Azure e abrir um modelo. O modelo cria uma conta Azure Maps.

    [![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-maps-create%2Fazuredeploy.json)

2. Selecione ou introduza os seguintes valores.

    ![Porta de implementação do modelo ARM](./media/how-to-create-template/create-account-using-template-portal.png)

    A menos que seja especificado, use o valor predefinido para criar a sua conta Azure Maps.

    * **Subscrição**: selecione uma subscrição do Azure.
    * **Grupo de recursos**: selecione **Criar novo,** insira um nome único para o grupo de recursos e, em seguida, clique em **OK**.
    * **Localização**: selecione uma localização. Por exemplo, **West US 2**.
    * **Nome da conta**: insira um nome para a sua conta Azure Maps, que deve ser globalmente única.
    * **Nível de preços**: selecione o nível de preços apropriado, o valor padrão para o modelo é S0.

3. Selecione **Rever + criar**. 
4. Confirme as suas definições na página de revisão e clique em **Criar**. Depois de o seu Azure Maps ter sido implementado com sucesso, recebe uma notificação:

    ![Notificação do portal de implementação do modelo ARM](./media/how-to-create-template/resource-manager-template-portal-deployment-notification.png)

O portal Azure é utilizado para implantar o seu modelo. Também pode utilizar a Azure PowerShell, Azure CLI e REST API. Para aprender outros métodos de implementação, consulte [os modelos de implementação](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="review-deployed-resources"></a>Revisão dos recursos implantados

Pode utilizar o portal Azure para verificar a sua conta Azure Maps e ver as suas chaves. Também pode utilizar o seguinte script Azure CLI para listar as chaves da sua conta.

```azurecli-interactive
az maps account keys list --name MyMapsAccount --resource-group MyResourceGroup
```

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não for necessário, elimine o grupo de recursos, que também elimina a conta Azure Maps. Para eliminar o grupo de recursos utilizando o Azure CLI:

```azurecli-interactive
az group delete --name MyResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a Azure Maps e a Azure Resource Manager, continue para os artigos abaixo.

- Criar uma [aplicação de demonstração Azure](quick-demo-map-app.md) Maps
- Saiba mais sobre [modelos ARM](../azure-resource-manager/templates/overview.md)