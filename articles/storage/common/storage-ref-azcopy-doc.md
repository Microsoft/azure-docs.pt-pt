---
title: azcopy doc | Microsoft Docs
description: Este artigo fornece informações de referência para o comando azcopy doc.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: d1f57ae40b47c1d910ba20ae8a8f19cdc6908d6b
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70196039"
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

## <a name="see-also"></a>Consulte também

- [azcopy](storage-ref-azcopy.md)
