---
title: Calcular tamanho de um recipiente de bolha com PowerShell
titleSuffix: Azure Storage
description: Calcular o tamanho de um recipiente no armazenamento de Azure Blob, totalizando o tamanho de cada uma das suas bolhas.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.devlang: powershell
ms.topic: sample
ms.date: 12/04/2019
ms.author: tamram
ms.openlocfilehash: de51ed7d91ba1102f5a9cd376ab95f49dd54d9f3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "80067062"
---
# <a name="calculate-the-size-of-a-blob-container-with-powershell"></a>Calcular o tamanho de um recipiente de bolha com PowerShell

Este script calcula o tamanho de um contentor no armazenamento do Blobs do Azure ao calcular o tamanho total dos blobs no contentor.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> Este script PowerShell fornece um tamanho estimado para o recipiente e não deve ser utilizado para cálculos de faturação. Para um script que calcule o tamanho do recipiente para efeitos de faturação, consulte [Calcular o tamanho de um recipiente de armazenamento Blob para efeitos](../scripts/storage-blobs-container-calculate-billing-size-powershell.md)de faturação .

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

## <a name="next-steps"></a>Passos seguintes

Para um script que calcule o tamanho do recipiente para efeitos de faturação, consulte [Calcular o tamanho de um recipiente de armazenamento Blob para efeitos](../scripts/storage-blobs-container-calculate-billing-size-powershell.md)de faturação .

Para obter mais informações sobre o módulo do Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/overview).

Armazenamento adicional As amostras de script PowerShell podem ser encontradas em [amostras powerShell para armazenamento azure](../blobs/storage-samples-blobs-powershell.md).
