---
title: Mapeamento de transformação de filtro de fluxo de dados de fábrica de dados do Azure
description: Mapeamento de transformação de filtro de fluxo de dados de fábrica de dados do Azure
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: d1751c47ad4507260d9f8d6ea44fcb32ed0e7338
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57864733"
---
# <a name="azure-data-factoryfilter-transformation"></a>Transformação de FactoryFilter de dados do Azure

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

As transformações de filtro fornece filtragem de linha. Crie uma expressão que define a condição de filtro. Clique na caixa de texto para iniciar o construtor de expressões. Dentro do construtor de expressões, crie uma expressão de filtro para controlar quais linhas do fluxo de dados atual estão autorizadas a pass-through (filtro) para a próxima transformação. Considere a transformação de filtro como a cláusula WHERE de uma instrução SQL.

## <a name="filter-on-loanstatus-column"></a>Filtrar pela coluna de loan_status:

```
in([‘Default’, ‘Charged Off’, ‘Fully Paid’], loan_status).
```

Filtre a coluna de ano a demonstração de filmes:

```
year > 1980
```

## <a name="next-steps"></a>Passos Seguintes

Tente uma coluna de filtragem de transformação, o [selecione transformação](data-flow-select.md)
