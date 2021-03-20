---
title: sys.sp_cleanup_data_retention (Transact-SQL) - Azure SQL Edge
description: Saiba mais sobre a utilização de sys.sp_cleanup_data_retention (Transact-SQL) em Azure SQL Edge
keywords: sys.sp_cleanup_data_retention (Transact-SQL), SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: reference
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 9c0a6700a476d4f7f875af5373e3c99bc4e25af2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "90938637"
---
# <a name="syssp_cleanup_data_retention-transact-sql"></a>sys.sp_cleanup_data_retention (Transact-SQL)

**Aplica-se a:** Borda Azure SQL

Executa a limpeza de registos obsoletos de tabelas que tenham políticas de retenção de dados ativadas. Para mais informações, consulte [a Retenção de Dados.](data-retention-overview.md) 

## <a name="syntax"></a>Sintaxe 

```sql
sys.sp_cleanup_data_retention   
    { [@schema_name = ] 'schema_name' },  
    { [@table_name = ] 'table_name' },   
    [ [@rowcount =] rowcount OUTPUT ]    

```

## <a name="arguments"></a>Argumentos  
`[ @schema_name = ] schema_name`    
 É o nome do próprio esquema para a mesa em que a limpeza precisa de ser realizada. *schema_name* é um parâmetro necessário do tipo **sysname**.
  
`[ @table_name = ] 'table_name'`    
 É o nome da tabela em que é necessária uma operação de limpeza. *table_name* é um parâmetro necessário do tipo **sysname**.

## <a name="output-parameter"></a>Parâmetro de saída  

`[ @rowcount = ] rowcount OUTPUT`   
 rowcount é um parâmetro de SAÍDA opcional que representa o número de registos de limpeza da tabela. *rowcount* é int.
  
## <a name="permissions"></a>Permissões    
 Requer permissões db_owner.

## <a name="next-steps"></a>Passos seguintes
- [Retenção de Dados e Purga automática de dados](data-retention-overview.md)
- [Gerir dados históricos com política de retenção](data-retention-cleanup.md) 
- [Permitir e desativar a retenção de dados](data-retention-enable-disable.md)
