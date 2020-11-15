---
title: Executar comandos PowerShell com credenciais AD AD Azure para aceder a dados blob
titleSuffix: Azure Storage
description: O PowerShell suporta a assinatura com credenciais AZure AD para executar comandos em dados blob no Azure Storage. Um sinal de acesso é fornecido para a sessão e usado para autorizar operações de chamada. As permissões dependem do papel Azure atribuído ao diretor de segurança da Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 09/14/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: blobs
ms.openlocfilehash: cca0b197bdef04ffca9b71a7f394d3359023e2b7
ms.sourcegitcommit: 295db318df10f20ae4aa71b5b03f7fb6cba15fc3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2020
ms.locfileid: "94637432"
---
# <a name="run-powershell-commands-with-azure-ad-credentials-to-access-blob-data"></a>Executar comandos PowerShell com credenciais AD AD Azure para aceder a dados blob

O Azure Storage fornece extensões para o PowerShell que lhe permitem iniciar sing e executar comandos de script com credenciais Azure Ative Directory (Azure AD). Quando você assinar no PowerShell com credenciais Azure AD, um token de acesso OAuth 2.0 é devolvido. Este token é automaticamente utilizado pela PowerShell para autorizar operações de dados subsequentes contra o armazenamento da Blob. Para operações apoiadas, já não precisa de passar uma chave de conta ou um sinal SAS com o comando.

Pode atribuir permissões para apresentar dados a um diretor de segurança AZure AD através do controlo de acesso baseado em funções Azure (Azure RBAC). Para obter mais informações sobre as funções Azure no Azure Storage, consulte [Gerir os direitos de acesso aos dados de armazenamento Azure com o Azure RBAC](../common/storage-auth-aad-rbac.md).

## <a name="supported-operations"></a>Operações apoiadas

As extensões de Armazenamento Azure são suportadas para operações em dados blob. Quais as operações que pode ligar dependem das permissões concedidas ao diretor de segurança da AD Azure com a qual se inscreve na PowerShell. As permissões aos recipientes de armazenamento Azure são atribuídas através do Azure RBAC. Por exemplo, se lhe foi atribuída a função **De Leitore de Dados Blob,** então pode executar comandos de script que lêem dados a partir de um recipiente. Se lhe foi atribuída a **função De Contribuinte de Dados Blob,** então pode executar comandos de script que leiam, escrevam ou apaguem um recipiente ou os dados que contêm.

Para obter mais informações sobre as permissões necessárias para cada operação de armazenamento Azure num recipiente, consulte [as operações de armazenamento de chamadas com fichas OAuth](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens).  

## <a name="call-powershell-commands-using-azure-ad-credentials"></a>Ligue para comandos PowerShell usando credenciais Azure AD

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Para utilizar o Azure PowerShell para iniciar sessão e executar operações subsequentes contra o Azure Storage utilizando credenciais AZure AD, crie um contexto de armazenamento para fazer referência à conta de armazenamento e inclua o `-UseConnectedAccount` parâmetro.

O exemplo a seguir mostra como criar um recipiente numa nova conta de armazenamento da Azure PowerShell utilizando as suas credenciais AZure AD. Lembre-se de substituir os valores do espaço reservado nos suportes angulares com os seus próprios valores:

1. Inscreva-se na sua conta Azure com o comando [Connect-AzAccount:](/powershell/module/az.accounts/connect-azaccount)

    ```powershell
    Connect-AzAccount
    ```

    Para obter mais informações sobre a inscrição no Azure com a PowerShell, consulte [Iniciar sessão com a Azure PowerShell](/powershell/azure/authenticate-azureps).

1. Crie um grupo de recursos Azure chamando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). 

    ```powershell
    $resourceGroup = "sample-resource-group-ps"
    $location = "eastus"
    New-AzResourceGroup -Name $resourceGroup -Location $location
    ```

1. Crie uma conta de armazenamento chamando [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount).

    ```powershell
    $storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
      -Name "<storage-account>" `
      -SkuName Standard_LRS `
      -Location $location `
    ```

1. Obtenha o contexto da conta de armazenamento que especifica a nova conta de armazenamento, ligando para [o New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext). Ao agir numa conta de armazenamento, pode referenciar o contexto em vez de passar repetidamente nas credenciais. Inclua o `-UseConnectedAccount` parâmetro para ligar para quaisquer operações de dados subsequentes usando as suas credenciais AZure AD:

    ```powershell
    $ctx = New-AzStorageContext -StorageAccountName "<storage-account>" -UseConnectedAccount
    ```

1. Antes de criar o recipiente, atribua a [função de Contribuinte de Dados de Armazenamento blob](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) a si mesmo. Apesar de ser o proprietário da conta, necessita de permissões explícitas para realizar operações de dados contra a conta de armazenamento. Para obter mais informações sobre a atribuição de funções Azure, consulte [utilizar o portal Azure para atribuir uma função Azure para acesso a dados de bolhas e filas](../common/storage-auth-aad-rbac.md).

    > [!IMPORTANT]
    > As atribuições de funções azure podem demorar alguns minutos a propagar-se.

1. Crie um recipiente chamando [New-AzStorageContainer](/powershell/module/az.storage/new-azstoragecontainer). Como esta chamada utiliza o contexto criado nos passos anteriores, o recipiente é criado utilizando as suas credenciais AZure AD.

    ```powershell
    $containerName = "sample-container"
    New-AzStorageContainer -Name $containerName -Context $ctx
    ```

## <a name="next-steps"></a>Passos seguintes

- [Use o PowerShell para atribuir uma função Azure para acesso a dados de bolhas e filas](../common/storage-auth-aad-rbac-powershell.md)
- [Autorizar o acesso a dados de blob e fila com identidades geridas para recursos Azure](../common/storage-auth-aad-msi.md)
