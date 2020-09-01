---
title: Crie um certificado Azure Attestation utilizando o modelo do Gestor de Recursos Azure
description: Saiba como criar um certificado Azure Attestation utilizando o modelo Azure Resource Manager.
services: azure-resource-manager
author: msmbaldwin
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: mbaldwin
ms.date: 08/31/2020
ms.openlocfilehash: a73294e41f27a15d70ab95b3aa7b100cab5320b4
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/01/2020
ms.locfileid: "89237388"
---
# <a name="quickstart-create-an-azure-attestation-provider-with-an-arm-template"></a>Quickstart: Criar um fornecedor de atestado Azure com um modelo ARM

[O Microsoft Azure Attestation](overview.md) é uma solução para atestar Ambientes de Execução Fidedigna (TEEs). Este quickstart foca-se no processo de implementação de um modelo de Gestor de Recursos Azure (modelo ARM) para criar uma política de Atesta microsoft Azure.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se o seu ambiente cumpre os pré-requisitos e se está familiarizado com a utilização de modelos ARM, selecione o botão **Implementar no Azure**. O modelo será aberto no portal do Azure.

[![Implementar para Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-attestation-provider-create%2Fazuredeploy.json)


## <a name="review-the-template"></a>Rever o modelo

O modelo utilizado neste início rápido pertence aos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-attestation-provider-create).

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "name": {
      "type": "string"
    },
    "location": {
      "type": "string"
    },
    "tags": {
      "type": "Object"
    },
    "policySigningCertificates": {
      "type": "string"
    }
  },
  "variables": {
    "PolicySigningCertificates": {
      "PolicySigningCertificates": {
        "keys": [
          {
            "kty": "RSA",
            "use": "sig",
            "x5c": [ "[parameters('policySigningCertificates')]" ]
          }
        ]
      }
    }
  },
  "resources": [
    {
      "apiVersion": "2018-09-01-preview",
      "name": "[parameters('name')]",
      "location": "[parameters('location')]",
      "type": "Microsoft.Attestation/attestationProviders",
      "tags": "[parameters('tags')]",
      "Properties": "[if(empty(parameters('policySigningCertificates')), json('{}'), variables('PolicySigningCertificates'))]"
    }
  ]
}
```

Recursos azuis definidos no modelo:
- Microsoft.Attestation/attestationProviders

## <a name="deploy-the-template"></a>Implementar o modelo

1. Selecione a seguinte imagem para iniciar seduca e abrir o modelo.

    [![Implementar para Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-attestation-provider-create%2Fazuredeploy.json)

2. Selecione ou introduza os seguintes valores.

    A menos que seja especificado, use o valor padrão para criar o fornecedor de atestado.

    * **nome**: Selecione um nome para o seu fornecedor de Azure Attestation.
    * **localização**: Selecione um local. Por exemplo, **E.U.A. Central**.
    * **tags**: selecione um local. Por exemplo, **E.U.A. Central**.

3. Selecione **Comprar**. Após a implementação do recurso atestado com sucesso, recebe uma notificação:

O portal do Azure é utilizado para implementar o modelo. Além do portal Azure, também pode utilizar o Azure PowerShell, Azure CLI e REST API. Para aprender outros métodos de implementação, consulte [os modelos de implementação](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="review-deployed-resources"></a>Revisão dos recursos implantados

Pode utilizar o portal Azure para verificar o recurso de atestado.

## <a name="clean-up-resources"></a>Limpar os recursos

Outros Azure Attestation constroem-se sobre este arranque rápido. Se quiser continuar a trabalhar com os inícios rápidos e tutoriais subsequentes, pode manter estes recursos.

Quando já não for necessário, elimine o grupo de recursos, que elimina o recurso Attestation . Para eliminar o grupo de recursos utilizando a Azure CLI ou a Azure PowerShell:

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
