---
title: Início rápido do Azure - criar um cofre de chave do Azure e um segredo com o modelo Azure Resource Manager | Documentos da Microsoft
description: Início rápido que mostra como criar cofres de chaves do Azure e adicione segredos aos cofres, utilizando o modelo Azure Resource Manager.
services: key-vault
author: mumian
manager: dougeby
tags: azure-resource-manager
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/04/2019
ms.author: jgao
ms.openlocfilehash: 4b774e5b0a5c64a9af9a5a54ba264c6226558a24
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57880045"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-resource-manager-template"></a>Início rápido: Definir e obter um segredo a partir do Azure Key Vault com o modelo do Resource Manager

[O Azure Key Vault](./key-vault-overview.md) é um serviço cloud que fornece um armazenamento seguro de segredos, tal como chaves, palavras-passe, certificados e outros segredos. Este início rápido centra-se sobre o processo de implantação de um modelo do Resource Manager para criar um cofre de chaves e um segredo. Para obter mais informações sobre como desenvolver modelos do Resource Manager, consulte [documentação do Resource Manager](/azure/azure-resource-manager/) e o [referência de modelo](/azure/templates/microsoft.keyvault/allversions).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, precisa de:

* O modelo precisa do ID de objeto de utilizador do Azure AD para configurar permissões. O procedimento seguinte obtém o objeto de ID (GUID).

    1. Execute o seguinte comando do Azure PowerShell ou a CLI do Azure, selecionando **experimente**e, em seguida, cole o script no painel de shell. Colar o script, o shell com o botão direito e, em seguida, selecione **colar**. 

        ```azurecli-interactive
        echo "Enter your email address that is used to sign in to Azure:" &&
        read upn &&
        az ad user show --upn-or-object-id $upn --query "objectId" 
        ```

        ```azurepowershell-interactive
        $upn = Read-Host -Prompt "Enter your email address used to sign in to Azure"
        (Get-AzADUser -UserPrincipalName $upn).Id
        ```

    2. Anote o ID de objeto. Precisa na próxima seção deste início rápido.

## <a name="create-a-vault-and-a-secret"></a>Criar um cofre e um segredo

O modelo utilizado neste início rápido é partir [modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/101-key-vault-create/). Pode ver mais exemplos de modelo do Azure Key Vault [aqui](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Keyvault).

1. Selecione a imagem seguinte para iniciar sessão no Azure e abrir um modelo. O modelo cria um cofre de chaves e um segredo.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-key-vault-create%2Fazuredeploy.json"><img src="./media/quick-create-template/deploy-to-azure.png" alt="deploy to azure"/></a>

2. Selecione ou introduza os seguintes valores.  

    ![Portal de implementação de integração de Cofres de Chaves de modelos do Resource Manager](./media/quick-create-template/create-key-vault-using-template-portal.png)

    Exceto se for especificado, utilize o valor predefinido para criar o Cofre de chaves e um segredo.

    * **Subscrição**: selecione uma subscrição do Azure.
    * **Grupo de recursos**: selecione **criar novo**, introduza um nome exclusivo para o grupo de recursos e, em seguida, clique em **OK**. 
    * **Localização**: selecione uma localização.  Por exemplo, **E.U.A. Central**.
    * **Nome do Key Vault**: introduza um nome exclusivo para o Cofre de chaves.  
    * **ID do inquilino**: a função de modelo obtém automaticamente o ID do inquilino.  Não altere o valor predefinido
    * **Id de utilizador do AD**: introduza o seu ID de objeto de utilizador do Azure AD obtidos a partir [pré-requisitos](#prerequisites).
    * **Nome do segredo**: introduza um nome para o segredo que armazenar no Cofre de chaves.  Por exemplo, **adminpassword**
    * **Valor secreto**: introduza o valor secreto.  Se armazenar uma palavra-passe, recomenda-se para utilizar a palavra-passe gerada que criou em pré-requisitos.
    * **Eu concordo com o estado de termos e condições acima**: Selecione.
3. Selecione **Comprar**.

## <a name="validate-the-deployment"></a>Validar a implementação

Pode utilizar o portal do Azure para verificar o Cofre de chaves e o segredo ou utilize o seguinte script de CLI do Azure ou do Azure PowerShell para listar o segredo criado.

```azurecli-interactive
echo "Enter your key vault name:" &&
read keyVaultName &&
az keyvault secret list --vault-name $keyVaultName
```

```azurepowershell-interactive
$keyVaultName = Read-Host -Prompt "Enter your key vault name"
Get-AzKeyVaultSecret -vaultName $keyVaultName
```

## <a name="clean-up-resources"></a>Limpar recursos

Outros inícios rápidos e tutoriais do Key Vault têm por base este início rápido. Se quiser continuar a trabalhar com os inícios rápidos e tutoriais subsequentes, pode manter estes recursos.
Quando já não for necessário, elimine o grupo de recursos, que elimina o Key Vault e todos os recursos relacionados. Para eliminar o grupo de recursos com a CLI do Azure ou do Azure Powershell:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName 
```
```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName 
```

## <a name="next-steps"></a>Passos Seguintes

* [Home Page do Azure Key Vault](https://azure.microsoft.com/services/key-vault/)
* [Documentação do Azure Key Vault](https://docs.microsoft.com/azure/key-vault/)
* [Azure SDK For Node](https://docs.microsoft.com/javascript/api/overview/azure/key-vault) (SDK do Azure para Node)
* [Referência à API REST do Azure](https://docs.microsoft.com/rest/api/keyvault/)
