---
title: Copiar discos geridos para subscrição - Amostra PowerShell
description: Amostra de script Azure PowerShell - Copiar ou mover discos geridos para a mesma subscrição ou uma subscrição diferente
documentationcenter: storage
author: ramankumarlive
manager: kavithag
ms.service: virtual-machines
ms.subservice: disks
ms.topic: sample
ms.workload: infrastructure
ms.date: 06/06/2017
ms.author: ramankum
ms.openlocfilehash: e682a1546297e7715a00c7c174ad9a17021e6029
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89320484"
---
# <a name="copy-managed-disks-in-the-same-subscription-or-different-subscription-with-powershell"></a>Copiar discos geridos na mesma subscrição ou subscrição diferente com PowerShell

Este script cria uma cópia de um disco gerido existente na mesma subscrição ou subscrição diferente. O novo disco é criado na mesma região que o disco gerido pelos pais.   

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-managed-disks-to-same-or-different-subscription/copy-managed-disks-to-same-or-different-subscription.ps1 "Copy managed disk")]


## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para criar um novo disco gerido na subscrição de destino com o ID do disco gerido de origem. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [New-AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig) | Cria a configuração de disco que é utilizada para a criação do disco. Inclui o ID de recursos do disco e localização dos pais que é igual à localização do disco-mãe.  |
| [Novo AzDisco](/powershell/module/az.compute/new-azdisk) | Cria um disco com a configuração de disco, o nome do disco e o nome do grupo de recursos transmitidos como parâmetros. |


## <a name="next-steps"></a>Passos seguintes

[Criar uma máquina virtual a partir de um disco gerido](./virtual-machines-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Para obter mais informações sobre o módulo do Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/).

Pode ver exemplos adicionais de scripts do PowerShell da máquina virtual na [Documentação da VM Windows do Azure](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
