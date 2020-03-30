---
title: Executar comandos PowerShell com credenciais AD Azure para aceder a dados blob ou fila
titleSuffix: Azure Storage
description: A PowerShell suporta a assinatura com credenciais da AD Azure para executar comandos em blob de armazenamento Azure e dados de filas. É fornecido um sinal de acesso para a sessão e utilizado para autorizar operações de chamada. As permissões dependem da função RBAC atribuída ao diretor de segurança da AD Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/30/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 373b939ac63f31ccaf6a9f01fac92920e19074ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75553452"
---
# <a name="run-powershell-commands-with-azure-ad-credentials-to-access-blob-or-queue-data"></a>Executar comandos PowerShell com credenciais AD Azure para aceder a dados blob ou fila

O Azure Storage fornece extensões para powerShell que lhe permitem iniciar sessão e executar comandos de scriptcom credenciais Azure Ative Directory (Azure AD). Quando você inscreveu na PowerShell com credenciais Azure AD, um token de acesso OAuth 2.0 é devolvido. Esta ficha é automaticamente utilizada pela PowerShell para autorizar operações de dados subsequentes contra o armazenamento de Blob ou Fila. Para operações apoiadas, já não precisa de passar uma chave de conta ou um símbolo SAS com o comando.

Pode atribuir permissões a dados de blob e fila a um diretor de segurança da AD Azure através do controlo de acesso baseado em funções (RBAC). Para obter mais informações sobre as funções RBAC no Armazenamento Azure, consulte Gerir os direitos de acesso aos dados de [Armazenamento Azure com RBAC](storage-auth-aad-rbac.md).

## <a name="supported-operations"></a>Operações apoiadas

As extensões de Armazenamento Azure são suportadas para operações em dados de blob e fila. Quais as operações que pode ligar depende das permissões concedidas ao diretor de segurança da AD Azure com as quais se inscreveu na PowerShell. As permissões para recipientes ou filas de armazenamento Azure são atribuídas via RBAC. Por exemplo, se lhe foi atribuído o papel **de Blob Data Reader,** então pode executar comandos de script que lêem dados de um recipiente ou fila. Se lhe foi atribuída a função **Blob Data Contributor,** então pode executar comandos de script que leiam, escrevem ou apaguem um recipiente ou fila ou os dados que contêm.

Para obter mais informações sobre as permissões necessárias para cada operação de armazenamento azure num recipiente ou fila, consulte as operações de armazenamento de [chamadas com fichas OAuth](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens).  

## <a name="call-powershell-commands-using-azure-ad-credentials"></a>Call PowerShell comanda usando credenciais Azure AD

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Para utilizar o Azure PowerShell para iniciar sessão e executar operações subsequentes contra o Armazenamento Azure `-UseConnectedAccount` utilizando credenciais De AD Azure, crie um contexto de armazenamento para fazer referência à conta de armazenamento e inclua o parâmetro.

O exemplo que se segue mostra como criar um contentor numa nova conta de armazenamento da Azure PowerShell utilizando as suas credenciais Azure AD. Lembre-se de substituir os valores do espaço reservado em suportes angulares por valores próprios:

1. Inscreva-se na sua conta Azure com o comando [Connect-AzAccount:](/powershell/module/az.accounts/connect-azaccount)

    ```powershell
    Connect-AzAccount
    ```

    Para mais informações sobre a assinatura no Azure com a PowerShell, consulte [O Signin com o Azure PowerShell](/powershell/azure/authenticate-azureps).

1. Crie um grupo de recursos Azure, chamando [new-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). 

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

1. Obtenha o contexto da conta de armazenamento que especifica a nova conta de armazenamento, chamando [New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext). Ao agir numa conta de armazenamento, pode fazer referência ao contexto em vez de passar repetidamente as credenciais. Inclua `-UseConnectedAccount` o parâmetro para ligar para quaisquer operações de dados subsequentes utilizando as suas credenciais De AD Azure:

    ```powershell
    $ctx = New-AzStorageContext -StorageAccountName "<storage-account>" -UseConnectedAccount
    ```

1. Antes de criar o recipiente, atribua a função de Contribuinte de [Dados blob](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) de armazenamento a si mesmo. Apesar de ser o proprietário da conta, necessita de permissões explícitas para efetuar operações de dados contra a conta de armazenamento. Para obter mais informações sobre a atribuição de funções RBAC, consulte o Acesso ao Grant à blob Azure e aos dados de [fila com rBAC no portal Azure.](storage-auth-aad-rbac.md)

    > [!IMPORTANT]
    > As atribuições de funções RBAC podem demorar alguns minutos a propagar-se.

1. Crie um recipiente chamando [New-AzStorageContainer](/powershell/module/az.storage/new-azstoragecontainer). Uma vez que esta chamada utiliza o contexto criado nos passos anteriores, o recipiente é criado utilizando as suas credenciais De AD Azure.

    ```powershell
    $containerName = "sample-container"
    New-AzStorageContainer -Name $containerName -Context $ctx
    ```

## <a name="next-steps"></a>Passos seguintes

- [Use powerShell para atribuir uma função RBAC para acesso a dados de blob e fila](storage-auth-aad-rbac-powershell.md)
- [Autorizar acesso a dados blob e fila com identidades geridas para recursos Azure](storage-auth-aad-msi.md)
