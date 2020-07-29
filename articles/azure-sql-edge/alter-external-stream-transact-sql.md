---
title: ALTER FLUXO EXTERNO (Transact-SQL) - Aresta SQL Azure (Pré-visualização)
description: Conheça a declaração ALTER EXTERNAL STREAM em Azure SQL Edge (Pré-visualização)
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 2559c4b4b875403b7c70671e27cb6222a3f1103a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84235203"
---
# <a name="alter-external-stream-transact-sql"></a>ALTER FLUXO EXTERNO (Transact-SQL)

Modifica a definição de fluxo externo. Não é permitido modificar uma corrente externa que seja utilizada por um trabalho de streaming num estado *de funcionamento.* 



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

Para obter mais informações sobre os argumentos de comando alter external stream, consulte [CREATE EXTERNAL STREAM (Transact-SQL)](create-external-stream-transact-sql.md).

## <a name="return-code-values"></a>Valores de código de devolução

ALTER EXTERNAL STREAM retorna 0 se for bem sucedido. Um valor de retorno não zero indica falha.


## <a name="see-also"></a>Veja também

- [CRIAR FLUXO EXTERNO (Transact-SQL)](create-external-stream-transact-sql.md) 
- [FLUXO EXTERNO DROP (Transact-SQL)](drop-external-stream-transact-sql.md) 
