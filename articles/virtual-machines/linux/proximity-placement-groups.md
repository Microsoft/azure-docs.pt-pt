---
title: Utilizar grupos de colocação por proximidade
description: Saiba como criar e utilizar grupos de colocação de proximidade para máquinas virtuais em Azure.
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: cynthn
ms.openlocfilehash: ea50d0fdc5a772cae449951901af36eed4e93771
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98737630"
---
# <a name="deploy-vms-to-proximity-placement-groups-using-azure-cli"></a>Implementar VMs em grupos de colocação por proximidade com a CLI do Azure

Para obter VMs o mais próximo possível, alcançando a menor latência possível, deve implantá-los dentro de um [grupo de colocação de proximidade](co-location.md#proximity-placement-groups).

Um grupo de colocação de proximidade é um agrupamento lógico usado para garantir que os recursos de computação Azure estão fisicamente localizados perto uns dos outros. Os grupos de colocação de proximidade são úteis para cargas de trabalho onde a baixa latência é um requisito.


## <a name="create-the-proximity-placement-group"></a>Criar o grupo de colocação de proximidade
Crie um grupo de colocação de proximidade utilizando [`az ppg create`](/cli/azure/ppg#az-ppg-create) . 

```azurecli-interactive
az group create --name myPPGGroup --location westus
az ppg create \
   -n myPPG \
   -g myPPGGroup \
   -l westus \
   -t standard 
```

## <a name="list-proximity-placement-groups"></a>Listar grupos de colocação de proximidade

Pode listar todos os seus grupos de colocação de proximidade usando [a lista az ppg](/cli/azure/ppg#az-ppg-list).

```azurecli-interactive
az ppg list -o table
```

## <a name="create-a-vm"></a>Criar uma VM

Crie um VM dentro do grupo de colocação de proximidade utilizando [o novo az vm](/cli/azure/vm#az-vm-create).

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

Pode ver o VM no grupo de colocação de proximidade usando [o show az ppg](/cli/azure/ppg#az-ppg-show).

```azurecli-interactive
az ppg show --name myppg --resource-group myppggroup --query "virtualMachines"
```

## <a name="availability-sets"></a>Conjuntos de Disponibilidade
Também pode criar um conjunto de disponibilidade no seu grupo de colocação de proximidade. Utilize o mesmo `--ppg` parâmetro com [a az vm disponibilidade-set criar](/cli/azure/vm/availability-set#az-vm-availability-set-create) para criar um conjunto de disponibilidade e todos os VMs no conjunto de disponibilidade também serão criados no mesmo grupo de colocação de proximidade.

## <a name="scale-sets"></a>Conjuntos de dimensionamento

Também pode criar um conjunto de escala no seu grupo de colocação de proximidade. Utilize o mesmo `--ppg` parâmetro com [az vmss criar](/cli/azure/vmss#az_vmss_create) para criar um conjunto de escala e todas as instâncias serão criadas no mesmo grupo de colocação de proximidade.

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre os comandos [Azure CLI](/cli/azure/ppg) para grupos de colocação de proximidade.
