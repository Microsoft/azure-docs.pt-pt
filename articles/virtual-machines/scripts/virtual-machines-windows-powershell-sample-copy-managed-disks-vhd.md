---
title: Exemplo de script do Azure PowerShell - exportar/copiar o VHD de um disco gerido para uma conta de armazenamento numa região diferente | Microsoft Docs
description: Exemplo de script do Azure PowerShell - exportar/copiar o VHD de um disco gerido para uma conta de armazenamento na mesma região ou numa região diferente
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/17/2018
ms.author: ramankum
ms.openlocfilehash: 978ac07037e1b7e29d83cc3258df01c6f902cd36
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/02/2018
ms.locfileid: "48045266"
---
# <a name="exportcopy-the-vhd-of-a-managed-disk-to-a-storage-account-in-different-region-with-powershell"></a>Exportar/copiar o VHD de um disco gerido para uma conta de armazenamento numa região diferente com o PowerShell

Este script exporta o VHD de um disco gerido para uma conta de armazenamento numa região diferente. Gera primeiro o URI de SAS do disco gerido e, em seguida, utiliza-o para copiar o VHD subjacente para uma conta de armazenamento numa região diferente. Utilize este script para copiar discos geridos para outra região, para expansão a nível regional.  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-managed-disks-vhd-to-storage-account/copy-managed-disks-vhd-to-storage-account.ps1 "Copy the VHD of a managed disk")]


## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para gerar o URI de SAS de um disco gerido e copia o VHD subjacente para uma conta de armazenamento com o URI de SAS. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [Grant-AzureRmDiskAccess](/powershell/module/azurerm.compute/grant-azurermdiskaccess) | Gera o URI de SAS para um disco gerido, que é utilizado para copiar o VHD subjacente para uma conta de armazenamento. |
| [New-AzureStorageContext](/powershell/module/azure.storage/New-AzureStorageContext) | Cria um contexto de conta de armazenamento com o nome e a chave da conta. Este contexto pode ser utilizado para executar operações de leitura/escrita na conta de armazenamento. |
| [Start-AzureStorageBlobCopy](/powershell/module/azure.storage/Start-AzureStorageBlobCopy) | Copia o VHD subjacente de um instantâneo para uma conta de armazenamento |

## <a name="next-steps"></a>Passos seguintes

[Criar um disco gerido a partir de um VHD](virtual-machines-windows-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fpowershell%2fmodule%2ftoc.json)

[Criar uma máquina virtual a partir de um disco gerido](./virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Para obter mais informações sobre o módulo do Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/overview).

Pode ver exemplos adicionais de scripts do PowerShell da máquina virtual na [Documentação da VM Windows do Azure](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).