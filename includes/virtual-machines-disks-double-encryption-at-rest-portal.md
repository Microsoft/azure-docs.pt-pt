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
ms.openlocfilehash: 8313259d5375bb19e7caa08a01a658707453d133
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2020
ms.locfileid: "87177049"
---
## <a name="supported-regions"></a>Regiões suportadas

[!INCLUDE [virtual-machines-disks-double-encryption-at-rest-regions](virtual-machines-disks-double-encryption-at-rest-regions.md)]

## <a name="getting-started"></a>Introdução

1. Inicie sessão no [portal do Azure](https://aka.ms/diskencryptionupdates).

    > [!IMPORTANT]
    > Deve utilizar o [link fornecido](https://aka.ms/diskencryptionupdates) para aceder ao portal Azure. A dupla encriptação em repouso não é atualmente visível no portal público Azure sem utilizar o link.

1. Procure e selecione **conjuntos de encriptação de discos**.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-disk-encryption-sets-search.png" alt-text="Screenshot do portal Azure principal, os conjuntos de encriptação do disco são destacados na barra de pesquisa.":::

1. Selecione **+ Adicionar**.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-add-disk-encryption-set.png" alt-text="A screenshot da lâmina do conjunto de encriptação do disco, + Add é realçada.":::

1. Selecione uma das regiões suportadas.
1. Para **o tipo de encriptação**, selecione **encriptação dupla com teclas geridas pela plataforma e geridas pelo cliente**.

    > [!NOTE]
    > Uma vez criado um conjunto de encriptação de disco com um determinado tipo de encriptação, este não pode ser alterado. Se quiser utilizar um tipo de encriptação diferente, tem de criar um novo conjunto de encriptação de disco.

1. Preencha as restantes informações.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-create-disk-encryption-set-blade.png" alt-text="A screenshot da lâmina de criação de conjunto de encriptação do disco, regiões e dupla encriptação com teclas geridas pela plataforma e geridas pelo cliente são destacadas.":::

1. Selecione um cofre e uma chave de chave Azure, ou crie um novo, se necessário.

    > [!NOTE]
    > Se criar uma instância key vault, deve ativar a proteção de eliminação e purga suave. Estas definições são obrigatórias quando se utiliza um Cofre-Chave para encriptar discos geridos e protegem-no de perder dados devido à eliminação acidental.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-select-key-vault.png" alt-text="Screenshot da lâmina de criação do Cofre chave.":::

1. Selecione **Criar**.
1. Navegue para o conjunto de encriptação do disco que criou e selecione o erro que é apresentado. Isto configurará o seu conjunto de encriptação do disco para funcionar.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-disk-set-error.png" alt-text="Screenshot do conjunto de encriptação do disco apresentado erro, o texto de erro é: Para associar um disco, imagem ou instantâneo com este conjunto de encriptação de disco, você deve conceder permissões ao cofre de chaves.":::

    Uma notificação deve aparecer e ter sucesso. Ao fazê-lo, irá utilizar o conjunto de encriptação do disco com o seu cofre de chaves.
    
    ![Screenshot de permissão bem sucedida e atribuição de função para o seu cofre de chaves.](media/virtual-machines-disk-encryption-portal/disk-encryption-notification-success.png)

1. Navegue para o seu disco.
1. Selecione **encriptação**.
1. Para **o tipo de encriptação**, selecione **encriptação dupla com teclas geridas pela plataforma e geridas pelo cliente**.
1. Selecione o seu conjunto de encriptação de disco.
1. selecionar **Guardar**.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-enable-disk-blade.png" alt-text="Screenshot da lâmina de encriptação para o seu disco gerido, o tipo de encriptação acima mencionado é realçado.":::

Agora, ativou a dupla encriptação em repouso no seu disco gerido.
