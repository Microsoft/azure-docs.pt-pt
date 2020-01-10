---
title: Migrar BLOBs entre contas de armazenamento com o AzCopy no Windows
description: Azure PowerShell exemplo de script – usando AzCopy, o copia o conteúdo do blob de uma conta de armazenamento do Azure para outra.
services: storage
documentationcenter: na
author: normesta
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: sample
ms.date: 02/01/2018
ms.author: normesta
ms.openlocfilehash: 559b8b2875b789034ae07901f668f241505073b1
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75465056"
---
# <a name="migrate-blobs-across-storage-accounts-using-azcopy-on-windows"></a>Migrar os blobs para várias contas de armazenamento com AzCopy no Windows

Este exemplo copia todos os objetos do blob de uma conta de armazenamento de origem fornecida pelo utilizador para outra de destino fornecida também pelo utilizador. 

Para fazê-lo, é utilizado o comando `Get-AzStorageContainer`, que lista todos os contentores numa conta de armazenamento. Depois, o exemplo emite comandos AzCopy, copiando cada contentor da conta de armazenamento de origem para a de destino. Se ocorrerem falhas, o exemplo repete $retryTimes (a predefinição é três vezes, mas pode ser modificada com o parâmetro `-RetryTimes`). Se as falhas se verificarem em todas as repetições, o utilizador pode voltar a executar o script ao fornecer ao exemplo o último contentor copiado com êxito através do comando `-LastSuccessContainerName`. Em seguida, o exemplo continua a copiar contentores a partir desse ponto.

Este exemplo requer o módulo de armazenamento Azure PowerShell versão **0,7** ou posterior. Pode utilizar `Get-Module -ListAvailable Az.storage` para verificar a versão que tem instalada. Se precisar de instalar ou atualizar, veja [Install Azure PowerShell module](/powershell/azure/install-Az-ps)(Instalar o módulo do Azure PowerShell). 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Este exemplo também requer a versão mais recente do [AzCopy no Windows](https://aka.ms/downloadazcopy). O diretório predefinido da instalação é `C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\`

Este exemplo utiliza o nome e a chave de uma conta de armazenamento de origem e de uma conta de armazenamento de destino e o caminho de ficheiro completo de AzCopy.exe (se não estiver instalado no diretório predefinido).

Seguem-se algumas amostras da entrada deste exemplo:

Se AzCopy estiver instalado no diretório predefinido:
```powershell
srcStorageAccountName: ExampleSourceStorageAccountName
srcStorageAccountKey: ExampleSourceStorageAccountKey
DestStorageAccountName: ExampleTargetStorageAccountName
DestStorageAccountKey: ExampleTargetStorageAccountKey
```

Se AzCopy não estiver instalado no diretório predefinido:

```Powershell
srcStorageAccountName: ExampleSourceStorageAccountName
srcStorageAccountKey: ExampleSourceStorageAccountKey
DestStorageAccountName: ExampleTargetStorageAccountName
DestStorageAccountKey: ExampleTargetStorageAccountKey
AzCopyPath: C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\AzCopy.exe
```

Se ocorrer uma falha e for necessário voltar a executar o exemplo a partir de um determinado contentor: 

`.\copyScript.ps1 -LastSuccessContainerName myContainerName`

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/storage/migrate-blobs-between-accounts/migrate-blobs-between-accounts.ps1 "Migrate blobs between storage accounts.")]

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para copiar dados de uma conta de armazenamento para outra. Cada item na tabela liga para documentação de comandos específicos.

| Comando | Notas |
|---|---|
| [Get-AzStorageContainer](/powershell/module/az.storage/Get-AzStorageContainer) | Devolve os contentores associados a esta conta de armazenamento. |
| [New-AzStorageContext](/powershell/module/az.storage/New-AzStorageContext) | Cria um contexto de armazenamento do Azure. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o módulo do Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/overview).

Pode ver exemplos adicionais de scripts de armazenamento do PowerShell em [PowerShell samples for Azure Blob storage](../blobs/storage-samples-blobs-powershell.md) (Exemplos do PowerShell para o armazenamento de Blobs do Azure).
