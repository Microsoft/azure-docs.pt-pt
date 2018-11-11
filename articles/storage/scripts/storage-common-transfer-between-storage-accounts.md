---
title: Exemplo de Script do Azure PowerShell - Migrar blobs em várias contas de utilizador com AzCopy no Windows | Microsoft Docs
description: Ao utilizar o AzCopy, copia os conteúdos do Blob de uma conta de Armazenamento do Azure para outra.
services: storage
documentationcenter: na
author: roygara
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: sample
ms.date: 02/01/2018
ms.author: rogarana
ms.openlocfilehash: 58e2275398216f29764d54d1a230959e86eda0b6
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2018
ms.locfileid: "51253266"
---
# <a name="migrate-blobs-across-storage-accounts-using-azcopy-on-windows"></a>Migrar os blobs para várias contas de armazenamento com AzCopy no Windows

Este exemplo copia todos os objetos do blob de uma conta de armazenamento de origem fornecida pelo utilizador para outra de destino fornecida também pelo utilizador. 

Para fazê-lo, é utilizado o comando `Get-AzureStorageContainer`, que lista todos os contentores numa conta de armazenamento. Depois, o exemplo emite comandos AzCopy, copiando cada contentor da conta de armazenamento de origem para a de destino. Se ocorrerem falhas, o exemplo repete $retryTimes (a predefinição é três vezes, mas pode ser modificada com o parâmetro `-RetryTimes`). Se as falhas se verificarem em todas as repetições, o utilizador pode voltar a executar o script ao fornecer ao exemplo o último contentor copiado com êxito através do comando `-LastSuccessContainerName`. Em seguida, o exemplo continua a copiar contentores a partir desse ponto.

Este exemplo requer a versão **4.0.2** ou posterior do módulo de Armazenamento do Azure PowerShell. Pode utilizar `Get-Module -ListAvailable Azure.storage` para verificar a versão que tem instalada. Se precisar de instalar ou atualizar, veja [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps)(Instalar o módulo do Azure PowerShell). 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Este exemplo também requer a versão mais recente do [AzCopy no Windows](https://aka.ms/downloadazcopy). O diretório predefinido da instalação é `C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\`

Este exemplo utiliza o nome e a chave de uma conta de armazenamento de origem e de uma conta de armazenamento de destino e o caminho de ficheiro completo de AzCopy.exe (se não estiver instalado no diretório predefinido).

Seguem-se algumas amostras da entrada deste exemplo:

Se AzCopy estiver instalado no diretório predefinido:
```PowerShell
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
| [Get-AzureStorageContainer](/powershell/module/azure.storage/Get-AzureStorageContainer) | Devolve os contentores associados a esta conta de armazenamento. |
| [New-AzureStorageContext](/powershell/module/azure.storage/New-AzureStorageContext) | Cria um contexto de armazenamento do Azure. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o módulo do Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/overview).

Pode ver exemplos adicionais de scripts de armazenamento do PowerShell em [PowerShell samples for Azure Blob storage](../blobs/storage-samples-blobs-powershell.md) (Exemplos do PowerShell para o armazenamento de Blobs do Azure).
