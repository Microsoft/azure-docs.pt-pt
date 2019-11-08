---
title: Exemplo de Script do Azure PowerShell - Criar um instantâneo de um VHD para criar vários discos geridos idênticos num pequeno período de tempo | Microsoft Docs
description: Exemplo de Script do Azure PowerShell - Criar um instantâneo de um VHD para criar vários discos geridos idênticos num pequeno período de tempo
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 06/05/2017
ms.author: ramankum
ms.openlocfilehash: 04c7e8ffbb747ee17a2da9f86151b7af99855288
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73749789"
---
# <a name="create-a-snapshot-from-a-vhd-to-create-multiple-identical-managed-disks-in-small-amount-of-time-with-powershell"></a>Criar um instantâneo de um VHD para criar vários discos geridos idênticos num pequeno período de tempo com o PowerShell

Este script cria um instantâneo a partir de um ficheiro VHD numa conta de armazenamento numa mesma subscrição ou numa subscrição diferente. Utilize este script para importar um VHD especializado (não generalizado/processado pelo Sysprep) para um instantâneo e, em seguida, utilize o instantâneo para criar vários discos geridos idênticos num pequeno período de tempo. Além disso, utilize-o para importar um VHD de dados para um instantâneo e, em seguida, utilize o instantâneo para criar vários discos geridos num pequeno período de tempo. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


 

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-snapshots-from-vhd-in-different-subscription/create-snapshots-from-vhd-in-different-subscription.ps1 "Create snapshot from VHD")]


## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para criar um disco gerido a partir de um VHD numa subscrição diferente. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [New-AzDiskConfig](https://docs.microsoft.com/powershell/module/az.compute/New-AzDiskConfig) | Cria a configuração de disco que é utilizada para a criação do disco. Inclui o tipo de armazenamento, localização, Id de recurso da conta de armazenamento onde o VHD principal está armazenado e o URI de VHD do VHD principal. |
| [New-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/New-AzDisk) | Cria um disco com a configuração de disco, o nome do disco e o nome do grupo de recursos transmitidos como parâmetros. |

## <a name="next-steps"></a>Passos seguintes

[Criar um disco gerido a partir de um instantâneo](virtual-machines-windows-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json)


[Criar uma máquina virtual ao anexar um disco gerido como disco do SO](./virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Para obter mais informações sobre o módulo do Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/overview).

Pode ver exemplos adicionais de scripts do PowerShell da máquina virtual na [Documentação da VM Windows do Azure](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
