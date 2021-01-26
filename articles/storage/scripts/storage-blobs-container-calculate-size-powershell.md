---
title: Calcular o tamanho de um recipiente de bolhas com PowerShell
titleSuffix: Azure Storage
description: Calcular o tamanho de um recipiente no Azure Blob Storage totalizando o tamanho de cada uma das suas bolhas.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.devlang: powershell
ms.topic: sample
ms.date: 12/04/2019
ms.author: tamram
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 87ef18530c549396b7d8fe1ec4ff0e08cb8535e8
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98784279"
---
# <a name="calculate-the-size-of-a-blob-container-with-powershell"></a>Calcular o tamanho de um recipiente de bolhas com PowerShell

Este script calcula o tamanho de um recipiente no Azure Blob Storage totalizando o tamanho das bolhas no recipiente.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> Este script PowerShell fornece um tamanho estimado para o recipiente e não deve ser utilizado para cálculos de faturação. Para obter um script que calcule o tamanho do recipiente para efeitos de faturação, consulte [calcular o tamanho de um recipiente de armazenamento Blob para efeitos de faturação](../scripts/storage-blobs-container-calculate-billing-size-powershell.md).

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/storage/calculate-container-size/calculate-container-size.ps1 "Calculate container size")]

## <a name="clean-up-deployment"></a>Limpar a implementação

Execute o seguinte comando para remover o grupo de recursos, o contentor e todos os recursos relacionados.

```powershell
Remove-AzResourceGroup -Name bloblisttestrg
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para calcular o tamanho do contentor de armazenamento de Blobs. Cada item na tabela liga para documentação de comandos específicos.

| Comando | Notas |
|---|---|
| [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) | Obtém uma conta de Armazenamento especificada ou todas as contas de Armazenamento num grupo de recursos ou na subscrição. |
| [Get-AzStorageBlob](/powershell/module/az.storage/Get-AzStorageBlob) | Lista bolhas num recipiente. |

## <a name="next-steps"></a>Próximos passos

Para obter um script que calcule o tamanho do recipiente para efeitos de faturação, consulte [calcular o tamanho de um recipiente de armazenamento Blob para efeitos de faturação](../scripts/storage-blobs-container-calculate-billing-size-powershell.md).

Para obter mais informações sobre o módulo do Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/).

Armazenamento adicional Amostras de script PowerShell podem ser encontradas em [amostras PowerShell para armazenamento Azure](../blobs/storage-samples-blobs-powershell.md).
