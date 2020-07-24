---
title: Snapshot do disco gerido para subscrição (Linux) - PowerShell
description: Amostra de script Azure PowerShell - Copiar (ou mover) instantâneo de um disco gerido para a mesma subscrição ou diferente
services: virtual-machines-linux
documentationcenter: storage
author: ramankumarlive
manager: kavithag
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/06/2017
ms.author: ramankum
ms.openlocfilehash: 54fbdc86ecd035593960eaa57187fbf9e35393fc
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87069312"
---
# <a name="copy-snapshot-of-a-managed-disk-in-same-subscription-or-different-subscription-with-powershell-linux"></a>Copiar instantâneo de um disco gerido na mesma subscrição ou subscrição diferente com PowerShell (Linux)

Este script copia um instantâneo de um disco gerido para uma subscrição idêntica ou diferente. Utilize este script para os seguintes cenários:

1. Migrar um instantâneo em armazenamento Premium (Premium_LRS) para o armazenamento Standard (Standard_LRS ou Standard_ZRS) para reduzir o seu custo.
1. Migrar um instantâneo do armazenamento localmente redundante (Premium_LRS, Standard_LRS) para a zona de armazenamento redundante (Standard_ZRS) para beneficiar da maior fiabilidade do armazenamento ZRS.
1. Mover um instantâneo para diferentes subscrições na mesma região para uma retenção mais longa.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-snapshot-to-same-or-different-subscription/copy-snapshot-to-same-or-different-subscription.ps1 "Copy snapshot")]

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para criar um instantâneo na subscrição de destino com o ID do instantâneo de origem. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [New-AzSnapshotConfig](/powershell/module/az.compute/new-azsnapshotconfig) | Cria a configuração de instantâneo que é utilizada para a criação do instantâneo. Inclui o Id de recurso do instantâneo principal e a localização que é a mesma que o instantâneo principal.  |
| [New-AzSnapshot](/powershell/module/az.compute/new-azdisk) | Cria um instantâneo com a configuração de instantâneo, o nome do instantâneo e o nome do grupo de recursos transmitidos como parâmetros. |

## <a name="next-steps"></a>Passos seguintes

[Criar uma máquina virtual a partir de um instantâneo](./virtual-machines-linux-powershell-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Para obter mais informações sobre o módulo do Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/).

Pode ver exemplos adicionais de scripts do PowerShell da máquina virtual na [Documentação da VM Linux do Azure](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
