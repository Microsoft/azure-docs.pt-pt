---
title: Ativar e desativar a Consola em Série Azure ! Microsoft Docs
description: Como ativar e desativar o serviço de consola em série Azure
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
ms.openlocfilehash: e09e08f8ba36cf576bc27551254225adee3bb0fd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "75451307"
---
# <a name="enable-and-disable-the-azure-serial-console"></a>Ativar e desativar a Consola em Série Azure

Tal como qualquer outro recurso, a Consola em Série Azure pode ser ativada e desativada. A Consola em Série é ativada por padrão para todas as subscrições do Azure global. Atualmente, a desativação da Consola Em Série irá desativar o serviço para toda a sua subscrição. Desativar ou reativar a Consola em Série para uma subscrição requer acesso ao nível do contribuinte ou superior na subscrição.

Também pode desativar a consola em série para um conjunto de escala de VM ou de uma máquina virtual, desativando diagnósticos de arranque. Necessitará de acesso ao nível do contribuinte ou acima tanto no conjunto de escala de vm/máquina virtual como na conta de armazenamento de diagnóstico de arranque.

## <a name="vm-level-disable"></a>Desativação ao nível VM
A consola em série pode ser desativada para uma balança de máquinas VM ou virtual definida desativando a definição de diagnóstico de arranque. Desligue os diagnósticos de arranque do portal Azure para desativar a consola em série para o VM ou o conjunto de escala de máquina virtual. Se estiver a utilizar uma consola em série num conjunto de escala de máquina virtual, certifique-se de que atualiza as instâncias de escala de máquina virtual para o modelo mais recente.


## <a name="subscription-level-enabledisable"></a>Ativação/desativação do nível de subscrição

> [!NOTE]
> Certifique-se de que está na nuvem certa (Azure Public Cloud, Azure US Government Cloud) antes de executar este comando. Pode consultar `az cloud list` e definir a sua nuvem com `az cloud set -n <Name of cloud>` .

### <a name="azure-cli"></a>CLI do Azure

A consola em série pode ser desativada e reativada para uma subscrição completa utilizando os seguintes comandos no CLI Azure (pode utilizar o botão "Experimente" para lançar uma instância da Azure Cloud Shell na qual pode executar os comandos):

Para desativar a consola em série para uma subscrição, utilize os seguintes comandos:
```azurecli-interactive
subscriptionId=$(az account show --output=json | jq -r .id)

az resource invoke-action --action disableConsole --ids "/subscriptions/$subscriptionId/providers/Microsoft.SerialConsole/consoleServices/default" --api-version="2018-05-01"
```

Para ativar a consola em série para uma subscrição, utilize os seguintes comandos:
```azurecli-interactive
subscriptionId=$(az account show --output=json | jq -r .id)

az resource invoke-action --action enableConsole --ids "/subscriptions/$subscriptionId/providers/Microsoft.SerialConsole/consoleServices/default" --api-version="2018-05-01"
```

Para obter o estado atual ativado/desativado da consola em série para uma subscrição, utilize os seguintes comandos:
```azurecli-interactive
subscriptionId=$(az account show --output=json | jq -r .id)

az resource show --ids "/subscriptions/$subscriptionId/providers/Microsoft.SerialConsole/consoleServices/default" --output=json --api-version="2018-05-01" | jq .properties
```

### <a name="powershell"></a>PowerShell

A consola em série também pode ser ativada e desativada utilizando o PowerShell.

Para desativar a consola em série para uma subscrição, utilize os seguintes comandos:
```azurepowershell-interactive
$subscription=(Get-AzContext).Subscription.Id

Invoke-AzResourceAction -Action disableConsole -ResourceId /subscriptions/$subscription/providers/Microsoft.SerialConsole/consoleServices/default -ApiVersion 2018-05-01
```

Para ativar a consola em série para uma subscrição, utilize os seguintes comandos:
```azurepowershell-interactive
$subscription=(Get-AzContext).Subscription.Id

Invoke-AzResourceAction -Action enableConsole -ResourceId /subscriptions/$subscription/providers/Microsoft.SerialConsole/consoleServices/default -ApiVersion 2018-05-01
```

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre a [Consola Em Série Azure para Os VMs Linux](./serial-console-linux.md)
* Saiba mais sobre a [Consola Em Série Azure para VMs Windows](./serial-console-windows.md)
* Conheça as [opções de gestão de energia dentro da Consola em Série Azure](./serial-console-power-options.md)