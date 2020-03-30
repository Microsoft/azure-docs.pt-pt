---
title: Partilha de ficheiros do Azure – falha ao eliminar ficheiros da partilha de ficheiros do Azure
description: Identifique e desameça a falha na eliminação de ficheiros da Partilha de Ficheiros Azure.
author: v-miegge
ms.topic: troubleshooting
ms.author: kartup
manager: dcscontentpm
ms.date: 10/25/2019
ms.service: storage
ms.subservice: common
services: storage
tags: ''
ms.openlocfilehash: d3a3763a8964810626bcdc47da230a9ee406f1f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74196484"
---
# <a name="azure-file-share--failed-to-delete-files-from-azure-file-share"></a>Partilha de ficheiros do Azure – falha ao eliminar ficheiros da partilha de ficheiros do Azure

A não eliminação de ficheiros da Partilha de Ficheiros Azure pode ter vários sintomas:

**Sintoma 1:**

Não apagou um ficheiro em partilha de ficheiros azure devido a uma das duas questões abaixo:

* O ficheiro marcado para apagar
* O recurso especificado pode ser utilizado por um cliente SMB

**Sintoma 2:**

Não há quota suficiente para processar este comando

## <a name="cause"></a>Causa

O erro 1816 ocorre quando se atinge o limite superior das pegas abertas simultâneas permitidas para um ficheiro, no computador onde a parte do ficheiro está a ser montada. Para mais informações, consulte o desempenho do [Armazenamento Azure e](https://docs.microsoft.com/azure/storage/blobs/storage-performance-checklist)a lista de verificação de escalabilidade .

## <a name="resolution"></a>Resolução

Reduza o número de pegas abertas simultâneas fechando algumas pegas.

## <a name="prerequisite"></a>Pré-requisito

### <a name="install-the-latest-azure-powershell-module"></a>Instale o mais recente módulo Azure PowerShell

* [Instalar o módulo do Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps)

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

## <a name="example-result"></a>Resultado do exemplo:

|Handleid|Caminho|ClientIp|ClientPort|Hora Aberta|LastReconnectTime|Fileid|Paria|SessãoId|
|---|---|---|---|---|---|---|---|---|
|259101229083|---|10.222.10.123|62758|2019-10-05|12:16:50Z|0|0|9507758546259807489|
|259101229131|---|10.222.10.123|62758|2019-10-05|12:36:20Z|0|0|9507758546259807489|
|259101229137|---|10.222.10.123|62758|2019-10-05|12:36:53Z|0|0|9507758546259807489|
|259101229136|Nova pasta/teste.zip|10.222.10.123|62758|2019-10-05|12:36:29Z|13835132822072852480|9223446803645464576|9507758546259807489|
|259101229135|teste.zip|37.222.22.143|62758|2019-10-05|12:36:24Z|11529250230440558592|0|9507758546259807489|

### <a name="close-an-open-handle"></a>Feche uma pega aberta:

Para fechar uma pega aberta, utilize o seguinte comando:

```
# Close-AzStorageFileHandle -Context $Context -ShareName "FileShareName" -Path 'New folder/test.zip' -CloseAll
```

## <a name="next-steps"></a>Passos seguintes

* [Troubleshoot Azure Files no Windows](storage-troubleshoot-windows-file-connection-problems.md)
* [Troubleshoot Azure Files em Linux](storage-troubleshoot-linux-file-connection-problems.md)
* [Resolver problemas do Azure File Sync](storage-sync-files-troubleshoot.md)