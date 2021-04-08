---
title: Crie uma imagem instantânea de um VHD utilizando o Azure CLI
description: Aprenda a criar uma cópia de um VHD em Azure como um back up ou para problemas de resolução de problemas.
author: roygara
manager: twooley
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/11/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: d041f864c6c8cd3ae9c522d79447d71c86f9ac04
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98875609"
---
# <a name="create-a-snapshot-using-the-portal-or-azure-cli"></a>Crie um instantâneo utilizando o portal ou O Azure CLI

Tire uma foto de um sistema operativo ou disco de dados para obter cópia de segurança ou para resolver problemas de VM. Uma foto é uma cópia completa e só de leitura de um VHD. 

## <a name="use-azure-cli"></a>Utilizar a CLI do Azure 

O exemplo a seguir requer que utilize [a Cloud Shell](https://shell.azure.com/bash) ou tenha o CLI Azure instalado.

Os passos a seguir mostram como tirar uma fotografia utilizando o **instantâneo az criar** comando com o parâmetro **--fonte-disco.** O exemplo a seguir pressupõe que existe um VM chamado *myVM* no grupo de recursos *myResourceGroup.*

Obtenha o ID do disco usando [o show az vm](/cli/azure/vm#az-vm-show).

```azurecli-interactive
osDiskId=$(az vm show \
   -g myResourceGroup \
   -n myVM \
   --query "storageProfile.osDisk.managedDisk.id" \
   -o tsv)
```

Tire uma foto chamada *osDisk-backup* usando [az snapshot create](/cli/azure/snapshot#az-snapshot-create).

```azurecli-interactive
az snapshot create \
    -g myResourceGroup \
    --source "$osDiskId" \
    --name osDisk-backup
```

> [!NOTE]
> Se você gostaria de armazenar o seu instantâneo em armazenamento resiliente de zona, você precisa criá-lo em uma região que suporta [zonas de disponibilidade](../../availability-zones/az-overview.md) e incluir o parâmetro **--sku Standard_ZRS.**

Pode ver uma lista dos instantâneos utilizando [a lista de instantâneos az](/cli/azure/snapshot#az-snapshot-list).

```azurecli-interactive
az snapshot list \
   -g myResourceGroup \
   - table
```

## <a name="use-azure-portal"></a>Utilizar o portal do Azure 

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. A partir da parte superior esquerda, clique em **Criar um recurso** e procurar por **instantâneo**. Selecione **Snapshot** dos resultados da pesquisa.
3. Na lâmina **Snapshot,** clique em **Criar**.
4. Insira um **nome** para a foto.
5. Selecione um grupo de recursos existente ou escreva o nome para um novo. 
7. Para **o disco Source**, selecione o disco gerido para o instantâneo.
8. Selecione o **tipo de Conta** a utilizar para armazenar o instantâneo. Utilize **o HDD standard** a menos que precise de ser armazenado num SSD de alto desempenho.
9. Clique em **Criar**.


## <a name="next-steps"></a>Passos seguintes

 Crie uma máquina virtual a partir de um instantâneo criando um disco gerido a partir do instantâneo e, em seguida, anexando o novo disco gerido como o disco SO. Para obter mais informações, consulte o [Create a VM a partir de um](/previous-versions/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot?toc=%2fcli%2fmodule%2ftoc.json) script instantâneo.