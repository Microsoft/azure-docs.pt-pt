---
title: Localizar e eliminar NICs desanexados do Azure | Documentos da Microsoft
description: Como encontrar e eliminar NICs do Azure que não estão ligados às VMs com a CLI do Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/10/2018
ms.author: cynthn
ms.openlocfilehash: d3fd807dcd920a951dcc5083022d4d264b5bdab7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60649405"
---
# <a name="how-to-find-and-delete-unattached-network-interface-cards-nics-for-azure-vms"></a>Como encontrar e eliminar a interface de rede desanexada (NICs) pode ser feito para VMs do Azure
Quando elimina uma máquina virtual (VM) no Azure, os cartões de interface de rede (NICs) não são eliminados por predefinição. Se cria e eliminar várias VMs, NICs não utilizados continuam a utilizar o as concessões de endereço IP internas. À medida que cria a outros NICs de VM, pode ser não é possível obter uma concessão de IP no espaço de endereços da sub-rede. Este artigo mostra-lhe como encontrar e eliminar NICs desanexados.

## <a name="find-and-delete-unattached-nics"></a>Localizar e eliminar NICs desanexados

O *virtualMachine* propriedade para uma NIC armazena o ID e grupo de recursos da VM a NIC está ligada a. O script seguinte executa loop em todos os NICs numa subscrição e verifica se o *virtualMachine* propriedade é nula. Se esta propriedade for nula, a NIC não está ligada a uma VM.

Para ver todos os NICs desanexados, ele tem altamente recomendável a primeira execução do script com o *deleteUnattachedNics* variável à *0*. Para eliminar todos os NICs desanexados depois de rever a saída de lista, execute o script com *deleteUnattachedNics* ao *1*.

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

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre como criar e gerir redes virtuais no Azure, consulte [criar e gerir redes VM](tutorial-virtual-network.md).
