---
title: Localizar e eliminar NICs Azure não ligados
description: Como encontrar e eliminar NICs Azure que não estão ligados a VMs com o ClI Azure
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.workload: infrastructure-services
ms.topic: article
ms.date: 04/10/2018
ms.author: cynthn
ms.openlocfilehash: 8142b95ee666e205a8328eafd5930f1f386e49af
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78945127"
---
# <a name="how-to-find-and-delete-unattached-network-interface-cards-nics-for-azure-vms"></a>Como encontrar e eliminar cartões de interface de rede não ligados (NICs) para VMs Azure
Quando elimina uma máquina virtual (VM) no Azure, os cartões de interface de rede (NICs) não são eliminados por predefinição. Se criar e eliminar vários VMs, os NICs não utilizados continuam a utilizar os contratos internos de endereçoip. À medida que cria outros NICs VM, eles podem não conseguir obter um contrato de arrendamento IP no espaço de endereço da sub-rede. Este artigo mostra-lhe como encontrar e eliminar NICs não ligados.

## <a name="find-and-delete-unattached-nics"></a>Localizar e eliminar NICs desanexados

A propriedade *virtualMachine* para um NIC armazena o ID e o grupo de recursos do VM a que o NIC está ligado. O script seguinte passa por todos os NICs numa subscrição e verifica se a propriedade *virtualMachine* é nula. Se esta propriedade for nula, o NIC não está ligado a um VM.

Para ver todos os NICs não ligados, é altamente recomendado executar primeiro o script com a variável *deleteUnattachedNics* a *0*. Para eliminar todos os NICs não ligados depois de rever a saída da lista, execute o script com *deleteUnattachedNics* a *1*.

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

Para obter mais informações sobre como criar e gerir redes virtuais no Azure, consulte [criar e gerir redes VM.](tutorial-virtual-network.md)
