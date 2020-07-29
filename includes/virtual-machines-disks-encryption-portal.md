---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/23/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 950fd82e14902c3a7a94fddb44d50ac372ebe119
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2020
ms.locfileid: "87177050"
---
[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-portal](virtual-machines-disks-encryption-create-key-vault-portal.md)]

## <a name="deploy-a-vm"></a>Implementar uma VM

Agora que criou e montou o cofre de chaves e o conjunto de encriptação do disco, pode implementar um VM usando a encriptação.
O processo de implementação de VM é semelhante ao processo de implementação padrão, as únicas diferenças são que você precisa implementar o VM na mesma região que os seus outros recursos e você opta por usar uma chave gerida pelo cliente.

1. Procure por **Máquinas Virtuais** e selecione **+ Adicionar** para criar um VM.
1. Na lâmina **Basic,** selecione a mesma região que o seu conjunto de encriptação de disco e a Azure Key Vault.
1. Preencha os outros valores na lâmina **Básica** como quiser.

    ![Screenshot da experiência de criação de VM, com o valor da região em destaque.](media/virtual-machines-disk-encryption-portal/server-side-encryption-create-a-vm-region.png)

1. Na lâmina **Discos,** selecione **Encriptação em repouso com uma chave gerida pelo cliente**.
1. Selecione o conjunto de encriptação do disco no conjunto de encriptação do **disco.**
1. Faça as restantes seleções como quiser.

    ![Screenshot da experiência de criação de VM, a lâmina dos discos. Com o conjunto de encriptação do disco em destaque.](media/virtual-machines-disk-encryption-portal/server-side-encryption-create-vm-select-customer-managed-key-disk-encryption-set.png)

## <a name="enable-on-an-existing-disk"></a>Ativar num disco existente

> [!CAUTION]
> Ativar a encriptação do disco em quaisquer discos ligados a um VM exigirá que pare o VM.
    
1. Navegue para um VM que está na mesma região que um dos seus conjuntos de encriptação de disco.
1. Abra o VM e **selecione Stop**.

    ![Screenshot da sobreposição principal para o seu exemplo VM, com o botão Stop realçado.](media/virtual-machines-disk-encryption-portal/server-side-encryption-stop-vm-to-encrypt-disk-fix.png)

1. Depois de o VM ter terminado de parar, selecione **Discos** e, em seguida, selecione o disco que pretende encriptar.

    ![Screenshot do seu exemplo VM, com a lâmina dos Discos aberta. O disco SO é realçado, como um disco de exemplo para você selecionar.](media/virtual-machines-disk-encryption-portal/server-side-encryption-existing-disk-select.png)

1. Selecione **Encriptação** e selecione **Encriptação em repouso com uma chave gerida pelo cliente** e, em seguida, selecione o conjunto de encriptação do disco na lista de drop-down.
1. Selecione **Guardar**.

    ![Screenshot do seu disco DE EXEMPLO. A lâmina de encriptação está aberta, a encriptação em repouso com uma chave gerida pelo cliente é selecionada, bem como o seu exemplo Azure Key Vault. Depois de estois, o botão de poupança é selecionado.](media/virtual-machines-disk-encryption-portal/server-side-encryption-encrypt-existing-disk-customer-managed-key.png)

1. Repita este processo para quaisquer outros discos ligados ao VM que pretende encriptar.
1. Quando os discos terminarem de mudar para teclas geridas pelo cliente, se não houver outros discos anexos que queira encriptar, poderá iniciar o seu VM.
