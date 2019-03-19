---
title: Execute comandos CLI do Azure ou o PowerShell sob uma identidade do Azure AD para aceder ao armazenamento do Azure | Documentos da Microsoft
description: CLI do Azure e o PowerShell suportam a iniciar sessão com uma identidade do Azure AD para executar comandos nos contentores de armazenamento do Azure e filas e os seus dados. Um token de acesso é fornecido para a sessão e utilizado para autorizar as operações de chamada. Permissões dependem a função atribuída para a identidade do Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/06/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: f8fd3cdcf73749d787fc6f1c2222946961091f80
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57849844"
---
# <a name="use-an-azure-ad-identity-to-access-azure-storage-with-cli-or-powershell"></a>Utilizar uma identidade do Azure AD para aceder ao armazenamento do Azure com a CLI ou o PowerShell

O armazenamento do Azure fornece extensões para a CLI do Azure e do PowerShell que permitem-lhe iniciar sessão e executar comandos de scripts sob uma identidade do Azure Active Directory (Azure AD). A identidade do Azure AD pode ser um utilizador, grupo ou principal de serviço de aplicações, ou pode ser um [identidade de recursos do Azure gerida](../../active-directory/managed-identities-azure-resources/overview.md). Pode atribuir permissões para aceder aos recursos de armazenamento para a identidade do Azure AD através do controlo de acesso baseado em funções (RBAC). Para obter mais informações sobre as funções do RBAC no armazenamento do Azure, consulte [gerir direitos de acesso aos dados de armazenamento do Azure com o RBAC (pré-visualização)](storage-auth-aad-rbac.md).

Quando iniciar sessão CLI do Azure ou do PowerShell com uma identidade do Azure AD, é devolvido um token de acesso para aceder ao armazenamento do Azure sob essa identidade. Esse token é automaticamente usado pela CLI ou o PowerShell para autorizar as operações de armazenamento do Azure. Para operações suportadas, já não tem de transmitir uma chave de conta ou o token SAS com o comando.

## <a name="supported-operations"></a>Operações suportadas

As extensões são suportadas para operações de contentores e filas. As operações que podem ser chamados depende das permissões concedidas à identidade do Azure AD com a qual inicia sessão na CLI do Azure ou o PowerShell. Permissões para os contentores de armazenamento do Azure ou filas são atribuídas através do controlo de acesso baseado em funções (RBAC). Por exemplo, se uma função de leitor de dados é atribuída à identidade, em seguida, pode executar comandos de scripts de leitura de dados de um contentor ou uma fila. Se uma função de contribuinte do Data for atribuída para a identidade, em seguida, pode executar comandos de scripts que ler, escreverem ou eliminar um contentor ou fila ou os dados contêm. 

Para obter detalhes sobre as permissões necessárias para cada operação de armazenamento do Azure num contentor ou fila, consulte [permissões para chamar as operações REST](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations).  

## <a name="call-cli-commands-using-azure-ad-credentials"></a>Chamar comandos da CLI com credenciais do Azure AD

CLI do Azure suporta o `--auth-mode` parâmetro para operações de dados no armazenamento do Azure:

- Definir o `--auth-mode` parâmetro `login` para iniciar sessão com um principal de segurança do Azure AD.
- Definir o `--auth-mode` parâmetro para o legado `key` valor para tentar consultar para um caso de chaves de conta sem parâmetros de autenticação da conta são fornecidos. 

O exemplo seguinte mostra como criar um contentor numa nova conta de armazenamento do CLI do Azure com as suas credenciais do Azure AD. Não se esqueça de substituir os valores de marcador de posição entre parênteses Retos ângulo pelos seus próprios valores: 

1. Certifique-se de que instalou a versão da CLI do Azure 2.0.46 ou posterior. Executar `az --version` para verificar a versão instalada.

1. Executar `az login` e autenticar-se na janela do browser: 

    ```azurecli
    az login
    ```
    
1. Especifique a sua subscrição pretendida. Crie um grupo de recursos com [az group create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create). Criar uma conta de armazenamento dentro desse grupo de recursos usando [criar conta de armazenamento az](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create): 

    ```azurecli
    az account set --subscription <subscription-id>

    az group create \
        --name sample-resource-group-cli \
        --location eastus

    az storage account create \
        --name <storage-account> \
        --resource-group sample-resource-group-cli \
        --location eastus \
        --sku Standard_LRS \
        --encryption-services blob
    ```
    
