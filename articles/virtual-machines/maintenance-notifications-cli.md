---
title: Receba notificações de manutenção usando o CLI
description: Ver notificações de manutenção para máquinas virtuais em funcionamento em Azure e iniciar a manutenção de self-service, utilizando o Azure CLI.
author: shants123
ms.service: virtual-machines
ms.subservice: maintenance-control
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: d8a9b7ec6425a3cd32b597c3f14f8227fde67064
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777874"
---
# <a name="handling-planned-maintenance-notifications-using-the-azure-cli"></a>Tratamento de notificações de manutenção planeadas utilizando o Azure CLI

**Este artigo aplica-se a máquinas virtuais que executam o Linux e o Windows.**

Pode utilizar o CLI para ver quando estão programados VMs para [manutenção.](maintenance-notifications.md) Informações de manutenção planeadas estão disponíveis a partir de [az vm get-instance-view](/cli/azure/vm#az_vm_get_instance_view).
 
As informações de manutenção só são devolvidas se houver manutenção planeada. 

```azurecli-interactive
az vm get-instance-view -n myVM -g myResourceGroup --query instanceView.maintenanceRedeployStatus
```

Saída
```
      "maintenanceRedeployStatus": {
      "additionalProperties": {},
      "isCustomerInitiatedMaintenanceAllowed": true,
      "lastOperationMessage": null,
      "lastOperationResultCode": "None",
      "maintenanceWindowEndTime": "2018-06-04T16:30:00+00:00",
      "maintenanceWindowStartTime": "2018-05-21T16:30:00+00:00",
      "preMaintenanceWindowEndTime": "2018-05-19T12:30:00+00:00",
      "preMaintenanceWindowStartTime": "2018-05-14T12:30:00+00:00"
```

## <a name="start-maintenance"></a>Iniciar a manutenção

A chamada seguinte iniciará a manutenção num VM se `IsCustomerInitiatedMaintenanceAllowed` for definido como verdadeiro.

```azurecli-interactive
az vm perform-maintenance -g myResourceGroup -n myVM 
```

## <a name="classic-deployments"></a>Implementações clássicas

[!INCLUDE [classic-vm-deprecation](../../includes/classic-vm-deprecation.md)]

Se ainda tiver VMs legados que foram implantados usando o modelo de implementação clássico, pode usar o CLI clássico do Azure para consultar VMs e iniciar a manutenção.

Certifique-se de que está no modo correto para trabalhar com vm clássicos digitando:

```
azure config mode asm
```

Para obter o estado de manutenção de um VM chamado *myVM,* escreva:

```
azure vm show myVM 
``` 

Para iniciar a manutenção do seu VM clássico nomeado *myVM* no serviço *myService* e na minha implementação *de Deployment, escreva:*

```
azure compute virtual-machine initiate-maintenance --service-name myService --name myDeployment --virtual-machine-name myVM
```

## <a name="next-steps"></a>Passos seguintes

Também pode manusear a manutenção planeada utilizando o [Azure PowerShell](maintenance-notifications-powershell.md) ou [o portal](maintenance-notifications-portal.md).
