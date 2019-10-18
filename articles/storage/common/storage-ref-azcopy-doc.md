---
title: azcopy doc | Microsoft Docs
description: Este artigo fornece informações de referência para o comando azcopy doc.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: b11d831be847716d4edfac4f9a0725e07a60c52a
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72513861"
---
# <a name="azcopy-doc"></a>azcopy doc

Gera documentação para a ferramenta no formato de redução.

## <a name="synopsis"></a>Resumo

Gera a documentação para a ferramenta no formato de redução e as armazena no local designado.

Por padrão, os arquivos são armazenados em uma pasta chamada ' Doc ' dentro do diretório atual.

```azcopy
azcopy doc [flags]
```

## <a name="options"></a>Opções

|Opção|Descrição|
|--|--|
|-h,--ajuda|Mostra o conteúdo da ajuda para o comando doc.|

## <a name="options-inherited-from-parent-commands"></a>Opções herdadas de comandos pai

|Opção|Descrição|
|---|---|
|--Cap-Mbps UInt32|Limita a taxa de transferência, em megabits por segundo. A taxa de transferência por minuto pode variar um pouco a partir do limite. Se essa opção for definida como zero ou for omitida, a taxa de transferência não será limitada.|
|--Cadeia de caracteres de tipo de saída|Formato da saída do comando. As opções incluem: Text, JSON. O valor padrão é "text".|

## <a name="see-also"></a>Ver também

- [azcopy](storage-ref-azcopy.md)
