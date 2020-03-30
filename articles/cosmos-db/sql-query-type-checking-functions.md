---
title: Funções de verificação de tipos em linguagem de consulta de Azure Cosmos DB
description: Saiba mais sobre a verificação de tipos de funções do sistema SQL em Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: cac8be4c39737ca209653532d00375c8c9bee48e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "71349076"
---
# <a name="type-checking-functions-azure-cosmos-db"></a>Funções de verificação de tipos (Azure Cosmos DB)

As funções de verificação de tipo permitem verificar o tipo de expressão dentro de uma consulta SQL. Pode utilizar funções de verificação de tipos para determinar os tipos de propriedades dentro dos itens em voo, quando são variáveis ou desconhecidas. 

## <a name="functions"></a>Funções

Aqui está uma tabela de funções de verificação de tipo incorporadas suportadas:

As seguintes funções suportam o tipo de verificação do tipo de verificação dos valores de entrada e cada retorno um valor Booleano.  
  
||||  
|-|-|-|  
|[IS_ARRAY](sql-query-is-array.md)|[IS_BOOL](sql-query-is-bool.md)|[IS_DEFINED](sql-query-is-defined.md)|  
|[IS_NULL](sql-query-is-null.md)|[IS_NUMBER](sql-query-is-number.md)|[IS_OBJECT](sql-query-is-object.md)|  
|[IS_PRIMITIVE](sql-query-is-primitive.md)|[IS_STRING](sql-query-is-string.md)||  
  

## <a name="next-steps"></a>Passos seguintes

- [Funcionamento do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
- [Funções Definidas pelo Utilizador](sql-query-udfs.md)
- [Agregados](sql-query-aggregates.md)
