---
title: Script do Azure PowerShell de exemplo - calcular o tamanho de contentor do blob | Documentos da Microsoft
description: Calcule o tamanho de um contentor no armazenamento de Blobs do Azure ao calcular o tamanho de cada um dos respetivos blobs total.
services: storage
documentationcenter: na
author: tamram
manager: jeconnoc
editor: tysonn
ms.assetid: ''
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: powershell
ms.topic: sample
ms.date: 11/07/2017
ms.author: tamram
ms.openlocfilehash: d8baec875c25556f1080cdd105c7fa466ffce74e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58094012"
---
# <a name="calculate-the-size-of-a-blob-storage-container"></a>Calcular o tamanho de um contentor de armazenamento de Blobs

Este script calcula o tamanho de um contentor no armazenamento do Blobs do Azure ao calcular o tamanho total dos blobs no contentor.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> Este script do PowerShell fornece um tamanho estimado do contentor e não deve ser utilizada para cálculos de faturação. Para obter um script que calcule o tamanho do contentor para efeitos de faturação, veja [calcular o tamanho de um contentor de armazenamento de BLOBs para fins de faturação](../scripts/storage-blobs-container-calculate-billing-size-powershell.md). 

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
| [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) | Obtém uma conta de armazenamento especificada ou todas as contas de armazenamento num grupo de recursos ou subscrição. |
| [Get-AzStorageBlob](/powershell/module/az.storage/Get-AzStorageBlob) | Lista os blobs num contentor. |

## <a name="next-steps"></a>Passos Seguintes

Para obter um script que calcule o tamanho do contentor para efeitos de faturação, veja [calcular o tamanho de um contentor de armazenamento de BLOBs para fins de faturação](../scripts/storage-blobs-container-calculate-billing-size-powershell.md).

Para obter mais informações sobre o módulo do Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/overview).

Exemplos de scripts do PowerShell de armazenamento adicional podem ser encontrados na [exemplos do PowerShell do armazenamento do Azure](../blobs/storage-samples-blobs-powershell.md).
