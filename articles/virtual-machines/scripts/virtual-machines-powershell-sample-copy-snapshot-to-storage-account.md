---
title: Amostra PowerShell - Amostra de exportação/Cópia instantânea como VHD para uma conta de armazenamento em diferentes regiões
description: Amostra de script Azure PowerShell - Amostra de exportação/cópia instantânea como VHD para uma conta de armazenamento na mesma região diferente
documentationcenter: storage
author: ramankumarlive
manager: kavithag
ms.service: virtual-machines
ms.subservice: disks
ms.topic: sample
ms.workload: infrastructure
ms.date: 06/05/2017
ms.author: ramankum
ms.openlocfilehash: 389c256e951c8a97f0233e5082ea13cc1573302d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89320380"
---
# <a name="exportcopy-managed-snapshots-as-vhd-to-a-storage-account-in-different-region-with-powershell"></a>Exportação/Cópia geriu instantâneos como VHD para uma conta de armazenamento em diferentes regiões com PowerShell

Este script exporta um instantâneo gerido para uma conta de armazenamento numa região diferente. Gera primeiro o URI de SAS do instantâneo e, em seguida, utiliza-o para o copiar para uma conta de armazenamento numa região diferente. Utilize este script para manter a cópia de segurança dos discos geridos numa região diferente para recuperação após desastre.  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-snapshot-to-storage-account/copy-snapshot-to-storage-account.ps1 "Copy snapshot")]


## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para gerar o URI de SAS para um instantâneo gerido e copia o instantâneo para uma conta de armazenamento com o URI de SAS. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [Grant-AzSnapshotAccess](/powershell/module/az.compute/new-azdisk) | Gera SAS URI para um instantâneo que é usado para copiá-lo para uma conta de armazenamento. |
| [New-AzureStorageContext](/powershell/module/azure.storage/new-azurestoragecontext) | Cria um contexto de conta de armazenamento com o nome e a chave da conta. Este contexto pode ser utilizado para executar operações de leitura/escrita na conta de armazenamento. |
| [Start-AzureStorageBlobCopy](/powershell/module/azure.storage/start-azurestorageblobcopy) | Copia o VHD subjacente de um instantâneo para uma conta de armazenamento |

## <a name="next-steps"></a>Passos seguintes

[Criar um disco gerido a partir de um VHD](virtual-machines-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Criar uma máquina virtual a partir de um disco gerido](./virtual-machines-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Para obter mais informações sobre o módulo do Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/).

Pode ver exemplos adicionais de scripts do PowerShell da máquina virtual na [Documentação da VM Linux do Azure](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
