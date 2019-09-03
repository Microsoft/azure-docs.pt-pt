---
title: Habilitar e desabilitar o console serial do Azure | Microsoft Docs
description: Como habilitar e desabilitar o serviço de console serial do Azure
services: virtual-machines
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 8/20/2019
ms.author: alsin
ms.openlocfilehash: 1c1fe208c77142351a786fa636896e64a8a467d7
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70129656"
---
# <a name="enable-and-disable-the-azure-serial-console"></a>Habilitar e desabilitar o console serial do Azure

Assim como qualquer outro recurso, o console serial do Azure pode ser habilitado e desabilitado. O console serial está habilitado por padrão para todas as assinaturas no Azure global. Atualmente, desabilitar o console serial desabilitará o serviço para toda a sua assinatura. Desabilitar ou reabilitar o console serial para uma assinatura requer acesso no nível de colaborador ou acima na assinatura.

Você também pode desabilitar o console serial para uma VM individual ou instância do conjunto de dimensionamento de máquinas virtuais desabilitando o diagnóstico de inicialização. Você precisará de acesso no nível de colaborador ou acima no conjunto de dimensionamento de máquinas virtuais/VM e na sua conta de armazenamento de diagnóstico de inicialização.

## <a name="vm-level-disable"></a>Desativar ao nível da VM
O console serial pode ser desabilitado para uma VM específica ou um conjunto de dimensionamento de máquinas virtuais desabilitando a configuração de diagnóstico de inicialização. Desative o diagnóstico de inicialização do portal do Azure para desabilitar o console serial para a VM ou o conjunto de dimensionamento de máquinas virtuais. Se você estiver usando o console serial em um conjunto de dimensionamento de máquinas virtuais, certifique-se de atualizar suas instâncias do conjunto de dimensionamento de máquinas virtuais para o modelo mais recente.


## <a name="subscription-level-disable"></a>Desativar o nível de assinatura

### <a name="azure-cli"></a>CLI do Azure

Console serial pode ser desabilitado e habilitado novamente para uma assinatura inteira usando os seguintes comandos na CLI do Azure:

Para desabilitar o console serial para uma assinatura, use os seguintes comandos:
```azurecli-interactive
subscriptionId=$(az account show -o=json | jq -r .id)

az resource invoke-action --action disableConsole --ids "/subscriptions/$subscriptionId/providers/Microsoft.SerialConsole/consoleServices/default"
```

Para habilitar o console serial para uma assinatura, use os seguintes comandos:
```azurecli-interactive
subscriptionId=$(az account show -o=json | jq -r .id)

az resource invoke-action --action enableConsole --ids "/subscriptions/$subscriptionId/providers/Microsoft.SerialConsole/consoleServices/default"
```

Para obter o status atual habilitado/desabilitado do console serial para uma assinatura, use os seguintes comandos:
```azurecli-interactive
subscriptionId=$(az account show -o=json | jq -r .id)

az resource show --ids "/subscriptions/$subscriptionId/providers/Microsoft.SerialConsole/consoleServices/default" -o=json | jq .properties
```

### <a name="powershell"></a>PowerShell

Console serial também pode ser habilitado e desabilitado usando o PowerShell.

Para desabilitar o console serial para uma assinatura, use os seguintes comandos:
```azurepowershell-interactive
$subscription=(Get-AzContext).Subscription.Id

Invoke-AzResourceAction -Action disableConsole -ResourceId /subscriptions/$subscription/providers/Microsoft.SerialConsole/consoleServices/default -ApiVersion 2018-05-01
```

Para habilitar o console serial para uma assinatura, use os seguintes comandos:
```azurepowershell-interactive
$subscription=(Get-AzContext).Subscription.Id

Invoke-AzResourceAction -Action enableConsole -ResourceId /subscriptions/$subscription/providers/Microsoft.SerialConsole/consoleServices/default -ApiVersion 2018-05-01
```

## <a name="next-steps"></a>Passos Seguintes
* Saiba mais sobre o [console serial do Azure para VMs Linux](./serial-console-linux.md)
* Saiba mais sobre o [console serial do Azure para VMs do Windows](./serial-console-windows.md)
* Saiba mais sobre [as opções de gerenciamento de energia no console serial do Azure](./serial-console-power-options.md)