---
title: Criar um disco gerido a partir de um ficheiro VHD numa conta de armazenamento numa subscrição - PowerShell Sample
description: Exemplo do Script do Azure PowerShell – Criar um disco gerido a partir de um ficheiro VHD numa conta de armazenamento na mesma subscrição ou numa subscrição diferente
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 06/05/2017
ms.author: ramankum
ms.openlocfilehash: d18841da4102d83dd4703bc08abe57090d3a533a
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87010007"
---
# <a name="create-a-managed-disk-from-a-vhd-file-in-a-storage-account-in-same-or-different-subscription-with-powershell"></a>Criar um disco gerido a partir de um ficheiro VHD numa conta de armazenamento na mesma subscrição ou numa subscrição diferente com o PowerShell

Este script cria um disco gerido a partir de um ficheiro VHD numa conta de armazenamento numa mesma subscrição ou numa subscrição diferente. Utilize este script para importar um VHD especializado (não generalizado/processado por sysprep) para o disco do SO gerido para criar uma máquina virtual. Além disso, utilize-o para importar um VHD de dados para o disco de dados gerido. 

Não crie múltiplos discos geridos idênticos de um ficheiro VHD num pequeno período de tempo. Para criar discos geridos de um ficheiro vhd, é criado instantâneos do blob do ficheiro vhd e, em seguida, é utilizado para criar discos geridos. Num minuto apenas pode ser criado um instantâneo do blob que provoca falhas na criação de disco devido à limitação. Para evitar esta limitação, crie um [Instantâneo gerido do ficheiro vhd](virtual-machines-windows-powershell-sample-create-snapshot-from-vhd.md?toc=%2fpowershell%2fmodule%2ftoc.json) e, em seguida, utilize o instantâneo gerido para criar vários discos geridos num pequeno período de tempo. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


 

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-managed-disks-from-vhd-in-different-subscription/create-managed-disks-from-vhd-in-different-subscription.ps1 "Create managed disk from VHD")]


## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para criar um disco gerido a partir de um VHD numa subscrição diferente. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [New-AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig) | Cria a configuração de disco que é utilizada para a criação do disco. Inclui o tipo de armazenamento, localização, Id de recurso da conta de armazenamento onde o VHD principal está armazenado e o URI de VHD do VHD principal. |
| [Novo AzDisco](/powershell/module/az.compute/new-azdisk) | Cria um disco com a configuração de disco, o nome do disco e o nome do grupo de recursos transmitidos como parâmetros. |

## <a name="next-steps"></a>Passos seguintes

[Criar uma máquina virtual ao anexar um disco gerido como disco do SO](./virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Para obter mais informações sobre o módulo do Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/).

Pode ver exemplos adicionais de scripts do PowerShell da máquina virtual na [Documentação da VM Windows do Azure](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
