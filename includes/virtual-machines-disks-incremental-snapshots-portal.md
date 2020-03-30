---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/12/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 8f2e2081ee31e2ce7b21677bd261ed7eda705334
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79486131"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

## <a name="regional-availability"></a>Disponibilidade regional
[!INCLUDE [virtual-machines-disks-incremental-snapshots-regions](virtual-machines-disks-incremental-snapshots-regions.md)]

### <a name="restrictions"></a>Restrições

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="portal"></a>Portal

Para criar e configurar instantâneos incrementais no portal Global [https://aka.ms/incrementalsnapshots](https://aka.ms/incrementalsnapshots)Azure, deve utilizar o seguinte link: . A criação incremental de instantâneos ainda não está disponível no portal global Azure.

1. Assine no [portal Azure](https://aka.ms/incrementalsnapshots) com o link fornecido e navegue para o disco que gostaria de tirar o instantâneo.
1. No seu disco, selecione **Criar um Instantâneo**

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/create-snapshot-button-incremental.png" alt-text="A imagem. A lâmina do disco, com **+Create snapshot** realçada, pois é isso que deve selecionar.":::

1. Selecione o grupo de recursos que deseja utilizar e introduza um nome.
1. **Selecione Incremental** e selecione **Review + Criar**

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/incremental-snapshot-create-snapshot-blade.png" alt-text="A imagem. Crie uma lâmina de instantâneo, preencha o nome e selecione incrementalmente e, em seguida, crie o seu instantâneo.":::

1. Selecione **Criar**

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/create-incremental-snapshot-validation.png" alt-text="A imagem. Página de validação para o seu instantâneo, confirme as suas seleções e, em seguida, crie o instantâneo.":::

## <a name="next-steps"></a>Passos seguintes

Se quiser ver o código de amostra que demonstra a capacidade diferencial de instantâneos incrementais, utilizando .NET, consulte cópia [seleções de discos geridos por Copy Azure para outra região com capacidade diferencial de instantâneos incrementais](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots).
