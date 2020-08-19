---
title: Executar comandos PowerShell com credenciais AD AZure para aceder a dados de blob ou fila
titleSuffix: Azure Storage
description: O PowerShell suporta a assinatura com credenciais AZure AD para executar comandos na bolha de armazenamento Azure e dados de filas. Um sinal de acesso é fornecido para a sessão e usado para autorizar operações de chamada. As permissões dependem do papel Azure atribuído ao diretor de segurança da Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/18/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 75ca39ad00966928bb7887cb14255470b17579dd
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88589151"
---
# <a name="run-powershell-commands-with-azure-ad-credentials-to-access-blob-or-queue-data"></a>Executar comandos PowerShell com credenciais AD AZure para aceder a dados de blob ou fila

O Azure Storage fornece extensões para o PowerShell que lhe permitem iniciar sing e executar comandos de script com credenciais Azure Ative Directory (Azure AD). Quando você assinar no PowerShell com credenciais Azure AD, um token de acesso OAuth 2.0 é devolvido. Este token é automaticamente utilizado pela PowerShell para autorizar operações de dados subsequentes contra o armazenamento de Blob ou Queue. Para operações apoiadas, já não precisa de passar uma chave de conta ou um sinal SAS com o comando.

Pode atribuir permissões a um principal de segurança AD AZure através de um controlo de acesso baseado em funções (RBAC). Para obter mais informações sobre as funções Azure no Azure Storage, consulte [Gerir os direitos de acesso aos dados de armazenamento do Azure com o RBAC.](storage-auth-aad-rbac.md)

## <a name="supported-operations"></a>Operações apoiadas

As extensões de Armazenamento Azure são suportadas para operações em dados de blob e fila. Quais as operações que pode ligar dependem das permissões concedidas ao diretor de segurança da AD Azure com a qual se inscreve na PowerShell. As permissões para os recipientes ou filas de armazenamento Azure são atribuídas através do RBAC. Por exemplo, se lhe foi atribuída a função **De Leitore de Dados Blob,** então pode executar comandos de script que lêem dados a partir de um recipiente ou fila. Se lhe foi atribuída a **função De Contribuinte de Dados Blob,** então pode executar comandos de script que leiam, escrevam ou apaguem um recipiente ou fila ou os dados que contêm.

Para obter mais informações sobre as permissões necessárias para cada operação de Armazenamento Azure num recipiente ou fila, consulte [as operações de armazenamento de chamadas com fichas OAuth](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens).  

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

1. Antes de criar o recipiente, atribua a [função de Contribuinte de Dados de Armazenamento blob](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) a si mesmo. Apesar de ser o proprietário da conta, necessita de permissões explícitas para realizar operações de dados contra a conta de armazenamento. Para obter mais informações sobre a atribuição de funções Azure, consulte [o Acesso do Grant à bolha de Azure e dados de fila com o RBAC no portal Azure](storage-auth-aad-rbac.md).

    > [!IMPORTANT]
    > As atribuições de funções azure podem demorar alguns minutos a propagar-se.

1. Crie um recipiente chamando [New-AzStorageContainer](/powershell/module/az.storage/new-azstoragecontainer). Como esta chamada utiliza o contexto criado nos passos anteriores, o recipiente é criado utilizando as suas credenciais AZure AD.

    ```powershell
    $containerName = "sample-container"
    New-AzStorageContainer -Name $containerName -Context $ctx
    ```

## <a name="next-steps"></a>Passos seguintes

- [Use o PowerShell para atribuir uma função Azure para acesso a dados de bolhas e filas](storage-auth-aad-rbac-powershell.md)
- [Autorizar o acesso a dados de blob e fila com identidades geridas para recursos Azure](storage-auth-aad-msi.md)
