---
title: Utilize grupos de colocação de proximidade
description: Aprenda sobre a criação e utilização de grupos de colocação de proximidade para máquinas virtuais em Azure.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: cynthn
ms.openlocfilehash: f89b28e7a3c29e45efa2796788e27325c01d7098
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81759245"
---
# <a name="deploy-vms-to-proximity-placement-groups-using-azure-cli"></a>Implementar VMs para grupos de colocação de proximidade usando o Azure CLI

Para obter VMs o mais próximo possível, alcançando a menor latência possível, você deve implantá-los dentro de um grupo de [colocação de proximidade](co-location.md#proximity-placement-groups).

Um grupo de colocação de proximidade é um agrupamento lógico usado para garantir que os recursos da computação Azure estão fisicamente localizados perto uns dos outros. Os grupos de colocação de proximidade são úteis para cargas de trabalho onde a baixa latência é um requisito.


## <a name="create-the-proximity-placement-group"></a>Criar o grupo de colocação de proximidade
Criar um grupo de [`az ppg create`](/cli/azure/ppg#az-ppg-create)colocação de proximidade usando . 

```azurecli-interactive
az group create --name myPPGGroup --location westus
az ppg create \
   -n myPPG \
   -g myPPGGroup \
   -l westus \
   -t standard 
```

## <a name="list-proximity-placement-groups"></a>Lista de grupos de colocação de proximidade

Pode listar todos os seus grupos de colocação de proximidade utilizando a [lista az ppg](/cli/azure/ppg#az-ppg-list).

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
Também pode criar um conjunto de disponibilidade sintetária no seu grupo de colocação de proximidade. Utilize o `--ppg` mesmo parâmetro com [az vm disponibilidade-set criar](/cli/azure/vm/availability-set#az-vm-availability-set-create) para criar um conjunto de disponibilidade e todos os VMs no conjunto de disponibilidade também serão criados no mesmo grupo de colocação de proximidade.

## <a name="scale-sets"></a>Conjuntos de dimensionamento

Também pode criar um conjunto de escala no seu grupo de colocação de proximidade. Use o `--ppg` mesmo parâmetro com [az vmss criar](/cli/azure/vmss?view=azure-cli-latest#az-vmss-create) para criar um conjunto de escala e todas as instâncias serão criadas no mesmo grupo de colocação de proximidade.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre os comandos [Azure CLI](/cli/azure/ppg) para grupos de colocação de proximidade.
