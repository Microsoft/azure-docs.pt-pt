---
title: Snapshot do disco gerido para subscrição (Windows) - PowerShell
description: Exemplo do Script do Azure PowerShell – Copiar (mover) o instantâneo de um disco gerido para uma subscrição idêntica ou diferente
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
ms.date: 02/28/2019
ms.author: ramankum
ms.openlocfilehash: 0dadb4102a16d63b028e7202e2d7a94f135d06bb
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86501072"
---
# <a name="copy-snapshot-of-a-managed-disk-in-same-subscription-or-different-subscription-with-powershell-windows"></a>Copiar instantâneo de um disco gerido na mesma subscrição ou subscrição diferente com PowerShell (Windows)

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
| [New-AzSnapshot](/powershell/module/az.compute/new-azsnapshot) | Cria um instantâneo com a configuração de instantâneo, o nome do instantâneo e o nome do grupo de recursos transmitidos como parâmetros. |

## <a name="next-steps"></a>Próximos passos

[Criar uma máquina virtual a partir de um instantâneo](./virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

Para obter mais informações sobre o módulo do Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/overview).

Pode ver exemplos adicionais de scripts do PowerShell da máquina virtual na [Documentação da VM Windows do Azure](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
