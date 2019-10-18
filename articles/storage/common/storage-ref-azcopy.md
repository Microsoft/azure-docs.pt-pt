---
title: azcopy | Microsoft Docs
description: Este artigo fornece informações de referência para o comando azcopy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 984d0c570c6c0d5048d58377f113319157411244
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72513393"
---
# <a name="azcopy"></a>azcopy

AzCopy é uma ferramenta de linha de comando que move dados para dentro e fora do armazenamento do Azure.

## <a name="synopsis"></a>Resumo

O formato geral dos comandos é: `azcopy [command] [arguments] --[flag-name]=[flag-value]`.

Para relatar problemas ou para saber mais sobre a ferramenta, consulte [https://github.com/Azure/azure-storage-azcopy](https://github.com/Azure/azure-storage-azcopy).

## <a name="options"></a>Opções

**--Cap-Mbps UInt32**   Limita a taxa de transferência, em megabits por segundo. A taxa de transferência por minuto pode variar um pouco a partir do limite. Se essa opção for definida como zero ou for omitida, a taxa de transferência não será limitada.

**-h,--ajuda** Ajuda para azcopy
      
**--tipo de saída**  Formato da saída do comando. As opções incluem: Text, JSON. O valor padrão é ' Text '. (padrão "texto")

## <a name="see-also"></a>Ver também

- [Introdução ao AzCopy](storage-use-azcopy-v10.md)
- [cópia azcopy](storage-ref-azcopy-copy.md)
- [azcopy doc](storage-ref-azcopy-doc.md)
- [azcopy env](storage-ref-azcopy-env.md)
- [trabalhos do azcopy](storage-ref-azcopy-jobs.md)
- [lista de azcopy](storage-ref-azcopy-list.md)
- [logon do azcopy](storage-ref-azcopy-login.md)
- [azcopy logout](storage-ref-azcopy-logout.md)
- [Make azcopy](storage-ref-azcopy-make.md)
- [azcopy remover](storage-ref-azcopy-remove.md)
- [sincronização de azcopy](storage-ref-azcopy-sync.md)
