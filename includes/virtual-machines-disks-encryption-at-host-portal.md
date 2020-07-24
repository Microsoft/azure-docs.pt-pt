---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/07/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: c5ea31217bd3088ec123281b36f8578b08ea25b2
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2020
ms.locfileid: "87136233"
---
## <a name="restrictions"></a>Restrições

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](virtual-machines-disks-encryption-at-host-restrictions.md)]

### <a name="supported-regions"></a>Regiões suportadas

[!INCLUDE [virtual-machines-disks-encryption-at-host-regions](virtual-machines-disks-encryption-at-host-regions.md)]

### <a name="supported-vm-sizes"></a>Tamanhos de VM suportados

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](virtual-machines-disks-encryption-at-host-suported-sizes.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para poder utilizar a encriptação no anfitrião para os seus VMs ou conjuntos de escala de máquinas virtuais, tem de obter a funcionalidade ativada na sua subscrição. Enviar um e-mail para encryptionAtHost@microsoft. com os seus Ids de subscrição para obter a funcionalidade ativada para as suas subscrições.

Inscreva-se no portal Azure utilizando o [link fornecido](https://aka.ms/diskencryptionupdates).

> [!IMPORTANT]
> Deve utilizar o [link fornecido](https://aka.ms/diskencryptionupdates) para aceder ao portal Azure. A encriptação no anfitrião não é atualmente visível no portal público Azure sem utilizar o link.

### <a name="create-an-azure-key-vault-and-disk-encryption-set"></a>Crie um cofre de chave Azure e conjunto de encriptação de disco

Uma vez ativada a funcionalidade, terá de configurar um Cofre de Chaves Azure e um conjunto de encriptação de disco, se ainda não o fez.

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-portal](virtual-machines-disks-encryption-create-key-vault-portal.md)]

## <a name="deploy-a-vm"></a>Implementar uma VM

Tem de implementar um novo VM para permitir a encriptação no hospedeiro, não podendo ser ativado em VM existentes.

1. Procure por **Máquinas Virtuais** e selecione **+ Adicionar** para criar um VM.
1. Crie uma nova máquina virtual, selecione uma região apropriada e um tamanho VM suportado.
1. Preencha os outros valores na lâmina **Básica** como quiser e, em seguida, dirija-se à lâmina **dos Discos.**

    :::image type="content" source="media/virtual-machines-disks-encryption-at-host-portal/disks-encryption-at-host-basic-blade.png" alt-text="texto exemplo":::

1. Na lâmina **Discos,** selecione **Sim** para **Encriptação no anfitrião**.
1. Faça as restantes seleções como quiser.

    :::image type="content" source="media/virtual-machines-disks-encryption-at-host-portal/disks-encryption-at-host-disk-blade.png" alt-text="texto exemplo":::

1. Termine o processo de implantação de VM, faça seleções que se adaptem ao seu ambiente.

Agora implementou um VM com encriptação no anfitrião ativado, todos os seus discos associados serão encriptados usando encriptação no anfitrião.