---
title: Crie uma imagem de um VHD em Azure
description: Aprenda a criar uma cópia de um VHD em Azure como um back up ou para problemas de resolução de problemas.
author: roygara
manager: twooley
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 07/11/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: f2d905d041198bd35c4657052bf18d981904dc44
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81758396"
---
# <a name="create-a-snapshot"></a>Criar um instantâneo 

Tire uma foto de um SISTEMA ou disco de dados para backup ou para resolver problemas com problemas de VM. Um instantâneo é uma cópia completa e só de leitura de um VHD. 

## <a name="use-azure-cli"></a>Utilizar a CLI do Azure 

O exemplo seguinte requer que utilize cloud [Shell](https://shell.azure.com/bash) ou tenha o CLI Azure instalado.

Os seguintes passos mostram como tirar uma foto usando o **instantâneo az criar** comando com o parâmetro **--source-disk-disk.** O exemplo que se segue pressupõe que existe um VM chamado *myVM* no grupo de recursos *myResourceGroup.*

Obtenha o ID do disco usando [az vm show](/cli/azure/vm#az-vm-show).

```azurecli-interactive
osDiskId=$(az vm show \
   -g myResourceGroup \
   -n myVM \
   --query "storageProfile.osDisk.managedDisk.id" \
   -o tsv)
```

Tire uma fotografia chamada *osDisk-backup* usando [a z snapshot criar](/cli/azure/snapshot#az-snapshot-create).

```azurecli-interactive
az snapshot create \
    -g myResourceGroup \
    --source "$osDiskId" \
    --name osDisk-backup
```

> [!NOTE]
> Se você gostaria de armazenar seu instantâneo em armazenamento resiliente de zona, você precisa criá-lo em uma região que suporta zonas de [disponibilidade](../../availability-zones/az-overview.md) e incluir o **--sku Standard_ZRS** parâmetro.

Pode ver uma lista das fotos usando a lista de [instantâneos az](/cli/azure/snapshot#az-snapshot-list).

```azurecli-interactive
az snapshot list \
   -g myResourceGroup \
   - table
```

## <a name="use-azure-portal"></a>Utilizar o portal do Azure 

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. A partir da parte superior esquerda, clique em **Criar um recurso** e procurar **instantâneos**. Selecione **Snapshot** a partir dos resultados da pesquisa.
3. Na lâmina **Snapshot,** clique em **Criar**.
4. Introduza um **nome** para a foto.
5. Selecione um grupo de recursos existente ou escreva o nome para um novo. 
7. Para **o disco Source,** selecione o disco gerido para instantâneo.
8. Selecione o **tipo de conta** a utilizar para armazenar o instantâneo. Utilize **o HDD Standard** a menos que necessite de ser armazenado num SSD de alto desempenho.
9. Clique em **Criar**.


## <a name="next-steps"></a>Passos seguintes

 Crie uma máquina virtual a partir de um instantâneo criando um disco gerido a partir do instantâneo e, em seguida, anexando o novo disco gerido como o disco OS. Para mais informações, consulte a [Create a VM a partir de um](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fcli%2fmodule%2ftoc.json) script instantâneo.

