---
title: Crie um certificado Azure Attestation utilizando o modelo do Gestor de Recursos Azure
description: Saiba como criar um certificado Azure Attestation utilizando o modelo Azure Resource Manager.
services: azure-resource-manager
author: msmbaldwin
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: mbaldwin
ms.date: 10/16/2020
ms.openlocfilehash: 8c56a37ebcc799b0170785666212eb4e3b00a5f0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92144969"
---
# <a name="quickstart-create-an-azure-attestation-provider-with-an-arm-template"></a>Quickstart: Criar um fornecedor de atestado Azure com um modelo ARM

[O Microsoft Azure Attestation](overview.md) é uma solução para atestar Ambientes de Execução Fidedigna (TEEs). Este quickstart foca-se no processo de implementação de um modelo de Gestor de Recursos Azure (modelo ARM) para criar uma política de Atesta microsoft Azure.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se o seu ambiente cumpre os pré-requisitos e se está familiarizado com a utilização de modelos ARM, selecione o botão **Implementar no Azure**. O modelo será aberto no portal do Azure.

[![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-attestation-provider-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="review-the-template"></a>Rever o modelo

O modelo utilizado neste início rápido pertence aos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-attestation-provider-create).

:::code language="json" source="~/quickstart-templates/101-attestation-provider-create/azuredeploy.json":::

Recursos azuis definidos no modelo:

- Microsoft.Attestation/attestationProviders

## <a name="deploy-the-template"></a>Implementar o modelo

1. Selecione a seguinte imagem para iniciar seduca e abrir o modelo.

    [![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-attestation-provider-create%2Fazuredeploy.json)

1. Selecione ou introduza os seguintes valores.

    A menos que seja especificado, use o valor padrão para criar o fornecedor de atestado.

    - **Nome do Provedor de Attestation**: Selecione um nome para o seu fornecedor de atestado Azure.
    - **Localização**: Selecione uma localização. Por exemplo, **E.U.A. Central**.
    - **Etiquetas**: Selecione uma localização. Por exemplo, **E.U.A. Central**.

1. Selecione **Comprar**. Depois de o recurso de atestado ter sido implementado com sucesso, recebe uma notificação.

O portal do Azure é utilizado para implementar o modelo. Além do portal Azure, também pode utilizar o Azure PowerShell, Azure CLI e REST API. Para aprender outros métodos de implementação, consulte [os modelos de implementação](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="review-deployed-resources"></a>Revisão dos recursos implantados

Pode utilizar o portal Azure para verificar o recurso de atestado.

## <a name="clean-up-resources"></a>Limpar os recursos

Outros Azure Attestation constroem-se sobre este arranque rápido. Se quiser continuar a trabalhar com os inícios rápidos e tutoriais subsequentes, pode manter estes recursos.

Quando já não for necessário, elimine o grupo de recursos, que elimina o recurso Attestation. Para eliminar o grupo de recursos utilizando a Azure CLI ou a Azure PowerShell:

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, criou um recurso de atestado usando um modelo ARM e validou a implementação. Para saber mais sobre a Azure Attestation, consulte [a visão geral da Azure Attestation](overview.md).
