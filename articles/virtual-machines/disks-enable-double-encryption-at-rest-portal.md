---
title: Ativar a dupla encriptação em repouso - portal Azure - discos geridos
description: Ativar a dupla encriptação em repouso para os dados do disco geridos utilizando o portal Azure.
author: roygara
ms.date: 08/24/2020
ms.topic: how-to
ms.author: rogarana
ms.service: virtual-machines-linux
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: bf2531536796965f145a9ac3e6a23cbb6634852a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88817922"
---
# <a name="use-the-azure-portal-to-enable-double-encryption-at-rest-for-managed-disks"></a>Utilize o portal Azure para permitir a dupla encriptação em repouso para discos geridos

O Azure Disk Storage suporta a dupla encriptação em repouso para discos geridos. Para obter informações conceptuais sobre a dupla encriptação em repouso, bem como outros tipos de encriptação de disco gerido, consulte a **encriptação dupla na** secção de repouso do nosso artigo de encriptação do disco:

- Para Linux: [Dupla encriptação em repouso](./linux/disk-encryption.md#double-encryption-at-rest)
- Para Windows: [Dupla encriptação em repouso](./windows/disk-encryption.md#double-encryption-at-rest)

## <a name="supported-regions"></a>Regiões suportadas

[!INCLUDE [virtual-machines-disks-double-encryption-at-rest-regions](../../includes/virtual-machines-disks-double-encryption-at-rest-regions.md)]

## <a name="getting-started"></a>Introdução

1. Inicie sessão no [portal do Azure](https://aka.ms/diskencryptionupdates).

    > [!IMPORTANT]
    > Deve utilizar o [link fornecido](https://aka.ms/diskencryptionupdates) para aceder ao portal Azure. A dupla encriptação em repouso não é atualmente visível no portal público Azure sem utilizar o link.

1. Procure e selecione **conjuntos de encriptação de discos**.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-disk-encryption-sets-search.png" alt-text="Screenshot do portal Azure principal, os conjuntos de encriptação do disco são destacados na barra de pesquisa.":::

1. Selecione **+ Adicionar**.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-add-disk-encryption-set.png" alt-text="Screenshot do portal Azure principal, os conjuntos de encriptação do disco são destacados na barra de pesquisa.":::

1. Selecione uma das regiões suportadas.
1. Para **o tipo de encriptação**, selecione **encriptação dupla com teclas geridas pela plataforma e geridas pelo cliente**.

    > [!NOTE]
    > Uma vez criado um conjunto de encriptação de disco com um determinado tipo de encriptação, este não pode ser alterado. Se quiser utilizar um tipo de encriptação diferente, tem de criar um novo conjunto de encriptação de disco.

1. Preencha as restantes informações.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-create-disk-encryption-set-blade.png" alt-text="Screenshot do portal Azure principal, os conjuntos de encriptação do disco são destacados na barra de pesquisa.":::

1. Selecione um cofre e uma chave de chave Azure, ou crie um novo, se necessário.

    > [!NOTE]
    > Se criar uma instância key vault, deve ativar a proteção de eliminação e purga suave. Estas definições são obrigatórias quando se utiliza um Cofre-Chave para encriptar discos geridos e protegem-no de perder dados devido à eliminação acidental.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-select-key-vault.png" alt-text="Screenshot do portal Azure principal, os conjuntos de encriptação do disco são destacados na barra de pesquisa.":::

1. Selecione **Criar**.
1. Navegue para o conjunto de encriptação do disco que criou e selecione o erro que é apresentado. Isto configurará o seu conjunto de encriptação do disco para funcionar.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-disk-set-error.png" alt-text="Screenshot do portal Azure principal, os conjuntos de encriptação do disco são destacados na barra de pesquisa.":::

    Uma notificação deve aparecer e ter sucesso. Ao fazê-lo, irá utilizar o conjunto de encriptação do disco com o seu cofre de chaves.
    
    ![Screenshot de permissão bem sucedida e atribuição de função para o seu cofre de chaves.](media/virtual-machines-disks-double-encryption-at-rest-portal/disk-encryption-notification-success.png)

1. Navegue para o seu disco.
1. Selecione **encriptação**.
1. Para **o tipo de encriptação**, selecione **encriptação dupla com teclas geridas pela plataforma e geridas pelo cliente**.
1. Selecione o seu conjunto de encriptação de disco.
1. selecionar **Guardar**.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-enable-disk-blade.png" alt-text="Screenshot do portal Azure principal, os conjuntos de encriptação do disco são destacados na barra de pesquisa.":::

Agora, ativou a dupla encriptação em repouso no seu disco gerido.


## <a name="next-steps"></a>Passos seguintes

- [Azure PowerShell - Ativar as chaves geridas pelo cliente com encriptação do lado do servidor - discos geridos](./windows/disks-enable-customer-managed-keys-powershell.md)
- [Amostras de modelo do gestor de recursos Azure](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/DoubleEncryption)
- [Ativar as chaves geridas pelo cliente com encriptação do lado do servidor - Exemplos](./linux/disks-enable-customer-managed-keys-cli.md#examples)