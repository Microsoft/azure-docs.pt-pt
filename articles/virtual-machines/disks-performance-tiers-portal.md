---
title: Alterar o desempenho dos discos geridos Azure utilizando o portal Azure
description: Saiba como alterar os níveis de desempenho para discos geridos novos e existentes utilizando o portal Azure.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 01/05/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 625fb1e3dd0b433da6b60f995aa6b380c23ec9ce
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97901038"
---
# <a name="change-your-performance-tier-using-the-azure-portal"></a>Altere o seu nível de desempenho utilizando o portal Azure

[!INCLUDE [virtual-machines-disks-performance-tiers-intro](../../includes/virtual-machines-disks-performance-tiers-intro.md)]

## <a name="restrictions"></a>Restrições

[!INCLUDE [virtual-machines-disks-performance-tiers-restrictions](../../includes/virtual-machines-disks-performance-tiers-restrictions.md)]

## <a name="getting-started"></a>Introdução

### <a name="new-disks"></a>Discos novos

Os seguintes passos mostram como alterar o nível de desempenho do seu disco quando cria o disco pela primeira vez:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
1. Navegue para o VM para o qual gostaria de criar um novo disco para.
1. Ao selecionar o novo disco, escolha primeiro o tamanho do disco de que necessita.
1. Uma vez selecionado um tamanho, selecione um nível de desempenho diferente, para alterar o seu desempenho.
1. Selecione **OK** para criar o disco.

:::image type="content" source="media/disks-performance-tiers-portal/new-disk-change-performance-tier.png" alt-text="Screenshot da lâmina de criação do disco, um disco é realçado, e o dropdown de nível de desempenho é realçado." lightbox="media/disks-performance-tiers-portal/performance-tier-settings.png":::


## <a name="existing-disks"></a>Discos existentes

Os seguintes passos descrevem como alterar o nível de desempenho de um disco existente:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
1. Navegue para o VM que contém o disco que gostaria de alterar.
1. Ou desloque o VM ou retire o disco.
1. Selecione o seu disco
1. Selecione **Tamanho + Desempenho**.
1. No **dropdown do nível** de desempenho, selecione um nível diferente do nível de desempenho atual do disco.
1. Selecione **Redimensionar**.

:::image type="content" source="media/disks-performance-tiers-portal/change-tier-existing-disk.png" alt-text="Screenshot do tamanho + lâmina de desempenho, nível de desempenho é realçado." lightbox="media/disks-performance-tiers-portal/performance-tier-settings.png":::

## <a name="next-steps"></a>Passos seguintes

Se precisar de redimensionar um disco para tirar partido dos níveis de desempenho mais elevados, consulte estes artigos:

- [Expandir discos rígidos virtuais num Linux VM com o Azure CLI](linux/expand-disks.md)
- [Expandir um disco gerido ligado a uma máquina virtual do Windows](windows/expand-os-disk.md)
