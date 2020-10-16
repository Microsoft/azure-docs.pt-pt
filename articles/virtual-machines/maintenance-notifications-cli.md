---
title: Receba notificações de manutenção usando o CLI
description: Ver notificações de manutenção para máquinas virtuais em funcionamento em Azure e iniciar a manutenção de self-service, utilizando o Azure CLI.
author: shants123
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: c449bce76cc9cb7e5f8b9659c11b443e186c65ad
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91306978"
---
# <a name="handling-planned-maintenance-notifications-using-the-azure-cli"></a>Tratamento de notificações de manutenção planeadas utilizando o Azure CLI

**Este artigo aplica-se a máquinas virtuais que executam o Linux e o Windows.**

Pode utilizar o CLI para ver quando estão programados VMs para [manutenção.](maintenance-notifications.md) Informações de manutenção planeadas estão disponíveis a partir de [az vm get-instance-view](/cli/azure/vm?view=azure-cli-latest#az-vm-get-instance-view).
 
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
