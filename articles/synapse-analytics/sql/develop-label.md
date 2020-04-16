---
title: Utilize etiquetas de consulta em Synapse SQL
description: Incluídos neste artigo são dicas essenciais para a utilização de etiquetas de consulta no Synapse SQL.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: 47b476cbc6997ca5ec63968bdc269e2273662100
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430035"
---
# <a name="use-query-labels-in-synapse-sql"></a>Utilize etiquetas de consulta em Synapse SQL
Incluídos neste artigo são dicas essenciais para a utilização de etiquetas de consulta no Synapse SQL.

> [!NOTE]
> SQL on-demand (pré-visualização) não suporta consultas de rotulagem.

## <a name="what-are-query-labels"></a>O que são rótulos de consulta
A piscina SQL suporta um conceito chamado etiquetas de consulta. Antes de entrar em profundidade, vamos olhar para um exemplo:

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

A última linha marca a corda "My Consulta Label" à consulta. Esta etiqueta é particularmente útil, uma vez que a etiqueta é que pode ser consultada através dos DMVs. A consulta de etiquetas fornece um mecanismo para localizar consultas de problemas e ajuda a identificar o progresso através de uma execução elt.

Uma boa convenção de nomeação é muito útil. Por exemplo, iniciar a etiqueta com PROJETO, PROCEDIMENTO, DECLARAÇÃO ou COMENTÁRIO identifica exclusivamente a consulta entre todo o código no controlo de fontes.

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
Para obter mais dicas de desenvolvimento, consulte a [visão geral do desenvolvimento.](develop-overview.md)


