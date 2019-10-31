---
title: Compartilhamento de arquivos do Azure – falha ao excluir arquivos do compartilhamento de arquivos do Azure
description: Identificar e solucionar problemas de falha ao excluir arquivos do compartilhamento de arquivos do Azure.
author: v-miegge
ms.topic: troubleshooting
ms.author: kartup
ms.date: 10/25/2019
ms.service: storage
ms.subservice: common
ms.openlocfilehash: b535578328e7ca77f1071187b6ac761bc7076ac1
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73065980"
---
# <a name="azure-file-share--failed-to-delete-files-from-azure-file-share"></a>Compartilhamento de arquivos do Azure – falha ao excluir arquivos do compartilhamento de arquivos do Azure

A falha ao excluir arquivos do compartilhamento de arquivos do Azure pode ter vários sintomas:

**Sintoma 1:**

Falha ao excluir um arquivo no compartilhamento de arquivos do Azure devido a um dos dois problemas abaixo:

* O arquivo marcado para exclusão
* O recurso especificado pode estar sendo usado por um cliente SMB

**Sintoma 2:**

Não há cota suficiente disponível para processar este comando

## <a name="cause"></a>Causa

O erro 1816 ocorre quando você atinge o limite superior de identificadores abertos simultâneos permitidos para um arquivo, no computador em que o compartilhamento de arquivos está sendo montado. Para obter mais informações, consulte a [lista de verificação de escalabilidade e desempenho do armazenamento do Azure](https://docs.microsoft.com/azure/storage/blobs/storage-performance-checklist).

## <a name="resolution"></a>Resolução

Reduza o número de identificadores abertos simultâneos fechando alguns identificadores.

## <a name="prerequisite"></a>Pré-requisito

### <a name="install-the-latest-azure-powershell-module"></a>Instalar o módulo de Azure PowerShell mais recente

* [Instalar o módulo do Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps)

### <a name="connect-to-azure"></a>Conectar-se ao Azure:

```
# Connect-AzAccount
```

### <a name="select-the-subscription-of-the-target-storage-account"></a>Selecione a assinatura da conta de armazenamento de destino:

```
# Select-AzSubscription -subscriptionid "SubscriptionID"
```

### <a name="create-context-for-the-target-storage-account"></a>Criar contexto para a conta de armazenamento de destino:

```
$Context = New-AzStorageContext -StorageAccountName "StorageAccountName" -StorageAccountKey "StorageAccessKey"
```

### <a name="get-the-current-open-handles-of-the-file-share"></a>Obtenha os identificadores abertos atuais do compartilhamento de arquivos:

```
# Get-AzStorageFileHandle -Context $Context -ShareName "FileShareName" -Recursive
```

## <a name="example-result"></a>Resultado do exemplo:

|Identificadorid|Caminho|clientIp|clientPort|Opentime|LastReconnectTime|FileId|ParentId|sessionId|
|---|---|---|---|---|---|---|---|---|
|259101229083|---|10.222.10.123|62758|2019-10-05|12:16:50Z|0|0|9507758546259807489|
|259101229131|---|10.222.10.123|62758|2019-10-05|12:36:20Z|0|0|9507758546259807489|
|259101229137|---|10.222.10.123|62758|2019-10-05|12:36:53Z|0|0|9507758546259807489|
|259101229136|Nova pasta/Test. zip|10.222.10.123|62758|2019-10-05|12:36:29Z|13835132822072852480|9223446803645464576|9507758546259807489|
|259101229135|Test. zip|37.222.22.143|62758|2019-10-05|12:36:24Z|11529250230440558592|0|9507758546259807489|

### <a name="close-an-open-handle"></a>Fechar um identificador aberto:

Para fechar um identificador aberto, use o seguinte comando:

```
# Close-AzStorageFileHandle -Context $Context -ShareName "FileShareName" -Path 'New folder/test.zip' -CloseAll
```

## <a name="next-steps"></a>Passos seguintes

* [Solucionar problemas de arquivos do Azure no Windows](storage-troubleshoot-windows-file-connection-problems.md)
* [Solucionar problemas de arquivos do Azure no Linux](storage-troubleshoot-linux-file-connection-problems.md)
* [Resolver problemas do Azure File Sync](storage-sync-files-troubleshoot.md)