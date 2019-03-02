---
title: Exemplo do Script do Azure PowerShell – Criar um disco gerido a partir de um ficheiro VHD numa conta de armazenamento na mesma subscrição ou numa subscrição diferente | Microsoft Docs
description: Exemplo do Script do Azure PowerShell – Criar um disco gerido a partir de um ficheiro VHD numa conta de armazenamento na mesma subscrição ou numa subscrição diferente
services: virtual-machines-linux
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/05/2017
ms.author: ramankum
ms.openlocfilehash: fd62b3e7008bcae4e5d6d91e165f4a382167b1e6
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57248783"
---
# <a name="create-a-managed-disk-from-a-vhd-file-in-a-storage-account-in-same-or-different-subscription-with-powershell"></a>Criar um disco gerido a partir de um ficheiro VHD numa conta de armazenamento na mesma subscrição ou numa subscrição diferente com o PowerShell

Este script cria um disco gerido a partir de um ficheiro VHD numa conta de armazenamento numa mesma subscrição ou numa subscrição diferente. Utilize este script para importar um VHD especializado (não generalizado/processado por sysprep) para o disco do SO gerido para criar uma máquina virtual. Além disso, utilize-o para importar um VHD de dados para o disco de dados gerido.

Não crie múltiplos discos geridos idênticos de um ficheiro VHD num pequeno período de tempo. Para criar discos geridos de um ficheiro vhd, é criado instantâneos do blob do ficheiro vhd e, em seguida, é utilizado para criar discos geridos. Num minuto apenas pode ser criado um instantâneo do blob que provoca falhas na criação de disco devido à limitação. Para evitar esta limitação, crie um [Instantâneo gerido do ficheiro vhd](virtual-machines-linux-powershell-sample-create-snapshot-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) e, em seguida, utilize o instantâneo gerido para criar vários discos geridos num pequeno período de tempo.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-managed-disks-from-vhd-in-different-subscription/create-managed-disks-from-vhd-in-different-subscription.ps1 "Create managed disk from VHD")]

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para criar um disco gerido a partir de um VHD numa subscrição diferente. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [New-AzDiskConfig](https://docs.microsoft.com/powershell/module/az.compute/New-AzDiskConfig) | Cria a configuração de disco que é utilizada para a criação do disco. Inclui o tipo de armazenamento, localização, Id de recurso da conta de armazenamento onde o VHD principal está armazenado e o URI de VHD do VHD principal. |
| [New-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/New-AzDisk) | Cria um disco com a configuração de disco, o nome do disco e o nome do grupo de recursos transmitidos como parâmetros. |

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre o módulo do Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/overview).

Pode ver exemplos adicionais de scripts do PowerShell da máquina virtual na [Documentação da VM Linux do Azure](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
