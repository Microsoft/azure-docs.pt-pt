---
title: Utilize etiquetas de consulta em Synapse SQL
description: Incluído neste artigo são dicas essenciais para a utilização de etiquetas de consulta no Synapse SQL.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: 59fa68d12f1d8be598810399fc5623c2af983979
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96462257"
---
# <a name="use-query-labels-in-synapse-sql"></a>Utilize etiquetas de consulta em Synapse SQL

Incluído neste artigo são dicas essenciais para a utilização de etiquetas de consulta no Synapse SQL.

> [!NOTE]
> A piscina SQL sem servidor não suporta consultas de rotulagem.

## <a name="what-are-query-labels"></a>O que são rótulos de consulta

A piscina dedicada SQL suporta um conceito chamado etiquetas de consulta. Antes de entrar em qualquer profundidade, vamos olhar para um exemplo:

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

A última linha marca a cadeia 'My Query Label' para a consulta. Esta etiqueta é útil uma vez que a etiqueta é passável através dos DMVs. A consulta de etiquetas fornece um mecanismo para localizar consultas de problemas e ajuda a identificar o progresso através de uma execução ELT.

Boas convenções de nomeação são muito úteis. Por exemplo, iniciar o rótulo com PROJECT, PROCEDURE, STATEMENT ou COMMENT identifica de forma única a consulta entre todo o código em controlo de origem.

A seguinte consulta utiliza uma visão de gestão dinâmica para pesquisar por etiqueta:

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
Para obter mais dicas de desenvolvimento, consulte [a visão geral do desenvolvimento.](develop-overview.md)


