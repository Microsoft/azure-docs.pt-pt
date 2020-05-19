---
title: sys.external_job_streams (Transact-SQL) - Ponta SQL Azure (Pré-visualização)
description: Saiba mais sobre o uso de sys.external_job_streams em Azure SQL Edge (Pré-visualização)
keywords: sys.external_job_streams, SQL Edge
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: reference
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2019
ms.openlocfilehash: 7f26c87c0de09618b2d24413f7ff692fd764b4cf
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83597290"
---
# <a name="sysexternal_job_streams-transact-sql"></a>sys.external_job_streams (Transact-SQL)

Devolve uma linha cada uma para o objeto de fluxo externo de entrada ou saída mapeado para um trabalho de streaming externo.

|Nome da coluna|Tipo de dados|Descrição|  
|-----------------|---------------|-----------------|
|**job_id**|**int**| Número de identificação de objetos para o objeto de trabalho de streaming. Esta coluna mapeia para a coluna object_id de sys.external_streaming_jobs.|
|**stream_id**|**int**| Número de identificação do objeto para o objeto de fluxo. Esta coluna mapeia para a coluna object_id de sys.external_streams. |
|**is_input**|**bit**| 1 se o objeto de fluxo for utilizado uma entrada para o trabalho de streaming, caso contrário 0.|
|**is_output**|**bit**| 1 se o objeto de fluxo for utilizado uma saída para o trabalho de streaming, caso contrário 0.|

## <a name="example"></a>Exemplo

Esta vista de catálogo é utilizada em conjunto com e com as vistas do `sys.external_streams` `sys.external_streaming_jobs` catálogo. Uma consulta de amostra é mostrada abaixo

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

A visibilidade dos metadados nas vistas do catálogo limita-se a securáveis que um utilizador detém ou nos quais o utilizador obteve alguma permissão. Para mais informações, consulte Configuração de [Visibilidade de Metadados](/sql/relational-databases/security/metadata-visibility-configuration/).

## <a name="see-also"></a>Ver também

- [Vistas de catálogo (Transact-SQL)](/sql/relational-databases/system-catalog-views/catalog-views-transact-sql/)
- [Pontos de vista do sistema (Transact-SQL)](/sql/t-sql/language-reference/)
