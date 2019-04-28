---
title: Criar um instantâneo de um VHD no Azure | Documentos da Microsoft
description: Saiba como criar uma cópia de um VHD no Azure como uma cópia de segurança ou para solução de problemas.
documentationcenter: ''
author: roygara
manager: twooley
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 07/11/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 9f2f3ac3668f0e48716fc30fb69cd1782dbd4e56
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2019
ms.locfileid: "63765676"
---
# <a name="create-a-snapshot"></a>Criar um instantâneo 

Tire um instantâneo de um disco de SO ou dados para cópia de segurança ou para resolver problemas de VMS. Um instantâneo é uma cópia completa, só de leitura de um VHD. 

## <a name="use-azure-cli"></a>Utilizar a CLI do Azure 

O exemplo seguinte é necessário utilizar [Cloud Shell](https://shell.azure.com/bash) ou a CLI do Azure.

Os passos seguintes mostram como tirar um instantâneo com o **criar instantâneo de az** comando com o **– disco de origem** parâmetro. O exemplo seguinte parte do princípio de que existe uma VM chamada *myVM* no *myResourceGroup* grupo de recursos.

Obter o ID de disco com [show de vm de az](/cli/azure/vm#az-vm-show).

```azurecli-interactive
osDiskId=$(az vm show \
   -g myResourceGroup \
   -n myVM \
   --query "storageProfile.osDisk.managedDisk.id" \
   -o tsv)
```

Tirar um instantâneo com o nome *cópia de segurança osDisk* usando [criar instantâneo de az](/cli/azure/snapshot#az-snapshot-create).

```azurecli-interactive
az snapshot create \
    -g myResourceGroup \
    --source "$osDiskId" \
    --name osDisk-backup
```

> [!NOTE]
> Se gostaria de armazenar o instantâneo no armazenamento resiliente para a zona, terá de criá-la numa região que suporta [zonas de disponibilidade](../../availability-zones/az-overview.md) e incluir o **– sku Standard_ZRS** parâmetro.

Pode ver uma lista de instantâneos usando [lista de instantâneos de az](/cli/azure/snapshot#az-snapshot-list).

```azurecli-interactive
az snapshot list \
   -g myResourceGroup \
   - table
```

## <a name="use-azure-portal"></a>Utilizar o portal do Azure 

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. No canto superior esquerdo, clique em **criar um recurso** e procure **instantâneo**. Selecione **instantâneo** resultados da pesquisa.
3. Na **instantâneo** painel, clique em **criar**.
4. Introduza um **nome** para o instantâneo.
5. Selecione um grupo de recursos existente ou escreva o nome para um novo. 
7. Para **disco de origem**, selecione o disco gerido para instantâneo.
8. Selecione o **tipo de conta** utilizar para armazenar o instantâneo. Uso **Standard HDD** a menos que precise de seu armazenamento num SSD com desempenho elevado.
9. Clique em **Criar**.


## <a name="next-steps"></a>Passos Seguintes

 Crie uma máquina virtual a partir de um instantâneo por criar um disco gerido a partir do instantâneo e, em seguida, anexar o novo disco gerido como disco do SO. Para obter mais informações, consulte a [criar uma VM a partir de um instantâneo](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fcli%2fmodule%2ftoc.json) script.

