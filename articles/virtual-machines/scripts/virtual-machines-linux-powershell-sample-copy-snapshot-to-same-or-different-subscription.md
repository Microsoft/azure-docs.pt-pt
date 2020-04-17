---
title: Foto de foto de um disco gerido para uma subscrição - PowerShell Sample
description: Amostra de script Azure PowerShell - Copiar (ou mover) instantâneo de um disco gerido para a mesma ou diferente subscrição
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
ms.openlocfilehash: a86f3e443abc86075fa0f5ff4cc129f871e5e6a3
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460873"
---
# <a name="copy-snapshot-of-a-managed-disk-in-same-subscription-or-different-subscription-with-powershell"></a>Copiar o instantâneo de um disco gerido na mesma subscrição ou numa subscrição diferente com a CLI

Este script copia um instantâneo de um disco gerido para uma subscrição idêntica ou diferente. Utilize este guião para os seguintes cenários:

1. Migrar uma foto no armazenamento Premium (Premium_LRS) para o armazenamento Standard (Standard_LRS ou Standard_ZRS) para reduzir o seu custo.
1. Migrar um instantâneo do armazenamento localmente redundante (Premium_LRS, Standard_LRS) para armazenamento redundante de zona (Standard_ZRS) para beneficiar da maior fiabilidade do armazenamento zRS.
1. Mova um instantâneo para uma subscrição diferente na mesma região para uma retenção mais longa.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-snapshot-to-same-or-different-subscription/copy-snapshot-to-same-or-different-subscription.ps1 "Copy snapshot")]

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para criar um instantâneo na subscrição de destino com o ID do instantâneo de origem. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [Novo AzSnapshotConfig](https://docs.microsoft.com/powershell/module/az.compute/New-AzSnapshotConfig) | Cria a configuração de instantâneo que é utilizada para a criação do instantâneo. Inclui o Id de recurso do instantâneo principal e a localização que é a mesma que o instantâneo principal.  |
| [Novo AzSnapshot](https://docs.microsoft.com/powershell/module/az.compute/New-AzDisk) | Cria um instantâneo com a configuração de instantâneo, o nome do instantâneo e o nome do grupo de recursos transmitidos como parâmetros. |

## <a name="next-steps"></a>Passos seguintes

[Criar uma máquina virtual a partir de um instantâneo](./virtual-machines-linux-powershell-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Para obter mais informações sobre o módulo do Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/overview).

Pode ver exemplos adicionais de scripts do PowerShell da máquina virtual na [Documentação da VM Linux do Azure](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).