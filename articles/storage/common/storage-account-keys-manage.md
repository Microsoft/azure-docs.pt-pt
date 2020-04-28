---
title: Gerir chaves de acesso à conta
titleSuffix: Azure Storage
description: Aprenda a visualizar, gerir e rodar as chaves de acesso à sua conta de armazenamento.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 04/24/2020
ms.author: tamram
ms.openlocfilehash: 4ade2c2e60373298eecf4e85df7fffeae4f45207
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "82176634"
---
# <a name="manage-storage-account-access-keys"></a>Gerir as chaves de acesso à conta de armazenamento

Ao criar uma conta de armazenamento, o Azure gera duas chaves de acesso à conta de armazenamento de 512 bits. Estas chaves podem ser utilizadas para autorizar o acesso aos dados na sua conta de armazenamento através de autorização de Chave Partilhada.

A Microsoft recomenda que utilize o Cofre de Chaves Azure para gerir as suas teclas de acesso e que rode e regenera regularmente as suas teclas. A utilização do Cofre de Chaves Azure facilita a rotação das chaves sem interrupção das suas aplicações. Também pode rodar manualmente as chaves.

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

## <a name="view-account-access-keys"></a>Ver chaves de acesso à conta

Pode visualizar e copiar as chaves de acesso da sua conta com o portal Azure, PowerShell ou Azure CLI. O portal Azure também fornece uma cadeia de ligação para a sua conta de armazenamento que pode copiar.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Para visualizar e copiar as chaves de acesso à sua conta de armazenamento ou a linha de ligação do portal Azure:

