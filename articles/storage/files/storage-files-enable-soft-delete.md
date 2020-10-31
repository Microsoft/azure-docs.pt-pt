---
title: Ativar a eliminação suave - Azure ações de ficheiros
description: Saiba como permitir a eliminação suave das ações de ficheiros Azure para recuperação de dados e evitar a eliminação acidental.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 05/28/2020
ms.author: rogarana
ms.subservice: files
services: storage
ms.openlocfilehash: 7defa8611080027a67a0d1db1daa4c4a9d44edfe
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93126146"
---
# <a name="enable-soft-delete-on-azure-file-shares"></a>Permitir a eliminação suave nas ações de ficheiros Azure

O Azure Storage oferece uma exclusão suave para ações de ficheiros para que possa recuperar mais facilmente os seus dados quando estes são erroneamente eliminados por uma aplicação ou outro utilizador de conta de armazenamento. Para saber mais sobre a eliminação suave, consulte [Como evitar a eliminação acidental das ações de ficheiros Azure](storage-files-prevent-file-share-deletion.md).

As seguintes secções mostram como ativar e utilizar a eliminação suave para ações de ficheiros Azure numa conta de armazenamento existente:

# <a name="portal"></a>[Portal](#tab/azure-portal)

## <a name="getting-started"></a>Introdução

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
1. Navegue para a sua conta de armazenamento e selecione **Soft delete** in **File service** .
1. Selecione **Ativado** para **apagar suavemente a partilha de ficheiros** .
1. Selecione **O período de retenção de partilha** de ficheiros em dias e introduza uma série de escolhas.
1. **Selecione Guardar** para confirmar as definições de retenção de dados.

:::image type="content" source="media/storage-how-to-recover-deleted-account/enable-soft-delete-files.png" alt-text="Screenshot da conta de armazenamento apagar suavemente as definições. Realçando a secção de partilhas de ficheiros, ativar alternar, definir um período de retenção e guardar. Isto permitirá eliminar suavemente todas as ações de ficheiros na sua conta de armazenamento.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

## <a name="prerequisite"></a>Pré-requisito

Os cmdlets de eliminação suave estão disponíveis na versão [3.0.0](https://www.powershellgallery.com/packages/Az.Storage/3.0.0) do módulo Az.Storage. 

## <a name="getting-started-with-powershell"></a>Introdução ao PowerShell

Para permitir a eliminação suave, tem de atualizar as propriedades de serviço de um cliente de ficheiro. O exemplo a seguir permite a eliminação suave de todas as ações de ficheiros numa conta de armazenamento:

```azurepowershell-interactive
$rgName = "yourResourceGroupName"
$accountName = "yourStorageAccountName"

Update-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName -EnableShareDeleteRetentionPolicy $true -ShareRetentionDays 7
```

Pode verificar se a eliminação suave está ativada e ver a sua política de retenção com o seguinte comando:

```azurepowershell-interactive
Get-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName
```
---

## <a name="restore-soft-deleted-file-share"></a>Restaurar a partilha de ficheiros suaves eliminados

# <a name="portal"></a>[Portal](#tab/azure-portal)

Para restaurar uma partilha de ficheiros suavemente eliminada:

1. Navegue para a sua conta de armazenamento e selecione **ações de ficheiros.**
1. Na lâmina de partilha de ficheiros, **ativar as ações eliminadas do Show** para exibir quaisquer ações que tenham sido apagadas suavemente.

    Isto apresentará quaisquer ações atualmente num estado **eliminado.**

    :::image type="content" source="media/storage-how-to-recover-deleted-account/undelete-file-share.png" alt-text="Screenshot da conta de armazenamento apagar suavemente as definições. Realçando a secção de partilhas de ficheiros, ativar alternar, definir um período de retenção e guardar. Isto permitirá eliminar suavemente todas as ações de ficheiros na sua conta de armazenamento.":::

1. Selecione a partilha e selecione **undelete,** isto irá restaurar a partilha.

    Pode confirmar que a ação foi restaurada uma vez que o seu estado muda para **Ative** .

    :::image type="content" source="media/storage-how-to-recover-deleted-account/restored-file-share.png" alt-text="Screenshot da conta de armazenamento apagar suavemente as definições. Realçando a secção de partilhas de ficheiros, ativar alternar, definir um período de retenção e guardar. Isto permitirá eliminar suavemente todas as ações de ficheiros na sua conta de armazenamento.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Os cmdlets de eliminação suave estão disponíveis na versão 3.0.0 do módulo Az.Storage. Para restaurar uma partilha de ficheiros apagadas suaves, utilize o seguinte comando:

```azurepowershell-interactive
Restore-AzRmStorageShare -ResourceGroupName $rgname -StorageAccountName $accountName -DeletedShareVersion 01D5E2783BDCDA97
```
---

## <a name="disable-soft-delete"></a>Desativar a eliminação suave

Se desejar parar de usar exclusão suave ou eliminar permanentemente uma partilha de ficheiros, siga estas instruções:

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Navegue na sua conta de armazenamento e selecione **Soft Delete** em **Definições** .
1. Em **'Ações de Ficheiro'** selecione **Disabled** for **Soft delete for file shares** .
1. **Selecione Guardar** para confirmar as definições de retenção de dados.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/disable-soft-delete-files.png" alt-text="Screenshot da conta de armazenamento apagar suavemente as definições. Realçando a secção de partilhas de ficheiros, ativar alternar, definir um período de retenção e guardar. Isto permitirá eliminar suavemente todas as ações de ficheiros na sua conta de armazenamento.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Os cmdlets de eliminação suave estão disponíveis na versão 3.0.0 do módulo Az.Storage. Pode utilizar o seguinte comando para desativar a eliminação suave na sua conta de armazenamento:

```azurepowershell-interactive
Update-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName -EnableShareDeleteRetentionPolicy $false
```
---

## <a name="next-steps"></a>Passos seguintes

Para saber mais uma forma de proteção e recuperação de dados, consulte o nosso artigo [Visão geral das fotos de partilha para Ficheiros Azure](storage-snapshots-files.md).
