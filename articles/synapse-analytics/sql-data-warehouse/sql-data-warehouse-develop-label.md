---
title: Utilização de rótulos para consultas de instrumentos
description: Dicas para usar etiquetas para consultas de instrumentos para piscinas SQL dedicadas em Azure Synapse Analytics.
services: synapse-analytics
author: MSTehrani
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: emtehran
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 7958caf71658dcdcbf31bac84697931e9049452f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96462747"
---
# <a name="using-labels-to-instrument-queries-for-dedicated-sql-pools-in-azure-synapse-analytics"></a>Utilização de rótulos para consultas de instrumentos para piscinas SQL dedicadas em Azure Synapse Analytics

Incluído neste artigo estão dicas para o desenvolvimento de soluções que usam rótulos para consultas de instrumentos em piscinas SQL dedicadas.

## <a name="what-are-labels"></a>O que são rótulos?

A piscina dedicada SQL suporta um conceito chamado etiquetas de consulta. Antes de entrar em qualquer profundidade, vamos olhar para um exemplo:

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
