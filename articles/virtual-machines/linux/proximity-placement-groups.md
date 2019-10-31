---
title: Usar grupos de posicionamento de proximidade para VMs Linux
description: Saiba como criar e usar grupos de posicionamento de proximidade para máquinas virtuais do Linux no Azure.
services: virtual-machines-linux
author: cynthn
manager: gwallace
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: cynthn
ms.openlocfilehash: 353a266b647c299515c15889c302ba4409aa511b
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73171051"
---
# <a name="deploy-vms-to-proximity-placement-groups-using-azure-cli"></a>Implantar VMs em grupos de posicionamento de proximidade usando CLI do Azure

Para obter as VMs o mais próximo possível, alcançando a menor latência possível, você deve implantá-las em um [grupo de posicionamento de proximidade](co-location.md#proximity-placement-groups).

Um grupo de posicionamento de proximidade é um agrupamento lógico usado para garantir que os recursos de computação do Azure estejam fisicamente localizados próximos um do outro. Os grupos de posicionamento de proximidade são úteis para cargas de trabalho em que a baixa latência é um requisito.


## <a name="create-the-proximity-placement-group"></a>Criar o grupo de posicionamento de proximidade
Crie um grupo de posicionamento de proximidade usando [`az ppg create`](/cli/azure/ppg#az-ppg-create). 

```azurecli-interactive
az group create --name myPPGGroup --location westus
az ppg create \
   -n myPPG \
   -g myPPGGroup \
   -l westus \
   -t standard 
```

## <a name="list-proximity-placement-groups"></a>Listar grupos de posicionamento de proximidade

Você pode listar todos os seus grupos de posicionamento de proximidade usando a [lista AZ PPG](/cli/azure/ppg#az-ppg-list).

```azurecli-interactive
az ppg list -o table
```

## <a name="create-a-vm"></a>Criar uma VM

Crie uma VM dentro do grupo de posicionamento de proximidade usando [New AZ VM](/cli/azure/vm#az-vm-create).

```azurecli-interactive
az vm create \
   -n myVM \
   -g myPPGGroup \
   --image UbuntuLTS \
   --ppg myPPG  \
   --generate-ssh-keys \
   --size Standard_D1_v2  \
   -l westus
```

Você pode ver a VM no grupo de posicionamento de proximidade usando [AZ PPG show](/cli/azure/ppg#az-ppg-show).

```azurecli-interactive
az ppg show --name myppg --resource-group myppggroup --query "virtualMachines"
```

## <a name="availability-sets"></a>Conjuntos de Disponibilidade
Você também pode criar um conjunto de disponibilidade em seu grupo de posicionamento de proximidade. Use o mesmo parâmetro `--ppg` com [AZ VM Availability – Set Create](/cli/azure/vm/availability-set#az-vm-availability-set-create) para criar um conjunto de disponibilidade e todas as VMs no conjunto de disponibilidade também serão criadas no mesmo grupo de posicionamento de proximidade.

## <a name="scale-sets"></a>Conjuntos de dimensionamento

Você também pode criar um conjunto de dimensionamento em seu grupo de posicionamento de proximidade. Use o mesmo parâmetro `--ppg` com [AZ vmss Create](/cli/azure/vmss?view=azure-cli-latest#az-vmss-create) para criar um conjunto de dimensionamento e todas as instâncias serão criadas no mesmo grupo de posicionamento de proximidade.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre os comandos de [CLI do Azure](/cli/azure/ppg) para grupos de posicionamento de proximidade.
