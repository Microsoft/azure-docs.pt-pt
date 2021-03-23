---
title: Aplicar um bloqueio do Gestor de Recursos Azure numa conta de armazenamento
titleSuffix: Azure Storage
description: Saiba como aplicar um bloqueio do Azure Resource Manager numa conta de armazenamento.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/09/2021
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 79b88ad58a2eb95a48a140b3b98d606af495cb94
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104799790"
---
# <a name="apply-an-azure-resource-manager-lock-to-a-storage-account"></a>Aplicar um bloqueio do Gestor de Recursos Azure numa conta de armazenamento

A Microsoft recomenda bloquear todas as suas contas de armazenamento com um bloqueio Azure Resource Manager para evitar a eliminação acidental ou maliciosa da conta de armazenamento. Existem dois tipos de bloqueios de recursos do Azure Resource Manager:

- Um **bloqueio Não-Proibido** impede os utilizadores de eliminar uma conta de armazenamento, mas permite ler e modificar a sua configuração.
- Um bloqueio **ReadOnly** impede que os utilizadores apaguem uma conta de armazenamento ou modifiquem a sua configuração, mas permite a leitura da configuração.

Para obter mais informações sobre as fechaduras do Azure Resource Manager, consulte [os recursos de bloqueio para evitar alterações.](../../azure-resource-manager/management/lock-resources.md)

> [!CAUTION]
> O bloqueio de uma conta de armazenamento não protege os contentores ou bolhas nessa conta de serem apagados ou substituídos. Para obter mais informações sobre como proteger os dados do blob, consulte a [visão geral da proteção de dados](../blobs/data-protection-overview.md).

## <a name="configure-an-azure-resource-manager-lock"></a>Configure um bloqueio de gestor de recursos Azure

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

Para configurar uma fechadura numa conta de armazenamento com o portal Azure, siga estes passos:

1. Navegue para a sua conta de armazenamento no portal do Azure.
1. Na secção **Definições,** selecione **Fechaduras**.
1. Selecione **Adicionar**.
1. Forneça um nome para o bloqueio de recursos e especifique o tipo de bloqueio. Adicione uma nota sobre a fechadura, se desejar.

    :::image type="content" source="media/lock-account-resource/lock-storage-account.png" alt-text="Screenshot mostrando como bloquear uma conta de armazenamento com uma fechadura Não-Delete":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para configurar uma fechadura numa conta de armazenamento com a PowerShell, certifique-se primeiro de que instalou o [módulo Az PowerShell](https://www.powershellgallery.com/packages/Az). Em seguida, ligue para o comando [New-AzResourceLock](/powershell/module/az.resources/new-azresourcelock) e especifique o tipo de bloqueio que pretende criar, como mostra o seguinte exemplo:

```azurepowershell
New-AzResourceLock -LockLevel CanNotDelete `
    -LockName <lock> `
    -ResourceName <storage-account> `
    -ResourceType Microsoft.Storage/storageAccounts `
    -ResourceGroupName <resource-group>
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para configurar um bloqueio numa conta de armazenamento com o Azure CLI, ligue para o comando [az lock create](/cli/azure/lock#az_lock_create) e especifique o tipo de bloqueio que pretende criar, como mostra o seguinte exemplo:

```azurecli
az lock create \
    --name <lock> \
    --resource-group <resource-group> \
    --resource <storage-account> \
    --lock-type CanNotDelete \
    --resource-type Microsoft.Storage/storageAccounts
```

---

## <a name="authorizing-data-operations-when-a-readonly-lock-is-in-effect"></a>Autorizar operações de dados quando um bloqueio ReadOnly estiver em vigor

Quando uma fechadura **ReadOnly** é aplicada a uma conta de armazenamento, a operação ['Chaves listas'](/rest/api/storagerp/storageaccounts/listkeys) fica bloqueada para essa conta de armazenamento. A operação **'List Keys'** é uma operação HTTPS POST e todas as operações POST são impedidas quando um bloqueio **ReadOnly** é configurado para a conta. A operação **Chaves listas** devolve as teclas de acesso à conta, que podem ser usadas para ler e escrever em quaisquer dados na conta de armazenamento.

Se um cliente estiver na posse das chaves de acesso à conta no momento em que o bloqueio é aplicado na conta de armazenamento, então esse cliente pode continuar a usar as chaves para aceder aos dados. No entanto, os clientes que não tenham acesso às chaves terão de utilizar credenciais do Azure Ative Directory (Azure AD) para aceder a dados de blob ou fila na conta de armazenamento.

Os utilizadores do portal Azure podem ser afetados quando é aplicado um bloqueio **ReadOnly,** caso tenham acedido previamente a dados de blob ou de fila no portal com as teclas de acesso à conta. Após a aplicação do bloqueio, os utilizadores do portal terão de utilizar credenciais AZure AD para aceder a dados de blob ou fila no portal. Para tal, um utilizador deve ter pelo menos duas funções RBAC atribuídas a eles: a função de Leitor de Gestor de Recursos Azure no mínimo, e uma das funções de acesso a dados de armazenamento Azure. Para mais informações, consulte um dos seguintes artigos:

- [Escolha como autorizar o acesso aos dados blob no portal Azure](../blobs/authorize-data-operations-portal.md)
- [Escolha como autorizar o acesso aos dados da fila no portal Azure](../queues/authorize-data-operations-portal.md)

Os dados nos Ficheiros Azure ou no serviço Table podem tornar-se inacessíveis aos clientes que tenham acedida previamente com as chaves da conta. Como melhor prática, se tiver de aplicar um bloqueio **ReadOnly** numa conta de armazenamento, em seguida, mova as cargas de trabalho dos ficheiros Azure e do serviço de tabela para uma conta de armazenamento que não esteja bloqueada com um bloqueio **ReadOnly.**

## <a name="next-steps"></a>Passos seguintes

- [Descrição geral da proteção de dados](../blobs/data-protection-overview.md)
- [Bloquear recursos para evitar alterações](../../azure-resource-manager/management/lock-resources.md)
