---
title: Executar comandos PowerShell com credenciais AD AZure para aceder a dados de fila
titleSuffix: Azure Storage
description: O PowerShell suporta a assinatura com credenciais AZure AD para executar comandos em dados de fila de armazenamento Azure. Um sinal de acesso é fornecido para a sessão e usado para autorizar operações de chamada. As permissões dependem do papel Azure atribuído ao diretor de segurança da Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 09/14/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: queues
ms.openlocfilehash: 3636b0366dfe687c4825ec1a16c5e8094a7db10b
ms.sourcegitcommit: 295db318df10f20ae4aa71b5b03f7fb6cba15fc3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2020
ms.locfileid: "94637406"
---
# <a name="run-powershell-commands-with-azure-ad-credentials-to-access-queue-data"></a>Executar comandos PowerShell com credenciais AD AZure para aceder a dados de fila

O Azure Storage fornece extensões para o PowerShell que lhe permitem iniciar sing e executar comandos de script com credenciais Azure Ative Directory (Azure AD). Quando você assinar no PowerShell com credenciais Azure AD, um token de acesso OAuth 2.0 é devolvido. Este token é automaticamente utilizado pela PowerShell para autorizar operações de dados subsequentes contra o armazenamento da fila. Para operações apoiadas, já não precisa de passar uma chave de conta ou um sinal SAS com o comando.

Pode atribuir permissões para os dados de fila a um diretor de segurança Azure AD através do controlo de acesso baseado em funções Azure (Azure RBAC). Para obter mais informações sobre as funções Azure no Azure Storage, consulte [Gerir os direitos de acesso aos dados de armazenamento Azure com o Azure RBAC](../common/storage-auth-aad-rbac-portal.md).

## <a name="supported-operations"></a>Operações apoiadas

As extensões de Armazenamento Azure são suportadas para operações em dados de fila. Quais as operações que pode ligar dependem das permissões concedidas ao diretor de segurança da AD Azure com a qual se inscreve na PowerShell. As permissões para as filas de armazenamento Azure são atribuídas através do Azure RBAC. Por exemplo, se lhe foi atribuído o papel de **Leitor de Dados** de Fila, então pode executar comandos de script que lêem dados a partir de uma fila. Se lhe foi atribuída a **função de Contribuinte de Dados de Fila,** então pode executar comandos de script que leiam, escrevam ou apaguem uma fila ou os dados que contêm.

Para obter mais informações sobre as permissões necessárias para cada operação de Armazenamento Azure em uma fila, consulte [as operações de armazenamento de chamadas com fichas OAuth](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens).

## <a name="call-powershell-commands-using-azure-ad-credentials"></a>Ligue para comandos PowerShell usando credenciais Azure AD

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Para utilizar o Azure PowerShell para iniciar sessão e executar operações subsequentes contra o Azure Storage utilizando credenciais AZure AD, crie um contexto de armazenamento para fazer referência à conta de armazenamento e inclua o `-UseConnectedAccount` parâmetro.

O exemplo a seguir mostra como criar uma fila numa nova conta de armazenamento da Azure PowerShell utilizando as suas credenciais AZure AD. Lembre-se de substituir os valores do espaço reservado nos suportes angulares com os seus próprios valores:

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

1. Antes de criar a fila, atribua o papel [de Contribuinte de Dados de Fila de Armazenamento](../../role-based-access-control/built-in-roles.md#storage-queue-data-contributor) para si mesmo. Apesar de ser o proprietário da conta, necessita de permissões explícitas para realizar operações de dados contra a conta de armazenamento. Para obter mais informações sobre a atribuição de funções Azure, consulte [utilizar o portal Azure para atribuir uma função Azure para acesso a dados de bolhas e filas](../common/storage-auth-aad-rbac-portal.md).

    > [!IMPORTANT]
    > As atribuições de funções azure podem demorar alguns minutos a propagar-se.

1. Crie uma fila chamando [New-AzStorageQueue](/powershell/module/az.storage/new-azstoragequeue). Como esta chamada utiliza o contexto criado nos passos anteriores, a fila é criada utilizando as suas credenciais AZure AD.

    ```powershell
    $queueName = "sample-queue"
    New-AzStorageQueue -Name $queueName -Context $ctx
    ```

## <a name="next-steps"></a>Passos seguintes

- [Use o PowerShell para atribuir uma função Azure para acesso a dados de bolhas e filas](../common/storage-auth-aad-rbac-powershell.md)
- [Autorizar o acesso a dados de blob e fila com identidades geridas para recursos Azure](../common/storage-auth-aad-msi.md)
