---
title: azcopy jobs [ Microsoft Docs
description: Este artigo fornece informações de referência para o comando de empregos de azcopy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 27c06656d95c5165b33b6056a3cf3b554f0e5469
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74034105"
---
# <a name="azcopy-jobs"></a>azcopy jobs

Subcomandos relacionados com a gestão de empregos.

## <a name="related-conceptual-articles"></a>Artigos conceituais relacionados

- [Introdução ao AzCopy](storage-use-azcopy-v10.md)
- [Transferir dados com armazenamento AzCopy e Blob](storage-use-azcopy-blobs.md)
- [Transferir dados com a AzCopy e armazenamento de ficheiros](storage-use-azcopy-files.md)
- [Configure, otimize e problemas AzCopy](storage-use-azcopy-configure.md)

## <a name="examples"></a>Exemplos

```azcopy
azcopy jobs show [jobID]
```

## <a name="options"></a>Opções

|Opção|Descrição|
|--|--|
|-h, --ajuda|Mostre conteúdo de ajuda para o comando de empregos.|

## <a name="options-inherited-from-parent-commands"></a>Opções herdadas dos comandos dos pais

|Opção|Descrição|
|---|---|
|--cap-mbps uint32|Cobre a taxa de transferência, em megabits por segundo. A entrada momentânea pode variar ligeiramente a partir da tampa. Se esta opção estiver definida para zero, ou for omitida, a entrada não está limitada.|
|--cadeia tipo saída|Formato da saída do comando. As escolhas incluem: texto, json. O valor predefinido é "texto".|

## <a name="see-also"></a>Consulte também

- [azcopy](storage-ref-azcopy.md)
- [azcopy jobs list](storage-ref-azcopy-jobs-list.md)
- [azcopy jobs resume](storage-ref-azcopy-jobs-resume.md)
- [azcopy jobs show](storage-ref-azcopy-jobs-show.md)
