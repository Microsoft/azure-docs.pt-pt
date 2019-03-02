---
title: Exemplo do Script do Azure PowerShell – Criar um disco gerido a partir de um instantâneo | Microsoft Docs
description: Exemplo do Script do Azure PowerShell – Criar um disco gerido a partir de um instantâneo
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
ms.openlocfilehash: 055a24aac01b58178e8a4e24986786eeffff3430
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57248747"
---
# <a name="create-a-managed-disk-from-a-snapshot-with-powershell"></a>Criar um disco gerido a partir de um instantâneo com o PowerShell

Este script cria um disco gerido a partir de um instantâneo. Utiliza-o para restaurar uma máquina virtual a partir de instantâneos do SO e discos de dados. Crie discos do SO e discos geridos de dados a partir dos respetivos instantâneos e, em seguida, crie uma nova máquina virtual ao anexar discos geridos. Também pode restaurar discos de dados de uma VM existente ao anexar discos de dados criados a partir de instantâneos.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-managed-disk-from-snapshot/create-managed-disk-from-snapshot.ps1 "Create managed disk from snapshot")]

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para criar um disco gerido a partir de um instantâneo. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [Get-AzSnapshot](https://docs.microsoft.com/powershell/module/az.compute/Get-AzSnapshot) | Obtém as propriedades do instantâneo.  |
| [New-AzDiskConfig](https://docs.microsoft.com/powershell/module/az.compute/New-AzDiskConfig) | Cria a configuração de disco que é utilizada para a criação do disco. Inclui o Id de recurso do instantâneo principal, a localização que seja a mesma que a localização do instantâneo principal e o tipo de armazenamento.  |
| [New-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/New-AzDisk) | Cria um disco com a configuração de disco, o nome do disco e o nome do grupo de recursos transmitidos como parâmetros. |

## <a name="next-steps"></a>Passos Seguintes


[Criar uma máquina virtual a partir de um disco gerido](./virtual-machines-linux-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

Para obter mais informações sobre o módulo do Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/overview).

Pode ver exemplos adicionais de scripts do PowerShell da máquina virtual na [Documentação da VM Linux do Azure](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
