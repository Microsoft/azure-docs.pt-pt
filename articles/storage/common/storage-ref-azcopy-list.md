---
title: lista de azcopy | Microsoft Docs
description: Este artigo fornece informações de referência para o comando azcopy List.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: b7348e3790166e1a1aecab422e571b8f2fc7cd5f
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72513515"
---
# <a name="azcopy-list"></a>azcopy list

Lista as entidades em um determinado recurso.

## <a name="synopsis"></a>Resumo

Somente os contêineres de blob têm suporte na versão atual.

```azcopy
azcopy list [containerURL] [flags]
```

## <a name="examples"></a>Exemplos

```azcopy
azcopy list [containerURL]
```

## <a name="options"></a>Opções

|Opção|Descrição|
|--|--|
|-h,--ajuda|Mostra o conteúdo da ajuda para o comando de lista.|
|--legível por máquina|Lista os tamanhos de arquivo em bytes.|
|--Mega-unidades|Exibe unidades em ordens de 1000, não 1024.|
|--com contagem alta|Conta o número total de arquivos e seus tamanhos.|

## <a name="options-inherited-from-parent-commands"></a>Opções herdadas de comandos pai

|Opção|Descrição|
|---|---|
|--Cap-Mbps UInt32|Limita a taxa de transferência, em megabits por segundo. A taxa de transferência por minuto pode variar um pouco a partir do limite. Se essa opção for definida como zero ou for omitida, a taxa de transferência não será limitada.|
|--Cadeia de caracteres de tipo de saída|Formato da saída do comando. As opções incluem: Text, JSON. O valor padrão é "text".|

## <a name="see-also"></a>Ver também

- [azcopy](storage-ref-azcopy.md)
