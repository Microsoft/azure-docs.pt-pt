---
title: Amostra de script Azure PowerShell - Eliminar recipientes por prefixo | Microsoft Docs
description: Leia um exemplo que mostre como eliminar o armazenamento do Azure Blob com base num prefixo no nome do recipiente, utilizando a Azure PowerShell.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.devlang: powershell
ms.topic: sample
ms.date: 06/13/2017
ms.author: tamram
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b95c5ab243fbd938e8a7eb1d3b9619b0d46fb046
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "89072959"
---
# <a name="delete-containers-based-on-container-name-prefix"></a>Eliminar contentores com base no prefixo do nome do contentor

Este script elimina os recipientes no armazenamento Azure Blob com base num prefixo no nome do recipiente.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/storage/delete-containers-by-prefix/delete-containers-by-prefix.ps1 "Delete containers by prefix")]

## <a name="clean-up-deployment"></a>Limpar a implementação

Execute o seguinte comando para remover o grupo de recursos, os restantes contentores e todos os recursos relacionados.

```powershell
Remove-AzResourceGroup -Name containerdeletetestrg
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para eliminar contentores com base no prefixo do nome do contentor. Cada item na tabela liga para documentação de comandos específicos.

| Comando | Notas |
|---|---|
| [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) | Obtém uma conta de Armazenamento especificada ou todas as contas de Armazenamento num grupo de recursos ou na subscrição. |
| [Get-AzStorageContainer](/powershell/module/az.storage/Get-AzStorageContainer) | Lista os recipientes de armazenamento associados a uma conta de armazenamento. |
| [Remove-AzStorageContainer](/powershell/module/az.storage/Remove-AzStorageContainer) | Remove o recipiente de armazenamento especificado. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o módulo do Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/).

Pode ver exemplos adicionais de scripts de armazenamento do PowerShell em [PowerShell samples for Azure Blob storage](../blobs/storage-samples-blobs-powershell.md) (Exemplos do PowerShell para o armazenamento de Blobs do Azure).
