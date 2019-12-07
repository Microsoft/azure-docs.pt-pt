---
title: Girar chaves de acesso da conta de armazenamento com o PowerShell
titleSuffix: Azure Storage
description: Crie uma conta de armazenamento do Azure e, em seguida, recupere e gire uma de suas chaves de acesso de conta.
services: storage
author: tamram
ms.custom: mvc
ms.service: storage
ms.topic: sample
ms.date: 12/04/2019
ms.author: tamram
ms.openlocfilehash: 54a7a6cb89c88726853a39ab5b2a61e1f0cde0b5
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74894930"
---
# <a name="rotate-storage-account-access-keys-with-powershell"></a>Girar chaves de acesso da conta de armazenamento com o PowerShell

Esse script cria uma conta de armazenamento do Azure, exibe a chave de acesso primária da nova conta de armazenamento e, em seguida, renova (gira) a chave.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/storage/rotate-storage-account-keys/rotate-storage-account-keys.ps1 "Rotate storage account keys")]

## <a name="clean-up-deployment"></a>Limpar a implementação

Execute o seguinte comando para remover o grupo de recursos, a conta de armazenamento e todos os recursos relacionados.

```powershell
Remove-AzResourceGroup -Name rotatekeystestrg
```

## <a name="script-explanation"></a>Explicação do script

Esse script usa os comandos a seguir para criar a conta de armazenamento e recuperar e girar uma de suas chaves de acesso. Cada item na tabela liga a documentação específica do comando.

| Comando | Notas |
|---|---|
| [Get-AzLocation](/powershell/module/az.resources/get-azlocation) | Obtém todos os locais e os provedores de recursos com suporte para cada local. |
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Cria um grupo de recursos do Azure. |
| [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) | Cria uma Conta de armazenamento. |
| [Get-AzStorageAccountKey](/powershell/module/az.storage/get-azstorageaccountkey) | Obtém as chaves de acesso para uma conta de Armazenamento do Azure. |
| [New-AzStorageAccountKey](/powershell/module/az.storage/new-azstorageaccountkey) | Regenera uma chave de acesso para uma conta de armazenamento do Azure. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o módulo do Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/overview).

Pode ver exemplos adicionais de scripts de armazenamento do PowerShell em [PowerShell samples for Azure Blob storage](../blobs/storage-samples-blobs-powershell.md) (Exemplos do PowerShell para o armazenamento de Blobs do Azure).
