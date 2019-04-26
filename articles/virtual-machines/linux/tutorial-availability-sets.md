---
title: Tutorial – Elevada disponibilidade para VMs do Linux no Azure | Microsoft Docs
description: Neste tutorial, vai aprender a utilizar a CLI do Azure para implementar máquinas virtuais altamente disponíveis em Conjuntos de Disponibilidade
documentationcenter: ''
services: virtual-machines-linux
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: tutorial
ms.date: 08/24/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 7fd671c77b4c0897134457f5aacaabc11d568694
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60418711"
---
# <a name="tutorial-create-and-deploy-highly-available-virtual-machines-with-the-azure-cli"></a>Tutorial: Criar e implementar máquinas virtuais altamente disponíveis com a CLI do Azure

Neste tutorial, irá aprender a aumentar a disponibilidade e a fiabilidade das suas soluções de Máquina Virtual no Azure através de uma função chamada Conjuntos de Disponibilidade. Os conjuntos de disponibilidade garantem que as VMs que implementa no Azure são distribuídas por vários clusters de hardware isolados. Fazer isto garante que, se ocorrer uma falha de hardware ou software no Azure, apenas um subconjunto das suas VMs é afetado e que a solução global permanece disponível e operacional.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um conjunto de disponibilidade
> * Criar uma VM num conjunto de disponibilidade
> * Verificar os tamanhos de VM disponíveis

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este tutorial requer que execute uma versão da CLI do Azure que seja a 2.0.30 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="availability-set-overview"></a>Descrição geral do conjunto de disponibilidade

Um Conjunto de Disponibilidade é uma função de agrupamento lógico que pode utilizar no Azure para garantir que os recursos de VM que nele colocar estão isolados uns dos outros quando são implementados num datacenter do Azure. O Azure garante que as VMs que colocar num Conjunto de Disponibilidade são executadas em vários servidores físicos, suportes de computação, unidades de armazenamento e comutadores de rede. Se ocorrer uma falha de software do Azure ou de hardware, apenas um subconjunto das suas VMs será afetado, e a aplicação global mantém-se e continua disponível para os seus clientes. Os Conjuntos de Disponibilidade são uma função essencial quando pretende criar soluções cloud fiáveis.

Consideremos uma solução típica baseada em VM em que poderá ter quatro servidores Web de front-end e utilizar duas VMs de back-end que alojam uma base de dados. Com o Azure, irá definir dois conjuntos de disponibilidade antes de implementar as suas VMs: um conjunto de disponibilidade para a camada "Web" e um conjunto de disponibilidade para a camada de "base de dados". Quando cria uma nova VM, pode especificar o conjunto de disponibilidade como um parâmetro para o comando az vm create, e o Azure garante automaticamente que as VMs que criar no conjunto disponível ficam isoladas em vários recursos de hardware físico. Se o hardware físico em execução por uma das VMs do Servidor Web ou Servidor de Base de Dados tiver um problema, sabe que as outras instâncias das VMs do Servidor Web e de Base de Dados permanecem em execução porque estão em hardware diferente.

Utilize Conjuntos de Disponibilidade quando pretender implementar soluções fiáveis baseadas em VM no Azure.


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

É possível adicionar posteriormente VMs adicionais ao conjunto de disponibilidade, quando os tamanhos da VM estiverem disponíveis no hardware. Utilize [az vm availability-set list-sizes](/cli/azure/vm/availability-set#az-vm-availability-set-list-sizes) para listar todos os tamanhos disponíveis no hardware de cluster para o conjunto de disponibilidade:

```azurecli-interactive
az vm availability-set list-sizes \
     --resource-group myResourceGroupAvailability \
     --name myAvailabilitySet \
     --output table
```

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Criar um conjunto de disponibilidade
> * Criar uma VM num conjunto de disponibilidade
> * Verificar os tamanhos de VM disponíveis

Avance para o tutorial seguinte para saber mais sobre os conjuntos de dimensionamento de máquinas virtuais.

> [!div class="nextstepaction"]
> [Criar um conjunto de dimensionamento de máquinas virtuais](tutorial-create-vmss.md)
