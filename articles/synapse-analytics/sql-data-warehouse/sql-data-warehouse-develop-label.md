---
title: Utilização de rótulos para consultas de instrumentos
description: Dicas para utilizar etiquetas para consultas de instrumentos no Azure SQL Data Warehouse para o desenvolvimento de soluções.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 828e4a406cd0fb12877af44263ab1f338c20850c
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351683"
---
# <a name="using-labels-to-instrument-queries-in-azure-sql-data-warehouse"></a>Utilização de etiquetas para consultas de instrumentos no Armazém de Dados Azure SQL
Dicas para utilizar etiquetas para consultas de instrumentos no Azure SQL Data Warehouse para o desenvolvimento de soluções.


## <a name="what-are-labels"></a>O que são rótulos?
O SQL Data Warehouse suporta um conceito chamado etiquetas de consulta. Antes de entrar em profundidade, vamos olhar para um exemplo:

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

A última linha marca a corda "My Consulta Label" à consulta. Esta etiqueta é particularmente útil, uma vez que a etiqueta é que pode ser consultada através dos DMVs. A consulta de etiquetas fornece um mecanismo para localizar consultas de problemas e ajudar a identificar o progresso através de uma execução elt.

Uma boa convenção de nomeação ajuda muito. Por exemplo, iniciar a etiqueta com PROJETO, PROCEDIMENTO, DECLARAÇÃO ou COMENTÁRIO ajuda a identificar exclusivamente a consulta entre todo o código no controlo de fontes.

A seguinte consulta utiliza uma visão de gestão dinâmica para pesquisar por etiqueta.

```sql
SELECT  *
FROM    sys.dm_pdw_exec_requests r
WHERE   r.[label] = 'My Query Label'
;
```

> [!NOTE]
> É essencial colocar suportes quadrados ou citações duplas em torno da etiqueta da palavra quando se consulta. A etiqueta é uma palavra reservada e causa um erro quando não é delimitada. 
> 
> 

## <a name="next-steps"></a>Passos seguintes
Para obter mais dicas de desenvolvimento, consulte a [visão geral do desenvolvimento.](sql-data-warehouse-overview-develop.md)


