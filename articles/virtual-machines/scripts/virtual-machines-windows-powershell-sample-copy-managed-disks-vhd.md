---
title: Exportar/Copiar o VHD de um disco gerido para uma conta de armazenamento em diferentes regiões - Amostra PowerShell
description: Exemplo de script do Azure PowerShell - exportar/copiar o VHD de um disco gerido para uma conta de armazenamento na mesma região ou numa região diferente
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
ms.date: 09/17/2018
ms.author: ramankum
ms.openlocfilehash: 8388863bb88e3e97d16ad7c80aad6387cf9e03b9
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81459377"
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
| [Grant-AzDiskAccess](https://docs.microsoft.com/powershell/module/az.compute/grant-azdiskaccess) | Gera o URI de SAS para um disco gerido, que é utilizado para copiar o VHD subjacente para uma conta de armazenamento. |
| [New-AzureStorageContext](https://docs.microsoft.com/powershell/module/azure.storage/New-AzureStorageContext) | Cria um contexto de conta de armazenamento com o nome e a chave da conta. Este contexto pode ser utilizado para executar operações de leitura/escrita na conta de armazenamento. |
| [Start-AzureStorageBlobCopy](https://docs.microsoft.com/powershell/module/azure.storage/Start-AzureStorageBlobCopy) | Copia o VHD subjacente de um instantâneo para uma conta de armazenamento |

## <a name="next-steps"></a>Passos seguintes

[Criar um disco gerido a partir de um VHD](virtual-machines-windows-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fpowershell%2fmodule%2ftoc.json)

[Criar uma máquina virtual a partir de um disco gerido](./virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Para obter mais informações sobre o módulo do Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/overview).

Pode ver exemplos adicionais de scripts do PowerShell da máquina virtual na [Documentação da VM Windows do Azure](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).