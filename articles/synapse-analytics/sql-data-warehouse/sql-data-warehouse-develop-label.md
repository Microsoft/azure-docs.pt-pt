---
title: Utilização de rótulos para consultas de instrumentos
description: Dicas para usar etiquetas para consultas de instrumentos na piscina SYnapse SQL para desenvolver soluções.
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
ms.openlocfilehash: 5e2cd03ae878e80139a7f7a8ba67cef15b24d571
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633496"
---
# <a name="using-labels-to-instrument-queries-in-synapse-sql-pool"></a>Utilização de etiquetas para consultas de instrumentos na piscina Synapse SQL

Incluídos neste artigo estão dicas para desenvolver soluções usando etiquetas para consultas de instrumentos na piscina SQL.

Dicas para utilizar etiquetas para consultas de instrumentos no Azure SQL Data Warehouse para o desenvolvimento de soluções.

## <a name="what-are-labels"></a>O que são rótulos?

A piscina SQL suporta um conceito chamado etiquetas de consulta. Antes de entrar em profundidade, vamos olhar para um exemplo:

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

A última linha marca a corda "My Consulta Label" à consulta. Esta etiqueta é útil porque a etiqueta é que pode ser consultada através dos DMVs.

A consulta de etiquetas fornece um mecanismo para localizar consultas de problemas e ajudar a identificar o progresso através de uma execução elt.

Uma boa convenção de nomeação ajuda muito. Por exemplo, iniciar a etiqueta com PROJETO, PROCEDIMENTO, DECLARAÇÃO ou COMENTÁRIO identifica exclusivamente a consulta entre todo o código no controlo de fontes.

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

Para obter mais dicas de desenvolvimento, consulte a [visão geral do desenvolvimento.](sql-data-warehouse-overview-develop.md)
