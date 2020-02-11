---
title: Obtenha notificações de manutenção utilizando o CLI
description: Veja as notificações de manutenção para máquinas virtuais em funcionamento em Azure e inicie a manutenção do self-service, utilizando o Azure CLI.
author: shants123
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: 5750d7cc6219714849aaf3e47b23708b54eefab1
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77115722"
---
# <a name="handling-planned-maintenance-notifications-using-the-azure-cli"></a>Manuseamento de notificações de manutenção planeadas utilizando o Azure CLI

**Este artigo aplica-se a máquinas virtuais que executam tanto o Linux como o Windows.**

Pode utilizar o CLI para ver quando os VMs estão programados para [manutenção](maintenance-notifications.md). As informações de manutenção planeadas estão disponíveis a partir da [visualização az vm get-instance](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-get-instance-view).
 
As informações de manutenção só são devolvidas se houver manutenção planeada. 

```azurecli-interactive
az vm get-instance-view -n myVM -g myResourceGroup --query instanceView.maintenanceRedeployStatus
```

## <a name="start-maintenance"></a>Iniciar a manutenção

A chamada seguinte iniciará a manutenção num VM se `IsCustomerInitiatedMaintenanceAllowed` for em vias de ser verdadeira.

```azurecli-interactive
az vm perform-maintenance -g myResourceGroup -n myVM 
```

## <a name="classic-deployments"></a>Implementações clássicas


Se ainda tiver VMs legados que foram implantados usando o modelo de implementação clássico, pode usar o CLI clássico azure para consultar VMs e iniciar a manutenção.

Certifique-se de que está no modo correto para trabalhar com VM clássico digitando:

```
azure config mode asm
```

Para obter o estado de manutenção de um VM chamado *myVM,* escreva:

```
azure vm show myVM 
``` 

Para iniciar a manutenção do seu VM clássico nomeado *myVM* no serviço *myService* e *myDeployment,* escreva:

```
azure compute virtual-machine initiate-maintenance --service-name myService --name myDeployment --virtual-machine-name myVM
```

## <a name="next-steps"></a>Passos seguintes

Também pode lidar com a manutenção planeada utilizando o [Azure PowerShell](maintenance-notifications-powershell.md) ou [portal](maintenance-notifications-portal.md).
