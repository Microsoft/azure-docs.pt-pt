---
title: Criar um instantâneo de um VHD no Azure
description: Saiba como criar uma cópia de um VHD no Azure como um backup ou para solucionar problemas.
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
ms.openlocfilehash: 15696469ca3861586617e9f418f8a55a7ea90467
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034785"
---
# <a name="create-a-snapshot"></a>Criar um instantâneo 

Tire um instantâneo de um sistema operacional ou disco de dados para backup ou para solucionar problemas de VM. Um instantâneo é uma cópia completa e somente leitura de um VHD. 

## <a name="use-azure-cli"></a>Utilizar a CLI do Azure 

O exemplo a seguir requer que você use [Cloud Shell](https://shell.azure.com/bash) ou tenha o CLI do Azure instalado.

As etapas a seguir mostram como fazer um instantâneo usando o comando **AZ snapshot Create** com o parâmetro **--Source-Disk** . O exemplo a seguir pressupõe que haja uma VM chamada *myVM* no grupo de recursos *MyResource* Group.

Obtenha a ID do disco usando [AZ VM show](/cli/azure/vm#az-vm-show).

```azurecli-interactive
osDiskId=$(az vm show \
   -g myResourceGroup \
   -n myVM \
   --query "storageProfile.osDisk.managedDisk.id" \
   -o tsv)
```

Faça um instantâneo chamado *osDisk-backup* usando [AZ snapshot Create](/cli/azure/snapshot#az-snapshot-create).

```azurecli-interactive
az snapshot create \
    -g myResourceGroup \
    --source "$osDiskId" \
    --name osDisk-backup
```

> [!NOTE]
> Se você quiser armazenar o instantâneo em um armazenamento com capacidade de zona, precisará criá-lo em uma região que ofereça suporte a [zonas de disponibilidade](../../availability-zones/az-overview.md) e incluir o parâmetro **--SKU Standard_ZRS** .

Você pode ver uma lista dos instantâneos usando [AZ snapshot List](/cli/azure/snapshot#az-snapshot-list).

```azurecli-interactive
az snapshot list \
   -g myResourceGroup \
   - table
```

## <a name="use-azure-portal"></a>Utilizar o portal do Azure 

1. Iniciar sessão no [portal do Azure](https://portal.azure.com).
2. A partir do canto superior esquerdo, clique em **criar um recurso** e procure por **instantâneo**. Selecione **instantâneo** nos resultados da pesquisa.
3. Na folha **instantâneo** , clique em **criar**.
4. Insira um **nome** para o instantâneo.
5. Selecione um grupo de recursos existente ou digite o nome de um novo. 
7. Para **disco de origem**, selecione o disco gerenciado para instantâneo.
8. Selecione o **tipo de conta** a ser usado para armazenar o instantâneo. Use **HDD Standard** , a menos que você precise dele armazenado em uma SSD de alto desempenho.
9. Clique em **Criar**.


## <a name="next-steps"></a>Passos seguintes

 Crie uma máquina virtual de um instantâneo criando um disco gerenciado do instantâneo e, em seguida, anexando o novo disco gerenciado como o disco do sistema operacional. Para obter mais informações, consulte [criar uma VM com base em um script de instantâneo](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fcli%2fmodule%2ftoc.json) .

