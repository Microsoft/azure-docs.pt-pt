---
title: Obter notificações de manutenção para VMs do Azure usando a CLI
description: Exibir notificações de manutenção para máquinas virtuais em execução no Azure e iniciar a manutenção de autoatendimento, usando o CLI do Azure.
services: virtual-machines
author: shants123
tags: azure-service-management,azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: 3c75adc2bf5974c1bce9f05306342068396ae62b
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74535863"
---
# <a name="handling-planned-maintenance-notifications-using-the-azure-cli"></a>Manipulando notificações de manutenção planejada usando o CLI do Azure

**Este artigo se aplica a máquinas virtuais que executam o Linux e o Windows.**

Você pode usar a CLI para ver quando as VMs estão agendadas para [manutenção](maintenance-notifications.md). As informações de manutenção planejada estão disponíveis em [AZ VM Get-Instance-View](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-get-instance-view).
 
As informações de manutenção serão retornadas somente se houver manutenção planejada. 

```azurecli-interactive
az vm get-instance-view -n myVM -g myResourceGroup --query instanceView.maintenanceRedeployStatus
```

## <a name="start-maintenance"></a>Iniciar manutenção

A chamada a seguir iniciará a manutenção em uma VM se `IsCustomerInitiatedMaintenanceAllowed` for definido como true.

```azurecli-interactive
az vm perform-maintenance -g myResourceGroup -n myVM 
```

## <a name="classic-deployments"></a>Implementações clássicas


Se você ainda tiver VMs herdadas que foram implantadas usando o modelo de implantação clássico, poderá usar a CLI clássica do Azure para consultar as VMs e iniciar a manutenção.

Verifique se você está no modo correto para trabalhar com a VM clássica digitando:

```
azure config mode asm
```

Para obter o status de manutenção de uma VM denominada *myVM*, digite:

```
azure vm show myVM 
``` 

Para iniciar a manutenção em sua VM clássica denominada *myVM* no serviço *MyService* e na implantação de *mydeployment* , digite:

```
azure compute virtual-machine initiate-maintenance --service-name myService --name myDeployment --virtual-machine-name myVM
```

## <a name="next-steps"></a>Passos seguintes

Você também pode manipular a manutenção planejada usando o [Azure PowerShell](maintenance-notifications-powershell.md) ou o [portal](maintenance-notifications-portal.md).
