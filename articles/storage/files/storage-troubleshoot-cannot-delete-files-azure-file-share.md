---
title: Partilha de ficheiros do Azure – falha ao eliminar ficheiros da partilha de ficheiros do Azure
description: Identifique e resolva a falha na eliminação de ficheiros do Azure File Share.
author: v-miegge
ms.topic: troubleshooting
ms.author: kartup
manager: dcscontentpm
ms.date: 10/25/2019
ms.service: storage
ms.subservice: files
services: storage
tags: ''
ms.openlocfilehash: 3d4f10745d90ccd83e7251af40d3e92a230f2fcd
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2020
ms.locfileid: "94629687"
---
# <a name="azure-file-share--failed-to-delete-files-from-azure-file-share"></a>Partilha de ficheiros do Azure – falha ao eliminar ficheiros da partilha de ficheiros do Azure

A falha na eliminação de ficheiros da Azure File Share pode ter vários sintomas:

**Sintoma 1:**

Não conseguiu eliminar um ficheiro em partilha de ficheiros azul devido a uma das duas questões abaixo:

* O ficheiro marcado para eliminar
* O recurso especificado pode estar a ser utilizado por um cliente SMB

**Sintoma 2:**

Não há quota suficiente disponível para processar este comando

## <a name="cause"></a>Causa

O erro 1816 ocorre quando se atinge o limite superior das pegas abertas simultâneas permitidas para um ficheiro, no computador onde a partilha de ficheiros está a ser montada. Para obter mais informações, consulte a [lista de verificação de desempenho e escalabilidade do Azure Storage](../blobs/storage-performance-checklist.md).

## <a name="resolution"></a>Resolução

Reduza o número de pegas abertas simultâneas fechando algumas pegas.

## <a name="prerequisite"></a>Pré-requisito

### <a name="install-the-latest-azure-powershell-module"></a>Instale o mais recente módulo Azure PowerShell

* [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps)

### <a name="connect-to-azure"></a>Ligue-se a Azure:

```
# Connect-AzAccount
```

### <a name="select-the-subscription-of-the-target-storage-account"></a>Selecione a subscrição da conta de armazenamento alvo:

```
# Select-AzSubscription -subscriptionid "SubscriptionID"
```

### <a name="create-context-for-the-target-storage-account"></a>Criar contexto para a conta de armazenamento alvo:

```
$Context = New-AzStorageContext -StorageAccountName "StorageAccountName" -StorageAccountKey "StorageAccessKey"
```

### <a name="get-the-current-open-handles-of-the-file-share"></a>Obtenha as pegas abertas atuais da partilha de ficheiros:

```
# Get-AzStorageFileHandle -Context $Context -ShareName "FileShareName" -Recursive
```

## <a name="example-result"></a>Resultado de exemplo:

|HandleId|Caminho|ClientIp|ClientPort|Horário aberto|Última Hora de Ligação|FileId|ParentId|SessionId|
|---|---|---|---|---|---|---|---|---|
|259101229083|---|10.222.10.123|62758|2019-10-05|12:16:50Z|0|0|9507758546259807489|
|259101229131|---|10.222.10.123|62758|2019-10-05|12:36:20Z|0|0|9507758546259807489|
|259101229137|---|10.222.10.123|62758|2019-10-05|12:36:53Z|0|0|9507758546259807489|
|259101229136|Nova pasta/test.zip|10.222.10.123|62758|2019-10-05|12:36:29Z|13835132822072852480|9223446803645464576|9507758546259807489|
|259101229135|test.zip|37.222.22.143|62758|2019-10-05|12:36:24Z|11529250230440558592|0|9507758546259807489|

### <a name="close-an-open-handle"></a>Feche uma pega aberta:

Para fechar uma pega aberta, utilize o seguinte comando:

```
# Close-AzStorageFileHandle -Context $Context -ShareName "FileShareName" -Path 'New folder/test.zip' -CloseAll
```

## <a name="next-steps"></a>Passos seguintes

* [Resolução de problemas Ficheiros Azure no Windows](storage-troubleshoot-windows-file-connection-problems.md)
* [Resolução de problemas Ficheiros Azure em Linux](storage-troubleshoot-linux-file-connection-problems.md)
* [Resolver problemas do Azure File Sync](storage-sync-files-troubleshoot.md)