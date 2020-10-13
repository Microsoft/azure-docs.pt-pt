---
title: Utilização de rótulos para consultas de instrumentos
description: Dicas para utilizar rótulos para consultas de instrumentos na piscina Synapse SQL para o desenvolvimento de soluções.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 91b6dac5fba4bb8dfd8cf4a3bb4e5952f8388bb5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89459152"
---
# <a name="using-labels-to-instrument-queries-in-synapse-sql-pool"></a>Utilização de rótulos para consultas de instrumentos na piscina Sinaapse SQL

Incluído neste artigo estão dicas para o desenvolvimento de soluções que usam rótulos para consultas de instrumentos na piscina SQL.

Dicas para utilizar rótulos para consultas de instrumentos no Azure Synapse Analytics para o desenvolvimento de soluções.

## <a name="what-are-labels"></a>O que são rótulos?

A piscina SQL suporta um conceito chamado etiquetas de consulta. Antes de entrar em qualquer profundidade, vamos olhar para um exemplo:

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

A última linha marca a cadeia 'My Query Label' para a consulta. Esta etiqueta é útil porque a etiqueta é passável através dos DMVs.

A consulta de etiquetas fornece um mecanismo para localizar consultas de problemas e ajudar a identificar o progresso através de uma execução ELT.

Uma boa convenção de nomeação ajuda muito. Por exemplo, iniciar o rótulo com PROJECT, PROCEDURE, STATEMENT ou COMMENT identifica de forma única a consulta entre todo o código em controlo de origem.

A seguinte consulta utiliza uma visão de gestão dinâmica para pesquisar por etiqueta:

```sql
SELECT  *
FROM    sys.dm_pdw_exec_requests r
WHERE   r.[label] = 'My Query Label'
;
```

> [!NOTE]
> É essencial colocar suportes quadrados ou citações duplas em torno da etiqueta da palavra quando se consulta. A etiqueta é uma palavra reservada e causa um erro quando não é delimitada.

## <a name="next-steps"></a>Passos seguintes

Para obter mais dicas de desenvolvimento, consulte [a visão geral do desenvolvimento.](sql-data-warehouse-overview-develop.md)
