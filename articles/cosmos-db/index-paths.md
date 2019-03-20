---
title: Trabalhar com caminhos de índice no Azure Cosmos DB
description: Descrição geral de caminhos de índice no Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 3/13/2019
ms.author: mjbrown
ms.openlocfilehash: d0fce763822ded374eab2f70c3f319aba0c89267
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57992825"
---
# <a name="index-paths-in-azure-cosmos-db"></a>Caminhos de índice no Azure Cosmos DB

Através de caminhos de índice no Azure Cosmos DB, pode optar por incluir ou excluir um caminho específico da indexação. Escolher caminhos específicos oferece desempenho melhorado de escrita e de armazenamento de índice mais baixo para cenários em que sabe que os padrões de consulta. Início de caminhos com a raiz de índice (`/`) o operador de caráter universal e, normalmente, terminar com o `?` operador de caráter universal. Este padrão indica que existem vários valores possíveis para o prefixo. Por exemplo, para servir a consulta `SELECT * FROM Families F WHERE F.familyName = "Andersen"`, tem de incluir um caminho de índice para `/familyName/?` na política de índice do contentor.

Caminhos de índice também podem utilizar o `*` operador de caráter universal para especificar o comportamento de caminhos recursivamente sob o prefixo. Por exemplo, `/payload/*` pode ser usado para excluir tudo sob a propriedade de payload da indexação.

## <a name="common-patterns-for-index-paths"></a>Padrões comuns para caminhos de índice

Aqui estão os padrões comuns para especificar caminhos de índice:

| **Caminho** | **Caso de utilização/descrição** |
| ---------- | ------- |
| /          | Caminho predefinido da coleção. Recursiva e aplica-se a árvore do documento inteiro.|
| / prop /?    | Caminho de índice necessário para servir consultas semelhante ao seguinte (com tipos de intervalo, respectivamente): <br><br>SELECT FROM collection c WHERE c.prop = "value"<br><br>SELECT FROM collection c WHERE c.prop > 5 <br><br>SELECT FROM collection c ORDER BY c.prop  |
| /prop/*    | Caminho de índice para todos os caminhos sob a etiqueta especificada. Funciona com as seguintes consultas <br><br>SELECT FROM collection c WHERE c.prop = "value"<br><br>SELECT FROM collection c WHERE c.prop.subprop > 5<br><br>SELECT FROM collection c WHERE c.prop.subprop.nextprop = "value"<br><br>SELECT FROM collection c ORDER BY c.prop |
| /props/[]/?| Caminho de índice necessário para servir a iteração e Junte-se a consultas em matrizes de escalares, como ["a", "b", "c"]:<br><br>SELECT tag FROM tag IN collection.props WHERE tag = "value"<br><br>SELECT tag FROM collection c JOIN tag IN c.props WHERE tag > 5 |
| /props/[]/subprop/? | Caminho de índice necessário para servir a iteração e consultas de JUNÇÃO em relação a matrizes de objetos, como [{subprop: "a"}, {subprop: "b"}]:<br><br>SELECT tag FROM tag IN collection.props WHERE tag.subprop = "value"<br><br>SELECT tag FROM collection c JOIN tag IN c.props WHERE tag.subprop = "value" |
| /prop/subprop/? | Caminho de índice necessário para servir consultas (com tipos de intervalo, respectivamente):<br><br>SELECT FROM collection c WHERE c.prop.subprop = "value"<br><br>SELECT FROM collection c WHERE c.prop.subprop > 5  |

Quando definir os caminhos de índice personalizado, tem de especificar a regra de indexação predefinida para o item inteiro, assinalado com o caminho especial `/*`.

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre a indexação no Azure Cosmos DB nos seguintes artigos:

- [Descrição geral de indexação](index-overview.md)
- [Políticas de indexação no Azure Cosmos DB](indexing-policies.md)
- [Tipos de índice no Azure Cosmos DB](index-types.md)
