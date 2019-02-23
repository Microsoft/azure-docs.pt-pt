---
title: Mapeamento de transformação de filtro de fluxo de dados de fábrica de dados do Azure
description: Mapeamento de transformação de filtro de fluxo de dados de fábrica de dados do Azure
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: 1930ca7406b6ef1b9fd20e1651bc031150dd952e
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56729548"
---
# <a name="azure-data-factory-mapping-data-flow-filter-transformation"></a>Mapeamento de transformação de filtro de fluxo de dados de fábrica de dados do Azure

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

As transformações de filtro fornece filtragem de linha. Crie uma expressão que define a condição de filtro. Clique na caixa de texto para iniciar o construtor de expressões. Dentro do construtor de expressões, crie uma expressão de filtro para controlar quais linhas do fluxo de dados atual estão autorizadas a pass-through (filtro) para a próxima transformação.

Ou seja, filtre pela coluna de loan_status:

```
in([‘Default’, ‘Charged Off’, ‘Fully Paid’], loan_status).
```

Filtre a coluna de ano a demonstração de filmes:

```
year > 1980
```
