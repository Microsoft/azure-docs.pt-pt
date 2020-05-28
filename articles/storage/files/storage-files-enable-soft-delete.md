---
title: Ativar soft delete - Ações de ficheiroS Azure
description: Aprenda a permitir a eliminação suave das partilhas de ficheiros Azure para a recuperação de dados e evitando a eliminação acidental.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: rogarana
ms.subservice: files
services: storage
ms.openlocfilehash: 70081114995d46e7c5db7c9549cfce2fdff42975
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84116498"
---
# <a name="enable-soft-delete-on-azure-file-shares"></a>Ativar soft delete em ações de ficheiros Azure

O Azure Storage oferece uma eliminação suave para partilhas de ficheiros para que possa recuperar mais facilmente os seus dados quando estes forem erroneamente eliminados por uma aplicação ou outro utilizador de conta de armazenamento. Para saber mais sobre a eliminação suave, consulte [como evitar a eliminação acidental de ações de ficheiros Azure](storage-files-prevent-file-share-deletion.md).

As seguintes secções mostram como ativar e utilizar a eliminação suave para as ações de ficheiros Azure numa conta de armazenamento existente:

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
1. Navegue na sua conta de armazenamento e selecione **Soft delete** sob o serviço **De Ficheiros**.
1. Selecione **Ativado** para **eliminar suavemente a partilha**de ficheiros .
1. Selecione o período de retenção de partilha de **ficheiros em dias** e introduza uma série da sua escolha.
1. Selecione **Guardar** para confirmar as definições de retenção de dados.

:::image type="content" source="media/storage-how-to-recover-deleted-account/enable-soft-delete-files.png" alt-text="Screenshot da conta de armazenamento soft delete definições painel. Realçar a secção de partilhas de ficheiros, permitir alternar, definir um período de retenção e economizar. Isto permitirá eliminar suavemente todas as ações de ficheiro seletivas na sua conta de armazenamento.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para permitir a eliminação suave, tem de atualizar as propriedades de serviço de um cliente de ficheiros. O exemplo que se segue permite eliminar suavemente todas as ações de ficheiros numa conta de armazenamento:

```azurepowershell-interactive
$rgName = "yourResourceGroupName"
$accountName = "yourStorageAccountName"

Update-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName -EnableShareDeleteRetentionPolicy $true -ShareRetentionDays 7
```

Pode verificar se o soft delete está ativado e ver a sua política de retenção com o seguinte comando:

```azurepowershell-interactive
Get-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName
```
---

## <a name="restore-soft-deleted-file-share"></a>Restaurar a parte suave do ficheiro eliminado

# <a name="portal"></a>[Portal](#tab/azure-portal)

Para restaurar uma partilha suave de ficheiros eliminados:

1. Navegue na sua conta de armazenamento e selecione **ações de Ficheiro**.
1. Na lâmina de partilha de ficheiros, ative a **exibição de ações eliminadas** do Show para exibir quaisquer ações que tenham sido eliminadas suavemente.

    Isto mostrará quaisquer ações atualmente num estado **Apagado.**

    :::image type="content" source="media/storage-how-to-recover-deleted-account/undelete-file-share.png" alt-text="Se a coluna de estado, a coluna ao lado da coluna de nome, estiver definida para Apagar, então a sua parte de ficheiro encontra-se num estado suave mente eliminado. E será permanentemente eliminado após o período de retenção especificado.":::

1. Selecione a parte e **selecione undelete,** isto irá restaurar a parte.

    Pode confirmar que a parte é restaurada uma vez que o seu estado muda para **Ative**.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/restored-file-share.png" alt-text="Se a coluna de estado, a coluna ao lado da coluna de nomes, estiver definida para Ativa, então a sua parte de ficheiro foi restaurada.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para restaurar uma parte suave de ficheiro eliminado, utilize o seguinte comando:

```azurepowershell-interactive
Restore-AzRmStorageShare -ResourceGroupName $rgname -StorageAccountName $accountName -DeletedShareVersion 01D5E2783BDCDA97
```
---

## <a name="disable-soft-delete"></a>Desativar a eliminação suave

Se pretender parar de utilizar a eliminação suave ou apagar permanentemente uma parte de ficheiro, siga estas instruções:

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Navegue na sua conta de armazenamento e selecione **Soft delete** em **Definições**.
1. Em **ações de Ficheiro,** selecione **Disabled** for **Soft delete for file shares**.
1. Selecione **Guardar** para confirmar as definições de retenção de dados.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/disable-soft-delete-files.png" alt-text="Desativar a eliminação suave permitir-lhe-á apagar imediatamente e permanentemente todas as ações de ficheiro na sua conta de armazenamento a seu bel-prazer.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pode utilizar o seguinte comando para desativar a eliminação suave na sua conta de armazenamento:

```azurepowershell-interactive
Update-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName -EnableShareDeleteRetentionPolicy $false
```
---

## <a name="next-steps"></a>Próximos passos

Para conhecer outra forma de proteção e recuperação de dados, consulte o nosso artigo [Visão geral de imagens de partilha para Ficheiros Azure](storage-snapshots-files.md).