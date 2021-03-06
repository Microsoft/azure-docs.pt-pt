---
title: Funções de verificação de tipo em linguagem de consulta DB Azure Cosmos
description: Saiba mais sobre o tipo de funções do sistema SQL no Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 2becc9216d847dfe26d8fd3a433993112fff7980
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96546357"
---
# <a name="type-checking-functions-azure-cosmos-db"></a>Funções de verificação de tipo (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

As funções de verificação de tipo permitem verificar o tipo de expressão dentro de uma consulta SQL. Pode utilizar funções de verificação de tipo para determinar os tipos de propriedades dentro dos itens em movimento, quando são variáveis ou desconhecidas. 

## <a name="functions"></a>Funções

Aqui está uma tabela de funções de verificação de tipo incorporada suportadas:

As funções seguintes suportam a verificação do tipo de verificação contra os valores de entrada, e cada retorno um valor Boolean.  

* [IS_ARRAY](sql-query-is-array.md)
* [IS_BOOL](sql-query-is-bool.md)
* [IS_DEFINED](sql-query-is-defined.md)
* [IS_NULL](sql-query-is-null.md)
* [IS_NUMBER](sql-query-is-number.md)
* [IS_OBJECT](sql-query-is-object.md)
* [IS_PRIMITIVE](sql-query-is-primitive.md)
* [IS_STRING](sql-query-is-string.md)

## <a name="next-steps"></a>Passos seguintes

- [Funciona O sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
- [Funções definidas pelo utilizador](sql-query-udfs.md)
- [Agregados](sql-query-aggregate-functions.md)
