---
title: Azure Quickstart - Crie um cofre chave Azure e um segredo usando o modelo de Gestor de Recursos Azure [ Microsoft Docs
description: Quickstart mostrando como criar cofres chave Azure, e adicionar segredos aos cofres usando o modelo De Gestor de Recursos Azure.
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
ms.openlocfilehash: 273a467f5db2201015352aaf4a232f5a42e29673
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81618078"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-resource-manager-template"></a>Quickstart: Definir e recuperar um segredo do Cofre de Chaves Azure usando o modelo de Gestor de Recursos

[Azure Key Vault](../general/overview.md) é um serviço na nuvem que fornece uma loja segura para segredos, tais como chaves, senhas, certificados e outros segredos. Este quickstart centra-se no processo de implementação de um modelo de Gestor de Recursos para criar um cofre chave e um segredo.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, precisa de:

* O modelo precisa do ID de objeto de utilizador do Azure AD para configurar permissões. O procedimento seguinte obtém o ID do objeto (GUID).

    1. Executar o seguinte comando Azure PowerShell ou Azure CLI **selecione-o Experimente-o**e, em seguida, colhe o script no painel da concha. Para colar o script, clique na concha e, em seguida, selecione **Pasta**.

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

## <a name="create-a-vault-and-a-secret"></a>Criar um cofre e um segredo

### <a name="review-the-template"></a>Reveja o modelo

O modelo utilizado neste quickstart é de [modelos Azure Quickstart](https://azure.microsoft.com/resources/templates/101-key-vault-create/).

:::code language="json" source="~/quickstart-templates/101-key-vault-create/azuredeploy.json" range="1-150" highlight="107-148":::

Dois recursos Azure são definidos no modelo:

* [**Microsoft.KeyVault/vaults**](/azure/templates/microsoft.keyvault/vaults): crie um cofre chave Azure.
* [**Microsoft.KeyVault/cofres/segredos**](/azure/templates/microsoft.keyvault/vaults/secrets): crie um segredo de cofre chave.

Mais amostras de modelo de cofre de chave azure podem ser encontradas [aqui](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Keyvault).

### <a name="deploy-the-template"></a>Implementar o modelo

1. Selecione a imagem seguinte para iniciar sessão no Azure e abrir um modelo. O modelo cria um cofre chave e um segredo.

    [![Implementar no Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-key-vault-create%2Fazuredeploy.json)

2. Selecione ou introduza os seguintes valores.

    ![Modelo de Gestor de Recursos, Integração do Cofre chave, portal de implantação](../media/quick-create-template/create-key-vault-using-template-portal.png)

    A menos que seja especificado, use o valor padrão para criar o cofre chave e um segredo.

    * **Subscrição**: selecione uma subscrição do Azure.
    * **Grupo de recursos**: selecione **Criar novo,** introduza um nome único para o grupo de recursos e, em seguida, clique **OK**.
    * **Localização**: selecione uma localização.  Por exemplo, **Centro dos EUA.**
    * **Nome do cofre chave**: introduza um nome para o cofre chave, que deve ser globalmente único dentro do espaço de nome .vault.azure.net. Precisa do nome na secção seguinte quando valida a implantação.
    * **Id**do inquilino : a função do modelo recupera automaticamente o seu ID de inquilino.  Não altere o valor padrão.
    * **Id do utilizador do anúncio**: introduza o ID do objeto de utilizador do Utilizador AD Azure que recuperou dos [pré-requisitos](#prerequisites).
    * **Nome Secreto**: insira um nome para o segredo que armazena no cofre da chave.  Por exemplo, **adminpassword**.
    * **Valor Secreto**: insira o valor secreto.  Se armazenar uma palavra-passe, recomenda-se utilizar a palavra-passe gerada criada em Pré-requisitos.
    * **Aceito os termos e condições acima apresentados**: selecione.
3. Selecione **Comprar**. Depois de o cofre ter sido implantado com sucesso, recebe uma notificação:

    ![Modelo de Gestor de Recursos, Integração do Cofre chave, notificação do portal de implementação](../media/quick-create-template/resource-manager-template-portal-deployment-notification.png)

O portal Azure é usado para implantar o modelo. Além do portal Azure, também pode utilizar o Azure PowerShell, Azure CLI e REST API. Para aprender outros métodos de implementação, consulte [os modelos de implantação](../../azure-resource-manager/templates/deploy-powershell.md).

## <a name="review-deployed-resources"></a>Rever os recursos implantados

Pode utilizar o portal Azure para verificar o cofre chave e o segredo, ou usar o seguinte script Azure CLI ou Azure PowerShell para listar o segredo criado.

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

![Modelo de Gestor de Recursos, Integração do Cofre Chave, saída de validação do portal de implementação](../media/quick-create-template/resource-manager-template-portal-deployment-cli-output.png)

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

![Modelo de Gestor de Recursos, Integração do Cofre Chave, saída de validação do portal de implementação](../media/quick-create-template/resource-manager-template-portal-deployment-powershell-output.png)

---
## <a name="clean-up-resources"></a>Limpar recursos

Outros inícios rápidos e tutoriais do Key Vault têm por base este início rápido. Se quiser continuar a trabalhar com os inícios rápidos e tutoriais subsequentes, pode manter estes recursos.
Quando já não for necessário, elimine o grupo de recursos, que elimina o Key Vault e todos os recursos relacionados. Para eliminar o grupo de recursos utilizando o Azure CLI ou o Azure PowerShell:

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

Neste arranque rápido, criou um cofre chave e um segredo usando um modelo de Gestor de Recursos Azure, e validou a implementação. Para saber mais sobre key vault e Azure Resource Manager, continue para os artigos abaixo.

- Leia uma [visão geral do Cofre chave Azure](../general/overview.md)
- Saiba mais sobre [o Gestor de Recursos Azure](../../azure-resource-manager/management/overview.md)
- Rever [as melhores práticas do Cofre de Chaves Azure](../general/best-practices.md)
