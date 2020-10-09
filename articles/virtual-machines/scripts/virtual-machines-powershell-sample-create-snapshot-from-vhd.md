---
title: Foto de VHD para fazer muitos discos geridos idênticos (Windows) - PowerShell
description: Exemplo de Script do Azure PowerShell - Criar um instantâneo de um VHD para criar vários discos geridos idênticos num pequeno período de tempo
documentationcenter: storage
author: ramankumarlive
manager: kavithag
ms.service: virtual-machines
ms.subservice: disks
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 06/05/2017
ms.author: ramankum
ms.openlocfilehash: 47ade94ad2e0be0ce44d346336c6c7eea4fca78b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89323184"
---
# <a name="create-a-snapshot-from-a-vhd-to-create-multiple-identical-managed-disks-in-small-amount-of-time-with-powershell-windows"></a>Crie um instantâneo a partir de um VHD para criar vários discos geridos idênticos em pouco tempo com PowerShell (Windows)

Este script cria um instantâneo a partir de um ficheiro VHD numa conta de armazenamento numa mesma subscrição ou numa subscrição diferente. Utilize este script para importar um VHD especializado (não generalizado/processado pelo Sysprep) para um instantâneo e, em seguida, utilize o instantâneo para criar vários discos geridos idênticos num pequeno período de tempo. Além disso, utilize-o para importar um VHD de dados para um instantâneo e, em seguida, utilize o instantâneo para criar vários discos geridos num pequeno período de tempo. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


 

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-snapshots-from-vhd-in-different-subscription/create-snapshots-from-vhd-in-different-subscription.ps1 "Create snapshot from VHD")]


## <a name="next-steps"></a>Passos seguintes

[Criar um disco gerido a partir de um instantâneo](virtual-machines-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json)


[Criar uma máquina virtual ao anexar um disco gerido como disco do SO](./virtual-machines-powershell-sample-create-vm-from-managed-os-disks.md)

Para obter mais informações sobre o módulo do Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/).

Pode ver exemplos adicionais de scripts do PowerShell da máquina virtual na [Documentação da VM Windows do Azure](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
