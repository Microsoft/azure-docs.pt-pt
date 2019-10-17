---
title: Transformação do filtro de fluxo de dados de mapeamento de Azure Data Factory
description: Transformação do filtro de fluxo de dados de mapeamento de Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: 2afe079c346a15ec212664ce022ac5e2926b12d4
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387812"
---
# <a name="azure-data-factory-filter-transformation"></a>Transformação de filtro de data factory do Azure



As transformações de filtro fornecem filtragem de linha. Crie uma expressão que define a condição de filtro. Clique na caixa de texto para iniciar o construtor de expressões. Dentro do construtor de expressões, crie uma expressão de filtro para controlar quais linhas do fluxo de dados atual têm permissão para passar (filtrar) para a próxima transformação. Considere a transformação de filtro como a cláusula WHERE de uma instrução SQL.

## <a name="filter-on-loan_status-column"></a>Filtrar na coluna loan_status:

```
in([‘Default’, ‘Charged Off’, ‘Fully Paid’], loan_status).
```

Filtre a coluna year na demonstração de filmes:

```
year > 1980
```

## <a name="next-steps"></a>Passos seguintes

Experimente uma transformação filtragem de coluna, a [transformação selecionar](data-flow-select.md)