1. Antes de criar o contentor, atribuir os [contribuinte de dados de Blob de armazenamento (pré-visualização)](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor-preview) função a próprio. Mesmo que seja o proprietário da conta, precisa de permissões explícitas para realizar operações de dados em relação à conta de armazenamento. Para obter mais informações sobre a atribuição de funções RBAC, veja [conceder acesso a contentores do Azure e filas com RBAC no portal do Azure (pré-visualização)](storage-auth-aad-rbac.md).

    > [!IMPORTANT]
    > Durante a pré-visualização do suporte do Azure AD para blobs e filas, atribuições de funções do RBAC podem demorar até 5 minutos para propagar.
    
1. Chamar o [criar contentor de armazenamento az](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create) comando com o `--auth-mode` parâmetro definido como `login` para criar o contentor com as suas credenciais do Azure AD:

    ```azurecli
    az storage container create \ 
        --account-name <storage-account> \ 
        --name sample-container \
        --auth-mode login
    ```

A variável de ambiente associada com o `--auth-mode` parâmetro é `AZURE_STORAGE_AUTH_MODE`. Pode especificar o valor apropriado na variável de ambiente para evitar a incluí-lo em cada chamada para uma operação de dados do armazenamento do Azure.

## <a name="call-powershell-commands-using-azure-ad-credentials"></a>Chamar comandos do PowerShell com credenciais do Azure AD

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Para utilizar o Azure PowerShell para iniciar sessão e executar operações subsequentes no armazenamento do Azure com credenciais do Azure AD, crie um contexto de armazenamento para fazer referência a conta de armazenamento e, incluindo o `-UseConnectedAccount` parâmetro.

O exemplo seguinte mostra como criar um contentor numa nova conta de armazenamento do Azure PowerShell com as suas credenciais do Azure AD. Não se esqueça de substituir os valores de marcador de posição entre parênteses Retos ângulo pelos seus próprios valores:

1. Inicie sessão na sua subscrição do Azure com o `Connect-AzAccount` de comando e siga na tela indicações para introduzir as credenciais do Azure AD: 

    ```powershell
    Connect-AzAccount
    ```
    
1. Criar um grupo de recursos do Azure ao chamar [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). 

    ```powershell
    $resourceGroup = "sample-resource-group-ps"
    $location = "eastus"
    New-AzResourceGroup -Name $resourceGroup -Location $location
    ```

1. Criar uma conta de armazenamento ao chamar [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount).

    ```powershell
    $storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
      -Name "<storage-account>" `
      -SkuName Standard_LRS `
      -Location $location `
    ```

1. Obter o contexto de conta de armazenamento que especifica a nova conta de armazenamento ao chamar [New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext). Ao efetuar ações numa conta de armazenamento, pode referenciar o contexto em vez de passar repetidamente as credenciais. Incluir o `-UseConnectedAccount` parâmetro chame quaisquer operações de dados subsequentes com as suas credenciais do Azure AD:

    ```powershell
    $ctx = New-AzStorageContext -StorageAccountName "<storage-account>" -UseConnectedAccount
    ```

1. Antes de criar o contentor, atribuir os [contribuinte de dados de Blob de armazenamento (pré-visualização)](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor-preview) função a próprio. Mesmo que seja o proprietário da conta, precisa de permissões explícitas para realizar operações de dados em relação à conta de armazenamento. Para obter mais informações sobre a atribuição de funções RBAC, veja [conceder acesso a contentores do Azure e filas com RBAC no portal do Azure (pré-visualização)](storage-auth-aad-rbac.md).

    > [!IMPORTANT]
    > Durante a pré-visualização do suporte do Azure AD para blobs e filas, atribuições de funções do RBAC podem demorar até 5 minutos para propagar.

1. Criar um contentor ao chamar [New-AzStorageContainer](/powershell/module/az.storage/new-azstoragecontainer). Uma vez que esta chamada utiliza o contexto criado nos passos anteriores, o contentor é criado com as suas credenciais do Azure AD. 

    ```powershell
    $containerName = "sample-container"
    New-AzStorageContainer -Name $containerName -Context $ctx
    ```

## <a name="next-steps"></a>Passos Seguintes

- Para saber mais sobre as funções do RBAC para o armazenamento do Azure, veja [gerir direitos de acesso aos dados de armazenamento com o RBAC (pré-visualização)](storage-auth-aad-rbac.md).
- Para saber mais sobre a utilização de identidades geridas para recursos do Azure com o armazenamento do Azure, veja [Authenticate acesso para blobs e filas com o Azure managed identidades para recursos do Azure (pré-visualização)](storage-auth-aad-msi.md).
- Para saber como autorizar o acesso a contentores e filas de dentro dos seus aplicativos de armazenamento, veja [utilize o Azure AD com aplicações de armazenamento](storage-auth-aad-app.md).