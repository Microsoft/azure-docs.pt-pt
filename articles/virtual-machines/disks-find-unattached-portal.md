---
title: Identificar discos Azure não ligados - Portal Azure
description: Como encontrar discos de Azure não ligados geridos e não geridos (VHDs/blobs de página) utilizando o portal Azure.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 01/26/2021
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 4d31ce4b6086c44de913afd1083bae25fa3d44cd
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98898160"
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks---azure-portal"></a>Localizar e apagar discos geridos e não geridos do Azure - portal Azure

Quando elimina uma máquina virtual (VM) em Azure, por padrão, quaisquer discos que estejam ligados ao VM não são eliminados. Isto ajuda a prevenir a perda de dados devido à eliminação não intencional de VMs. Depois de eliminado um VM, continuará a pagar por discos não ligados. Este artigo mostra-lhe como encontrar e eliminar quaisquer discos não ligados usando o portal Azure, e reduzir custos desnecessários. As eliminações são permanentes, não poderá recuperar dados uma vez que apague um disco.

## <a name="managed-disks-find-and-delete-unattached-disks"></a>Discos geridos: Localizar e apagar discos não ligados

Se tiver discos geridos desvinculados e já não precisar dos dados sobre eles, o seguinte processo explica como encontrá-los a partir do portal Azure:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
1. Procure e selecione **Discos**.

    Na lâmina **Discos,** é-lhe apresentada uma lista de todos os discos. Qualquer disco que tenha " **-** " na coluna **Proprietário** é um disco não ligado.

    [![Screenshot da lâmina de discos geridos, se um disco tiver - na coluna Proprietário, é um disco não ligado.](media/disks-find-unattached-portal/managed-disk-unattached-owner.png)](media/disks-find-unattached-portal/managed-disk-owner-unattached.png#lightbox)

1. Selecione o disco não ligado que pretende apagar, isto abre a lâmina do disco.
1. Na lâmina do disco, pode confirmar que o estado do disco não está ligado e, em seguida, selecione **Delete**.

    :::image type="content" source="media/disks-find-unattached-portal/delete-managed-disk-unattached.png" alt-text="Screenshot de uma lâmina de discos geridos individualmente. Esta lâmina mostrar-se-á desapegado no estado do disco se não estiver presa. Pode eliminar este disco se não precisar de preservar os seus dados por mais tempo":::

## <a name="unmanaged-disks-find-and-delete-unattached-disks"></a>Discos não geridos: Encontrar e apagar discos não ligados

Os discos não geridos são ficheiros VHD que são armazenados como [bolhas de página](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) nas contas de armazenamento [Azure](../storage/common/storage-account-overview.md).

Se tiver discos não geridos que não estejam ligados a um VM, já não precisa dos dados sobre eles, e gostaria de os apagar, o seguinte processo explica como fazê-lo a partir do portal Azure:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
1. Procure e selecione **Discos (Clássicos)**.

    É-lhe apresentada uma lista de todos os seus discos não geridos. Qualquer disco que tenha " **-** " na coluna **anexada** é um disco não ligado.

    :::image type="content" source="media/disks-find-unattached-portal/unmanaged-disk-unattached-attached-to.png" alt-text="Screenshot da lâmina dos discos não geridos. Os discos desta lâmina que têm - na coluna anexa à coluna estão desapegados.":::

1. Selecione o disco não ligado que gostaria de apagar, isto traz a lâmina do disco.

1. Na lâmina do disco, pode confirmar que não está ligada, uma vez que **a attached to** will still is **-** .

    :::image type="content" source="media/disks-find-unattached-portal/unmanaged-disk-unattached-select-blade.png" alt-text="Screenshot de uma lâmina de disco individual não gerido. Terá - como o anexo ao valor se não estiver ligado. Se já não necessitar destes dados dos discos, pode eliminá-lo.":::

1. Selecione **Eliminar**.

    :::image type="content" source="media/disks-find-unattached-portal/delete-unmanaged-disk-unattached.png" alt-text="Screenshot de uma lâmina de disco individual não gerido, realçando a exclusão.":::

## <a name="next-steps"></a>Próximos passos

Se quiser uma forma automatizada de encontrar e eliminar contas de armazenamento não coladas, consulte os nossos artigos [do CLI](linux/find-unattached-disks.md) ou [da PowerShell.](windows/find-unattached-disks.md)

Para obter mais informações, consulte [Eliminar uma conta de armazenamento](../storage/common/storage-account-create.md#delete-a-storage-account) e identificar discos [órfãos usando PowerShell](/archive/blogs/ukplatforms/azure-cost-optimisation-series-identify-orphaned-disks-using-powershell)
