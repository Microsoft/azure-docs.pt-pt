---
title: sys.external_job_streams (Transact-SQL) - Azure SQL Edge (Preview)
description: Saiba como utilizar sys.external_job_streams em Azure SQL Edge (Preview)
keywords: sys.external_job_streams, SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: reference
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2019
ms.openlocfilehash: 2634128f4d431e4283f59032c6474a71f2af364d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84233091"
---
# <a name="sysexternal_job_streams-transact-sql"></a>sys.external_job_streams (Transact-SQL)

Devolve uma linha cada uma para o objeto de fluxo externo de entrada ou saída mapeado para uma tarefa de streaming externa.

|Nome da coluna|Tipo de dados|Descrição|  
|-----------------|---------------|-----------------|
|**job_id**|**int**| Número de identificação de objeto para o objeto de trabalho de streaming. Esta coluna mapeia para a coluna object_id de sys.external_streaming_jobs.|
|**stream_id**|**int**| Número de identificação do objeto de fluxo. Esta coluna mapeia para a coluna object_id de sys.external_streams. |
|**is_input**|**bit**| 1 se o objeto de fluxo for utilizado uma entrada para o trabalho de streaming, caso contrário 0.|
|**is_output**|**bit**| 1 se o objeto de fluxo for utilizado uma saída para o trabalho de streaming, caso contrário 0.|

## <a name="example"></a>Exemplo

Esta vista de catálogo é usada juntamente com `sys.external_streams` vistas de `sys.external_streaming_jobs` catálogo. Uma consulta de amostra é mostrada abaixo

```sql
Select
    sj.Name as Job_Name,
    sj.Create_date as Job_Create_date,
    sj.modify_date as Job_Modify_date,
    sj.statement as Stream_Job_Query,
    Input_Stream_Name =
        Case js.is_input
            when 1 then s.Name
            else null
        END,
    output_Stream_Name =
        case js.is_output
            when 1 then s.Name
            else null
        END,
    s.location as Stream_Location
from sys.external_job_streams js
inner join sys.external_streams s on s.object_id = js.stream_id
inner join sys.external_streaming_jobs sj on sj.object_id = js.job_id
```

## <a name="permissions"></a>Permissões

A visibilidade dos metadados nas vistas do catálogo limita-se a securáveis que um utilizador possui ou sobre os quais o utilizador tenha alguma permissão. Para obter mais informações, consulte [a Configuração de Visibilidade dos Metadados](/sql/relational-databases/security/metadata-visibility-configuration/).

## <a name="see-also"></a>Veja também

- [Vistas do Catálogo (Transact-SQL)](/sql/relational-databases/system-catalog-views/catalog-views-transact-sql/)
- [Vistas do Sistema (Transact-SQL)](/sql/t-sql/language-reference/)