1. Navegue para a sua conta de armazenamento no [portal Azure.](https://portal.azure.com)
1. Em **Definições**, selecione **Chaves de acesso**. As chaves de acesso da conta são apresentadas, bem como a cadeia de ligação completa para cada chave.
1. Localize o valor **chave** em **tecla1**e clique no botão **Copiar** a chave da conta.
1. Alternadamente, pode copiar toda a cadeia de ligação. Encontre o valor da **Cadeia de ligação** em **key1**e clique no botão **Copiar** para copiar a cadeia de ligação.

    :::image type="content" source="media/storage-account-keys-manage/portal-connection-string.png" alt-text="Screenshot mostrando como ver chaves de acesso no portal Azure":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para recuperar as chaves de acesso à sua conta com o PowerShell, ligue para o comando [Get-AzStorageAccountKey.](/powershell/module/az.Storage/Get-azStorageAccountKey)

O exemplo que se segue recupera a primeira chave. Para recuperar a segunda `Value[1]` tecla, use em vez de `Value[0]`. Lembre-se de substituir os valores do espaço reservado em parênteses por valores próprios.

```powershell
$storageAccountKey = `
    (Get-AzStorageAccountKey `
    -ResourceGroupName <resource-group> `
    -Name <storage-account>).Value[0]
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para listar as chaves de acesso à sua conta com o Azure CLI, ligue para o comando da lista de chaves de [armazenamento az,](/cli/azure/storage/account/keys#az-storage-account-keys-list) como mostra o seguinte exemplo. Lembre-se de substituir os valores do espaço reservado em parênteses por valores próprios. 

```azurecli-interactive
az storage account keys list \
  --resource-group <resource-group> \
  --account-name <storage-account>
```

---

Pode utilizar qualquer uma das duas teclas para aceder ao Armazenamento Azure, mas em geral é uma boa prática utilizar a primeira tecla e reservar a utilização da segunda tecla para quando estiver a rodar chaves.

Para visualizar ou ler as chaves de acesso de uma conta, o utilizador deve ser um Administrador de Serviço, ou deve ser atribuído uma função RBAC que inclua a **Microsoft.Storage/storageAccounts/listkeys/action**. Algumas funções de RBAC incorporadas que incluem esta ação são as funções **de Proprietário,** **Contribuinte**e Serviço de Serviço chave de conta de **armazenamento.** Para obter mais informações sobre o papel de Administrador de Serviço, consulte funções de administrador de [subscrição Classic, funções Azure RBAC e funções Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md). Para obter informações detalhadas sobre as funções incorporadas para o Armazenamento Azure, consulte a secção de **armazenamento** em [papel azure incorporado para o Azure RBAC.](../../role-based-access-control/built-in-roles.md#storage)

## <a name="use-azure-key-vault-to-manage-your-access-keys"></a>Utilize o Cofre de Chaves Azure para gerir as suas chaves de acesso

A Microsoft recomenda a utilização do Cofre de Chaves Azure para gerir e rodar as suas teclas de acesso. A sua aplicação pode aceder de forma segura às suas chaves no Cofre de Chaves, para que possa evitar armazená-las com o seu código de aplicação. Para obter mais informações sobre a utilização do Key Vault para gestão de chaves, consulte os seguintes artigos:

- [Gerir as chaves da conta de armazenamento com o Cofre de Chaves Azure e a PowerShell](../../key-vault/secrets/overview-storage-keys-powershell.md)
- [Gerir as chaves da conta de armazenamento com o Cofre de Chaves Azure e o Azure CLI](../../key-vault/secrets/overview-storage-keys.md)

## <a name="manually-rotate-access-keys"></a>Manualmente, roda manuais chaves de acesso

A Microsoft recomenda que rode periodicamente as suas chaves de acesso para ajudar a manter a sua conta de armazenamento segura. Se possível, utilize o Cofre de Chaves Azure para gerir as suas chaves de acesso. Se não estiver a utilizar o Cofre de Chaves, terá de rodar manualmente as chaves.

Duas chaves de acesso são atribuídas para que possa rodar as chaves. Ter duas chaves garante que a sua aplicação mantém o acesso ao Armazenamento Azure durante todo o processo.

> [!WARNING]
> Regenerar as chaves de acesso pode afetar quaisquer aplicações ou serviços Azure que estejam dependentes da chave da conta de armazenamento. Todos os clientes que utilizem a chave da conta para aceder à conta de armazenamento devem ser atualizados para utilizar a nova chave, incluindo serviços de mídia, aplicações de nuvem, ambiente de trabalho e mobile, e aplicações gráficas de interface de utilizador para o Armazenamento Azure, como [o Azure Storage Explorer.](https://azure.microsoft.com/features/storage-explorer/)

# <a name="portal"></a>[Portal](#tab/azure-portal)

Para rodar as chaves de acesso à sua conta de armazenamento no portal Azure:

1. Atualize as cadeias de ligação no seu código de aplicação para fazer referência à chave de acesso secundária para a conta de armazenamento.
1. Navegue para a sua conta de armazenamento no [portal Azure.](https://portal.azure.com)
1. Em **Definições**, selecione **Chaves de acesso**.
1. Para regenerar a chave de acesso primária para a sua conta de armazenamento, selecione o botão **Regenerar** ao lado da chave de acesso primária.
1. Atualize as cadeias de ligação no código para fazer referência à nova chave de acesso primária.
1. Volte a gerar a chave de acesso secundária da mesma forma.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para rodar as chaves de acesso à sua conta de armazenamento com a PowerShell:

1. Atualize as cadeias de ligação no seu código de aplicação para fazer referência à chave de acesso secundária para a conta de armazenamento.
1. Ligue para o comando [New-AzStorageAccountKey](/powershell/module/az.storage/new-azstorageaccountkey) para regenerar a chave de acesso primária, como mostra o seguinte exemplo:

    ```powershell
    New-AzStorageAccountKey -ResourceGroupName <resource-group> `
      -Name <storage-account> `
      -KeyName key1
    ```

1. Atualize as cadeias de ligação no código para fazer referência à nova chave de acesso primária.
1. Volte a gerar a chave de acesso secundária da mesma forma. Para regenerar a chave `key2` secundária, use como `key1`nome-chave em vez de .

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para rodar as chaves de acesso à sua conta de armazenamento com o Azure CLI:

1. Atualize as cadeias de ligação no seu código de aplicação para fazer referência à chave de acesso secundária para a conta de armazenamento.
1. Ligue para as chaves da conta de [armazenamento az renovar](/cli/azure/storage/account/keys#az-storage-account-keys-renew) o comando para regenerar a chave de acesso primário, como mostra o seguinte exemplo:

    ```azurecli-interactive
    az storage account keys renew \
      --resource-group <resource-group> \
      --account-name <storage-account>
      --key primary
    ```

1. Atualize as cadeias de ligação no código para fazer referência à nova chave de acesso primária.
1. Volte a gerar a chave de acesso secundária da mesma forma. Para regenerar a chave `key2` secundária, use como `key1`nome-chave em vez de .

---

> [!NOTE]
> A Microsoft recomenda a utilização de apenas uma das chaves em todas as suas aplicações ao mesmo tempo. Se utilizar a Chave 1 em alguns locais e a Chave 2 noutros, não poderá rodar as chaves sem que alguma aplicação perca acesso.

Para rodar as chaves de acesso de uma conta, o utilizador deve ser um Administrador de Serviço, ou deve ser atribuído uma função RBAC que inclua a **Microsoft.Storage/storageAccounts/regeneratekey/action**. Algumas funções de RBAC incorporadas que incluem esta ação são as funções **de Proprietário,** **Contribuinte**e Serviço de Serviço chave de conta de **armazenamento.** Para obter mais informações sobre o papel de Administrador de Serviço, consulte funções de administrador de [subscrição Classic, funções Azure RBAC e funções Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md). Para obter informações detalhadas sobre as funções rBAC incorporadas para o Armazenamento Azure, consulte a secção de **armazenamento** em [papel azure incorporado para o Azure RBAC.](../../role-based-access-control/built-in-roles.md#storage)

## <a name="next-steps"></a>Passos seguintes

- [Visão geral da conta de armazenamento azure](storage-account-overview.md)
- [Criar uma conta de armazenamento](storage-account-create.md)
