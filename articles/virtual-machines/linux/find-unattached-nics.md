---
title: Localizar e excluir NICs do Azure desconectados
description: Como localizar e excluir NICs do Azure que não estão anexadas a VMs com o CLI do Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 04/10/2018
ms.author: cynthn
ms.openlocfilehash: 3d7a0e3405dd87d17c9d7aac2e989ab82531d9b0
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74036135"
---
# <a name="how-to-find-and-delete-unattached-network-interface-cards-nics-for-azure-vms"></a>Como localizar e excluir NICs (placas de interface de rede) desconectadas para VMs do Azure
Quando você exclui uma VM (máquina virtual) no Azure, as placas de interface de rede (NICs) não são excluídas por padrão. Se você criar e excluir várias VMs, as NICs não usadas continuarão a usar as concessões de endereço IP interno. À medida que você cria outras NICs de VM, elas podem não conseguir obter uma concessão de IP no espaço de endereço da sub-rede. Este artigo mostra como localizar e excluir NICs desanexadas.

## <a name="find-and-delete-unattached-nics"></a>Localizar e eliminar NICs desanexados

A propriedade *VirtualMachine* para uma NIC armazena a ID e o grupo de recursos da VM à qual a NIC está anexada. O script a seguir executa um loop em todas as NICs em uma assinatura e verifica se a propriedade *VirtualMachine* é nula. Se essa propriedade for nula, a NIC não será anexada a uma VM.

Para exibir todos os NICs desconectados, é altamente recomendável primeiro executar o script com a variável *deleteunattachedvhds* para *0*. Para excluir todas as NICs desanexadas depois de examinar a saída da lista, execute o script com *deleteunattachedvhds* como *1*.

```azurecli
# Set deleteUnattachedNics=1 if you want to delete unattached NICs
# Set deleteUnattachedNics=0 if you want to see the Id(s) of the unattached NICs
deleteUnattachedNics=0

unattachedNicsIds=$(az network nic list --query '[?virtualMachine==`null`].[id]' -o tsv)
for id in ${unattachedNicsIds[@]}
do
   if (( $deleteUnattachedNics == 1 ))
   then

       echo "Deleting unattached NIC with Id: "$id
       az network nic delete --ids $id
       echo "Deleted unattached NIC with Id: "$id
   else
       echo $id
   fi
done
```

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre como criar e gerenciar redes virtuais no Azure, consulte [criar e gerenciar redes VM](tutorial-virtual-network.md).
