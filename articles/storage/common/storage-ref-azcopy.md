---
title: azcopy | Microsoft Docs
description: Este artigo fornece informações de referência para o comando azcopy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 4da9206f4500941179d781a0fe2a57ad15d7393d
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195896"
---
# <a name="azcopy"></a>azcopy

AzCopy é uma ferramenta de linha de comando que move dados para dentro e fora do armazenamento do Azure.

## <a name="synopsis"></a>Resumo

O formato geral dos comandos é: `azcopy [command] [arguments] --[flag-name]=[flag-value]`.

Para relatar problemas ou para saber mais sobre a ferramenta, consulte [https://github.com/Azure/azure-storage-azcopy](https://github.com/Azure/azure-storage-azcopy).

## <a name="options"></a>Opções

|Opção|Descrição|
|---|---|
|--Cap-Mbps UInt32|Limita a taxa de transferência, em megabits por segundo. A taxa de transferência por minuto pode variar um pouco a partir do limite. Se essa opção for definida como zero ou for omitida, a taxa de transferência não será limitada.|
|-h,--ajuda|Mostra o conteúdo da ajuda para azcopy.|
|--Cadeia de caracteres de tipo de saída|Formato da saída do comando. As opções incluem: Text, JSON. O valor padrão é "text".|

## <a name="see-also"></a>Consulte também

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
