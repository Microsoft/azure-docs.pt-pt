---
title: Gerir chaves de acesso à conta
titleSuffix: Azure Storage
description: Saiba como ver, gerir e rodar as teclas de acesso à conta de armazenamento.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 04/24/2020
ms.author: tamram
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: e5ea94fea00771b64634d6c28a7879fabb195f09
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89069664"
---
# <a name="manage-storage-account-access-keys"></a>Gerir chaves de acesso à conta de armazenamento

Quando cria uma conta de armazenamento, o Azure gera duas chaves de acesso à conta de armazenamento de 512 bits. Estas chaves podem ser usadas para autorizar o acesso aos dados na sua conta de armazenamento através da autorização da Chave Partilhada.

A Microsoft recomenda que utilize o Azure Key Vault para gerir as suas teclas de acesso e que rode regularmente e regenerar as suas chaves. A utilização do Cofre da Chave Azure facilita a rotação das chaves sem interrupção das suas aplicações. Também pode rodar manualmente as suas chaves.

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

## <a name="view-account-access-keys"></a>Ver chaves de acesso à conta

Pode visualizar e copiar as chaves de acesso à sua conta com o portal Azure, PowerShell ou Azure CLI. O portal Azure também fornece uma cadeia de ligação para a sua conta de armazenamento que pode copiar.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Para visualizar e copiar as chaves de acesso à sua conta de armazenamento ou cadeia de ligação a partir do portal Azure:

