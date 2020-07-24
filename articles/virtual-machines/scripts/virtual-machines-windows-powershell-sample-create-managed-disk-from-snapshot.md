---
title: Criar disco gerido a partir de snapshot (Windows) - Amostra PowerShell
description: Exemplo do Script do Azure PowerShell – Criar um disco gerido a partir de um instantâneo
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
ms.openlocfilehash: c3b429e9aab26313ace9dbc3672b08d03ec78c15
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87088619"
---
# <a name="create-a-managed-disk-from-a-snapshot-with-powershell-windows"></a>Criar um disco gerido a partir de um instantâneo com PowerShell (Windows)

Este script cria um disco gerido a partir de um instantâneo. Utiliza-o para restaurar uma máquina virtual a partir de instantâneos do SO e discos de dados. Crie discos do SO e discos geridos de dados a partir dos respetivos instantâneos e, em seguida, crie uma nova máquina virtual ao anexar discos geridos. Também pode restaurar discos de dados de uma VM existente ao anexar discos de dados criados a partir de instantâneos.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-managed-disk-from-snapshot/create-managed-disk-from-snapshot.ps1 "Create managed disk from snapshot")]


## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para criar um disco gerido a partir de um instantâneo. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [Get-AzSnapshot](/powershell/module/az.compute/get-azsnapshot) | Obtém as propriedades do instantâneo.  |
| [New-AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig) | Cria a configuração de disco que é utilizada para a criação do disco. Inclui o Id de recurso do instantâneo principal, a localização que seja a mesma que a localização do instantâneo principal e o tipo de armazenamento.  |
| [Novo AzDisco](/powershell/module/az.compute/new-azdisk) | Cria um disco com a configuração de disco, o nome do disco e o nome do grupo de recursos transmitidos como parâmetros. |


## <a name="next-steps"></a>Passos seguintes

[Criar uma máquina virtual a partir de um disco gerido](./virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Para obter mais informações sobre o módulo do Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/).

Pode ver exemplos adicionais de scripts do PowerShell da máquina virtual na [Documentação da VM Windows do Azure](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
