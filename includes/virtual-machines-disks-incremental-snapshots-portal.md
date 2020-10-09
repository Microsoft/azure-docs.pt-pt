---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/02/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: de0cf260207747f4acb02a377819a13de8b8ba22
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "80628402"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

## <a name="regional-availability"></a>Disponibilidade regional
[!INCLUDE [virtual-machines-disks-incremental-snapshots-regions](virtual-machines-disks-incremental-snapshots-regions.md)]

### <a name="restrictions"></a>Restrições

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="portal"></a>Portal


1. Inscreva-se no [portal Azure](https://portal.azure.com/) e navegue para o disco que pretende tirar.
1. No seu disco, **selecione Criar um Instantâneo**

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/create-snapshot-button-incremental.png" alt-text="A screenshot. A lâmina do seu disco, com **+Create snapshot** realçada, pois é isso que deve selecionar.":::

1. Selecione o grupo de recursos que pretende utilizar e introduza um nome.
1. Selecione **Incremental** e selecione **Review + Criar**

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/incremental-snapshot-create-snapshot-blade.png" alt-text="A screenshot. A lâmina do seu disco, com **+Create snapshot** realçada, pois é isso que deve selecionar.":::

1. Selecione **Criar**

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/create-incremental-snapshot-validation.png" alt-text="A screenshot. A lâmina do seu disco, com **+Create snapshot** realçada, pois é isso que deve selecionar.":::

## <a name="next-steps"></a>Passos seguintes

Se quiser ver o código de amostra que demonstre a capacidade diferencial de instantâneos incrementais, utilizando .NET, consulte [cópias de segurança dos Discos Geridos copy Azure para outra região com capacidade diferencial de instantâneos incrementais](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots).
