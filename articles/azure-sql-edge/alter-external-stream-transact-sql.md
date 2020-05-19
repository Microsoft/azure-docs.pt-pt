---
title: ALTER FLUXO EXTERNO (Transact-SQL) - Borda SQL Azure (Pré-visualização)
description: Conheça a declaração alter external stream em Azure SQL Edge (Pré-visualização)
keywords: ''
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 0183972b5eb92d3f081b857940609bffc183b331
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83597941"
---
# <a name="alter-external-stream-transact-sql"></a>ALTER FLUXO EXTERNO (Transact-SQL)

Modifica a definição de um Fluxo Externo. Não é permitida modificar um Fluxo Externo que é utilizado por um trabalho de streaming num estado de *funcionamento.* 



## <a name="syntax"></a>Sintaxe

```sql
  ALTER EXTERNAL STREAM external_stream_name 
  SET 
    [DATA_SOURCE] = <data_source_name> 
    , LOCATION = <location_name> 
    , EXTERNAL_FILE_FORMAT = <external_file_format_name> 
    , INPUT_OPTIONS = <input_options> 
    , OUTPUT_OPTIONS = <output_options> 
```

## <a name="arguments"></a>Argumentos

Para obter mais detalhes sobre os argumentos de comando Alter External Stream, consulte [CREATE EXTERNAL STREAM (Transact-SQL)](create-external-stream-transact-sql.md).

## <a name="return-code-values"></a>Valores de código de devolução

ALTER EXTERNAL STREAM retorna 0 se for bem sucedido. Um valor de retorno não-zero indica falha.


## <a name="see-also"></a>Ver também

- [CRIAR FLUXO EXTERNO (Transact-SQL)](create-external-stream-transact-sql.md) 
- [FLUXO EXTERNO DROP (Transact-SQL)](drop-external-stream-transact-sql.md) 
