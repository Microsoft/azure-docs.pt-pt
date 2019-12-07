---
title: Calcular o tamanho de um contêiner de blob com o PowerShell
titleSuffix: Azure Storage
description: Calcule o tamanho de um contêiner no armazenamento de BLOBs do Azure, totalizando o tamanho de cada um de seus BLOBs.
services: storage
author: tamram
ms.custom: mvc
ms.service: storage
ms.topic: sample
ms.date: 12/04/2019
ms.author: tamram
ms.openlocfilehash: 890da96ac1d97a2eb84f5296c74a02824d657273
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74895049"
---
# <a name="calculate-the-size-of-a-blob-container-with-powershell"></a>Calcular o tamanho de um contêiner de blob com o PowerShell

Este script calcula o tamanho de um contentor no armazenamento do Blobs do Azure ao calcular o tamanho total dos blobs no contentor.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> Esse script do PowerShell fornece um tamanho estimado para o contêiner e não deve ser usado para cálculos de cobrança. Para obter um script que calcula o tamanho do contêiner para fins de cobrança, consulte [calcular o tamanho de um contêiner de armazenamento de BLOBs para fins de cobrança](../scripts/storage-blobs-container-calculate-billing-size-powershell.md). 

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/storage/calculate-container-size/calculate-container-size.ps1 "Calculate container size")]

## <a name="clean-up-deployment"></a>Limpar a implementação 

Execute o seguinte comando para remover o grupo de recursos, o contentor e todos os recursos relacionados.

```powershell
Remove-AzResourceGroup -Name bloblisttestrg
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para calcular o tamanho do contentor de armazenamento de Blobs. Cada item na tabela liga a documentação específica do comando.

| Comando | Notas |
|---|---|
| [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) | Obtém uma conta de armazenamento especificada ou todas as contas de armazenamento em um grupo de recursos ou na assinatura. |
| [Get-AzStorageBlob](/powershell/module/az.storage/Get-AzStorageBlob) | Lista os BLOBs em um contêiner. |

## <a name="next-steps"></a>Passos seguintes

Para obter um script que calcula o tamanho do contêiner para fins de cobrança, consulte [calcular o tamanho de um contêiner de armazenamento de BLOBs para fins de cobrança](../scripts/storage-blobs-container-calculate-billing-size-powershell.md).

Para obter mais informações sobre o módulo do Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/overview).

Exemplos de script do PowerShell de armazenamento adicionais podem ser encontrados em [exemplos do PowerShell para armazenamento do Azure](../blobs/storage-samples-blobs-powershell.md).
