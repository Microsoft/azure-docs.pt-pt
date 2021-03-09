---
title: Implementar VMs num conjunto de disponibilidade usando O Azure CLI
description: Neste tutorial, vai aprender a utilizar a CLI do Azure para implementar máquinas virtuais altamente disponíveis em Conjuntos de Disponibilidade
documentationcenter: ''
services: virtual-machines
author: mimckitt
ms.service: virtual-machines
ms.topic: how-to
ms.date: 3/8/2021
ms.author: mimckitt
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 6a54e0d808ef734a26a0fa309bd7367e73316856
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102507070"
---
# <a name="create-and-deploy-virtual-machines-in-an-availability-set-using-azure-cli"></a>Criar e implementar máquinas virtuais num conjunto de disponibilidade usando O Azure CLI

Neste tutorial, irá aprender a aumentar a disponibilidade e a fiabilidade das suas soluções de Máquina Virtual no Azure através de uma função chamada Conjuntos de Disponibilidade. Os conjuntos de disponibilidade garantem que as VMs que implementa no Azure são distribuídas por vários clusters de hardware isolados. Fazer isto garante que, se ocorrer uma falha de hardware ou software no Azure, apenas um subconjunto das suas VMs é afetado e que a solução global permanece disponível e operacional.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um conjunto de disponibilidade
> * Criar uma VM num conjunto de disponibilidade
> * Verificar os tamanhos de VM disponíveis

Este tutorial utiliza o CLI dentro da [Azure Cloud Shell](../../cloud-shell/overview.md), que é constantemente atualizado para a versão mais recente. Para abrir a Cloud Shell, selecione **Experimente-a** a partir da parte superior de qualquer bloco de código.

Se optar por instalar e utilizar a CLI localmente, este tutorial requer que execute uma versão da CLI do Azure que seja a 2.0.30 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)]( /cli/azure/install-azure-cli).

## <a name="create-an-availability-set"></a>Criar um conjunto de disponibilidade

Pode criar um conjunto de disponibilidade com [az vm availability-set create](/cli/azure/vm/availability-set). Neste exemplo, o número de domínios de atualização e com falha é definido como *2* para o conjunto de disponibilidade designado *myAvailabilitySet* no grupo de recursos *myResourceGroupAvailability*.

Em primeiro lugar, crie um grupo de recursos com [az group create](/cli/azure/group#az-group-create) e, em seguida, crie o conjunto de disponibilidade:

```azurecli-interactive
az group create --name myResourceGroupAvailability --location eastus

az vm availability-set create \
    --resource-group myResourceGroupAvailability \
    --name myAvailabilitySet \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

Os Conjuntos de Disponibilidade permitem isolar os recursos em domínios com falha e domínios de atualização. Um **domínio com falha** representa uma coleção isolada do servidor + rede + recursos de armazenamento. No exemplo anterior, o conjunto de disponibilidade é distribuído por, pelo menos, dois domínios com falha quando as VMs forem implementadas. O conjunto de disponibilidade é também distribuído por dois **domínios de atualização**. Dois domínios de atualização garantem que, quando o Azure executa as atualizações de software, os recursos da VM ficam isolados, impedindo assim que todo o software em execução na VM seja atualizado ao mesmo tempo.


## <a name="create-vms-inside-an-availability-set"></a>Criar VMs num conjunto de disponibilidade

As VMs têm de ser criadas no conjunto de disponibilidade para garantir que são distribuídas corretamente pelo hardware. Não é possível adicionar uma VM existente a um conjunto de disponibilidade depois de ter sido criado.

Quando uma VM for criada com [az vm create](/cli/azure/vm), utilize o parâmetro `--availability-set` para especificar o nome do conjunto de disponibilidade.

```azurecli-interactive
for i in `seq 1 2`; do
   az vm create \
     --resource-group myResourceGroupAvailability \
     --name myVM$i \
     --availability-set myAvailabilitySet \
     --size Standard_DS1_v2  \
     --vnet-name myVnet \
     --subnet mySubnet \
     --image UbuntuLTS \
     --admin-username azureuser \
     --generate-ssh-keys
done
```

Existem agora duas máquinas virtuais no conjunto de disponibilidade. Dado que se encontram no mesmo conjunto de disponibilidade, o Azure garante que as VMs e todos os respetivos recursos (incluindo os discos de dados) são distribuídos por hardware físico isolado. Esta distribuição ajuda a garantir uma disponibilidade muito mais elevada da solução global de VM.

A distribuição do conjunto de disponibilidade pode ser visualizada no portal, ao aceder a Grupos de Recursos > myResourceGroupAvailability > myAvailabilitySet. As VMs estão distribuídas pelos dois domínios de atualização e com falha, conforme apresentado no seguinte exemplo:

![Conjunto de disponibilidade no portal](./media/tutorial-availability-sets/fd-ud.png)

## <a name="check-for-available-vm-sizes"></a>Verificar os tamanhos de VM disponíveis

É possível adicionar posteriormente VMs adicionais ao conjunto de disponibilidade, quando os tamanhos da VM estiverem disponíveis no hardware. Utilize [tamanhos de lista definidos az vm](/cli/azure/vm/availability-set#az-vm-availability-set-list-sizes) para listar todos os tamanhos disponíveis no cluster de hardware para o conjunto de disponibilidade:

```azurecli-interactive
az vm availability-set list-sizes \
     --resource-group myResourceGroupAvailability \
     --name myAvailabilitySet \
     --output table
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Criar um conjunto de disponibilidade
> * Criar uma VM num conjunto de disponibilidade
> * Verificar os tamanhos de VM disponíveis

Avance para o tutorial seguinte para saber mais sobre os conjuntos de dimensionamento de máquinas virtuais.

> [!div class="nextstepaction"]
> [Criar um conjunto de dimensionamento de máquinas virtuais](tutorial-create-vmss.md)

* Para saber mais sobre as zonas de disponibilidade, visite a [documentação zonas de disponibilidade.](../../availability-zones/az-overview.md)
* Mais documentação sobre os conjuntos de disponibilidade e zonas de disponibilidade também está disponível [aqui.](../availability.md)
* Para experimentar zonas de disponibilidade, visite [Criar uma máquina virtual Linux numa zona de disponibilidade com o Azure CLI](./create-cli-availability-zone.md)