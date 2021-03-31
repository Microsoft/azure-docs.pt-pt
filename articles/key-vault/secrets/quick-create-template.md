---
title: Azure Quickstart - Crie um cofre de chaves Azure e um segredo usando o modelo do Azure Resource Manager | Microsoft Docs
description: Quickstart mostrando como criar cofres-chave Azure, e adicionar segredos aos cofres usando o modelo de Gestor de Recursos Azure.
services: key-vault
author: mumian
manager: dougeby
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc,subject-armqs
ms.date: 02/27/2020
ms.author: jgao
ms.openlocfilehash: 1cbe5f986ca36ecc3b45cf4bb7ecffa7067a27bd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97936622"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-an-arm-template"></a>Quickstart: set and retrieve a secret from Azure Key Vault using a ARM template

[Azure Key Vault](../general/overview.md) é um serviço de nuvem que fornece uma loja segura para segredos, tais como chaves, senhas, certificados e outros segredos. Este quickstart foca-se no processo de implantação de um modelo de Gestor de Recursos Azure (modelo ARM) para criar um cofre chave e um segredo.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Se o seu ambiente cumpre os pré-requisitos e se está familiarizado com a utilização de modelos ARM, selecione o botão **Implementar no Azure**. O modelo será aberto no portal do Azure.

[![Implementar no Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-key-vault-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

Para completar este artigo:

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

* O modelo precisa do ID de objeto de utilizador do Azure AD para configurar permissões. O procedimento a seguir obtém o ID do objeto (GUID).

    1. Executar o seguinte comando Azure PowerShell ou Azure CLI selecione **Experimente-o** e, em seguida, cole o script no painel de conchas. Para colar o script, clique com o botão direito da casca e, em seguida, **selecione Pasta**.

        # <a name="cli"></a>[CLI](#tab/CLI)
        ```azurecli-interactive
        echo "Enter your email address that is used to sign in to Azure:" &&
        read upn &&
        az ad user show --id $upn --query "objectId" &&
        echo "Press [ENTER] to continue ..."
        ```

        # <a name="powershell"></a>[PowerShell](#tab/PowerShell)
        ```azurepowershell-interactive
        $upn = Read-Host -Prompt "Enter your email address used to sign in to Azure"
        (Get-AzADUser -UserPrincipalName $upn).Id
        Write-Host "Press [ENTER] to continue..."
        ```

        ---

    2. Escreva a identificação do objeto. Precisa na próxima secção deste arranque rápido.

## <a name="review-the-template"></a>Rever o modelo

O modelo utilizado neste início rápido pertence aos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-key-vault-create/).

:::code language="json" source="~/quickstart-templates/101-key-vault-create/azuredeploy.json":::

Dois recursos Azure são definidos no modelo:

* [**Microsoft.KeyVault/vaults:**](/azure/templates/microsoft.keyvault/vaults)crie um cofre de chaves Azure.
* [**Microsoft.KeyVault/vaults/secrets**](/azure/templates/microsoft.keyvault/vaults/secrets): crie um segredo chave do cofre.

Mais amostras de modelo de azure key vault podem ser encontradas em [modelos Azure Quickstart](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Keyvault&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Implementar o modelo

1. Selecione a imagem seguinte para iniciar sessão no Azure e abrir um modelo. O modelo cria um cofre chave e um segredo.

    [![Implementar no Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-key-vault-create%2Fazuredeploy.json)

2. Selecione ou introduza os seguintes valores.

    ![Modelo ARM, integração do Cofre chave, portal de implantação](../media/quick-create-template/create-key-vault-using-template-portal.png)

    A menos que seja especificado, use o valor padrão para criar o cofre chave e um segredo.

    * **Subscrição**: selecione uma subscrição do Azure.
    * **Grupo de recursos**: selecione **Criar novo,** insira um nome único para o grupo de recursos e, em seguida, clique em **OK**.
    * **Localização**: selecione uma localização. Por exemplo, **E.U.A. Central**.
    * **Key Vault Name**: insira um nome para o cofre de chaves, que deve ser globalmente único dentro do espaço de nome .vault.azure.net. Precisa do nome na secção seguinte quando validar a implementação.
    * **Id do inquilino:** a função do modelo recupera automaticamente o ID do seu inquilino. Não altere o valor predefinido.
    * **ID do utilizador de anúncios:** introduza o seu ID do objeto de utilizador Azure que recuperou dos [Pré-requisitos](#prerequisites).
    * **Nome Secreto:** insira um nome para o segredo que guarda no cofre da chave. Por exemplo, **palavra-passe.**
    * **Valor Secreto:** insira o valor secreto. Se armazenar uma palavra-passe, recomenda-se a utilização da palavra-passe gerada que criou nos Pré-requisitos.
    * **Aceito os termos e condições acima apresentados**: selecione.
3. Selecione **Comprar**. Depois de o cofre da chave ter sido implantado com sucesso, recebe uma notificação:

    ![Modelo ARM, integração do Cofre chave, implementar notificação do portal](../media/quick-create-template/resource-manager-template-portal-deployment-notification.png)

O portal do Azure é utilizado para implementar o modelo. Além do portal Azure, também pode utilizar o Azure PowerShell, Azure CLI e REST API. Para aprender outros métodos de implementação, consulte [os modelos de implementação](../../azure-resource-manager/templates/deploy-powershell.md).

## <a name="review-deployed-resources"></a>Revisão dos recursos implantados

Pode utilizar o portal Azure para verificar o cofre de chaves e o segredo, ou utilizar o seguinte script Azure CLI ou Azure PowerShell para listar o segredo criado.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter your key vault name:" &&
read keyVaultName &&
az keyvault secret list --vault-name $keyVaultName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$keyVaultName = Read-Host -Prompt "Enter your key vault name"
Get-AzKeyVaultSecret -vaultName $keyVaultName
Write-Host "Press [ENTER] to continue..."
```

---

A saída é semelhante a:

# <a name="cli"></a>[CLI](#tab/CLI)

![Screenshot que mostra a saída de validação do portal de implementação em CLI.](../media/quick-create-template/resource-manager-template-portal-deployment-cli-output.png)

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

![Modelo ARM, integração do Cofre chave, implementar saída de validação do portal](../media/quick-create-template/resource-manager-template-portal-deployment-powershell-output.png)

---

## <a name="clean-up-resources"></a>Limpar os recursos

Outros inícios rápidos e tutoriais do Key Vault têm por base este início rápido. Se quiser continuar a trabalhar com os inícios rápidos e tutoriais subsequentes, pode manter estes recursos.
Quando já não for necessário, elimine o grupo de recursos, que elimina o Key Vault e todos os recursos relacionados. Para eliminar o grupo de recursos utilizando a Azure CLI ou a Azure PowerShell:

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

Neste arranque rápido, criou um cofre chave e um segredo usando um modelo ARM, e validou a implementação. Para saber mais sobre Key Vault e Azure Resource Manager, continue para os artigos abaixo.

- Leia uma [visão geral do cofre da chave Azure](../general/overview.md)
- Saiba mais sobre o [Azure Resource Manager](../../azure-resource-manager/management/overview.md)
- Reveja a visão geral da [segurança do Cofre-Chave](../general/security-overview.md)