1. Navegue para a sua conta de armazenamento no [portal Azure.](https://portal.azure.com)
1. Em **Definições**, selecione **Chaves de acesso**. As chaves de acesso da conta são apresentadas, bem como a cadeia de ligação completa para cada chave.
1. Localize o valor **chave** na **tecla1**e clique no botão **Copiar** para copiar a tecla da conta.
1. Alternadamente, pode copiar toda a cadeia de ligação. Encontre o valor da **Cadeia de ligação** em **key1**e clique no botão **Copiar** para copiar a cadeia de ligação.

    :::image type="content" source="media/storage-account-keys-manage/portal-connection-string.png" alt-text="Screenshot mostrando como ver as chaves de acesso no portal Azure":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para recuperar as chaves de acesso à sua conta com o PowerShell, ligue para o comando [Get-AzStorageAccountKey.](/powershell/module/az.Storage/Get-azStorageAccountKey)

O exemplo a seguir recupera a primeira chave. Para recuperar a segunda chave, use `Value[1]` em vez de `Value[0]` . Lembre-se de substituir os valores do espaço reservado nos parênteses pelos seus próprios valores.

```powershell
$storageAccountKey = `
    (Get-AzStorageAccountKey `
    -ResourceGroupName <resource-group> `
    -Name <storage-account>).Value[0]
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para listar as chaves de acesso à sua conta com o Azure CLI, ligue para o comando da [lista de chaves de armazenamento AZ,](/cli/azure/storage/account/keys#az-storage-account-keys-list) como mostra o exemplo seguinte. Lembre-se de substituir os valores do espaço reservado nos parênteses pelos seus próprios valores. 

```azurecli-interactive
az storage account keys list \
  --resource-group <resource-group> \
  --account-name <storage-account>
```

---

Pode utilizar qualquer uma das duas teclas para aceder ao Azure Storage, mas em geral é uma boa prática usar a primeira chave e reservar a utilização da segunda chave para quando estiver a rodar as teclas.

Para ver ou ler as chaves de acesso de uma conta, o utilizador deve ser administrador de serviço ou deve ser atribuído a uma função Azure que inclua o **Microsoft.Storage/storageAcounts/listkeys/action**. Algumas funções incorporadas da Azure que incluem esta ação são as funções **de Proprietário,** **Contribuinte**e **Serviço de Serviço chave de armazenamento.** Para obter mais informações sobre a função de Administrador de Serviço, consulte [as funções de administrador de subscrição clássica, funções de Azure e AD AZure](../../role-based-access-control/rbac-and-directory-admin-roles.md). Para obter informações detalhadas sobre funções incorporadas para o Armazenamento Azure, consulte a secção **de armazenamento** em [funções incorporadas Azure para o Azure RBAC](../../role-based-access-control/built-in-roles.md#storage).

## <a name="use-azure-key-vault-to-manage-your-access-keys"></a>Use o Cofre de Chaves Azure para gerir as suas chaves de acesso

A Microsoft recomenda a utilização do Cofre da Chave Azure para gerir e rodar as suas teclas de acesso. A sua aplicação pode aceder de forma segura às suas chaves no Key Vault, para evitar que as guarde com o seu código de aplicação. Para obter mais informações sobre a utilização do Key Vault para gestão de chaves, consulte os seguintes artigos:

- [Gerir chaves de conta de armazenamento com cofre de chave Azure e PowerShell](../../key-vault/secrets/overview-storage-keys-powershell.md)
- [Gerir chaves de conta de armazenamento com Azure Key Vault e o Azure CLI](../../key-vault/secrets/overview-storage-keys.md)

## <a name="manually-rotate-access-keys"></a>Rode manualmente as teclas de acesso

A Microsoft recomenda que rode as teclas de acesso periodicamente para ajudar a manter a sua conta de armazenamento segura. Se possível, utilize o Cofre da Chave Azure para gerir as suas chaves de acesso. Se não estiver a utilizar o Key Vault, terá de rodar as chaves manualmente.

São atribuídas duas chaves de acesso para que possa rodar as suas chaves. Ter duas teclas garante que a sua aplicação mantém o acesso ao Azure Storage durante todo o processo.

> [!WARNING]
> A regeneração das chaves de acesso pode afetar quaisquer aplicações ou serviços Azure que dependam da chave da conta de armazenamento. Todos os clientes que utilizem a chave de conta para aceder à conta de armazenamento devem ser atualizados para utilizar a nova chave, incluindo serviços de mídia, aplicações de ambiente de trabalho, ambiente de trabalho e mobile, e aplicações de interface gráfica de utilizador para armazenamento [Azure, como o Azure Storage Explorer.](https://azure.microsoft.com/features/storage-explorer/)

# <a name="portal"></a>[Portal](#tab/azure-portal)

Para rodar as chaves de acesso à sua conta de armazenamento no portal Azure:

1. Atualize as cadeias de ligação no seu código de aplicação para fazer referência à chave de acesso secundário para a conta de armazenamento.
1. Navegue para a sua conta de armazenamento no [portal Azure.](https://portal.azure.com)
1. Em **Definições**, selecione **Chaves de acesso**.
1. Para regenerar a chave de acesso primária para a sua conta de armazenamento, selecione o botão **Regenerar** ao lado da tecla de acesso primário.
1. Atualize as cadeias de ligação no código para fazer referência à nova chave de acesso primária.
1. Regenere a chave de acesso secundária da mesma forma.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para rodar as teclas de acesso à sua conta de armazenamento com o PowerShell:

1. Atualize as cadeias de ligação no seu código de aplicação para fazer referência à chave de acesso secundário para a conta de armazenamento.
1. Ligue para o comando [New-AzStorageAccountKey](/powershell/module/az.storage/new-azstorageaccountkey) para regenerar a chave de acesso primário, como mostra o seguinte exemplo:

    ```powershell
    New-AzStorageAccountKey -ResourceGroupName <resource-group> `
      -Name <storage-account> `
      -KeyName key1
    ```

1. Atualize as cadeias de ligação no código para fazer referência à nova chave de acesso primária.
1. Regenere a chave de acesso secundária da mesma forma. Para regenerar a chave secundária, use `key2` como nome chave em vez de `key1` .

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para rodar as chaves de acesso à sua conta de armazenamento com o Azure CLI:

1. Atualize as cadeias de ligação no seu código de aplicação para fazer referência à chave de acesso secundário para a conta de armazenamento.
1. Ligue para as chaves da [conta de armazenamento az renovar](/cli/azure/storage/account/keys#az-storage-account-keys-renew) o comando para regenerar a chave de acesso primário, como mostra o exemplo seguinte:

    ```azurecli-interactive
    az storage account keys renew \
      --resource-group <resource-group> \
      --account-name <storage-account>
      --key primary
    ```

1. Atualize as cadeias de ligação no código para fazer referência à nova chave de acesso primária.
1. Regenere a chave de acesso secundária da mesma forma. Para regenerar a chave secundária, use `key2` como nome chave em vez de `key1` .

---

> [!NOTE]
> A Microsoft recomenda a utilização de apenas uma das chaves em todas as suas aplicações ao mesmo tempo. Se utilizar a Chave 1 em alguns lugares e a Chave 2 noutros, não poderá rodar as chaves sem que alguma aplicação perca acesso.

Para rodar as teclas de acesso de uma conta, o utilizador deve ser administrador de serviço ou deve ser atribuído a uma função Azure que inclua o **Microsoft.Storage/storageAccounts/regeneratekey/action**. Algumas funções incorporadas da Azure que incluem esta ação são as funções **de Proprietário,** **Contribuinte**e **Serviço de Serviço chave de armazenamento.** Para obter mais informações sobre a função de Administrador de Serviço, consulte [as funções de administrador de subscrição clássica, funções de Azure e AD AZure](../../role-based-access-control/rbac-and-directory-admin-roles.md). Para obter informações detalhadas sobre as funções incorporadas do Azure para o Armazenamento Azure, consulte a secção **de armazenamento** em [funções incorporadas Azure para o Azure RBAC](../../role-based-access-control/built-in-roles.md#storage).

## <a name="next-steps"></a>Passos seguintes

- [Visão geral da conta de armazenamento Azure](storage-account-overview.md)
- [Criar uma conta de armazenamento](storage-account-create.md)
