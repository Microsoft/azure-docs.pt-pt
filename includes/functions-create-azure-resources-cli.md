---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: 99ae35aca485ac928f7c5ef9f98295eed4bc1245
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/03/2021
ms.locfileid: "99500020"
---
## <a name="create-supporting-azure-resources-for-your-function"></a>Crie recursos Azure de suporte para a sua função

Antes de poder implantar o seu código de função para Azure, tem de criar três recursos:

- Um [grupo de recursos](../articles/azure-resource-manager/management/overview.md), que é um recipiente lógico para recursos relacionados.
- Uma [conta de Armazenamento,](../articles/storage/common/storage-account-create.md)que é usada para manter o estado e outras informações sobre as suas funções.
- Uma aplicação de função, que fornece o ambiente para a execução do seu código de função. Uma aplicação de função mapeia para o seu projeto de função local e permite-lhe agrupar funções como uma unidade lógica para uma gestão, implementação e partilha mais fácil de recursos.

Utilize os seguintes comandos para criar estes itens. Tanto o Azure CLI como o PowerShell são apoiados.

1. Se ainda não o fez, inscreva-se no Azure:

    # <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
    ```azurecli
    az login
    ```

    O comando [de login az](/cli/azure/reference-index#az_login) assina-o na sua conta Azure.

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell) 
    ```azurepowershell
    Connect-AzAccount
    ```

    O [cmdlet Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) indica-o na sua conta Azure.

    ---

1. Criar um grupo de recursos nomeado `AzureFunctionsQuickstart-rg` na `westeurope` região:

    # <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
    
    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location westeurope
    ```
 
    O [grupo az cria](/cli/azure/group#az_group_create) um grupo de recursos. Geralmente cria o seu grupo de recursos e recursos numa região próxima de si, utilizando uma região disponível devolvida do `az account list-locations` comando.

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzResourceGroup -Name AzureFunctionsQuickstart-rg -Location westeurope
    ```

    O comando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) cria um grupo de recursos. Você geralmente cria o seu grupo de recursos e recursos em uma região próxima de você, usando uma região disponível devolvida do cmdlet [Get-AzLocation.](/powershell/module/az.resources/get-azlocation)

    ---

1. Crie uma conta de armazenamento para fins gerais no seu grupo de recursos e região:

    # <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```

    A [conta de armazenamento az cria](/cli/azure/storage/account#az_storage_account_create) o comando cria a conta de armazenamento. 

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzStorageAccount -ResourceGroupName AzureFunctionsQuickstart-rg -Name <STORAGE_NAME> -SkuName Standard_LRS -Location westeurope
    ```

    O [Cmdlet New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) cria a conta de armazenamento.

    ---

    No exemplo anterior, `<STORAGE_NAME>` substitua-o por um nome que lhe seja adequado e único no Azure Storage. Os nomes devem conter apenas três a 24 caracteres e letras minúsculas. `Standard_LRS` especifica uma conta de finalidade geral, que é [suportada por Funções](../articles/azure-functions/storage-considerations.md#storage-account-requirements).
