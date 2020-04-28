---
title: azcopy jobs remove [ Microsoft Docs
description: Este artigo fornece informações de referência para os trabalhos de azcopy remover o comando.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: ba403c8d823b7ead0414521ebd51dc6f6601ccd8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "74034165"
---
# <a name="azcopy-jobs-remove"></a>azcopy jobs remove

Remova todos os ficheiros associados à identificação do trabalho.

> [!NOTE] 
> Pode personalizar a localização onde os ficheiros de registo e plano são guardados. Veja o comando da [Azcopy ENV](storage-ref-azcopy-env.md) para saber mais.

```
azcopy jobs remove [jobID] [flags]
```

## <a name="related-conceptual-articles"></a>Artigos conceituais relacionados

- [Introdução ao AzCopy](storage-use-azcopy-v10.md)
- [Transferir dados com armazenamento AzCopy e Blob](storage-use-azcopy-blobs.md)
- [Transferir dados com a AzCopy e armazenamento de ficheiros](storage-use-azcopy-files.md)
- [Configure, otimize e problemas AzCopy](storage-use-azcopy-configure.md)

## <a name="examples"></a>Exemplos

```
  azcopy jobs rm e52247de-0323-b14d-4cc8-76e0be2e2d44
```

## <a name="options"></a>Opções

**-h, --ajuda**                Ajuda para remover.

## <a name="options-inherited-from-parent-commands"></a>Opções herdadas dos comandos dos pais

**--cap-mbps uint32**      Cobre a taxa de transferência, em megabits por segundo. A entrada momentânea pode variar ligeiramente a partir da tampa. Se esta opção estiver definida para zero, ou for omitida, a entrada não está limitada.

**-formato** de cadeia do tipo de saída da saída do comando. As escolhas incluem: texto, json. O valor predefinido é "texto". ("texto por defeito")

## <a name="see-also"></a>Consulte também

- [azcopy jobs](storage-ref-azcopy-jobs.md)
