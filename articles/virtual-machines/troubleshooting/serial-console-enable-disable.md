---
title: Ativar e desativar a Consola Em Série Azure [ Microsoft Docs
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75451307"
---
# <a name="enable-and-disable-the-azure-serial-console"></a>Ativar e desativar a consola em série Azure

Tal como qualquer outro recurso, a Consola Em Série Azure pode ser ativada e desativada. A Consola de Série é ativada por padrão para todas as subscrições no Global Azure. Atualmente, a Desativação da Consola Em Série irá desativar o serviço para toda a sua subscrição. Desativar ou reativar a Consola Em Série para uma subscrição requer acesso ao nível dos contribuintes ou superior na subscrição.

Também pode desativar a consola em série para uma instância de conjunto de vm ou de escala virtual de máquina, desativando os diagnósticos de arranque. Necessitará de acesso ao nível do contribuinte ou superior tanto no conjunto de escala VM/máquina virtual como na sua conta de armazenamento de diagnóstico de arranque.

## <a name="vm-level-disable"></a>Desativação do nível VM
A consola em série pode ser desativada para um conjunto específico de vm ou máquina virtual, desativando a definição de diagnóstico de arranque. Desligue os diagnósticos de boot do portal Azure para desativar a consola em série para o VM ou o conjunto de escala de máquina virtual. Se estiver a utilizar uma consola em série num conjunto de escala de máquina virtual, certifique-se de atualizar as instâncias de conjunto de escala de máquinavirtual para o modelo mais recente.


## <a name="subscription-level-enabledisable"></a>Ativação/desativação a nível de subscrição

> [!NOTE]
> Certifique-se de que está na nuvem certa (Azure Public Cloud, Azure US Government Cloud) antes de executar este comando. Pode verificar `az cloud list` e definir a `az cloud set -n <Name of cloud>`sua nuvem com .

### <a name="azure-cli"></a>CLI do Azure

A consola em série pode ser desativada e reativada para uma subscrição inteira utilizando os seguintes comandos no ClI Azure (pode utilizar o botão "Experimente".

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
* Saiba mais sobre a [Consola Em Série Azure para VMs Linux](./serial-console-linux.md)
* Saiba mais sobre a [consola em série Azure para VMs do Windows](./serial-console-windows.md)
* Conheça [as opções](./serial-console-power-options.md) de gestão de energia dentro da Consola Série Azure