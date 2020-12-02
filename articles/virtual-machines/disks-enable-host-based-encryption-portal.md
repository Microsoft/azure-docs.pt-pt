---
title: Ativar encriptação de ponta a ponta utilizando encriptação no host - portal Azure - discos geridos
description: Utilize encriptação no anfitrião para ativar a encriptação de ponta a ponta nos discos geridos Azure - Portal Azure.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/24/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: fd74872bc1c46f3dddda1b6d15f14f26dc2187cb
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499378"
---
# <a name="use-the-azure-portal-to-enable-end-to-end-encryption-using-encryption-at-host"></a>Utilize o portal Azure para ativar a encriptação de ponta a ponta utilizando encriptação no anfitrião

Quando ativa a encriptação no anfitrião, os dados armazenados no anfitrião VM são encriptados em repouso e os fluxos encriptados para o serviço de Armazenamento. Para obter informações conceptuais sobre encriptação no anfitrião, bem como outros tipos de encriptação de discos geridos, consulte:

* Linux: [Encriptação no anfitrião - Encriptação de ponta a ponta para os seus dados VM](./disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data).

* Windows: [Encriptação no anfitrião - Encriptação de ponta a ponta para os seus dados VM](./disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data).

## <a name="restrictions"></a>Restrições

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]

### <a name="supported-regions"></a>Regiões suportadas

[!INCLUDE [virtual-machines-disks-encryption-at-host-regions](../../includes/virtual-machines-disks-encryption-at-host-regions.md)]

### <a name="supported-vm-sizes"></a>Tamanhos de VM suportados

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para poder utilizar a encriptação no anfitrião para os seus VMs ou conjuntos de escala de máquinas virtuais, tem de obter a funcionalidade ativada na sua subscrição. Enviar um e-mail para encryptionAtHost@microsoft. com os seus Ids de subscrição para obter a funcionalidade ativada para as suas subscrições.

Inscreva-se no portal Azure utilizando o [link fornecido](https://aka.ms/diskencryptionupdates).

> [!IMPORTANT]
> Deve utilizar o [link fornecido](https://aka.ms/diskencryptionupdates) para aceder ao portal Azure. A encriptação no anfitrião não é atualmente visível no portal público Azure sem utilizar o link.

### <a name="create-an-azure-key-vault-and-disk-encryption-set"></a>Crie um cofre de chave Azure e conjunto de encriptação de disco

Uma vez ativada a funcionalidade, terá de configurar um Cofre de Chaves Azure e um conjunto de encriptação de disco, se ainda não o fez.

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-portal](../../includes/virtual-machines-disks-encryption-create-key-vault-portal.md)]

## <a name="deploy-a-vm"></a>Implementar uma VM

Tem de implementar um novo VM para permitir a encriptação no hospedeiro, não podendo ser ativado em VM existentes.

1. Procure por **Máquinas Virtuais** e selecione **+ Adicionar** para criar um VM.
1. Crie uma nova máquina virtual, selecione uma região apropriada e um tamanho VM suportado.
1. Preencha os outros valores na lâmina **Básica** como quiser e, em seguida, dirija-se à lâmina **dos Discos.**

    :::image type="content" source="media/virtual-machines-disks-encryption-at-host-portal/disks-encryption-at-host-basic-blade.png" alt-text="A screenshot da máquina virtual criação de lâminas básicas, região e tamanho V M são destacados.":::

1. Na lâmina **Discos,** selecione **Sim** para **Encriptação no anfitrião**.
1. Faça as restantes seleções como quiser.

    :::image type="content" source="media/virtual-machines-disks-encryption-at-host-portal/disks-encryption-at-host-disk-blade.png" alt-text="A screenshot da lâmina de criação de discos de criação de máquinas virtuais, a encriptação no anfitrião é realçada.":::

1. Termine o processo de implantação de VM, faça seleções que se adaptem ao seu ambiente.

Agora implementou um VM com encriptação no anfitrião ativado, todos os seus discos associados serão encriptados usando encriptação no anfitrião.

## <a name="next-steps"></a>Passos seguintes

[Amostras de modelo do gestor de recursos Azure](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/EncryptionAtHost)