---
title: Início rápido do Azure-criar um cofre de chaves do Azure e um segredo usando Azure Resource Manager modelo | Microsoft Docs
description: Início rápido mostrando como criar cofres de chaves do Azure e adicionar segredos aos cofres usando Azure Resource Manager modelo.
services: key-vault
author: mumian
manager: dougeby
tags: azure-resource-manager
ms.service: key-vault
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/03/2019
ms.author: jgao
ms.openlocfilehash: 13ac8b9f6d82cc8b0060f87237ef3140cb245379
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70259210"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-resource-manager-template"></a>Início rápido: Definir e recuperar um segredo de Azure Key Vault usando o modelo do Resource Manager

[Azure Key Vault](./key-vault-overview.md) é um serviço de nuvem que fornece um repositório seguro para segredos, como chaves, senhas, certificados e outros segredos. Este guia de início rápido concentra-se no processo de implantação de um modelo do Resource Manager para criar um cofre de chaves e um segredo. Para obter mais informações sobre como desenvolver modelos do Resource Manager, consulte a [documentação do Resource Manager](/azure/azure-resource-manager/) e a [referência de modelo](/azure/templates/microsoft.keyvault/allversions).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, precisa de:

* O modelo precisa do ID de objeto de utilizador do Azure AD para configurar permissões. O procedimento a seguir obtém a ID do objeto (GUID).

    1. Execute o comando a seguir Azure PowerShell ou CLI do Azure selecionando **Experimente**e, em seguida, Cole o script no painel Shell. Para colar o script, clique com o botão direito do mouse no Shell e selecione **colar**. 

        ```azurecli-interactive
        echo "Enter your email address that is used to sign in to Azure:" &&
        read upn &&
        az ad user show --upn-or-object-id $upn --query "objectId" 
        ```

        ```azurepowershell-interactive
        $upn = Read-Host -Prompt "Enter your email address used to sign in to Azure"
        (Get-AzADUser -UserPrincipalName $upn).Id
        ```

    2. Anote a ID do objeto. Você precisará dela na próxima seção deste guia de início rápido.

## <a name="create-a-vault-and-a-secret"></a>Criar um cofre e um segredo

O modelo usado neste guia de início rápido é de [modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/101-key-vault-create/). Mais exemplos de modelos de Azure Key Vault podem ser encontrados [aqui](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Keyvault).

1. Selecione a imagem seguinte para iniciar sessão no Azure e abrir um modelo. O modelo cria um cofre de chaves e um segredo.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-key-vault-create%2Fazuredeploy.json"><img src="./media/quick-create-template/deploy-to-azure.png" alt="deploy to azure"/></a>

2. Selecione ou introduza os seguintes valores.  

    ![Portal de implementação de integração de Cofres de Chaves de modelos do Resource Manager](./media/quick-create-template/create-key-vault-using-template-portal.png)

    A menos que seja especificado, use o valor padrão para criar o cofre de chaves e um segredo.

    * **Subscrição**: selecione uma subscrição do Azure.
    * **Grupo de recursos**: selecione **criar novo**, insira um nome exclusivo para o grupo de recursos e clique em **OK**. 
    * **Localização**: selecione uma localização.  Por exemplo, **E.U.A. Central**.
    * **Nome do Key Vault**: Insira um nome para o cofre de chaves que deve ser globalmente exclusivo no namespace. Vault.Azure.net.  
    * **ID do inquilino**: a função de modelo obtém automaticamente o ID do inquilino.  Não altere o valor padrão.
    * **ID de usuário do AD**: Insira sua ID de objeto de usuário do Azure AD que você recuperou dos [pré-requisitos](#prerequisites).
    * **Nome do segredo**: Insira um nome para o segredo que você armazena no cofre de chaves.  Por exemplo, **AdminPassword**.
    * **Valor secreto**: Insira o valor do segredo.  Se você armazenar uma senha, é recomendável usar a senha gerada que você criou em pré-requisitos.
    * **Eu concordo com o estado dos termos e condições acima**: Não.
3. Selecione **Comprar**.

## <a name="validate-the-deployment"></a>Validar a implementação

Você pode usar o portal do Azure para verificar o cofre de chaves e o segredo, ou usar o script CLI do Azure ou Azure PowerShell a seguir para listar o segredo criado.

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
Quando já não for necessário, elimine o grupo de recursos, que elimina o Key Vault e todos os recursos relacionados. Para excluir o grupo de recursos usando CLI do Azure ou o Azure PowerShell:

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

Neste guia de início rápido, você criou um cofre de chaves e um segredo usando um modelo do Azure Resource Manager e validou a implantação. Para saber mais sobre Key Vault e Azure Resource Manager, continue com os artigos abaixo.

- Leia uma [visão geral do Azure Key Vault](key-vault-overview.md)
- Saiba mais sobre o [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md)
- Obter mais informações sobre [chaves, segredos e certificados](about-keys-secrets-and-certificates.md)
- Examinar [Azure Key Vault práticas recomendadas](key-vault-best-practices.md)