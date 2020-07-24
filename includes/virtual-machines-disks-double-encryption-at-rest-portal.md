---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/08/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: f96bc753d14d53584aec6d9661e52adcd21ab764
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2020
ms.locfileid: "87136172"
---
## <a name="supported-regions"></a>Regiões suportadas

[!INCLUDE [virtual-machines-disks-double-encryption-at-rest-regions](virtual-machines-disks-double-encryption-at-rest-regions.md)]

## <a name="getting-started"></a>Introdução

1. Inicie sessão no [portal do Azure](https://aka.ms/diskencryptionupdates).

    > [!IMPORTANT]
    > Deve utilizar o [link fornecido](https://aka.ms/diskencryptionupdates) para aceder ao portal Azure. A dupla encriptação em repouso não é atualmente visível no portal público Azure sem utilizar o link.

1. Procure e selecione **conjuntos de encriptação de discos**.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-disk-encryption-sets-search.png" alt-text="texto exemplo":::

1. Selecione **+ Adicionar**.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-add-disk-encryption-set.png" alt-text="texto exemplo":::

1. Selecione uma das regiões suportadas.
1. Para **o tipo de encriptação**, selecione **encriptação dupla com teclas geridas pela plataforma e geridas pelo cliente**.

    > [!NOTE]
    > Uma vez criado um conjunto de encriptação de disco com um determinado tipo de encriptação, este não pode ser alterado. Se quiser utilizar um tipo de encriptação diferente, tem de criar um novo conjunto de encriptação de disco.

1. Preencha as restantes informações.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-create-disk-encryption-set-blade.png" alt-text="texto exemplo":::

1. Selecione um cofre e uma chave de chave Azure, ou crie um novo, se necessário.

    > [!NOTE]
    > Se criar uma instância key vault, deve ativar a proteção de eliminação e purga suave. Estas definições são obrigatórias quando se utiliza um Cofre-Chave para encriptar discos geridos e protegem-no de perder dados devido à eliminação acidental.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-select-key-vault.png" alt-text="texto exemplo":::

1. Selecione **Criar**.
1. Navegue para o conjunto de encriptação do disco que criou e selecione o erro que é apresentado. Isto configurará o seu conjunto de encriptação do disco para funcionar.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-disk-set-error.png" alt-text="texto exemplo":::

    Uma notificação deve aparecer e ter sucesso. Ao fazê-lo, irá utilizar o conjunto de encriptação do disco com o seu cofre de chaves.
    
    ![Screenshot de permissão bem sucedida e atribuição de função para o seu cofre de chaves.](media/virtual-machines-disk-encryption-portal/disk-encryption-notification-success.png)

1. Navegue para o seu disco.
1. Selecione **encriptação**.
1. Para **o tipo de encriptação**, selecione **encriptação dupla com teclas geridas pela plataforma e geridas pelo cliente**.
1. Selecione o seu conjunto de encriptação de disco.
1. selecionar **Guardar**.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-enable-disk-blade.png" alt-text="texto exemplo":::

Agora, ativou a dupla encriptação em repouso no seu disco gerido.
